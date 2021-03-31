---
title: Flusso di lavoro dell'architettura VM di Windows Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dei processi del flusso di lavoro quando si distribuisce un servizio.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 606510940460db963a2aa63deb57b6dba77de3ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700134"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Flusso di lavoro dell'architettura di macchine virtuali di Windows Azure classico 

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Questo articolo fornisce una panoramica dei processi del flusso di lavoro che si verificano quando si distribuisce o si aggiorna una risorsa di Azure, ad esempio una macchina virtuale. 

> [!NOTE]
>Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Resource Manager e distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica.

Il diagramma seguente illustra l'architettura delle risorse di Azure.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<Alt l'immagine del flusso di lavoro di Azure>":::

## <a name="workflow-basics"></a>Nozioni fondamentali sui flussi di lavoro
   
**A**. RDFE/FFE è il percorso di comunicazione tra l'utente e l'infrastruttura. RDFE (RedDog Front End) è l'API esposta pubblicamente che rappresenta il front-end per l'portale di gestione e il API Gestione dei servizi, ad esempio Visual Studio, Azure MMC e così via.  Tutte le richieste dall'utente passano attraverso RDFE. FFE (Fabric front-end) è il livello che converte le richieste da RDFE in comandi di infrastruttura. Tutte le richieste da RDFE passano attraverso il FFE per raggiungere i controller di infrastruttura.

**B**. Il controller di infrastruttura è responsabile della gestione e del monitoraggio di tutte le risorse nel data center. Comunica con gli agenti host dell'infrastruttura nel sistema operativo dell'infrastruttura che invia informazioni quali la versione del sistema operativo guest, il pacchetto del servizio, la configurazione del servizio e lo stato del servizio.

**C**. L'agente host risiede nel sistema operativo host ed è responsabile della configurazione del sistema operativo guest e della comunicazione con l'agente Guest (WindowsAzureGuestAgent) per aggiornare il ruolo verso uno stato obiettivo previsto ed eseguire controlli heartbeat con l'agente guest. Se l'agente host non riceve risposta heartbeat per 10 minuti, l'agente host riavvia il sistema operativo guest.

**C2**. WaAppAgent è responsabile dell'installazione, della configurazione e dell'aggiornamento di WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent è responsabile per gli elementi seguenti:

1. Configurazione del sistema operativo guest, tra cui firewall, ACL, risorse LocalStorage, pacchetto e configurazione del servizio e certificati.
2. Impostazione del SID per l'account utente con cui viene eseguito il ruolo.
3. Comunicare lo stato del ruolo all'infrastruttura.
4. Avviare WaHostBootstrapper e monitorarlo per verificare che il ruolo sia in stato obiettivo.

**E**. WaHostBootstrapper è responsabile di:

1. Leggere la configurazione del ruolo e avviare tutte le attività e i processi appropriati per configurare ed eseguire il ruolo.
2. Monitoraggio di tutti i processi figlio.
3. Generazione dell'evento StatusCheck nel processo host del ruolo.

**F**. IISConfigurator viene eseguito se il ruolo è configurato come un ruolo Web IIS completo. È responsabile di:

1. Avvio dei servizi IIS standard
2. Configurazione del modulo Rewrite nella configurazione Web
3. Configurazione di AppPool per il ruolo configurato nel modello del servizio
4. Impostazione della registrazione IIS in modo che punti alla cartella DiagnosticStore LocalStorage
5. Configurazione di autorizzazioni e ACL
6. Il sito Web si trova in% RoleRoot%: \sitesroot\0 e AppPool punta a questo percorso per eseguire IIS. 

**G**. Le attività di avvio sono definite dal modello di ruolo e avviate da WaHostBootstrapper. Le attività di avvio possono essere configurate per l'esecuzione in background in modo asincrono e il programma di avvio automatico dell'host avvierà l'attività di avvio e quindi continuerà ad altre attività di avvio. Le attività di avvio possono anche essere configurate per essere eseguite in modalità semplice (impostazione predefinita) in cui il programma di avvio automatico dell'host attende il completamento dell'esecuzione dell'attività di avvio e restituisce un codice di uscita con esito positivo (0) prima di continuare con l'attività di avvio successiva.

**H**. Queste attività fanno parte dell'SDK e sono definite come plug-in nella definizione del servizio del ruolo (csdef). Quando si espandono le attività di avvio, **DiagnosticsAgent** e **RemoteAccessAgent** sono univoci in quanto ognuna definiscono due attività di avvio, una normale e una con un parametro **/blockStartup** . L'attività di avvio normale è definita come un'attività di avvio in background in modo che possa essere eseguita in background mentre il ruolo stesso è in esecuzione. L'attività di avvio **/blockStartup** è definita come semplice attività di avvio in modo che WaHostBootstrapper attenda la chiusura prima di continuare. L'attività **/blockStartup** è in attesa del completamento dell'inizializzazione dell'attività normale, quindi termina e consente al programma di avvio automatico dell'host di continuare. Questa operazione viene eseguita in modo che sia possibile configurare la diagnostica e l'accesso RDP prima dell'avvio dei processi del ruolo. questa operazione viene eseguita tramite l'attività/blockStartup. Questo consente anche di continuare a eseguire la diagnostica e l'accesso RDP dopo che il programma di avvio automatico dell'host ha completato le attività di avvio (questa operazione viene eseguita tramite la normale attività).

