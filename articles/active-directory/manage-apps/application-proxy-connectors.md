---
title: Comprendere i connettori del proxy applicazione Azure AD | Microsoft Docs
description: Informazioni sui connettori del proxy di applicazione Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f57d390ed71cb4e0e76972e02170afde7b13e4ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253460"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Comprendere i connettori del proxy applicazione Azure AD

I connettori sono ciò che rende possibile il proxy di applicazione di Azure AD. Sono semplici, estremamente potenti e facile da distribuire e gestire. Questo articolo illustra i connettori, il loro funzionamento e alcune procedure consigliate per ottimizzare al meglio la distribuzione.

## <a name="what-is-an-application-proxy-connector"></a>Informazioni su un connettore proxy di applicazione

I connettori sono agenti semplici che si trovano a livello locale e facilitano la connessione in uscita al servizio proxy di applicazione. I connettori devono essere installati su un server Windows dotato di accesso all'applicazione back-end. È possibile organizzare i connettori in gruppi di connettori, con ogni gruppo che gestisce il traffico per applicazioni specifiche. Per altre informazioni sul proxy di applicazione e una rappresentazione diagrammatiche dell'architettura del proxy di applicazione, vedere [uso di Azure ad proxy di applicazione per pubblicare app locali per gli utenti remoti](what-is-application-proxy.md#application-proxy-connectors)

## <a name="requirements-and-deployment"></a>Requisiti e distribuzione

Per distribuire correttamente il proxy di applicazione, è necessario almeno un connettore, ma sono consigliati due o più connettori per assicurare maggiore resilienza. Installare il connettore in un computer che esegue Windows Server 2012 R2 o versione successiva. Il connettore deve poter comunicare con il servizio proxy di applicazione e con le applicazioni locali che vengono pubblicate.

### <a name="windows-server"></a>Windows Server
È necessario un server che esegue Windows Server 2012 R2 o versione successiva in cui poter installare il connettore del proxy applicazione. Questo server deve connettersi ai servizi proxy di applicazione in Azure e alle applicazioni locali che vengono pubblicate.

È necessario abilitare TLS 1.2 per Windows Server prima di installare il connettore del proxy di applicazione. Per abilitare TLS 1,2 sul server:

1. Impostare le chiavi del Registro di sistema seguenti:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Riavviare il server

Per ulteriori informazioni sui requisiti di rete per il server del connettore, vedere [Introduzione al proxy dell'applicazione e installazione di un connettore](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenzione

I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. Possono essere aggiunti o rimossi in modo dinamico. Ogni volta che arriva una nuova richiesta, questa viene indirizzata a uno dei connettori attualmente disponibili. Se un connettore è temporaneamente non disponibile, non risponde a questo traffico.

I connettori sono senza stato e senza dati di configurazione nel computer. Gli unici dati che archiviano sono le impostazioni per la connessione del servizio e il relativo certificato di autenticazione. Quando si connettono al servizio, eseguono il pull di tutti i dati di configurazione necessari e li aggiornano ogni due minuti.

I connettori eseguono inoltre il polling del server per verificare se è disponibile una versione più recente del connettore. Se ne viene individuata una, i connettori vengono aggiornati.

È possibile monitorare i connettori dal computer in cui vengono eseguiti usando il registro eventi e i contatori delle prestazioni. In alternativa è possibile vedere lo stato dalla pagina del proxy di applicazione del portale di Azure:

![Esempio: Azure AD connettori del proxy di applicazione](./media/application-proxy-connectors/app-proxy-connectors.png)

Non è necessario eliminare manualmente i connettori che non vengono usati. Quando un connettore è in esecuzione, rimane attivo quando si connette al servizio. I connettori inutilizzati vengono contrassegnati come _inattivi_ e vengono rimossi dopo 10 giorni di inattività. Per disinstallare un connettore, disinstallare sia il servizio connettore che il servizio di aggiornamento dal server. Riavviare il computer per rimuovere completamente il servizio.

## <a name="automatic-updates"></a>Aggiornamenti automatici

Azure AD offre aggiornamenti automatici per tutti i connettori da distribuire. Fino a quando il servizio di aggiornamento del connettore proxy di applicazione è in esecuzione, i connettori vengono aggiornati automaticamente. Se non viene visualizzato il servizio di aggiornamento del connettore nel server, è necessario [reinstallare il connettore](application-proxy-add-on-premises-application.md) per ottenere gli aggiornamenti.

Se non si vuole attendere l'aggiornamento automatico del connettore, è possibile eseguire un aggiornamento manuale. Passare alla [pagina di download del connettore](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) nel server in cui si trova il connettore e selezionare **Download**. Grazie a questo processo viene avviato un aggiornamento del connettore locale.

In caso di tenant con più connettori, gli aggiornamenti automatici vengono applicati a un connettore per volta in ogni gruppo, al fine di evitare tempo di inattività nell'ambiente in uso.

Potrebbero verificarsi tempi di inattività quando viene aggiornato il connettore se:
  
- Si ha un solo connettore. È consigliabile installare un secondo connettore e [creare un gruppo di connettori](application-proxy-connector-groups.md): in questo modo è possibile evitare periodi di inattività e assicurare una disponibilità più elevata.  
- Un connettore si trovava nel mezzo di una transazione quando è iniziato l'aggiornamento. Anche se la transazione iniziale viene persa, il browser dovrebbe ripetere automaticamente l'operazione. In caso contrario, è possibile aggiornare la pagina. Quando la richiesta viene inviata di nuovo, il traffico viene indirizzato a un connettore di backup.

Per visualizzare informazioni sulle versioni rilasciate in precedenza e sulle modifiche che includono, vedere la pagina relativa alla cronologia delle versioni del [proxy di applicazione](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Creazione di gruppi di connettori

I gruppi di connettori consentono di assegnare connettori specifici per gestire applicazioni specifiche. È possibile raggruppare una serie di connettori e quindi assegnare ogni applicazione a un gruppo.

I gruppi di connettori rendono più semplice gestire le distribuzioni di grandi dimensioni. Migliorano inoltre la latenza per i tenant che dispongono di applicazioni ospitate in diverse aree geografiche, poiché è possibile creare gruppi di connettori basati sulla posizione per gestire solo le applicazioni in locale.

Per altre informazioni sui gruppi di connettori, vedere [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Pianificazione della capacità

È importante assicurarsi di aver pianificato la capacità sufficiente tra i connettori per gestire il volume di traffico previsto. È consigliabile che ogni gruppo di connettori disponga di almeno due connettori per garantire disponibilità elevata e scalabilità. La presenza di tre connettori è ottimale nel caso in cui sia necessario servire un computer in qualsiasi momento.

In generale, più utenti si hanno, più sarà grande il computer necessario. Di seguito è riportata una tabella che fornisce un contorno del volume e la latenza prevista può essere gestita da computer diversi. Tutto è basato sulle transazioni al secondo (TPS) previste e non sugli utenti, poiché i modelli di utilizzo variano e non possono essere usati per stimare il carico. Esistono inoltre alcune differenze basate sulla dimensione delle risposte e sul tempo di risposta dell'applicazione back-end. Dimensioni di risposta più grandi e tempi di risposta più lenti comporteranno un numero massimo di TPS inferiore. Si consiglia inoltre di avere computer aggiuntivi, in modo che il carico distribuito tra le macchine fornisca sempre un ampio buffer. La capacità aggiuntiva che si ottiene garantisce elevati livelli di disponibilità e resilienza.

|Core|RAM|Latenza prevista (MS)-P99|Numero massimo di TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Questo computer usava un'impostazione personalizzata per generare alcuni dei limiti di connessione predefiniti oltre le impostazioni consigliate per .NET. È consigliabile eseguire un test con le impostazioni predefinite prima di contattare il supporto per ottenere la modifica di questo limite per il tenant specifico.

> [!NOTE]
> Non c'è molta differenza nel numero massimo di TPS tra computer Core 4, 8 e 16. La differenza principale è la latenza prevista.
>
> Questa tabella è incentrata anche sulle prestazioni previste di un connettore in base al tipo di computer in cui è installato. Questa operazione è separata dai limiti di limitazione del servizio proxy di applicazione, vedere [limiti e restrizioni del servizio](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Sicurezza e rete

I connettori possono essere installati in qualsiasi punto della rete che consenta loro di inviare richieste al servizio proxy dell'applicazione. È importante che il computer che esegue il connettore abbia anche accesso alle app. È possibile installare i connettori all'interno della rete aziendale o in una macchina virtuale che viene eseguita nel cloud. I connettori possono essere eseguiti all'interno di una rete perimetrale, nota anche come zona demilitarizzata (DMZ), ma non è necessario poiché tutto il traffico è in uscita, in modo che la rete rimanga protetta.

I connettori inviano le richieste soltanto in uscita. Il traffico in uscita viene inviato al servizio proxy applicazione e alle applicazioni pubblicate. Non è necessario aprire porte in ingresso perché il traffico scorre in entrambe le direzioni, dopo aver stabilito una sessione. Non è inoltre necessario configurare l'accesso in ingresso attraverso firewall.

Per maggiori informazioni sulla configurazione delle regole del firewall in uscita, vedere [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestazioni e scalabilità

Anche se la scalabilità per il servizio proxy di applicazione è trasparente, è comunque un fattore per i connettori. È necessario disporre di connettori sufficienti per gestire il traffico di picco. Poiché i connettori sono senza stato, non vengono influenzati dal numero di utenti o sessioni. Dipendono invece dal numero di richieste e dalle dimensioni del payload. In un traffico Web standard, un computer medio può gestire circa duemila richieste al secondo. La capacità specifica dipende dalle esatte caratteristiche del computer.

Le prestazioni del connettore sono legate alla CPU e alla rete. Le prestazioni della CPU sono necessarie per la crittografia e la decrittografia TLS, mentre la rete è importante per ottenere una connettività veloce alle applicazioni e al servizio online in Azure.

La memoria, al contrario, ha meno importanza per i connettori. Il servizio online si occupa di gran parte dell'elaborazione e di tutto il traffico non autenticato. Tutto ciò che può essere fatto nel cloud viene fatto nel cloud.

Se per qualsiasi motivo il connettore o il computer non sono più disponibili, il traffico inizierà ad andare su un altro connettore del gruppo. Questa resilienza esiste anche perché si consiglia di avere più connettori.

Un altro fattore che influenza le prestazioni è la qualità della connessione di rete tra i connettori, inclusi:

- **Il servizio online**: connessioni lente o a elevata latenza al servizio proxy di applicazione in Azure influenzano le prestazioni del connettore. Per ottenere prestazioni ottimali, connettere l'organizzazione ad Azure con Express Route. In caso contrario, assicurarsi che il team di rete garantisca una gestione il più possibile efficiente delle connessioni ad Azure.
- **Applicazioni back-end:** in alcuni casi ci sono altri proxy tra il connettore e le applicazioni back-end che possono rallentare o impedire la connessione. Per risolvere questo scenario, aprire un browser dal server del connettore e tentare di accedere all'applicazione. Se si eseguono i connettori in Azure, ma le applicazioni sono locali, l'esperienza degli utenti potrebbe essere diversa da quella prevista.
- **Controller di dominio**: se i connettori eseguono Single Sign-on (SSO) con la delega vincolata Kerberos, contattano i controller di dominio prima di inviare la richiesta al back-end. I connettori hanno una cache dei ticket Kerberos, ma in ambienti affollati la velocità di risposta dei controller di dominio può influenzare le prestazioni. Questa situazione è più comune per i connettori eseguiti in Azure, ma che comunicano con i controller di dominio locali.

Per maggiori informazioni sull'ottimizzazione della rete, vedere [Considerazioni relative alla topologia di rete quando si usa il proxy di applicazione di Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Aggiunta al dominio

I connettori possono essere eseguiti in un computer che non fa parte del dominio. È necessario tuttavia un computer appartenente al dominio se si sceglie di implementare un accesso Single Sign-On (SSO) per le applicazioni che usano l'autenticazione integrata di Windows (IWA). In questo caso i computer di connessione devono appartenere a un dominio che può eseguire la delega vincolata [Kerberos](https://web.mit.edu/kerberos) per conto degli utenti per le applicazioni pubblicate.

I connettori possono anche essere aggiunti a domini in foreste con attendibilità parziale o a controller di dominio di sola lettura.

## <a name="connector-deployments-on-hardened-environments"></a>Distribuzione dei connettori in ambienti protetti

Nella maggior parte dei casi la distribuzione dei connettori è molto semplice e non richiede una configurazione speciale. Esistono tuttavia alcune condizioni specifiche che devono essere considerate:

- Le organizzazioni che limitano il traffico in uscita devono [aprire le porte necessarie](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Per i computer conformi FIPS potrebbe essere necessario modificare la configurazione per consentire ai processi connettore di generare e archiviare un certificato.
- Le organizzazioni, che bloccano il proprio ambiente in base ai processi che inviano le richieste di rete, devono assicurarsi che siano abilitati entrambi i servizi connettore per accedere a tutte le porte e agli indirizzi IP necessari.
- In alcuni casi il proxy di inoltro in uscita può interrompere l'autenticazione bidirezionale con certificato e non consentire la comunicazione.

## <a name="connector-authentication"></a>Autenticazione del connettore

Per garantire un servizio sicuro, i connettori devono eseguire l'autenticazione verso il servizio e il servizio deve eseguire l'autenticazione verso il connettore. Questa autenticazione viene eseguita usando i certificati client e server quando i connettori avviano la connessione. In questo modo il nome utente e la password dell'amministratore non sono archiviati sul computer di connessione.

I certificati utilizzati sono specifici per il servizio proxy applicazione. Vengono creati durante la registrazione iniziale e rinnovati automaticamente dai connettori ogni due mesi.

Al termine del primo rinnovo del certificato riuscito, il servizio di Azure AD connettore del proxy di applicazione (servizio di rete) non dispone dell'autorizzazione per rimuovere il certificato precedente dall'archivio del computer locale. Se il certificato è scaduto o non verrà più usato dal servizio, è possibile eliminarlo in modo sicuro.

Per evitare problemi con il rinnovo del certificato, assicurarsi che la comunicazione di rete dal connettore verso le [destinazioni documentate](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) sia abilitata.

Se un connettore non viene connesso al servizio per molti mesi, i relativi certificati potrebbero non essere più aggiornati. In questo caso, disinstallare e reinstallare il connettore per attivare la registrazione. È possibile eseguire i seguenti comandi di PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Per enti pubblici, usare `-EnvironmentName "AzureUSGovernment"` . Per informazioni dettagliate, vedere [installare l'agente per il cloud di Azure per enti pubblici](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Per ulteriori informazioni su come verificare il certificato e risolvere i problemi, vedere [verificare il supporto dei componenti di computer e back-end per il certificato di attendibilità del proxy di applicazione](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>dietro le quinte

I connettori sono basati su proxy di applicazione Web di Windows Server, per cui condividono la maggior parte degli strumenti di gestione, inclusi i log eventi di Windows

![Gestire i log eventi con il Visualizzatore eventi](./media/application-proxy-connectors/event-view-window.png)

e con i contatori delle prestazioni di Windows.

![Aggiungere contatori al connettore con Performance Monitor](./media/application-proxy-connectors/performance-monitor.png)

I connettori hanno sia log di **amministratore** che di **sessione** . Il log di **Amministrazione** include gli eventi principali e i relativi errori. Il log della **sessione** include tutte le transazioni e i relativi dettagli di elaborazione.

Per visualizzare i log, aprire **Visualizzatore eventi** e passare a **registri applicazioni e servizi**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**. Per rendere visibile il log della **sessione** , scegliere **Mostra log analitici e di debug** dal menu **Visualizza** . Il log della **sessione** viene in genere utilizzato per la risoluzione dei problemi ed è disabilitato per impostazione predefinita. Abilitarlo per iniziare a raccogliere gli eventi e disabilitarlo quando non è più necessario.

È possibile esaminare lo stato del servizio nella finestra Servizi. Il connettore è costituito da due servizi di Windows, ovvero il connettore stesso e il programma di aggiornamento. Entrambi devono essere eseguiti costantemente.

 ![Esempio: finestra dei servizi che mostra Azure AD servizi locali](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passaggi successivi

- [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
- [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
- [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
- [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