**I**. WaWorkerHost è il processo host standard per i ruoli di lavoro normali. Questo processo host ospita tutte le dll e il codice del punto di ingresso del ruolo, ad esempio OnStart ed Run.

**J**. WaIISHost è il processo host per il codice del punto di ingresso del ruolo per i ruoli Web che usano la versione completa di IIS. Questo processo carica la prima DLL trovata che usa la classe **RoleEntryPoint** ed esegue il codice da questa classe (OnStart, Run, OnStop). In questo processo vengono generati tutti gli eventi **RoleEnvironment** , ad esempio StatusCheck e Changed, creati nella classe RoleEntryPoint.

**K**. W3WP è il processo di lavoro IIS standard utilizzato se il ruolo è configurato per l'utilizzo di IIS completo. Viene eseguito il AppPool configurato da IISConfigurator. In questo processo vengono generati tutti gli eventi RoleEnvironment, ad esempio StatusCheck e Changed. Si noti che gli eventi RoleEnvironment vengono attivati in entrambe le posizioni (WaIISHost e w3wp.exe) se si sottoscrivono eventi in entrambi i processi.

## <a name="workflow-processes"></a>Processi del flusso di lavoro

1. Un utente effettua una richiesta, ad esempio il caricamento di file ". cspkg" e ". cscfg", indicando a una risorsa di arrestare o apportare una modifica alla configurazione e così via. Questa operazione può essere eseguita tramite il portale di Azure o uno strumento che usa il API Gestione dei servizi, ad esempio la funzionalità di pubblicazione di Visual Studio. Questa richiesta passa a RDFE per eseguire tutte le operazioni correlate alla sottoscrizione e quindi comunica la richiesta a FFE. Il resto di questi passaggi del flusso di lavoro consiste nel distribuire un nuovo pacchetto e avviarlo.
2. FFE trova il pool di computer corretto (in base all'input del cliente, ad esempio il gruppo di affinità o la posizione geografica più l'input dall'infrastruttura, ad esempio la disponibilità del computer) e comunica con il controller di infrastruttura master nel pool di computer.
3. Il controller di infrastruttura trova un host che dispone di core CPU disponibili (oppure avvia un nuovo host). Il pacchetto e la configurazione del servizio vengono copiati nell'host e il controller di infrastruttura comunica con l'agente host nel sistema operativo host per distribuire il pacchetto (configurare DIP, porte, sistema operativo guest e così via).
4. L'agente host avvia il sistema operativo guest e comunica con l'agente Guest (WindowsAzureGuestAgent). L'host invia heartbeat al Guest per assicurarsi che il ruolo stia lavorando allo stato dell'obiettivo.
5. WindowsAzureGuestAgent configura il sistema operativo guest (firewall, ACL, LocalStorage e così via), copia un nuovo file di configurazione XML in c:\Config, quindi avvia il processo WaHostBootstrapper.
6. Per i ruoli Web IIS completi, WaHostBootstrapper avvia IISConfigurator e indica di eliminare eventuali AppPools esistenti per il ruolo Web da IIS.
7. WaHostBootstrapper legge le attività di **avvio** da E:\RoleModel.xml e inizia a eseguire le attività di avvio. WaHostBootstrapper attende il completamento di tutte le semplici attività di avvio e restituisce un messaggio "operazione riuscita".
8. Per i ruoli Web IIS completi, WaHostBootstrapper indica a IISConfigurator di configurare IIS AppPool e indirizza il sito a `E:\Sitesroot\<index>` , dove `<index>` è un indice in base zero nel numero di `<Sites>` elementi definiti per il servizio.
9. WaHostBootstrapper avvierà il processo host a seconda del tipo di ruolo:
    1. **Ruolo di lavoro**: WaWorkerHost.exe avviata. WaHostBootstrapper esegue il metodo OnStart (). Una volta restituito, WaHostBootstrapper inizia a eseguire il metodo Run () e quindi contrassegna simultaneamente il ruolo come pronto e lo inserisce nella rotazione del servizio di bilanciamento del carico (se InputEndpoints sono definiti). WaHostBootsrapper entra quindi in un ciclo di controllo dello stato del ruolo.
    2. **Ruolo Web IIS completo**: aIISHost è stato avviato. WaHostBootstrapper esegue il metodo OnStart (). Una volta restituito, inizia a eseguire il metodo Run () e quindi contrassegna simultaneamente il ruolo come pronto e lo inserisce nella rotazione del servizio di bilanciamento del carico. WaHostBootsrapper entra quindi in un ciclo di controllo dello stato del ruolo.
10. Le richieste Web in ingresso a un ruolo Web IIS completo attivano IIS per avviare il processo W3WP e gestire la richiesta, allo stesso modo in un ambiente IIS locale.

## <a name="log-file-locations"></a>Percorsi file di log

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Questo log contiene le modifiche apportate al servizio, tra cui l'avvio, l'arresto e le nuove configurazioni. Se il servizio non viene modificato, in questo file di log è possibile che si verifichino notevoli gap di tempo.
- C:\Logs\WaAppAgent.Log.  
Questo log contiene gli aggiornamenti di stato e le notifiche heartbeat e viene aggiornato ogni 2-3 secondi.  Questo log contiene una visualizzazione cronologica dello stato dell'istanza di e indica quando lo stato dell'istanza non è pronto.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Log IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Log eventi di Windows**

`D:\Windows\System32\Winevt\Logs`
