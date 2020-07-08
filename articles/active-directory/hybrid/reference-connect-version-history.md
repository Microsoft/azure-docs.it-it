---
title: 'Azure AD Connect: Cronologia del rilascio delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni di Azure AD Connect e Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf5e161b46b9ec6e39702ddd8435a7c7672111f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550124"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Cronologia delle versioni
Il team di Azure Active Directory (Azure AD) aggiorna regolarmente Azure AD Connect con nuove funzionalità. Le nuove funzionalità potrebbero non essere disponibili in tutti i paesi.

Lo scopo di questo articolo è consentire agli utenti di esaminare le versioni rilasciate e conoscere le modifiche della versione più recente.

Questa tabella contiene un elenco degli argomenti correlati:

Argomento |  Dettagli
--------- | --------- |
Passaggi da eseguire per l'aggiornamento da Azure AD Connect | Metodi per [eseguire l'aggiornamento da una versione precedente alla versione più recente](how-to-upgrade-previous-version.md) di Azure AD Connect.
Autorizzazioni necessarie | Per le autorizzazioni necessarie per applicare un aggiornamento, vedere [account e autorizzazioni](reference-connect-accounts-permissions.md#upgrade).
Download| [Scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Il rilascio di una nuova versione di Azure AD Connect è un processo che richiede diversi passaggi di controllo della qualità per garantire la funzionalità operativa del servizio e, durante questo processo, il numero di una nuova versione e lo stato della versione verranno aggiornati in modo da riflettere lo stato più recente.
Durante questo processo, il numero della versione verrà visualizzato con una "X" nella posizione del numero di versione secondario, come in "1.3.X.0", che indica che le note sulla versione di questo documento sono valide per tutte le versioni che iniziano con "1.3". Non appena è stato completato il processo di rilascio, il numero di versione di rilascio verrà aggiornato alla versione rilasciata più di recente e lo stato della versione verrà aggiornato a "Rilasciato per il download e l'aggiornamento automatico".
Non tutte le versioni di Azure AD Connect saranno disponibili per l'aggiornamento automatico. Lo stato della versione indicherà se una versione sarà disponibile per l'aggiornamento automatico o solo per il download. Se l'aggiornamento automatico è stato abilitato nel server di Azure AD Connect, tale server eseguirà automaticamente l'aggiornamento per la versione più recente di Azure AD Connect, rilasciata per l'aggiornamento automatico. Si noti che non tutte le configurazioni di Azure AD Connect sono idonee per l'aggiornamento automatico. Seguire questo collegamento per altre informazioni sull'[aggiornamento automatico](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partire dal 1° novembre 2020, verrà avviata l'implementazione di un processo di deprecazione, in base al quale le versioni di Azure AD Connect rilasciate più di 18 mesi prima saranno deprecate. Da quel momento inizieremo questo processo deprecando tutte le versioni di Azure AD Connect con la versione 1.3.20.0 (rilasciata il 24/4/2019) e versioni precedenti e procederemo alla valutazione della deprecazione delle versioni precedenti di Azure AD Connect ogni volta che viene rilasciata una nuova versione.
>
> È necessario assicurarsi di eseguire una versione recente di Azure AD Connect per ricevere un'esperienza di supporto ottimale. 
>
>Se si esegue una versione deprecata di Azure AD Connect è possibile che non siano disponibili gli aggiornamenti di sicurezza più recenti, i miglioramenti delle prestazioni, la risoluzione dei problemi e gli strumenti di diagnostica e i miglioramenti del servizio e, se è necessaria l'assistenza, potrebbe non essere possibile fornire il livello di servizio necessario per l'organizzazione.
>
>Se è stata abilitata la sincronizzazione di Azure AD Connect, si inizierà a ricevere automaticamente notifiche sull'integrità che segnalano eventuali deprecazioni imminenti quando si esegue una delle versioni precedenti.
>
>Per altre informazioni su come aggiornare Azure AD Connect alla versione più recente, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version).

## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Stato della versione
07/05/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema per cui i domini non selezionati venivano selezionati erroneamente dall'interfaccia utente della procedura guidata se erano stati selezionati solo i contenitori nipote.


>[!NOTE]
>Questa versione include la nuova API dell'endpoint V2 Azure AD Connect Sync.  Questo nuovo endpoint V2 è attualmente disponibile in anteprima pubblica.  Questa versione o una versione successiva richiede la nuova API endpoint V2.  Tuttavia, la semplice installazione di questa versione non abilita l'endpoint V2. Si continuerà a usare l'endpoint V1 a meno che non si abiliti l'endpoint V2.  Per abilitarlo e fornire il consenso esplicito per l'anteprima pubblica, è necessario seguire la procedura descritta in [API dell'endpoint V2 Azure AD Connect Sync (anteprima pubblica](how-to-connect-sync-endpoint-api-v2.md)).  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Stato della versione
23/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema introdotto nella versione 1.5.20.0, in cui un amministratore tenant con autenticazione a più fattori non era in grado di abilitare DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stato della versione
20/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema della versione 1.5.20.0 se è stata clonata la regola **In from AD - Group Join** e non è stata clonata la regola **In from AD - Group Common**.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stato della versione
09/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
- Questa versione di aggiornamento rapido risolve un problema relativo alla versione 1.5.18.0 se è abilitata la funzionalità di filtri di gruppo e si usa mS-DS-ConsistencyGuid come ancoraggio di origine.
- È stato risolto un problema nel modulo ADSyncConfig di PowerShell, in cui richiamare il comando DSACLS usato in tutti i cmdlet Set-ADSync * per le autorizzazioni provocherebbe uno degli errori seguenti:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Se la regola di sincronizzazione **In from AD - Group Join** è stata clonata e non è stata clonata la regola di sincronizzazione **In from AD - Group Common** e si prevede di eseguire l'aggiornamento, completare i passaggi seguenti come parte dell'aggiornamento:
> 1. Durante l'aggiornamento deselezionare l'opzione **Avvia il processo di sincronizzazione al termine della configurazione**.
> 2. Modificare la regola di sincronizzazione del join clonato e aggiungere le due trasformazioni seguenti:
>     - Impostare il flusso diretto `objectGUID` su `sourceAnchorBinary`.
>     - Impostare il flusso di espressione `ConvertToBase64([objectGUID])` su `sourceAnchor`.     
> 3. Abilitare l'utilità di pianificazione usando `Set-ADSyncScheduler -SyncCycleEnabled $true`.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stato della versione
02/04/2020: resa disponibile per il download

### <a name="functional-changes-adsyncautoupgrade"></a>Modifiche funzionali ADSyncAutoUpgrade 

- Aggiunta del supporto per la funzionalità mS-DS-ConsistencyGuid per gli oggetti di gruppo. In questo modo è possibile spostare gruppi tra foreste o riconnettere i gruppi in Active Directory ad Azure AD in cui è stato modificato il valore objectID del gruppo Active Directory, ad esempio quando un server di Active Directory viene ricompilato dopo una calamità. Per altre informazioni vedere [Esecuzione della migrazione di gruppi da una foresta a un'altra](how-to-connect-migrate-groups.md).
- L'attributo mS-DS-ConsistencyGuid viene impostato automaticamente su tutti i gruppi sincronizzati e non è necessario eseguire alcuna operazione per abilitare questa funzionalità. 
- Get-ADSyncRunProfile è stato rimosso perché non è più in uso. 
- È stato modificato l'avviso visualizzato quando si tenta di usare un account amministratore Enterprise o di dominio per l'account del connettore di Active Directory Domain Services per fornire un contesto maggiore. 
- È stato aggiunto un nuovo cmdlet per rimuovere gli oggetti dallo spazio connettore, lo strumento CSDelete.exe precedente è stato rimosso e sostituito con il nuovo cmdlet Remove-ADSyncCSObject. Il cmdlet Remove-ADSyncCSObject accetta CsObject come input. Questo oggetto può essere recuperato tramite il cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Lo strumento CSDelete.exe precedente è stato rimosso e sostituito con il nuovo cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemi risolti

- Correzione di un bug nella foresta writeback del gruppo/selettore OU durante la riesecuzione della procedura guidata Azure AD Connect dopo la disabilitazione della funzionalità. 
- È stata introdotta una nuova pagina di errore che viene visualizzata se mancano i valori del registro DCOM necessari con un nuovo collegamento alla guida. Le informazioni sono scritte anche nei file di log. 
- È stato risolto un problema relativo alla creazione dell'account di sincronizzazione Azure Active Directory in cui l'abilitazione di estensioni di directory o PHS potrebbe non riuscire perché prima dell'uso l'account non è stato propagato in tutte le repliche del servizio. 
- Correzione di un bug nell'utilità di compressione degli errori di sincronizzazione che non gestiva correttamente i caratteri surrogati. 
- Correzione di un bug per l'aggiornamento automatico che lasciava il server nello stato utilità di pianificazione sospesa. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stato della versione
12/9/2019: Versione per il download. Non disponibile tramite aggiornamento automatico.
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
- La sincronizzazione dell'hash delle password è stata aggiornata per Azure AD Domain Services per tenere in considerazione correttamente la spaziatura interna negli hash Kerberos.  In questo modo si otterrà un miglioramento delle prestazioni durante la sincronizzazione delle password da AAD ad Azure AD Domain Services.
- È stato aggiunto il supporto per le sessioni affidabili tra l'agente di autenticazione e il bus di servizio.
- Questa versione applica TLS 1.2 per la comunicazione tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta una cache DNS per le connessioni WebSocket tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta la possibilità di indirizzare un agente specifico dal cloud per testare la connettività degli agenti.

### <a name="fixed-issues"></a>Problemi risolti
- La versione 1.4.18.0 presentava un bug in cui il cmdlet di PowerShell per DSSO usava le credenziali di accesso di Windows anziché le credenziali di amministratore fornite durante l'esecuzione di PS. Di conseguenza, non era possibile abilitare DSSO in più foreste tramite l'interfaccia utente di AADConnect. 
- È stato eseguito un aggiornamento per abilitare DSSO simultaneamente in tutte le foreste tramite l'interfaccia utente di AADConnect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stato della versione
11/08/2019: resa disponibile per il download. Non disponibile tramite aggiornamento automatico.

>[!IMPORTANT]
>A causa di una modifica dello schema interno in questa versione di Azure AD Connect, se si gestiscono impostazioni di configurazione della relazione di trust AD FS con MSOnline PowerShell, è necessario aggiornare il modulo MSOnline PowerShell alla versione 1.1.183.57 o successiva.

### <a name="fixed-issues"></a>Problemi risolti

Questa versione corregge un problema con i dispositivi di Azure AD ibrido aggiunti a quelli esistenti. Questa versione contiene una nuova regola di sincronizzazione del dispositivo che corregge questo problema.
La modifica di questa regola può causare l'eliminazione di dispositivi obsoleti da Azure AD. Questo non è un problema, perché questi oggetti dispositivo non vengono usati da Azure AD durante l'autorizzazione dell'accesso condizionale. Per alcuni clienti, il numero di dispositivi che verranno eliminati tramite questa modifica della regola può superare la soglia di eliminazione. Se l'eliminazione degli oggetti dispositivo in Azure AD supera la soglia di eliminazione dell'esportazione, è consigliabile consentire l'eliminazione delle eliminazioni. [Come consentire il flusso delle eliminazioni quando superano la soglia di eliminazione](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stato della versione
28/9/2019: resa disponibile per l'aggiornamento automatico per selezionare i tenant. Non disponibile per il download.

Questa versione corregge un bug in cui alcuni server aggiornati automaticamente da una versione precedente alla versione 1.4.18.0 hanno riscontrato problemi con la reimpostazione della password self-service (SSPR) e il writeback delle password.

### <a name="fixed-issues"></a>Problemi risolti

In determinate circostanze, i server che sono stati aggiornati automaticamente alla versione 1.4.18.0 non riabilitano la reimpostazione della password self-service e il writeback delle password al termine dell'aggiornamento. Questa versione di aggiornamento automatico corregge il problema e riattiva la reimpostazione della password self-service e il writeback delle password.

Correzione di un bug nell'utilità di compressione degli errori di sincronizzazione che non gestiva correttamente i caratteri surrogati.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>È in corso l'analisi di un evento imprevisto in cui alcuni clienti riscontrano problemi con i dispositivi esistenti Azure AD ibrido aggiunti dopo l'aggiornamento a questa versione di Azure AD Connect. Si consiglia ai clienti che hanno distribuito un join Azure AD ibrido di posticipare l'aggiornamento a questa versione fino a quando la causa principale di questi problemi non sarà identificata e risolta. Verranno fornite altre informazioni il prima possibile.

>[!IMPORTANT]
>Con questa versione di Azure AD Connect alcuni clienti potrebbero vedere alcuni o tutti i dispositivi Windows scomparire da Azure AD. Questo non è un problema, perché queste identità di dispositivo non vengono usate da Azure AD durante l'autorizzazione dell'accesso condizionale. Per altre informazioni vedere [Informazioni sulla scomparsa di dispositivi Azure AD Connect 1.4.xx.x](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Stato della versione
25/9/2019: resa disponibile solo per l'aggiornamento automatico.

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
- I nuovi strumenti per la risoluzione dei problemi consentono di risolvere i problemi relativi agli scenari di "non sincronizzazione dell'utente", "non sincronizzazione del gruppo" o "membri del gruppo non sincronizzati".
- Aggiungere il supporto per i cloud nazionali nello script per la risoluzione dei problemi di AAD Connect 
- I clienti devono essere informati che gli endpoint WMI deprecati per MIIS_Service ora sono stati rimossi. Tutte le operazioni WMI dovrebbero ora essere eseguite tramite i cmdlet di PS.
- Miglioramento della sicurezza reimpostando la delega vincolata sull'oggetto AZUREADSSOACC
- Quando si aggiunge o modifica una regola di sincronizzazione, se nello schema del connettore sono presenti attributi usati nella regola, ma non aggiunti al connettore, gli attributi vengono aggiunti automaticamente al connettore. Lo stesso vale per il tipo di oggetto su cui la regola ha effetto. Se viene aggiunto un elemento al connettore, il connettore verrà contrassegnato per l'importazione completa al successivo ciclo di sincronizzazione.
- L'uso di un amministratore Enterprise o di dominio come account connettore non è più supportato nelle nuove distribuzioni di Azure AD Connect. Le distribuzioni correnti di AAD Connect che usano un amministratore Enterprise o di dominio come account connettore non saranno interessate da questa versione.
- In Gestione sincronizzazione viene eseguita una sincronizzazione completa per la creazione/modifica/eliminazione di regole. Per qualsiasi modifica della regola, viene visualizzata una finestra popup che informa l'utente se verrà eseguita l'importazione completa o la sincronizzazione completa.
- Sono stati aggiunti dei passaggi di mitigazione per gli errori relativi alle password nella pagina "connettori > proprietà > connettività"
- È stato aggiunto un avviso di deprecazione per Sync Service Manager nella pagina delle proprietà del connettore. Questo avviso informa l'utente che le modifiche devono essere apportate tramite la procedura guidata di Azure AD Connect.
- È stato aggiunto un nuovo errore per i problemi relativi ai criteri password di un utente.
- Impedire la configurazione errata dei filtri di gruppo per filtri di dominio e unità organizzativa. Quando il dominio o l'unità organizzativa del gruppo immesso è già stato escluso, i filtri di gruppo visualizzano un errore e l'utente non può andare avanti fino alla risoluzione del problema.
- Gli utenti non possono più creare un connettore per Active Directory Domain Services o Windows Azure Active Directory nell'interfaccia utente di Synchronization Service Manager.
- Accessibilità fissa dei controlli dell'interfaccia utente personalizzati in Synchronization Service Manager.
- Sono state abilitate sei attività di gestione federative per tutti i metodi di accesso in Azure AD Connect.  (In precedenza, per tutti gli accessi era disponibile solo l'attività "Aggiorna AD FS certificato TLS/SSL".)
- È stato aggiunto un avviso che viene visualizzato quando si modifica il metodo di accesso dalla federazione a PHS o PTA. L'avviso informa che tutti i domini e gli utenti di Azure AD verranno convertiti in autenticazione gestita.
- Sono stati rimossi i certificati per la firma di token dall'attività "Reimposta Azure AD e attendibilità AD FS" ed è stata aggiunta una sottoattività separata per aggiornare questi certificati.
- È stata aggiunta una nuova attività di gestione della federazione denominata "Gestione certificati" con attività secondarie per aggiornare i certificati TLS o per la firma di token per la farm AD FS.
- È stata aggiunta una nuova sottoattività di gestione della federazione denominata "Definizione del server primario", che consente agli amministratori di specificare un nuovo server primario per la farm AD FS.
- È stata aggiunta una nuova attività di gestione della federazione denominata "Gestione server" con attività secondarie per la distribuzione di un server di AD FS, la distribuzione di un server proxy applicazione Web e la definizione del server primario.
- È stata aggiunta una nuova attività di gestione delle federazione denominata "Visualizza la configurazione della federazione", che visualizza le impostazioni di AD FS correnti.  (A causa di questa aggiunta, le impostazioni AD FS sono state rimosse dalla pagina "Verifica della soluzione".)

### <a name="fixed-issues"></a>Problemi risolti
- Risoluzione del problema di errore di sincronizzazione per lo scenario in cui un oggetto utente che acquisisce l'oggetto contatto corrispondente ha un riferimento automatico (ad esempio, l'utente è il proprio manager).
- I popup della guida ora vengono visualizzati sullo stato attivo della tastiera.
- Per l'aggiornamento automatico, se un'app in conflitto è in esecuzione da 6 ore, terminare e continuare con l'aggiornamento.
- Limitazione del numero di attributi che un cliente può selezionare a 100 per oggetto quando si selezionano le estensioni della directory. In questo modo si eviterà che l'errore si verifichi durante l'esportazione poiché Azure ha un massimo di 100 attributi di estensione per oggetto.
- Correzione di un bug per rendere più affidabile lo script AD Connectivity
- Correzione di un bug per rendere l'installazione di AADConnect su un computer che usa un servizio WCF Named Pipes più affidabile.
- Diagnostica migliorata e risoluzione dei problemi relativi a criteri di gruppo che non consentono l'avvio del servizio ADSync al momento dell'installazione iniziale.
- Correzione di un bug per cui il nome visualizzato per un computer Windows non era scritto correttamente.
- Correzione di un bug in cui il tipo di sistema operativo per un computer Windows non era scritto correttamente.
- Correzione di un bug per cui i computer non Windows 10 venivano sincronizzati in modo imprevisto. In conseguenza a questa modifica, i computer non Windows 10 sincronizzati in precedenza verranno ora eliminati. Questa operazione non influisce sulle funzionalità perché la sincronizzazione dei computer Windows viene usata solo per l'aggiunta a un dominio Azure AD ibrido, che funziona solo per i dispositivi Windows 10.
- Sono stati aggiunti diversi nuovi cmdlet (interni) al modulo PowerShell ADSync.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>C'è un problema noto relativo all'aggiornamento di Azure AD Connect da una versione precedente alla versione 1.3.21.0, in cui il portale di O365 non riflette la versione aggiornata anche se Azure AD Connect è stato aggiornato correttamente.
>
> Per risolvere questo problema, è necessario importare il modulo **AdSync**, quindi eseguire il cmdlet di PowerShell`Set-ADSyncDirSyncConfiguration` nel server Azure AD Connect.  È possibile seguire la procedura seguente:
>
>1. Aprire PowerShell in modalità di amministratore.
>2. Eseguire `Import-Module "ADSync"`.
>3. Eseguire `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stato della versione 

14/05/2019: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti 

- Correzione di una vulnerabilità di elevazione dei privilegi presente nella versione 1.3.20.0 di Microsoft Azure Active Directory Connect.  Questa vulnerabilità, in determinate condizioni, può consentire a un utente malintenzionato di eseguire due cmdlet di PowerShell nel contesto di un account con privilegi ed eseguire azioni con privilegi.  Questa patch di sicurezza risolve il problema disabilitando questi cmdlet. Per altre informazioni, vedere [Patch di sicurezza](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stato della versione 

24/04/2019: resa disponibile per il download

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 

- Aggiunta del supporto per l'aggiornamento del dominio 
- La funzionalità delle Cartelle pubbliche della posta di Exchange diventa di disponibilità generale 
- Miglioramento della gestione degli errori della procedura guidata per gli errori del servizio 
- È stato aggiunto il collegamento di avviso nell'interfaccia utente di Synchronization Service Manager nella pagina delle proprietà del connettore. 
- La funzionalità di writeback dei gruppi unificati è ora di disponibilità generale 
- Miglioramento del messaggio di errore SSPR se nel DC manca un controllo LDAP 
- È stata aggiunta la diagnostica per gli errori del registro DCOM durante l'installazione  
- Miglioramento dell'analisi degli errori RPC PHS 
- Le credenziali EA da un dominio figlio sono consentite 
- L'immissione del nome del database durante l'installazione (nome predefinito ADSync) è consentita
- Eseguire l'aggiornamento ad ADAL 3.19.8 per selezionare un aggiornamento WS-Trust per il ping e aggiungere il supporto per le nuove istanze di Azure 
- Modificare le regole di sincronizzazione dei gruppi per eseguire il flusso samAccountName, DomainNetbios e DomainFQDN nel cloud, necessario per le attestazioni 
- Gestione della regola di sincronizzazione predefinita modificata: per altre informazioni vedere [qui](how-to-connect-fix-default-rules.md).
- È stato aggiunto un nuovo agente in esecuzione come servizio di Windows. Questo agente, denominato "Agente di amministrazione", consente la diagnostica remota più approfondita del server Azure AD Connect per consentire ai tecnici Microsoft di risolvere i problemi quando si apre un caso di supporto. L'agente non viene installato e abilitato per impostazione predefinita.  Per altre informazioni su come installare e abilitare l'agente, vedere [Cos'è l'agente di amministrazione di Azure AD Connect?](whatis-aadc-admin-agent.md). 
- Il contratto di licenza con l'utente finale (EULA) è stato aggiornato. 
- È stato aggiunto il supporto per l'aggiornamento automatico per le distribuzioni che usano AD FS come tipo di accesso.  In questo modo è stato rimosso anche il requisito di aggiornare il trust della relying party di AD FS Azure AD come parte del processo di aggiornamento. 
- È stata aggiunta un'attività di gestione del trust di Azure AD che fornisce due opzioni: analizzare/aggiornare il trust e reimpostare il trust. 
- È stato modificato il trust della relying party di AD FS Azure in modo che usi sempre l'opzione -SupportMultipleDomain (include gli aggiornamenti del trust e del dominio di Azure AD). 
- È stato modificato il comportamento di installazione di una nuova AD FS farm in modo che richieda un certificato con estensione pfx rimuovendo l'opzione di uso di un certificato pre-installato.
- Aggiornamento del flusso di lavoro di installazione di una nuova farm AD FS in modo da consentire la distribuzione di 1 AD FS e 1 server WAP.  Tutti i server aggiuntivi verranno eseguiti dopo l'installazione iniziale. 

### <a name="fixed-issues"></a>Problemi risolti 

- Aggiornamento della logica di riconnessione SQL per il servizio ADSync 
- Aggiornamento per consentire l'installazione pulita usando un database AOA SQL vuoto 
- Aggiornamento dello script delle autorizzazioni PS per perfezionare le autorizzazioni di GWB 
- Aggiornamento degli errori VSS con Local DB  
- Aggiornamento di un messaggio di errore fuorviante quando il tipo di oggetto non rientra nell'ambito 
- Correzione di un problema per cui l'installazione di Azure AD PowerShell in un server potrebbe causare un conflitto di assembly con Azure AD Connect. 
- Correzione del bug PHS nel server di staging quando le credenziali del connettore vengono aggiornate nell'interfaccia utente di Synchronization Service Manager. 
- Correzione di alcune perdite di memoria 
- Correzioni di aggiornamenti automatici vari 
- Correzioni varie per l'esportazione e l'elaborazione di importazione non confermata 
- Correzione di un bug relativo alla gestione di una barra rovesciata nei filtri di dominio e di unità organizzativa 
- È stato risolto un problema per cui il servizio ADSync richiede più di 2 minuti per l'arresto e causa un problema in fase di aggiornamento. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stato della versione

18/12/2018: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti

Questa compilazione aggiorna i connettori non standard (ad esempio, il connettore Generic LDAP e il connettore Generic SQL) forniti con Azure AD Connect. Per altre informazioni sui connettori applicabili, vedere la versione 1.1.911.0 nella [Cronologia di rilascio delle versioni dei connettori](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stato della versione
11/12/2018: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido permette all'utente di selezionare un dominio di destinazione, all'interno della foresta specificata, per il contenitore RegisteredDevices quando si abilita il writeback dispositivi.  Nelle versioni precedenti che contengono la nuova funzionalità Opzioni del dispositivo (1.1.819.0-1.2.68.0) la posizione del contenitore RegisteredDevices è limitato alla radice della foresta e non permette domini figlio.  Questa limitazione si manifesta solo nelle nuove distribuzioni, mentre gli aggiornamenti sul posto non sono interessati.  

Se è stata distribuita una compilazione contenente la funzionalità Opzioni del dispositivo aggiornata in un nuovo server e il writeback dispositivi è abilitato, è necessario specificare manualmente la posizione del contenitore se si vuole che sia diversa dalla radice della foresta.  A questo scopo, è necessario disabilitare il writeback dispositivi e abilitarlo di nuovo, in quanto questa operazione permette di specificare la posizione del contenitore nella pagina "Foresta di writeback".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stato della versione 

30/11/2018:  resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti

Questa versione di aggiornamento rapido corregge un conflitto per cui può essere generato un errore di autenticazione a causa della presenza indipendente del modulo MSOnline PowerShell Gallery nel server di sincronizzazione.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stato della versione 

19/11/2018:  resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti

Questa versione di aggiornamento rapido risolve una regressione nella versione precedente a causa di cui il writeback delle password ha esito negativo quando si usa un controller di dominio Active Directory Domain Services in Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stato della versione 

10/25/2018: rilasciato per il download

 
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità 


- È stata modificata la funzionalità dell'attributo write-back per garantire che la casella vocale ospitata funzioni correttamente.  In determinati scenari, Azure AD sovrascriveva l'attributo msExchUcVoicemailSettings durante il write-back con un valore null.  Azure AD ora non cancellerà più il valore di un'istanza locale di questo attributo se il valore del cloud non è impostato.
- È stata aggiunta di diagnostica nella procedura guidata di Azure AD Connect per analizzare e identificare i problemi di connettività ad Azure AD. Tale diagnostica può anche essere eseguita direttamente tramite PowerShell usando il cmdlet Test-AdSyncAzureServiceConnectivity. 
- È stata aggiunta la diagnostica nella procedura guidata di Azure AD Connect per analizzare e identificare i problemi di connettività ad AD. Tale diagnostica può anche essere eseguita direttamente tramite PowerShell con la funzione Start-ConnectivityValidation nel modulo ADConnectivityTools di PowerShell.  Per altre informazioni vedere [What is the ADConnectivityTool PowerShell Module?](how-to-connect-adconnectivitytools.md) (Che cos'è il modulo di PowerShell ADConnectivityTool?)
- È stato aggiunto un controllo preliminare della versione dello schema AD per il join Azure Active Directory ibrido e il write-back dei dispositivi 
- È stato modificato l'attributo della pagina Estensione della directory in modo da non fare distinzione tra maiuscole/minuscole.
-   È stato aggiunto il supporto completo per TLS 1.2. Questa versione supporta la disabilitazione di tutti gli altri protocolli e solo l'abilitazione di TLS 1.2 sulla macchina in cui è installato Azure AD Connect.  Per altre informazioni, vedere [Imposizione di TLS 1.2 per Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Problemi risolti   

- Risolto un bug per il quale l'aggiornamento Azure AD Connect ha esito negativo se viene usato SQL Always On. 
- Risolto un bug per analizzare correttamente i nomi di unità organizzative contenenti una barra rovesciata. 
- Risolto un problema che disabilitava l'autenticazione pass-through per un'installazione pulita in modalità di staging. 
- Risolto un bug che impediva di caricare il modulo di PowerShell durante l'esecuzione degli strumenti di risoluzione dei problemi 
- Risolto un bug che bloccava l'uso di valori numerici da parte degli utenti nel primo carattere del nome host. 
- Risolto un bug in cui Azure AD Connect consentiva le partizioni non valide e la selezione del contenitore 
- Risolto il messaggio di errore "Password non valida" quando è abilitato l'accesso SSO Desktop. 
- Varie correzioni di bug per la gestione di Trust AD FS  
- Durante la configurazione del write-back dei dispositivi: è stato corretto il controllo dello schema per cercare la classe di oggetti msDs-DeviceContainer (introdotta in WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

07/09/2018: rilasciato per il download, non sarà rilasciato per l'aggiornamento automatico 

### <a name="fixed-issues"></a>Problemi risolti  

L'aggiornamento di Azure AD Connect non avviene se la disponibilità di SQL Always On è configurata per il database ADSync. Questo hotfix risolve il problema e consente di eseguire l'aggiornamento. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stato della versione

21/8/2018: resa disponibile per il download e l'aggiornamento automatico. 

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

- L'integrazione di PingFederate in Azure AD Connect è ora disponibile per la disponibilità generale. [Altre informazioni su come effettuare la federazione di Azure AD con PingFederate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect ora crea il backup del trust di Azure AD in AD FS ogni volta che viene effettuato un aggiornamento e lo archivia in un file separato per un facile ripristino, se necessario. [Altre informazioni sulla nuova funzionalità e sulla gestione dei trust di Azure AD in Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Il nuovo strumento di risoluzione dei problemi consente di risolvere i problemi relativi alla modifica dell'indirizzo di posta elettronica principale e all'esclusione dell'account dall'elenco globale degli indirizzi
- Azure AD Connect è stato aggiornato per includere il più recente client nativo di SQL Server 2012
- Quando si passa dall'accesso utente a Sincronizzazione dell'hash delle password o a Autenticazione pass-through nell'attività "Cambia l'accesso utente", la casella di controllo Accesso Single Sign-On facile è abilitata per impostazione predefinita.
- Aggiunto supporto per Windows Server Essentials 2019
- L'agente di Azure AD Connect Health è stato aggiornato alla versione più recente 3.1.7.0
- Durante un aggiornamento, se il programma di installazione rileva modifiche alle regole di sincronizzazione predefinite, l'amministratore visualizza un messaggio di avviso prima di sovrascrivere le regole modificate. Con questa operazione consentirà all'utente di intraprendere azioni correttive e di riprendere in un secondo momento. Comportamento nelle versioni precedenti: se una regola predefinita viene modificata, l'aggiornamento manuale sovrascrive le regole senza generare alcun avviso all'utente e l'utilità di pianificazione della sincronizzazione viene disabilitata senza informare l'utente. Nuovo comportamento: l'utente riceve un avviso prima di sovrascrivere le regole di sincronizzazione predefinite modificate. L'utente avrà la possibilità di interrompere il processo di aggiornamento e di riprenderlo dopo aver intrapreso un'azione correttiva.
- Garantire una migliore gestione di un problema di conformità FIPS, fornendo un messaggio di errore per la generazione di hash MD5 in un ambiente conforme a FIPS e un collegamento alla documentazione che offre una soluzione per questo problema.
- Aggiornamento dell'interfaccia utente per migliorare le attività di federazione nella procedura guidata, che ora si trovano in un sottogruppo separato per la federazione. 
- Tutte le attività aggiuntive della federazione sono ora raggruppate in un singolo sottomenu per facilitarne l'uso.
- Un nuovo modulo ADSyncConfig Posh rinnovato (AdSyncConfig.psm1) con nuove funzioni di autorizzazioni di AD spostate dal vecchio ADSyncPrep.psm1 (che potrebbe essere ritirato a breve)

### <a name="fixed-issues"></a>Problemi risolti 

- Risolto un bug in cui il server AAD Connect mostrava un elevato uso della CPU dopo l'aggiornamento a .NET 4.7.2
- Risolto un bug che generava in modo intermittente un messaggio di errore per un problema di deadlock SQL risolto automaticamente
- Risolti alcuni problemi di accessibilità per Sync Rules Editor e Sync Service Manager  
- Risolto un bug a causa del quale Azure AD Connect non poteva ottenere le informazioni sull'impostazione del registro
- Risolto un bug che creava problemi quando l'utente andava avanti/indietro nella procedura guidata
- Risolto un bug per evitare che si verificasse un errore a causa di una gestione multi-thread errata nella procedura guidata
- Se la pagina Group Sync Filtering (Filtro sincronizzazione di gruppo) rileva un errore LDAP durante la risoluzione dei gruppi di sicurezza, Azure AD Connect ora restituisce l'eccezione con piena fedeltà.  La causa radice dell'eccezione di riferimento non è ancora nota e verrà risolta da un bug diverso.
-  Risolto un bug per cui le autorizzazioni per le chiavi STK e NGC (attributo ms-DS-KeyCredentialLink su oggetti User/Device per WHfB) non erano state impostate correttamente.     
- Risolto un bug in cui "Set-ADSyncRestrictedPermissions" non veniva chiamato correttamente
-  Aggiunta del supporto per la concessione delle autorizzazioni sul Writeback dei gruppi nell'installazione guidata di AAD Connect
- Quando è stato cambiato il metodo di accesso da Sincronizzazione dell'hash delle password ad AD FS, Sincronizzazione dell'hash delle password non è stata disabilitata.
- Aggiunta verifica per gli indirizzi IPv6 nella configurazione di AD FS
- Aggiornato il messaggio di notifica per informare della presenza di una configurazione esistente.
- Il writeback del dispositivo non riesce a rilevare il contenitore nella foresta non attendibile. È stato aggiornato per fornire un messaggio di errore migliore e un collegamento alla documentazione appropriata
- La deselezione di un'Unità organizzativa e quindi la sincronizzazione/il writeback corrispondente a tale Unità organizzativa produce un errore di sincronizzazione generico. È stato modificato per creare un messaggio di errore più comprensibile.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stato della versione

14/5/2018: resa disponibile per l'aggiornamento automatico e il download.

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

Miglioramenti e nuove funzionalità

- Questa versione include l'anteprima pubblica dell'integrazione di PingFederate in Azure AD Connect. Con questa versione, i clienti possono configurare in modo facile e affidabile l'ambiente di Azure Active Directory per sfruttare PingFederate come provider federativo. Per altre informazioni su come usare questa nuova funzionalità, vedere la [documentazione online](plan-connect-user-signin.md#federation-with-pingfederate). 
- È stata aggiornata l'utilità di risoluzione dei problemi della procedura guidata di Azure AD Connect, in cui vengono ora analizzati più scenari di errore, ad esempio caselle di posta collegate e gruppi dinamici di AD. Per altre informazioni sull'utilità di risoluzione dei problemi, vedere [qui](tshoot-connect-objectsync.md).
- La configurazione del writeback dispositivi viene ora gestita esclusivamente nella procedura guidata di Azure AD Connect.
- Sono stati aggiunti un nuovo modulo di PowerShell, denominato ADSyncTools.psm1, che può essere usato per risolvere i problemi di connettività di SQL, e diverse altre utilità di risoluzione dei problemi. Per altre informazioni sul modulo ADSyncTools, vedere [qui](tshoot-connect-tshoot-sql-connectivity.md). 
- È stata aggiunta una nuova attività "Configura le opzioni del dispositivo". È possibile usare l'attività per configurare le due operazioni seguenti: 
  - **Aggiunta ad Azure AD ibrido**: Se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi vengono aggiunti sia ad Active Directory locale che ad Azure Active Directory.
  - **Writeback dispositivi**: il writeback dispositivi viene usato per abilitare l'accesso condizionale basato su dispositivi ai dispositivi protetti da AD FS (2012 R2 o versioni successive)

    >[!NOTE] 
    > - L'opzione per abilitare il writeback dispositivi da Personalizzazione delle opzioni di sincronizzazione verrà disabilitata. 
    > -  Il modulo PowerShell per ADPrep è deprecato in questa versione.



### <a name="fixed-issues"></a>Problemi risolti 

- Questa versione aggiorna l'installazione di SQL Server Express a SQL Server 2012 SP4, che, tra le altre cose, corregge diverse vulnerabilità della sicurezza.  Altre informazioni su SQL Server 2012 SP4 sono disponibili [qui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information).
- Elaborazione delle regole di sincronizzazione: l'applicazione delle regole di sincronizzazione di unione in uscita senza condizioni di join dovrebbe risultare annullata se la regola di sincronizzazione padre non è più applicabile
- Diverse correzioni a livello di accessibilità sono state applicate all'interfaccia utente di Synchronization Service Manager e all'editor delle regole di sincronizzazione
- Procedura guidata di Azure AD Connect: errore durante la creazione dell'account AD Connector quando Azure AD Connect è in un gruppo di lavoro
- Procedura guidata di Azure AD Connect: nella pagina di accesso di Azure AD viene visualizzata la casella di controllo di verifica in caso di mancata corrispondenza tra domini di Active Directory e domini verificati di Azure AD
- Correzione in PowerShell dell'aggiornamento automatico per impostare correttamente lo stato di aggiornamento automatico in alcuni casi, dopo il tentativo di aggiornamento automatico.
- Procedura guidata di Azure AD Connect: aggiornamento dei dati di telemetria per acquisire informazioni precedentemente mancanti
- Procedura guidata di Azure AD Connect: sono state apportate le modifiche seguenti relative all'uso dell'attività **Cambia l'accesso utente** per passare da AD FS all'autenticazione pass-through:
    - L'agente di autenticazione pass-through viene installato nel server Azure AD Connect e l'autenticazione pass-through viene abilitata prima di convertire i domini da federati a gestiti.
    - Gli utenti non vengono più convertiti da federati a gestiti. Vengono convertiti solo i domini.
- Procedura guidata di Azure AD Connect: regex a dominio multiplo di AD FS non è corretto quando l'UPN dell'utente contiene il carattere speciale '. L'aggiornamento di regex ha introdotto il supporto per i caratteri speciali
- Procedura guidata di Azure AD Connect: rimozione del messaggio non corretto "Configure source anchor attribute" (Configurare l'attributo dell'ancoraggio di origine) se non vengono apportate modifiche 
- Procedura guidata di Azure AD Connect: supporto di AD FS per lo scenario di doppia federazione
- Procedura guidata di Azure AD Connect: le attestazioni per AD FS non vengono aggiornate per il dominio aggiunto quando si converte un dominio gestito in federato
- Procedura guidata di Azure AD Connect: durante il rilevamento dei pacchetti installati, vengono individuati prodotti correlati a Dirsync/Azure AD Sync/Azure AD Connect non aggiornati. Si proverà ora a disinstallare i prodotti non aggiornati.
- Procedura guidata di Azure AD Connect: correzione del mapping del messaggio di errore quando l'installazione dell'agente di autenticazione pass-through non riesce
- Procedura guidata di Azure AD Connect: rimozione del contenitore "Configurazione" dalla pagina Filtro di domini e unità organizzative
- Installazione del motore di sincronizzazione: rimuovere la logica legacy non necessaria, che a volte ha esito negativo, dal file MSI di installazione del motore di sincronizzazione
- Procedura guidata di Azure AD Connect: correzione del testo della Guida popup nella pagina Funzionalità facoltative per Sincronizzazione hash password
- Runtime del motore di sincronizzazione: correzione dello scenario in cui un oggetto CS è associato a un'eliminazione importata e le regole di sincronizzazione tentano di effettuare di nuovo il provisioning dell'oggetto.
- Runtime del motore di sincronizzazione: aggiunta del collegamento della Guida alla risoluzione dei problemi di connettività online al log eventi per un errore di importazione
- Runtime del motore di sincronizzazione: riduzione dell'utilizzo di memoria nell'utilità di pianificazione della sincronizzazione durante l'enumerazione dei connettori
- Procedura guidata di Azure AD Connect: correzione di un problema relativo alla risoluzione di un account del servizio di sincronizzazione personalizzato privo di privilegi di lettura di Active Directory
- Procedura guidata di Azure AD Connect: miglioramento della registrazione delle opzioni di Filtro di domini e unità organizzative
- Procedura guidata di Azure AD Connect: aggiunta in AD FS di attestazioni predefinite al trust di federazione creato per lo scenario MFA
- Procedura guidata di Azure AD Connect: distribuzione di WAP in AD FS: l'aggiunta del server non permette di usare il nuovo certificato
- Procedura guidata di Azure AD Connect: eccezione DSSO quando le credenziali onPremCredentials non vengono inizializzate per un dominio 
- È preferibile propagare l'attributo distinguishedName di AD da Active User Object.
- È stato risolto un bug estetico a causa del quale la precedenza della prima regola di sincronizzazione di OOB era impostata su 99 invece che su 100



## <a name="117510"></a>1.1.751.0
Stato 12/4/2018: resa disponibile solo per il download

>[!NOTE]
>Questa versione è un aggiornamento rapido per Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Servizio di sincronizzazione Azure AD Connect
#### <a name="fixed-issues"></a>Problemi risolti
È stato corretto un problema per il quale l'individuazione automatica di istanze di Azure per i tenant della Cina talvolta aveva esito negativo.  

### <a name="ad-fs-management"></a>Gestione di AD FS
#### <a name="fixed-issues"></a>Problemi risolti

Un problema nella logica di ripetizione dei tentativi di configurazione causava l'ArgumentException "È stato già aggiunto un elemento con la stessa chiave"  e l'esito negativo di tutti i nuovi tentativi.

## <a name="117500"></a>1.1.750.0
Stato 22/3/2018: resa disponibile per l'aggiornamento automatico e il download.
>[!NOTE]
>Al termine dell'aggiornamento a questa nuova versione, verrà attivata automaticamente la sincronizzazione completa e l'importazione completa per il connettore Azure AD e la sincronizzazione completa per il connettore AD. Poiché l'operazione potrebbe richiedere del tempo, a seconda delle dimensioni dell'ambiente Azure AD Connect, assicurarsi di aver eseguito i passaggi necessari per supportare questa operazione oppure rimandare l'aggiornamento a un momento più opportuno.

>[!NOTE]
>"La funzionalità AutoUpgrade è stata erroneamente disabilitata ad alcuni tenant che hanno distribuito build successive a 1.1.524.0. Per garantire che l'istanza di Azure AD Connect sia ancora idonea per AutoUpgrade, eseguire questo cmdlet di PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemi risolti

* Il cmdlet Set-ADSyncAutoUpgrade bloccava in precedenza l'aggiornamento automatico con lo stato di aggiornamento automatico impostato su Sospeso. Questa funzionalità è ora stata cambiata in modo che non venga bloccato l'aggiornamento automatico delle build future.
* L'opzione "Sincronizzazione password" della pagina **Accesso utente** è stata modificata in "Sincronizzazione dell'hash delle password".  Azure AD Connect sincronizza gli hash delle password, non le password, quindi questa modifica è in linea con ciò che si verifica effettivamente.  Per altre informazioni, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stato: resa disponibile per alcuni clienti selezionati

>[!NOTE]
>Al termine dell'aggiornamento a questa nuova versione, verrà attivata automaticamente la sincronizzazione completa e l'importazione completa per il connettore Azure AD e la sincronizzazione completa per il connettore AD. Poiché l'operazione potrebbe richiedere del tempo, a seconda delle dimensioni dell'ambiente Azure AD Connect, assicurarsi di aver eseguito i passaggi necessari per supportare questa operazione oppure rimandare l'aggiornamento a un momento più opportuno.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemi risolti
* Correzione della finestra temporale nelle attività in background per la pagina Filtro partizioni nel passaggio alla pagina successiva.

* Correzione di un bug che provocava la violazione di accesso durante l'azione personalizzata ConfigDB.

* Correzione di un bug per il ripristino dal timeout di connessione SQL.

* Correzione di un bug in cui i certificati con caratteri jolly SAN non superavano una verifica preliminare.

* Correzione di un bug provocava l'arresto anomalo di miiserver.exe durante l'esportazione del connettore Azure AD.

* Correzione di un bug a causa del quale tentativi di password non validi consentivano l'accesso al controller di dominio durante l'esecuzione della procedura guidata di Azure AD Connect per la modifica della configurazione.


#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Aggiunta di impostazioni di privacy per la normativa GDPR (General Data Protection Regulation).  Per altre informazioni, vedere l'articolo [qui](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Dati di telemetria dell'applicazione: l'amministratore può attivare/disattivare questa classe di dati in base alle esigenze

* Dati di integrità di Azure AD: l'amministratore deve visitare il portale dei dati di integrità per gestire le impostazioni di integrità.
   Dopo aver modificato i criteri del servizio, gli agenti li leggeranno e applicheranno.

* Aggiunta di azioni di configurazione per il writeback dei dispositivi e indicatore di stato per l'inizializzazione della pagina

* Miglioramento della diagnostica generale con report HTML e raccolta completa di dati in un report in formato ZIP di testo/HTML

* Migliore affidabilità dell'aggiornamento automatico e aggiunta di dati di telemetria per assicurare che sia possibile determinare l'integrità del server

* Limitazione delle autorizzazioni disponibili per gli account con privilegi per account AD Connect

  * Per le nuove installazioni, la procedura guidata limita le autorizzazioni degli account con privilegi per l'account MSOL dopo la creazione dell'account MSOL stesso.

Le modifiche riguarderanno:
1. Installazioni rapide
2. Installazioni personalizzate con account a creazione automatica
3. Modifica del programma di installazione in modo da non richiedere il privilegio SA per l'installazione pulita di Azure AD Connect

* Aggiunta di una nuova utilità per risolvere i problemi di sincronizzazione per un oggetto specifico. È disponibile nell'opzione 'Troubleshoot Object Synchronization' (Risoluzione dei problemi di sincronizzazione oggetti) dell'attività aggiuntiva di risoluzione dei problemi della procedura guidata di Azure AD Connect. L'utilità esegue attualmente le verifiche seguenti:

  * Mancata corrispondenza di UserPrincipalName tra l'oggetto utente sincronizzato e l'account utente nel tenant Azure AD.
  * Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri di dominio
  * Se l'oggetto viene escluso dalla sincronizzazione a causa dei filtri dell'unità organizzativa

* Aggiunta di una nuova utilità per la sincronizzazione dell'hash delle password corrente archiviato nell'istanza locale di Active Directory per un account utente specifico.

L'utilità non richiede la modifica delle password. È disponibile nell'opzione ''Troubleshoot Password Hash Synchronization' (Risoluzione dei problemi di sincronizzazione dell'hash delle password) dell'attività aggiuntiva di risoluzione dei problemi della procedura guidata di Azure AD Connect.






## <a name="116540"></a>1.1.654.0
Stato: 12 dicembre 2017

>[!NOTE]
>Questa versione è un aggiornamento rapido relativo alla sicurezza per Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
È stato aggiunto un miglioramento alla versione 1.1.654.0 (e successive) di Azure AD Connect per garantire che le modifiche consigliate alle autorizzazioni descritte nella sezione [Bloccare l'accesso all'account di Active Directory Domain Services](#lock) vengano applicate automaticamente quando Azure AD Connect crea l'account di Active Directory Domain Services. 

- Quando si configura Azure AD Connect, l'amministratore che esegue l'installazione può specificare un account di Active Directory Domain Services esistente oppure consentire ad Azure AD Connect di creare automaticamente l'account. Le modifiche alle autorizzazioni vengono applicate automaticamente all'account di Active Directory Domain Services creato da Azure AD Connect durante l'installazione. Non vengono applicate all'account di Active Directory Domain Services esistente fornito dall'amministratore che esegue l'installazione.
- Per i clienti che hanno eseguito l'aggiornamento da una versione precedente di Azure AD Connect alla versione 1.1.654.0 (o successive), le modifiche alle autorizzazioni non verranno applicate retroattivamente agli account di Active Directory Domain Services esistenti creati prima dell'aggiornamento. Le modifiche verranno applicate solo ai nuovi account di Active Directory Domain Services creati dopo l'aggiornamento. Ciò si verifica quando si aggiungono nuove foreste di AD da sincronizzare con Azure AD.

>[!NOTE]
>Questa versione rimuove solo la vulnerabilità per le nuove installazioni di Azure AD Connect, in cui l'account del servizio viene creato dal processo di installazione. Per le installazioni esistenti o nei casi in cui l'utente specifichi manualmente l'account, è consigliabile assicurarsi che questa vulnerabilità non esista.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Bloccare l'accesso all'account di Active Directory Domain Services
Bloccare l'accesso all'account di Active Directory Domain Services implementando le seguenti modifiche alle autorizzazioni in AD locale:  

*   Disabilitare l'ereditarietà nell'oggetto specificato
*   Rimuovere tutte le voci ACE nell'oggetto specifico, ad eccezione delle voci ACE specifiche di SELF. Le autorizzazioni predefinite devono rimanere inalterate per SELF.
*   Assegnare le autorizzazioni specifiche seguenti:

Type     | Nome                          | Accesso               | Si applica a
---------|-------------------------------|----------------------|--------------|
Allow    | SYSTEM                        | Controllo completo         | Questo oggetto  |
Allow    | Enterprise Admins             | Controllo completo         | Questo oggetto  |
Allow    | Domain Admins                 | Controllo completo         | Questo oggetto  |
Allow    | Administrators                | Controllo completo         | Questo oggetto  |
Allow    | Controller di dominio organizzazione | Contenuto elenco        | Questo oggetto  |
Allow    | Controller di dominio organizzazione | Leggi tutte le proprietà  | Questo oggetto  |
Allow    | Controller di dominio organizzazione | Autorizzazioni di lettura     | Questo oggetto  |
Allow    | Utenti autenticati           | Contenuto elenco        | Questo oggetto  |
Allow    | Utenti autenticati           | Leggi tutte le proprietà  | Questo oggetto  |
Allow    | Utenti autenticati           | Autorizzazioni di lettura     | Questo oggetto  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script di PowerShell per restringere le impostazioni per un account del servizio pre-esistente

Per usare lo script di PowerShell per applicare queste impostazioni a un account di Active Directory Domain Services preesistente (fornito dall'organizzazione o creato da un'installazione precedente di Azure AD Connect), scaricare lo script dal collegamento fornito sopra.

##### <a name="usage"></a>Utilizzo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = account Active Directory di cui restringere le autorizzazioni.

**$Credential** = credenziali dell'amministratore che ha i privilegi necessari per limitare le autorizzazioni per l'account $ObjectDN. Questi privilegi sono generalmente gestiti dall'amministratore Enterprise o del dominio. Usare il nome di dominio completo dell'account amministratore per evitare errori di ricerca dell'account. Esempio: contoso.com\admin.

>[!NOTE] 
>Il formato di $credential.UserName deve essere FQDN\nomeutente. Esempio: contoso.com\admin 

##### <a name="example"></a>Esempio:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Questa vulnerabilità è stata usata per ottenere un accesso non autorizzato?

Per controllare se questa vulnerabilità è stata usata per compromettere la configurazione di Azure AD Connect, è necessario verificare la data dell'ultima reimpostazione della password dell'account del servizio.  Se il timestamp non è previsto, è necessario eseguire un'analisi più approfondita tramite il log eventi dell'evento di reimpostazione della password.

Per altre informazioni, vedere [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318) (Avviso di sicurezza Microsoft 4056318).

## <a name="116490"></a>1.1.649.0
Stato: 27 ottobre 2017

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problema risolto
* È stato risolto il problema di compatibilità di versione tra Azure AD Connect e l'agente di Azure AD Connect Health (per la sincronizzazione). Questo problema interessa i clienti che eseguono l'aggiornamento sul posto di Azure AD Connect versione 1.1.647.0, ma hanno attualmente l'agente integrità versione 3.0.127.0. Dopo l'aggiornamento, l'agente integrità non può più inviare dati di integrità relativi al servizio di sincronizzazione di Azure AD Connect al servizio integrità di Azure Active Directory. Con questa correzione, l'agente di integrità versione 3.0.129.0 viene installato durante l'aggiornamento sul posto di Azure AD Connect. L'agente integrità versione 3.0.129.0 non presenta il problema di compatibilità con Azure AD Connect versione 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stato: 19 ottobre 2017

> [!IMPORTANT]
> Si verifica un problema noto di compatibilità tra Azure AD Connect versione 1.1.647.0 e l'agente di Azure AD Connect Health (per la sincronizzazione) versione 3.0.127.0. Questo problema impedisce l'invio da parte dell'agente Health dei dati di integrità sul servizio di sincronizzazione di Azure AD Connect (inclusi errori di sincronizzazione degli oggetti e dati di cronologia delle esecuzioni) al servizio Azure AD Health. Prima di aggiornare manualmente la distribuzione di Azure AD Connect alla versione 1.1.647.0, verificare la versione corrente dell'agente di Azure AD Connect Health installata nel server di Azure AD Connect. Per eseguire questa operazione, accedere a *Pannello di controllo → Installazione applicazioni* e cercare l'applicazione *Microsoft Azure AD Connect Health Agent for Sync*. Se la versione è 3.0.127.0, è consigliabile attendere la successiva versione di Azure AD Connect disponibile prima di eseguire l'aggiornamento. Se la versione dell'agente Health non è 3.0.127.0, è possibile procedere con l'aggiornamento manuale sul posto. Questo problema non riguarda l'aggiornamento swing o i clienti che eseguono una nuova installazione di Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemi risolti
* È stato risolto un problema con l'attività *Cambia l'accesso utente* nella procedura guidata di Azure AD Connect:

  * Il problema si verifica in presenza di una distribuzione esistente di Azure AD Connect con sincronizzazione della password **abilitata** e si prova a impostare il metodo di accesso utente come *Autenticazione pass-through*. Prima che la modifica venisse applicata, la procedura guidata mostrava in modo errato il prompt di *disabilitazione della sincronizzazione delle password*. La sincronizzazione delle password rimaneva tuttavia abilitata anche dopo l'applicazione della modifica. Con questa correzione, la procedura guidata non visualizza più il prompt.

  * Per impostazione predefinita, la procedura guidata non disabilita la sincronizzazione delle password quando si aggiorna il metodo di accesso utente usando l'attività *Cambia l'accesso utente*. In questo modo si evitano interruzioni dei servizi per i clienti che vogliono mantenere la sincronizzazione delle password, anche se abilitano l'autenticazione pass-through o la federazione come metodo di accesso utente primario.
  
  * Per disabilitare la sincronizzazione delle password dopo aver aggiornato il metodo di accesso utente, è necessario eseguire l'attività *Customize Synchronization Configuration* (Personalizzare la configurazione della sincronizzazione) nella procedura guidata. Quando si accede alla pagina *Funzionalità facoltative*, deselezionare l'opzione *Sincronizzazione password*.
  
  * Lo stesso problema si verifica anche se si prova ad abilitare/disabilitare Accesso Single Sign-On facile. Nello specifico, si verifica in presenza di una distribuzione esistente di Azure AD Connect con sincronizzazione della password abilitata quando il metodo di accesso utente è già configurato come *Autenticazione pass-through*. Con l'attività *Cambia l'accesso utente*, si prova a selezionare o deselezionare l'opzione *Enable Seamless Single Sign-On* (Abilita accesso Single Sign-On facile) mentre il metodo di accesso utente rimane configurato come "Autenticazione pass-through". Prima che la modifica venisse applicata, la procedura guidata mostrava in modo errato il prompt di *disabilitazione della sincronizzazione delle password*. La sincronizzazione delle password rimaneva tuttavia abilitata anche dopo l'applicazione della modifica. Con questa correzione, la procedura guidata non visualizza più il prompt.

* È stato risolto un problema con l'attività *Cambia l'accesso utente* nella procedura guidata di Azure AD Connect:

  * Il problema si verifica in presenza di una distribuzione esistente di Azure AD Connect con sincronizzazione della password **disabilitata** e se l'utente prova a impostare il metodo di accesso utente come *Autenticazione pass-through*. Quando viene applicata la modifica, la procedura guidata consente sia l'autenticazione pass-through e la sincronizzazione delle password. Con questa correzione, la procedura guidata non abilita più la sincronizzazione delle password.

  * In precedenza, la sincronizzazione delle password era un prerequisito per l'abilitazione dell'autenticazione pass-through. Quando si impostava il metodo di accesso utente come *Autenticazione pass-through*, la procedura guidata abilitava sia l'autenticazione pass-through sia la sincronizzazione delle password. Di recente, la sincronizzazione delle password non costituisce più un prerequisito. Nell'ambito di Azure AD Connect versione 1.1.557.0 è stata apportata una modifica ad Azure AD Connect per non abilitare la sincronizzazione delle password quando si imposta il metodo di accesso utente come *Autenticazione pass-through*. Tuttavia, la modifica veniva applicata solo all'installazione di Azure AD Connect. Con questa correzione, la stessa modifica viene applicata anche all'attività *Cambia l'accesso utente*.
  
  * Lo stesso problema si verifica anche se si prova ad abilitare/disabilitare Accesso Single Sign-On facile. Nello specifico, si verifica in presenza di una distribuzione esistente di Azure AD Connect con sincronizzazione della password disabilitata quando il metodo di accesso utente è già configurato come *Autenticazione pass-through*. Con l'attività *Cambia l'accesso utente*, si prova a selezionare o deselezionare l'opzione *Enable Seamless Single Sign-On* (Abilita accesso Single Sign-On facile) mentre il metodo di accesso utente rimane configurato come "Autenticazione pass-through". Quando viene applicata la modifica, la procedura guidata abilita la sincronizzazione delle password. Con questa correzione, la procedura guidata non abilita più la sincronizzazione delle password. 

* È stato risolto un problema che impediva l'aggiornamento di Azure AD Connect con errore "*Unable to upgrade the Synchronization Service*" (Impossibile aggiornare il servizio di sincronizzazione). Inoltre, il servizio di sincronizzazione non è più avviato con l'evento di errore "*The service was unable to start because the version of the database is newer than the version of the binaries installed*" (Impossibile avviare il servizio, poiché la versione del database è più recente della versione dei binari installati). Il problema si verifica quando l'amministratore che esegue l'aggiornamento non dispone dei privilegi di amministratore di sistema per l'istanza di SQL Server usata da Azure AD Connect. Con questa correzione, Azure AD Connect richiede solo che l'amministratore disponga del privilegio db_owner per il database ADSync durante l'aggiornamento.

* È stato corretto un problema relativo all'aggiornamento di Azure AD Connect che interessa i clienti per i quali è abilitata la funzionalità [Accesso Single Sign-On facile](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Dopo l'aggiornamento di Azure AD Connect, Accesso Single Sign-On facile appare erroneamente come disabilitato nella procedura guidata di Azure AD Connect, anche se la funzionalità rimane abilitata e completamente funzionante. Con questa correzione, la funzionalità appare ora correttamente abilitata nella procedura guidata.

* È stato risolto un problema in base al quale la procedura guidata di Azure AD Connect mostrava sempre il prompt "*Configura l'ancoraggio di origine*" nella pagina *Pronto per la configurazione*, anche se non venivano apportate modifiche relative all'ancoraggio di origine.

* Quando si esegue l'aggiornamento manuale sul posto di Azure AD Connect, il cliente deve fornire le credenziali di amministratore globale del tenant di Azure AD corrispondente. In precedenza, l'aggiornamento poteva procedere anche se le credenziali di amministratore globale appartenevano a un tenant di Azure AD diverso. Sebbene l'aggiornamento risulti completato, alcune configurazioni non vengono mantenute correttamente con l'aggiornamento. Con questa modifica, la procedura guidata impedisce all'aggiornamento di procedere se le credenziali fornite non corrispondono al tenant di Azure AD.

* È stata rimossa la logica ridondante che riavvia inutilmente il servizio Azure AD Connect Health all'inizio di un aggiornamento manuale.


#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* È stata aggiunta logica per semplificare i passaggi necessari per configurare Azure AD Connect con il cloud di Microsoft Germania. In precedenza era necessario aggiornare specifiche chiavi del Registro di sistema nel server di Azure AD Connect per garantirne il funzionamento con il cloud di Microsoft Germania, come descritto in questo articolo. Ora, Azure AD Connect è in grado di rilevare automaticamente se il tenant si trova nel cloud di Microsoft Germania in base alle credenziali di amministratore globale specificate durante l'installazione.

### <a name="azure-ad-connect-sync"></a>Servizio di sincronizzazione Azure AD Connect
> [!NOTE]
> Nota: il servizio di sincronizzazione ha un'interfaccia WMI che consente di sviluppare un'utilità di pianificazione personalizzata. Questa interfaccia è ora obsoleta e verrà rimossa dalle versioni future di Azure AD Connect spedite dopo il 30 giugno 2018. I clienti che vogliono personalizzare la pianificazione della sincronizzazione devono usare [l'utilità di pianificazione predefinita](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problemi risolti
* Quando la procedura guidata di Azure AD Connect crea l'account AD Connector necessario per sincronizzare le modifiche da Active Directory in locale, non assegna correttamente all'account l'autorizzazione necessaria per leggere gli oggetti PublicFolder. Questo problema riguarda sia l'installazione rapida sia l'installazione personalizzata. Questa modifica risolve il problema.

* È stato risolto un problema che impediva il rendering corretto della pagina relativa alla risoluzione dei problemi della procedura guidata di Azure AD Connect per gli amministratori a partire da Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* Durante la risoluzione dei problemi di sincronizzazione delle password usando la pagina relativa alla risoluzione dei problemi della procedura guidata di Azure AD Connect, la pagina ora restituisce lo stato specifico del dominio.

* In precedenza, se si provava ad abilitare la sincronizzazione degli hash password, Azure AD Connect non controllava se l'account AD Connector disponesse delle autorizzazioni necessarie per la sincronizzazione degli hash password da AD in locale. Ora, la procedura guidata di Azure AD Connect verifica e avvisa l'utente se l'account AD Connector non dispone di autorizzazioni sufficienti.

### <a name="ad-fs-management"></a>Gestione di AD FS
#### <a name="fixed-issue"></a>Problema risolto
* È stato risolto un problema relativo all'uso della funzionalità [ms-DS-ConsistencyGuid come sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor). Questo problema interessa i clienti che hanno configurato *Federazione tramite AD FS* come metodo di accesso utente. Quando si esegue l'attività *Configura ancoraggio di origine* nella procedura guidata, Azure AD Connect inizia a usare *ms-DS-ConsistencyGuid come attributo di origine per immutableId. Nell'ambito di questa modifica, Azure AD Connect prova ad aggiornare le regole attestazioni per ImmutableId in AD FS. Tuttavia, questo passaggio non riusciva, poiché Azure AD Connect non disponeva delle credenziali di amministratore necessarie per configurare AD FS. Con questa correzione, Azure AD Connect richiede ora di immettere le credenziali di amministratore per AD FS quando si esegue l'attività *Configura ancoraggio di origine*.



## <a name="116140"></a>1.1.614.0
Stato: 5 settembre 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemi noti
* Un problema noto impedisce l'aggiornamento di Azure AD Connect con errore "*Unable to upgrade the Synchronization Service*" (Impossibile aggiornare il servizio di sincronizzazione). Inoltre, il servizio di sincronizzazione non è più avviato con l'evento di errore "*The service was unable to start because the version of the database is newer than the version of the binaries installed*" (Impossibile avviare il servizio, poiché la versione del database è più recente della versione dei binari installati). Il problema si verifica quando l'amministratore che esegue l'aggiornamento non dispone dei privilegi di amministratore di sistema per l'istanza di SQL Server usata da Azure AD Connect. Le autorizzazioni dbo non sono sufficienti.

* Esiste un problema noto relativo all'aggiornamento di Azure AD Connect che interessa i clienti per i quali è abilitata la funzionalità [Accesso Single Sign-On facile](how-to-connect-sso.md). Dopo l'aggiornamento di Azure AD Connect, la funzionalità risulta disabilitata nella procedura guidata, anche se in realtà è ancora abilitata. La correzione per questo problema verrà implementata in una versione successiva. I clienti interessati da questo problema di visualizzazione possono correggerlo manualmente abilitando la funzionalità Accesso Single Sign-On facile nella procedura guidata.

#### <a name="fixed-issues"></a>Problemi risolti
* È stato risolto un problema che impedisce ad Azure AD Connect di aggiornare le regole attestazioni in AD FS locale mentre era abilitata la funzionalità [ms-DS-ConsistencyGuid come Ancoraggio di origine](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor). Il problema si verifica se si tenta di abilitare la funzionalità per una distribuzione di Azure AD Connect esistente che ha AD FS configurato come metodo di accesso. Il problema si verifica perché la procedura guidata non richiede le credenziali del file system distribuito di Azure prima di tentare di aggiornare le regole attestazioni in AD FS.
* È stato risolto un problema che impedisce l'installazione di Azure AD Connect se nella foresta locale di Active Directory è disabilitato NTLM. Il problema è dovuto al fatto che la procedura guidata di Azure AD Connect non fornisce le credenziali complete durante la creazione dei contesti di sicurezza necessari per l'autenticazione Kerberos. L'autenticazione Kerberos ha pertanto esito negativo e la procedura guidata di Azure AD Connect deve ricorrere all'utilizzo di NTLM.

### <a name="azure-ad-connect-sync"></a>Servizio di sincronizzazione Azure AD Connect
#### <a name="fixed-issues"></a>Problemi risolti
* È stato risolto un problema a causa del quale non è possibile creare una nuova regola di sincronizzazione se l'attributo Tag non è specificato.
* È stato risolto un problema a causa del quale, per sincronizzare le password, Azure AD Connect si connette all'istanza locale di Active Directory tramite NTLM, anche se è disponibile Kerberos. Questo problema si verifica se la topologia dell'istanza locale di Active Directory ha uno o più controller di dominio ripristinati da un backup.
* È stato risolto un problema a causa del quale vengono eseguiti inutilmente passaggi di sincronizzazione completa dopo l'aggiornamento. In generale, l'esecuzione di questi passaggi è necessaria dopo l'aggiornamento in caso di modifiche alle regole di sincronizzazione predefinite. Il problema è dovuto a un errore nella logica di rilevamento delle modifiche che individua erroneamente una modifica se un'espressione di una regola di sincronizzazione include caratteri di nuova riga. Questi caratteri vengono inseriti nelle espressioni delle regole di sincronizzazione per favorire la leggibilità.
* È stato risolto un problema che può impedire il funzionamento del server di Azure AD Connect dopo l'aggiornamento automatico. Questo problema interessa i server di Azure AD Connect con versione 1.1.443.0 o precedente. Per informazioni dettagliate sul problema, vedere l'articolo [Azure AD Connect non funziona correttamente dopo un aggiornamento automatico](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* È stato risolto un problema che può causare la ripetizione del tentativo di aggiornamento automatico ogni 5 minuti quando vengono rilevati errori. Con la correzione, in caso di errori, il tentativo di aggiornamento automatico viene ripetuto con una frequenza esponenziale.
* È stato risolto un problema a causa del quale l'evento di sincronizzazione password 611 non è visualizzato in modo corretto nei log eventi applicazioni di Windows e risulta come **evento informativo** anziché come **errore**. L'evento 611 viene generato ogni volta che un problema di sincronizzazione password viene rilevato. 
* È stato risolto un problema nella procedura guidata di Azure AD Connect che consente l'abilitazione della funzionalità Writeback gruppi senza che sia selezionata un'unità organizzativa necessaria per il writeback dei gruppi.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* Nella procedura guidata di Azure AD Connect è stata aggiunta un'attività di risoluzione dei problemi nell'area Attività aggiuntive. I clienti possono usare questa attività per risolvere i problemi relativi alla sincronizzazione delle password e raccogliere dati di diagnostica generali. In futuro l'attività di risoluzione dei problemi verrà estesa in modo da includere altri problemi relativi alla sincronizzazione delle directory.
* Azure AD Connect supporta ora una nuova modalità di installazione che prevede l'**utilizzo di un database esistente**. Questa modalità consente di installare Azure AD Connect specificando un database ADSync esistente. Per altre informazioni su questa funzionalità, vedere l'articolo [Installare Azure AD Connect usando un database ADSync esistente](how-to-connect-install-existing-database.md).
* Per maggiore sicurezza, Azure AD Connect prevede ora l'utilizzo di TLS 1.2 per connettersi ad Azure AD per la sincronizzazione delle directory. Nelle versioni precedenti, per impostazione predefinita viene usato TLS 1.0.
* Quando viene avviato, l'agente di sincronizzazione password di Azure AD Connect prova a connettersi all'endpoint noto di Azure AD per la sincronizzazione delle password. Al momento della connessione, l'agente viene reindirizzato a un endpoint specifico dell'area. Nelle versioni precedenti l'agente di sincronizzazione password memorizza nella cache l'endpoint specifico dell'area finché non viene riavviato. Nella versione corrente l'agente cancella il contenuto della cache e riprova a connettersi all'endpoint noto se riscontra un problema di connessione con l'endpoint specifico dell'area. Questa modifica consente il failover della sincronizzazione delle password in un altro endpoint specifico dell'area quando quello memorizzato nella cache non è più disponibile.
* Per sincronizzare le modifiche da una foresta locale di Active Directory è necessario un account di Active Directory Domain Services. È possibile (i) creare manualmente l'account di Active Directory Domain Services e specificarne le credenziali per Azure AD Connect oppure (ii) specificare le credenziali di amministratore dell'organizzazione e consentire ad Azure AD Connect di creare automaticamente l'account di Active Directory Domain Services. Nelle versioni precedenti della procedura guidata di Azure AD Connect l'opzione predefinita è la (i), mentre nella versione corrente è la (ii).

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* È stato aggiunto il supporto per il cloud di Microsoft Azure per enti pubblici e Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gestione di AD FS
#### <a name="fixed-issues"></a>Problemi risolti
* Nelle versioni precedenti il cmdlet Initialize-ADSyncNGCKeysWriteBack nel modulo ADPrep di PowerShell applica erroneamente gli ACL al contenitore di registrazione del dispositivo e pertanto eredita solo le autorizzazioni esistenti.  Questo problema è stato risolto in modo che l'account del servizio di sincronizzazione disponga delle autorizzazioni corrette.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* L'attività Verifica l'accesso AD FS di AAD Connect è stata aggiornata in modo da verificare gli account di accesso con Microsoft Online e non solo il recupero dei token da AD FS.
* Nella procedura di configurazione di una nuova farm AD FS con AAD Connect, la pagina in cui sono richieste le credenziali AD FS è stata spostata ed è ora visualizzata prima che all'utente venga chiesto di specificare i server AD FS e WAP.  In questo modo AAD Connect può verificare che l'account specificato disponga delle autorizzazioni corrette.
* Durante l'aggiornamento di AAD Connect, non si verifica più un errore se la relazione di trust tra AD FS e AAD non viene aggiornata.  In caso di mancato aggiornamento, l'utente viene informato con un messaggio di avviso appropriato e deve reimpostare la relazione di trust usando l'attività aggiuntiva di AAD Connect.

### <a name="seamless-single-sign-on"></a>Accesso Single Sign-On facile
#### <a name="fixed-issues"></a>Problemi risolti
* È stato risolto un problema a causa del quale la procedura guidata di Azure AD Connect restituisce un errore se si prova ad abilitare la funzionalità [Accesso Single Sign-On facile](how-to-connect-sso.md). Il messaggio di errore visualizzato segnala che *non è stato possibile configurare l'agente di autenticazione di Microsoft Azure AD Connect*. Questo problema interessa i clienti esistenti che hanno aggiornato manualmente la versione di anteprima degli agenti di autenticazione per l'[autenticazione pass-through](how-to-connect-sso.md) in base ai passaggi descritti in questo [articolo](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stato: 23 luglio 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema risolto

* È stato risolto un problema che causava la rimozione della regola di sincronizzazione predefinita "Uscita ad Active Directory - Utente ImmutableId":

  * Il problema si verifica quando viene aggiornato Azure AD Connect o quando l'opzione dell’attività *Aggiorna configurazione di sincronizzazione* nella procedura guidata di AD Azure Connect viene usata per aggiornare la configurazione di sincronizzazione di Azure AD Connect.
  
  * Questa regola di sincronizzazione è applicabile ai clienti che hanno attivato la [funzione ms-DS-ConsistencyGuid come ancoraggio di origine](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Questa funzione è stata introdotta nella versione 1.1.524.0 e successive. Quando la regola di sincronizzazione viene rimossa, Azure AD Connect non può più popolare l'attributo locale ms-DS-ConsistencyGuid di Active Directory con il valore dell'attributo ObjectGuid. Ciò non impedisce il provisioning di nuovi utenti in Azure AD.
  
  * La correzione assicura che la regola di sincronizzazione non venga più rimossa durante l'aggiornamento o durante la modifica della configurazione, purché la funzione sia abilitata. Per i clienti esistenti interessati da questo problema, la correzione assicura inoltre che la regola di sincronizzazione venga nuovamente aggiunta dopo l'aggiornamento a questa versione di Azure AD Connect.

* È stato risolto un problema che imposta la precedenza delle regole di sincronizzazione predefinite a un valore minore di 100:

  * in generale, i valori di priorità 0 - 99 sono riservati alle regole di sincronizzazione personalizzate. Durante l'aggiornamento, vengono aggiornati i valori di precedenza delle regole di sincronizzazione predefinite per implementare le modifiche alle regole di sincronizzazione. A causa di questo problema, può essere assegnato un valore di precedenza alle regole di sincronizzazione predefinite minore di 100.
  
  * La correzione impedisce che il problema si verifichi durante l'aggiornamento. Tuttavia, non verranno ripristinati i valori di priorità per i clienti esistenti che sono stati interessati dal problema. Verrà fornita in futuro una correzione separata per facilitare il ripristino.

* È stato risolto un problema che nella [schermata di dominio e filtro dell’unità organizzativa](how-to-connect-install-custom.md#domain-and-ou-filtering) della procedura guidata di AD Azure Connect mostra l’opzione *Sincronizza tutti i domini e le unità organizzative* come selezionata, anche se è abilitato il filtro basato sulle unità organizzative.

*   È stato risolto un problema che causava la comparsa di un errore nella [schermata Configura partizioni di Directory](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) all’interno del Synchronization Service Manager quando si faceva clic sul pulsante *Aggiorna*. Messaggio di errore: *Si è verificato un errore durante l'aggiornamento dei domini: impossibile eseguire il cast dell'oggetto di tipo 'System.Collections.ArrayList' al tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject'* . L'errore si verifica quando il nuovo dominio di Active Directory è stato aggiunto a una foresta di Active Directory esistente e si sta tentando di aggiornare Azure AD Connect con il pulsante Aggiorna.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* La [funzionalità di aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) è stata ampliata per supportare i clienti con le seguenti configurazioni:
  * È stata abilitata la funzionalità di writeback dei dispositivi.
  * È stata abilitata la funzionalità di writeback dei gruppi.
  * L'installazione non è un aggiornamento delle impostazioni rapide o di DirSync.
  * Il metaverse include più di 100.000 oggetti.
  * Viene stabilita la connessione a più foreste. L'installazione rapida si connette a una sola foresta.
  * L'account AD Connect non è più l'account MSOL_ predefinito.
  * Il server è in modalità di staging.
  * È stata abilitata la funzionalità di writeback degli utenti.
  
  >[!NOTE]
  >L'espansione dell'ambito di applicazione della funzionalità di aggiornamento automatico interessa i clienti di Azure AD Connect con build 1.1.105.0 e successive. Se non si desidera l'aggiornamento automatico del server di Azure AD Connect, è necessario eseguire il seguente cmdlet nel server di Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Per altre informazioni su come abilitare/disabilitare l'aggiornamento automatico, vedere l'articolo [Azure AD Connect: aggiornamento automatico](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stato: non verrà resa disponibile. Le modifiche apportate a questa build sono incluse nella versione 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema risolto

* È stato risolto un problema che causava la rimozione della regola di sincronizzazione predefinita "Uscita ad Active Directory - Utente ImmutableId" in caso di aggiornamento della configurazione del filtro basato sulle unità organizzative. Questa regola di sincronizzazione è richiesta per la [funzione ms-DS-ConsistencyGuid come ancoraggio di origine](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* È stato risolto un problema che nella [schermata di dominio e filtro dell’unità organizzativa](how-to-connect-install-custom.md#domain-and-ou-filtering) della procedura guidata di AD Azure Connect mostra l’opzione *Sincronizza tutti i domini e le unità organizzative* come selezionata, anche se è abilitato il filtro basato sulle unità organizzative.

*   È stato risolto un problema che causava la comparsa di un errore nella [schermata Configura partizioni di Directory](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) all’interno del Synchronization Service Manager quando si faceva clic sul pulsante *Aggiorna*. Messaggio di errore: *Si è verificato un errore durante l'aggiornamento dei domini: impossibile eseguire il cast dell'oggetto di tipo 'System.Collections.ArrayList' al tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject'* . L'errore si verifica quando il nuovo dominio di Active Directory è stato aggiunto a una foresta di Active Directory esistente e si sta tentando di aggiornare Azure AD Connect con il pulsante Aggiorna.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* La [funzionalità di aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) è stata ampliata per supportare i clienti con le seguenti configurazioni:
  * È stata abilitata la funzionalità di writeback dei dispositivi.
  * È stata abilitata la funzionalità di writeback dei gruppi.
  * L'installazione non è un aggiornamento delle impostazioni rapide o di DirSync.
  * Il metaverse include più di 100.000 oggetti.
  * Viene stabilita la connessione a più foreste. L'installazione rapida si connette a una sola foresta.
  * L'account AD Connect non è più l'account MSOL_ predefinito.
  * Il server è in modalità di staging.
  * È stata abilitata la funzionalità di writeback degli utenti.
  
  >[!NOTE]
  >L'espansione dell'ambito di applicazione della funzionalità di aggiornamento automatico interessa i clienti di Azure AD Connect con build 1.1.105.0 e successive. Se non si desidera l'aggiornamento automatico del server di Azure AD Connect, è necessario eseguire il seguente cmdlet nel server di Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Per altre informazioni su come abilitare/disabilitare l'aggiornamento automatico, vedere l'articolo [Azure AD Connect: aggiornamento automatico](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stato: Luglio 2017

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema risolto
* È stato risolto un problema relativo al cmdlet Initialize-ADSyncDomainJoinedComputerSync a causa del quale il dominio verificato configurato nell'oggetto del punto di connessione del servizio esistente viene modificato anche se è ancora un dominio valido. Questo problema si verifica quando il tenant di Azure AD ha più domini verificati che possono essere usati per configurare il punto di connessione del servizio.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* Il writeback delle password è ora disponibile per l'anteprima con il cloud di Microsoft Azure per enti pubblici e Microsoft Cloud Germany. Per altre informazioni sul supporto di Azure AD Connect per le diverse istanze del servizio, vedere l'articolo [Azure AD Connect: Considerazioni speciali per le istanze](reference-connect-instances.md).

* Il cmdlet Initialize-ADSyncDomainJoinedComputerSync include ora un nuovo parametro facoltativo denominato AzureADDomain che consente di specificare il dominio verificato da usare per configurare il punto di connessione del servizio.

### <a name="pass-through-authentication"></a>Autenticazione pass-through

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* Il nome dell'agente necessario per l'autenticazione pass-through è stato modificato da *connettore del proxy di applicazione di Microsoft Azure AD* ad *agente di autenticazione di Microsoft Azure AD Connect*.

* Quando si abilita l'autenticazione pass-through, la sincronizzazione degli hash delle password non viene più abilitata per impostazione predefinita.


## <a name="115530"></a>1.1.553.0
Stato: giugno 2017

> [!IMPORTANT]
> Questa build introduce modifiche alle regole dello schema e della sincronizzazione. Il servizio di sincronizzazione Azure AD Connect attiva i passaggi di importazione e sincronizzazione completa dopo l'aggiornamento. Di seguito vengono descritti i dettagli delle modifiche. Per rinviare temporaneamente i passaggi di importazione e sincronizzazione completa dopo l'aggiornamento, vedere l'articolo [How to defer full synchronization after upgrade](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade) (Come rinviare la sincronizzazione completa dopo l'aggiornamento).
>
>

### <a name="azure-ad-connect-sync"></a>Servizio di sincronizzazione Azure AD Connect

#### <a name="known-issue"></a>Problema noto
* Un problema interessa i clienti che usano il [filtro basato su unità organizzativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) con il servizio di sincronizzazione Azure AD Connect. Quando si accede alla [pagina Filtro di domini e unità organizzative](how-to-connect-install-custom.md#domain-and-ou-filtering) della procedura guidata di Azure AD Connect, è previsto il comportamento seguente:
  * Se il filtro basato su unità organizzativa è abilitato, è selezionata l'opzione **Sincronizza le unità organizzative e i domini selezionati**.
  * In caso contrario, è selezionata l'opzione **Sincronizza tutti i domini e le unità organizzative**.

A causa del problema, l'opzione **Sincronizza tutti i domini e le unità organizzative** rimane sempre selezionata quando si esegue la procedura guidata.  Ciò si verifica anche se in precedenza è stato configurato il filtro basato su unità organizzativa. Prima di salvare le modifiche alla configurazione di AAD Connect, assicurarsi che l'opzione **Sincronizza le unità organizzative e i domini selezionati** sia selezionata e verificare che tutte le unità organizzative da sincronizzare siano nuovamente abilitate. In caso contrario, il filtro basato su unità organizzativa verrà disabilitato.

#### <a name="fixed-issues"></a>Problemi risolti

* È stato risolto un problema relativo al writeback delle password che consente a un amministratore di Azure AD di reimpostare la password di un account utente AD locale con privilegi. Il problema si verifica quando ad Azure AD Connect viene concessa l'autorizzazione di reimpostare la password per l'account con privilegi. Il problema viene risolto in questa versione di Azure AD Connect consentendo a un amministratore di Azure AD di reimpostare la password di un account utente AD locale con privilegi solo se è il proprietario di tale account. Per altre informazioni, vedere [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453) (Avviso di sicurezza 4033453).

* È stato risolto un problema relativo alla funzionalità [ms-DS-ConsistencyGuid come sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) per cui Azure AD Connect non esegue il writeback nell'attributo ms-DS-ConsistencyGuid di Active Directory locale. Il problema si verifica quando sono state aggiunte più foreste di Active Directory locali ad Azure AD Connect e l'opzione *Le identità utente sono presenti in più directory* è selezionata. Quando viene usato questo tipo di configurazione, le regole di sincronizzazione risultanti non popolano l'attributo sourceAnchorBinary nel metaverse. L'attributo sourceAnchorBinary viene usato come origine per l'attributo ms-DS-ConsistencyGuid. Il writeback nell'attributo ms-DSConsistencyGuid non viene pertanto eseguito. Per risolvere il problema, le regole di sincronizzazione seguenti sono state aggiornate in modo da garantire che l'attributo sourceAnchorBinary nel metaverse sia sempre popolato:
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* Nelle versioni precedenti, anche se la funzionalità [ms-DS-ConsistencyGuid come sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) non è abilitata, la regola di sincronizzazione "Out to AD - User ImmutableId" viene comunque aggiunta ad Azure AD Connect. L'effetto non è grave e il writeback dell'attributo ms-DS-ConsistencyGuid non viene eseguito. Per evitare confusione, è stata introdotta la logica per garantire che la regola di sincronizzazione venga aggiunta solo quando la funzionalità è abilitata.

* È stato risolto un problema che non consente la sincronizzazione degli hash delle password generando l'evento di errore 611. Questo problema si verifica dopo che uno o più controller di dominio sono stati rimossi dall'istanza di AD locale. Alla fine di ogni ciclo di sincronizzazione delle password, il cookie di sincronizzazione emesso dall'istanza di AD locale contiene gli ID di chiamata dei controller di dominio rimossi con numero di sequenza di aggiornamento (USN) pari a 0. Gestione sincronizzazione password non è in grado di salvare in modo permanente il cookie di sincronizzazione contenente il valore USN 0 e quindi la sincronizzazione non riesce con evento di errore 611. Durante il successivo ciclo di sincronizzazione, Gestione sincronizzazione password riutilizza l'ultimo cookie di sincronizzazione persistente che non contiene il valore USN 0. In questo modo vengono sincronizzate nuovamente le stesse modifiche alle password. Con questa correzione, Gestione sincronizzazione password salva correttamente il cookie di sincronizzazione in modo permanente.

* Nelle versioni precedenti, anche se l'aggiornamento automatico è stato disabilitato tramite il cmdlet Set-ADSyncAutoUpgrade, il processo di aggiornamento automatico continua a verificare periodicamente la presenza di aggiornamenti e si basa sul programma di installazione scaricato per rispettare lo stato di disabilitazione. Con questa correzione, il processo di aggiornamento automatico non esegue più il controllo periodico degli aggiornamenti. La correzione viene applicata quando viene eseguito il programma di installazione degli aggiornamenti per la versione corrente di Azure AD Connect.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità

* Nelle versioni precedenti la funzionalità [ms-DS-ConsistencyGuid come sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) è disponibile solo per le nuove distribuzioni. Ora questa funzionalità è disponibile per le distribuzioni esistenti. Più in particolare:
  * Per accedere alla funzionalità, avviare la procedura guidata di Azure AD Connect e scegliere l'opzione *Update Source Anchor* (Aggiorna ancoraggio di origine).
  * Questa opzione è visibile solo per le distribuzioni esistenti che usano objectGuid come attributo sourceAnchor.
  * Quando si configura l'opzione, la procedura guidata controlla lo stato dell'attributo ms-DS-ConsistencyGuid nell'istanza di Active Directory locale. Se l'attributo non è configurato in nessun oggetto utente nella directory, la procedura usa l'attributo ms-DS-ConsistencyGuid come attributo sourceAnchor. Se invece l'attributo è configurato in uno o più oggetti utente nella directory, la procedura deduce che l'attributo è usato da altre applicazioni e non è adatto come attributo sourceAnchor e quindi impedisce la modifica dell'ancoraggio di origine. Se si è certi che l'attributo non è usato dalle applicazioni esistenti, è necessario contattare il supporto tecnico per informazioni su come eliminare l'errore.

* Per quanto riguarda l'attributo **userCertificate** degli oggetti dispositivo, Azure AD Connect esegue ora la ricerca dei valori di certificato necessari per [Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) ed esclude i valori restanti prima della sincronizzazione con Azure AD. Per abilitare questo comportamento, è stata aggiornata la regola di sincronizzazione predefinita "Out to AAD - Device Join SOAInAD".

* Azure AD Connect supporta ora il writeback dell'attributo **cloudPublicDelegates** di Exchange Online nell'attributo **publicDelegates** di Active Directory locale. Per effetto di questo aggiornamento, in una cassetta postale di Exchange Online possono essere concessi i diritti SendOnBehalfTo agli utenti con cassette postali di Exchange locali. Per supportare questa funzionalità, è stata introdotta una nuova regola di sincronizzazione predefinita "Out to AD - User Exchange Hybrid PublicDelegates writeback". Questa regola di sincronizzazione viene aggiunta ad Azure AD Connect solo quando la funzionalità di distribuzione ibrida di Exchange è abilitata.

* Azure AD Connect supporta ora la sincronizzazione dell'attributo **altRecipient** da Azure AD. Per supportare questa modifica, sono state aggiornate le regole di sincronizzazione predefinite seguenti in modo da includere il flusso di attributi necessario:
  * In from AD – User Exchange
  * Out to AAD - User ExchangeOnline
  
* L'attributo **cloudSOAExchMailbox** nel metaverse indica se un determinato utente dispone di una cassetta postale di Exchange Online. La relativa definizione è stata aggiornata in modo da includere altri RecipientDisplayTypes di Exchange Online come cassette postali di sala conferenze e attrezzature. Per abilitare questa modifica, la definizione dell'attributo cloudSOAExchMailbox, presente nella regola di sincronizzazione predefinita "In from AAD - User Exchange Hybrid" è stata aggiornata da:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... a:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* È stato aggiunto il set seguente di funzioni compatibili con X509Certificate2 per la creazione di espressioni di regole di sincronizzazione per gestire i valori di certificato nell'attributo userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Select
  * CertKeyAlgorithmParams
  * CertHashString
  * Where
  * With

* Le modifiche seguenti dello schema sono state introdotte per consentire ai clienti di creare regole di sincronizzazione personalizzate per includere nel flusso gli attributi sAMAccountName, domainNetBios e domainFQDN per gli oggetti gruppo e l'attributo distinguishedName per gli oggetti utente:

  * Gli attributi seguenti sono stati aggiunti allo schema Metaverse:
    * Gruppo: AccountName
    * Group: domainNetBios
    * Group: domainFQDN
    * Person: distinguishedName

  * Gli attributi seguenti sono stati aggiunti allo schema di Azure AD Connector:
    * Gruppo: OnPremisesSamAccountName
    * Gruppo: NetBiosName
    * Gruppo: DnsDomainName
    * Utente: OnPremisesDistinguishedName

* Lo script del cmdlet ADSyncDomainJoinedComputerSync include ora un nuovo parametro facoltativo denominato AzureEnvironment. Il parametro viene usato per specificare l'area in cui è ospitato il tenant di Azure Active Directory corrispondente. I valori validi includono:
  * AzureCloud (predefinito)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* L'editor delle regole di sincronizzazione è stato aggiornato in modo da usare Join (anziché Provision) come valore predefinito del tipo di collegamento durante la creazione di regole di sincronizzazione.

### <a name="ad-fs-management"></a>Gestione di AD FS

#### <a name="issues-fixed"></a>Problemi risolti

* Gli URL seguenti sono nuovi endpoint di WS-Federation introdotti da Azure AD per migliorare la resilienza in caso di interruzione dell'autenticazione e verranno aggiunti alla configurazione locale del trust della relying party di AD FS:
  * https:\//ests.login.microsoftonline.com/login.srf
  * https:\//stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* È stato risolto un problema a causa del quale AD FS genera un valore di attestazione non corretto per IssuerID. Il problema si verifica se sono presenti più domini verificati nel tenant di Azure AD e il suffisso di dominio dell'attributo userPrincipalName usato per generare l'attestazione IssuerID include almeno 3 livelli, ad esempio johndoe@us.contoso.com. Il problema viene risolto aggiornando l'espressione regolare usata dalle regole di attestazione.

#### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
* Nelle versioni precedenti la funzionalità di gestione dei certificati AD FS fornita da Azure AD Connect può essere usata solo con farm AD FS gestite tramite Azure AD Connect. È ora possibile usare la funzionalità con farm AD FS che non sono gestite tramite Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Resa disponibile: maggio 2017

> [!IMPORTANT]
> Questa build introduce modifiche alle regole dello schema e della sincronizzazione. Dopo l'aggiornamento, il servizio di sincronizzazione Azure AD Connect attiva i passaggi di importazione e sincronizzazione completa. Di seguito vengono descritti i dettagli delle modifiche.
>
>

**Problemi risolti:**

Servizio di sincronizzazione Azure AD Connect

* È stato risolto il problema che causa l'aggiornamento automatico sul server di Azure AD Connect, anche se l'utente ha disabilitato la funzionalità usando il cmdlet Set-ADSyncAutoUpgrade. Con questa correzione, il processo di aggiornamento automatico sul server controlla periodicamente la presenza di aggiornamenti, ma il programma di installazione scaricato rispetta la configurazione di Aggiornamento automatico.
* Durante l'aggiornamento sul posto di DirSync, Azure AD Connect crea un account di servizio Azure AD che viene usato dal connettore Azure AD per la sincronizzazione con Azure AD. Dopo aver creato l'account, Azure AD Connect esegue l'autenticazione in Azure AD tramite l'account. In alcuni casi, l'autenticazione non riesce a causa di problemi temporanei, che a loro volta impediscono la riuscita dell'aggiornamento sul posto di DirSync con il messaggio *Errore di esecuzione dell'attività di configurazione della sincronizzazione AAD: AADSTS50034. Per accedere a questa applicazione, l'account deve essere aggiunto alla directory xxx.onmicrosoft.com*. Per migliorare la flessibilità dell'aggiornamento di DirSync, Azure AD Connect ritenta il passaggio di autenticazione.
* Un problema con la build 443 causa la riuscita dell'aggiornamento sul posto di DirSync, ma i profili di esecuzione necessari per la sincronizzazione delle directory non vengono creati. La logica di correzione è inclusa in questa build di Azure AD Connect. Quando si esegue l'aggiornamento a questa build, Azure AD Connect rileva i profili di esecuzione mancanti e li crea.
* È stato risolto il problema che non consente la riuscita del processo di sincronizzazione delle password di avvio con ID evento 6900. Viene visualizzato l'errore *"È già stato aggiunto un elemento con la stessa chiave"* . Questo problema si verifica quando si aggiorna la configurazione del filtro dell'unità organizzativa per includere una partizione di configurazione di AD. Per risolvere il problema, il processo di sincronizzazione delle password ora sincronizza le modifiche delle password solo dalle partizioni di dominio di AD. Le partizioni non di dominio, come la partizione di configurazione, vengono ignorate.
* Durante l'installazione rapida, Azure AD Connect crea un account AD DS locale usato dal connettore AD per comunicare con AD locale. In precedenza, veniva creato l'account con il flag PASSWD_NOTREQD impostato sull'attributo user-Account-Control e veniva impostata una password casuale per l'account. Ora Azure AD Connect rimuove in modo esplicito il flag PASSWD_NOTREQD dopo aver impostato la password dell'account.
* È stato risolto il problema che causa l'esito negativo dell'aggiornamento di DirSync. Viene visualizzato l'errore *"Si è verificato un deadlock in SQL Server mentre si tenta di acquisire un blocco di applicazione"* quando l'attributo mailNickname viene trovato nello schema di AD locale, ma non è limitato alla classe di oggetti utente di AD.
* È stato risolto il problema che causa la disattivazione automatica della funzionalità Writeback dispositivi quando un amministratore aggiorna la configurazione della sincronizzazione di Azure AD Connect usando la procedura guidata di Azure AD Connect. Il problema è causato dalla procedura guidata che esegue una verifica dei prerequisiti della configurazione della funzione Writeback dispositivi esistente in AD locale. La verifica ha esito negativo. La correzione ignora la verifica se Writeback dispositivi è stata abilitata in precedenza.
* Per configurare il filtro dell'unità organizzativa, è possibile usare la procedura guidata di Azure AD Connect oppure Synchronization Service Manager. In precedenza, se si usava la procedura guidata di Azure AD Connect per configurare il filtro dell'unità organizzativa, le nuove unità organizzative create in un secondo momento venivano incluse nella sincronizzazione delle directory. Se non si intende includere le nuove unità organizzative nella sincronizzazione, è necessario configurare il filtro delle unità usando Synchronization Service Manager. Ora è possibile ottenere lo stesso comportamento con la procedura guidata di Azure AD Connect.
* È stato risolto il problema che causa la creazione delle stored procedure necessarie da Azure AD Connect nello schema dell'amministratore dell'installazione, invece che nello schema dbo.
* È stato risolto il problema che causa l'omissione dell'attributo TrackingId restituito da Azure AD dai log eventi del server di Azure AD Connect. Il problema si verifica se Azure AD Connect riceve un messaggio di reindirizzamento da Azure AD e non è in grado di connettersi all'endpoint specificato. TrackingId viene usato dal personale del supporto tecnico per la correlazione con i log sul lato servizio durante la risoluzione dei problemi.
* Quando Azure AD Connect riceve l'errore LargeObject da Azure AD, genera un evento con ID evento 6941. Viene visualizzato il messaggio *"L'oggetto di cui è stato eseguito il provisioning presenta dimensioni troppo elevate. Ridurre il numero di valori attributo dell'oggetto."* Allo stesso tempo, Azure AD Connect genera anche un evento fuorviante con ID evento 6900 e il messaggio *Microsoft.Online.Coexistence.ProvisionRetryException: Impossibile comunicare con il servizio Windows Azure Active Directory*. Per evitare possibili confusioni, Azure AD Connect non genera più l'ultimo evento quando riceve l'errore LargeObject.
* È stato risolto il problema di Synchronization Service Manager che non risponde durante il tentativo di aggiornamento della configurazione del connettore LDAP generico.

**Nuove funzionalità o miglioramenti:**

Servizio di sincronizzazione Azure AD Connect
* Modifiche alle regole di sincronizzazione. Sono state implementate le modifiche alle regole di sincronizzazione seguenti:
  * È stata aggiornata la regola di sincronizzazione predefinita affinché non esporti gli attributi **userCertificate** e **userSMIMECertificate** se gli attributi hanno più di 15 valori.
  * Gli attributi di AD **employeeID** e **msExchBypassModerationLink** sono ora inclusi nel set di regole di sincronizzazione predefinito.
  * L'attributo di AD **photo** è stato rimosso dal set di regole di sincronizzazione predefinito.
  * **preferredDataLocation** è stato aggiunto allo schema di Metaverse e allo schema di AAD Connector. Gli utenti che intendono aggiornare entrambi gli attributi in Azure AD possono a tal fine implementare regole di sincronizzazione personalizzate. 
  * **userType** è stato aggiunto allo schema di Metaverse e allo schema di AAD Connector. Gli utenti che intendono aggiornare entrambi gli attributi in Azure AD possono a tal fine implementare regole di sincronizzazione personalizzate.

* Azure AD Connect abilita ora automaticamente l'uso dell'attributo ConsistencyGuid come l'attributo sourceAnchor per gli oggetti AD locali. Inoltre, Azure AD Connect popola l'attributo ConsistencyGuid con il valore dell'attributo objectGuid se è vuoto. Questa funzionalità è applicabile soltanto alla nuova distribuzione. Per altre informazioni su questa funzionalità, vedere [Azure AD Connect: Concetti relativi alla progettazione - Uso di msDS-ConsistencyGuid come sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* È stato aggiunto il nuovo cmdlet di risoluzione dei problemi Invoke-ADSyncDiagnostics. Il cmdlet facilita la diagnosi dei problemi relativi alla sincronizzazione dell'hash delle password. Per informazioni sull'uso del cmdlet, vedere l'articolo [Risolvere i problemi di sincronizzazione dell'hash delle password con la sincronizzazione di Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect supporta ora la sincronizzazione delle cartelle pubbliche abilitate alla posta elettronica da AD locale ad Azure AD. È possibile abilitare la funzionalità tramite la procedura guidata di Azure AD Connect, in Funzionalità facoltative. Per altre informazioni su questa funzionalità, vedere l'articolo [Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218) (Supporto del blocco Edge basato sulla directory di Office 365 per le cartelle pubbliche abilitate alla posta elettronica in locale).
* Azure AD Connect richiede un account AD DS per la sincronizzazione da AD locale. In precedenza, se era stato installato Azure AD Connect usando la modalità rapida, era possibile fornire le credenziali di un account amministratore Enterprise e Azure AD Connect creava quindi l'account Active Directory Domain Services necessario. Tuttavia, per le installazioni personalizzate e per l'aggiunta di foreste a una distribuzione esistente, era necessario invece fornire l'account Active Directory Domain Services. Ora è possibile specificare le credenziali di un account amministratore Enterprise durante un'installazione personalizzata e lasciare che Azure AD Connect crei l'account di AD DS necessario.
* Azure AD Connect supporta ora SQL AOA. È necessario abilitare SQL AOA prima di installare Azure AD Connect. Durante l'installazione, Azure AD Connect rileva se l'istanza SQL specificata è abilitata per SQL AOA oppure no. Se SQL AOA è abilitato, Azure AD Connect rileva quindi se SQL AOA è configurato per usare la replica sincrona o asincrona. Quando si configura il listener del gruppo di disponibilità, è consigliabile impostare la proprietà RegisterAllProvidersIP su 0. Ciò perché Azure AD Connect usa SQL Native Client per connettersi a SQL e SQL Native Client non supporta l'uso della proprietà MultiSubNetFailover.
* Se si usa LocalDB come database di Azure AD Connect ed è stato raggiunto il limite di 10 GB, il servizio di sincronizzazione non si avvia. In precedenza, era necessario eseguire l'operazione ShrinkDatabase su LocalDB per recuperare lo spazio sufficiente per l'avvio del servizio di sincronizzazione. Dopo questa operazione, era possibile usare Synchronization Service Manager per eliminare la cronologia dell'esecuzione e recuperare spazio per il database. Ora è possibile usare il cmdlet ADSyncPurgeRunHistory per eseguire la pulizia dei dati della cronologia di esecuzione da LocalDB e recuperare spazio per il database. Questo cmdlet supporta una modalità offline (specificando il parametro -offline) che può essere usata quando il servizio di sincronizzazione non è in esecuzione. Nota: la modalità offline può essere usata solo se il servizio di sincronizzazione non è in esecuzione e se il database in uso è Local DB.
* Per ridurre lo spazio di archiviazione necessario, Azure AD Connect consente ora di comprimere i dettagli degli errori di sincronizzazione prima di archiviarli nei database LocalDB/SQL. Durante l'aggiornamento da una versione precedente di Azure AD Connect a questa versione, Azure AD Connect esegue una singola compressione dei dettagli di errore di sincronizzazione esistenti.
* In precedenza, dopo l'aggiornamento della configurazione del filtro dell'unità organizzativa, era necessario eseguire manualmente l'importazione completa per verificare che gli oggetti esistenti fossero correttamente inclusi/esclusi dalla sincronizzazione della directory. Ora Azure AD Connect attiva automaticamente l'importazione completa durante il ciclo di sincronizzazione successivo. Inoltre, l'importazione completa viene applicata soltanto ai connettori AD interessati dall'aggiornamento. Nota: questo miglioramento è applicabile agli aggiornamenti del filtro dell'unità organizzativa, eseguiti soltanto con la procedura guidata di Azure AD Connect. Non è applicabile all'aggiornamento del filtro dell'unità organizzativa eseguito tramite Synchronization Service Manager.
* In precedenza, il filtro basato sui gruppi supportava soltanto oggetti utenti, gruppi e contatti. Ora il filtro supporta anche gli oggetti computer.
* In precedenza, era possibile eliminare i dati nello spazio connettore senza disabilitare l'utilità di pianificazione della sincronizzazione di Azure AD Connect. Ora Synchronization Service Manager blocca l'eliminazione dei dati nello spazio connettore se rileva che l'utilità di pianificazione è abilitata. Viene inoltre inviato un avviso che informa i clienti di potenziali perdite di dati se i dati nello spazio connettore vengono eliminati.
* In precedenza, era necessario disattivare la trascrizione di PowerShell affinché la procedura guidata di Azure AD Connect funzionasse correttamente. Questo problema è stato risolto parzialmente. Se si usa la procedura guidata di Azure AD Connect per gestire la configurazione della sincronizzazione, è possibile abilitare la trascrizione di PowerShell. Se si usa la procedura guidata di Azure AD Connect per gestire la configurazione di AD FS, è necessario disabilitare la trascrizione di PowerShell.



## <a name="114860"></a>1.1.486.0
Resa disponibile: Aprile 2017

**Problemi risolti:**
* È stato risolto il problema di Azure AD Connect che non veniva installato correttamente nella versione localizzata di Windows Server.

## <a name="114840"></a>1.1.484.0
Resa disponibile: Aprile 2017

**Problemi noti:**

* Questa versione di Azure AD Connect non viene installa correttamente se le condizioni seguenti sono tutte vere:
   1. Si sta eseguendo l'aggiornamento sul posto di DirSync o una nuova installazione di Azure AD Connect.
   2. Si sta usando una versione localizzata di Windows Server in cui il nome del gruppo Amministratori predefinito nel server non è "Administrators".
   3. Si sta usando l'istanza predefinita di SQL Server 2012 Express LocalDB installata con Azure AD Connect anziché la versione di SQL completa.

**Problemi risolti:**

Servizio di sincronizzazione Azure AD Connect
* È stato risolto il problema dell'utilità di pianificazione della sincronizzazione che ignora l'intero passaggio di sincronizzazione se a uno o più connettori manca il profilo di esecuzione della sincronizzazione. Ad esempio, tramite Synchronization Service Manager è stato aggiunto manualmente un connettore per il quale non è stato creato un profilo di importazione delta. Grazie a questa correzione si garantisce che l'utilità di pianificazione della sincronizzazione continui a eseguire l'importazione delta per gli altri connettori.
* È stato risolto il problema del servizio di sincronizzazione che arresta immediatamente l'elaborazione di un profilo di esecuzione in caso si verifichi un problema con uno dei passaggi di esecuzione. Grazie a questa correzione si garantisce che il servizio di sincronizzazione ignori il passaggio interessato e continui a elaborare il resto. Ad esempio, il profilo d'importazione delta per il connettore di Active Directory contiene più passaggi di esecuzione, uno per ogni dominio locale di Active Directory. Il servizio di sincronizzazione esegue l'importazione delta con gli altri domini di Active Directory anche se uno di essi presenta problemi di connettività di rete.
* È stato risolto un problema che ignora l'aggiornamento del connettore di Azure AD durante l'aggiornamento automatico.
* È stato risolto un problema di Azure AD Connect che determina in modo non corretto se il server è un controller di dominio durante l'installazione e che a su volta non completa l'aggiornamento di DirSync.
* È stato risolto un problema dell'aggiornamento di DirSync locale che non crea un profilo di esecuzione per il connettore di Azure AD.
* È stato risolto un problema dell'interfaccia utente di Synchronization Service Manager che non risponde durante la configurazione del connettore LDAP generico.

Gestione di AD FS
* È stato risolto un problema della procedura guidata di Azure AD Connect che ha esito negativo se il nodo primario di AD FS è stato spostato in un altro server.

Desktop SSO
* È stato risolto un problema della procedura guidata di Azure AD Connect in cui la schermata di accesso non consente di abilitare la funzionalità Desktop SSO se è stata selezionata la sincronizzazione password come opzione di accesso durante la nuova installazione.

**Nuove funzionalità o miglioramenti:**

Servizio di sincronizzazione Azure AD Connect
* Il servizio di sincronizzazione Azure AD supporta ora l'uso di account del servizio virtuale, account del servizio gestito e account del servizio gestito del gruppo come account del servizio stesso. Si applica solo alla nuova installazione di Azure AD Connect. Durante l'installazione di Azure AD Connect:
    * Per impostazione predefinita, la procedura guidata di Azure AD Connect crea un account del servizio virtuale che usa come account del servizio.
    * Se l'installazione viene eseguita in un controller di dominio, Azure AD Connect ripete il comportamento precedente durante il quale crea una account utente di dominio e lo usa come account del servizio.
    * È possibile sostituire il comportamento predefinito specificando una delle opzioni seguenti:
      * Account del servizio gestito del gruppo
      * Account del servizio gestito
      * Account utente di dominio
      * Account utente locale
* In passato, se si eseguiva l'aggiornamento a una nuova build di Azure AD Connect contenente modifiche alle regole di aggiornamento o di sincronizzazione dei connettori, Azure AD Connect attivava un ciclo completo di sincronizzazione. Ora Azure AD Connect attiva in modo selettivo il passaggio di importazione completa solo per i connettori con modifiche alle regole di aggiornamento e il passaggio di sincronizzazione completa solo per i connettori con modifiche alle regole di sincronizzazione.
* In passato la soglia di eliminazione delle esportazioni veniva applicava solo alle esportazioni attivate tramite l'utilità di pianificazione della sincronizzazione. Ora la funzionalità è estesa e include le esportazioni che l'utente ha attivato manualmente tramite Synchronization Service Manager.
* Il tenant di Azure AD contiene una configurazione del servizio che indica se la funzionalità Sincronizzazione password è abilitata o meno per il tenant. In passato poteva capitare facilmente che Azure AD Connect eseguisse la configurazione del servizio in modo non corretto se era presente un server attivo e di gestione temporanea. Ora Azure AD Connect tenta di mantenere la configurazione del servizio coerente solo con il server di Azure AD Connect.
* Ora la procedura guidata di Azure AD Connect rileva e restituisce un avviso se in Active Directory locale non è abilitato il Cestino di AD.
* In passato, l'esportazione in Azure AD si interrompeva e non veniva completata se le dimensioni totali degli oggetti nel batch superavano una certa soglia. Ora, se si verifica tale problema, il servizio di sincronizzazione tenta di inviare nuovamente gli oggetti in batch più piccoli e separati.
* L'applicazione Synchronization Service Key Management (Gestione delle chiavi del servizio di sincronizzazione) è stata rimossa dal menu Start di Windows. La gestione della chiave di crittografia continua a essere supportata tramite l'interfaccia della riga di comando usando miiskmu.exe. Per informazioni sulla gestione della chiave di crittografia, fare riferimento all'articolo [Abandoning the Azure AD Connect Sync encryption key](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key) (Abbandono della chiave di crittografia del servizio di sincronizzazione Azure AD Connect).
* In passato, se si modificava la password dell'account del servizio di sincronizzazione Azure AD Connect, il servizio di sincronizzazione non veniva avviato correttamente finché non si abbandonava la chiave di crittografia e non si reinizializzava la password dell'account del servizio. Ora, questo processo non è più necessario.

Desktop SSO

* Durante la procedura guidata di Azure AD Connect non è più necessario che la porta 9090 sia aperta in rete quando si configura l'autenticazione pass-through e Desktop SSO. È necessaria solo la porta 443.

## <a name="114430"></a>1.1.443.0
Resa disponibile: Marzo 2017

**Problemi risolti:**

Servizio di sincronizzazione Azure AD Connect
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect se il nome visualizzato di Azure AD Connector non conteneva il dominio onmicrosoft.com iniziale assegnato al tenant di Azure AD.
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect durante la connessione al database SQL quando la password dell'account del servizio di sincronizzazione conteneva caratteri speciali come apostrofi, due punti e spazi.
* Risolto un problema che causava l'errore "The image has an anchor that is different than the image" (L'immagine contiene un ancoraggio diverso dall'immagine) in un server Azure AD Connect in modalità di gestione temporanea, dopo aver escluso temporaneamente un oggetto AD locale dalla sincronizzazione e averlo nuovamente incluso.
* Risolto un problema che causava l'errore "The object located by DN is a phantom" (L'oggetto localizzato da DN è un fantasma) in un server Azure AD Connect in modalità di gestione temporanea, dopo aver escluso temporaneamente un oggetto AD locale dalla sincronizzazione e averlo nuovamente incluso.

Gestione di AD FS
* Risolto un problema per cui la procedura guidata di Azure AD Connect non aggiornava la configurazione di AD FS e non impostava le attestazioni corrette nel trust della relying party dopo aver configurato un ID di accesso alternativo.
* Risolto un problema per cui la procedura guidata di Azure AD Connect non era in grado di gestire correttamente i server AD FS con account di servizio configurati usando il formato userPrincipalName invece del formato sAMAccountName.

Autenticazione pass-through
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect se si selezionava Autenticazione pass-through ma la registrazione del relativo connettore aveva esito negativo.
* Risolto un problema che causava il bypass dei controlli di convalida da parte di Azure AD Connect sul metodo di accesso selezionato con la funzionalità Desktop SSO attivata.

Reimpostazione delle password
* Correzione di un problema che potrebbe causare il mancato tentativo di riconnessione da parte del server Azure AAD Connect, se la connessione è stata terminata da un firewall o un proxy.

**Nuove funzionalità o miglioramenti:**

Servizio di sincronizzazione Azure AD Connect
* Il cmdlet Get-ADSyncScheduler restituisce ora una nuova proprietà booleana denominata SyncCycleInProgress. Se il valore restituito è true, significa che è in corso un ciclo di sincronizzazione pianificato.
* La cartella di destinazione per archiviare i log di installazione e configurazione di Azure AD Connect è stata modificata da %localappdata%\AADConnect a %programdata%\AADConnect per migliorare l'accessibilità.

Gestione di AD FS
* Aggiunto il supporto per l'aggiornamento del certificato TLS/SSL Farm AD FS.
* Aggiunto il supporto per la gestione di AD FS 2016.
* È ora possibile specificare un gMSA (account del servizio gestito di gruppo) durante l'installazione di AD FS.
* È ora possibile configurare SHA-256 come algoritmo di hash della firma per il trust della relying party di Azure AD.

Reimpostazione delle password
* Miglioramenti per consentire al prodotto di funzionare in ambienti con regole più severe del firewall.
* Migliore affidabilità della connessione al bus di servizio.

## <a name="113800"></a>1.1.380.0
Resa disponibile: Dicembre 2016

**Problema risolto:**

* Risolto il problema secondo cui in questa build manca la regola di attestazione issuerid per Active Directory Federation Services (AD FS).

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Resa disponibile: Dicembre 2016

**Problema noto:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure Active Directory (Azure AD). Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](how-to-connect-install-multiple-domains.md).

**Problema risolto:**

* L'installazione o l'aggiornamento di Azure AD Connect ha esito negativo se la porta 9090 non è aperta per la connessione in uscita.

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Resa disponibile: Dicembre 2016

**Problemi noti:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure AD. Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](how-to-connect-install-multiple-domains.md).
* La porta 9090 deve essere aperta in uscita per completare l'installazione.

**Nuove funzionalità:**

* Autenticazione pass-through (anteprima).

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Resa disponibile: Novembre 2016

**Problema noto:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure AD. Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](how-to-connect-install-multiple-domains.md).

**Problemi risolti:**

* In alcuni casi, l'installazione di Azure AD Connect, ha esito negativo perché non è possibile creare un account di servizio locale la cui password soddisfi il livello di complessità specificato dai criteri di password aziendale.
* È stato risolto un problema in cui le regole di unione non vengono rivalutate quando un oggetto nello spazio connettore viene contemporaneamente esce dall'ambito di una regola di unione ed nell'ambito di un'altra. Questa situazione può verificarsi se sono presenti due o più regole di unione, le cui condizioni di unione si escludono a vicenda.
* È stato risolto un problema in cui le regole di sincronizzazione in ingresso (di Azure AD), che non contengono regole di unione, non vengono elaborate se dispongono di valori di precedenza inferiori rispetto a quelle contenenti le regole di unione.

**Miglioramenti:**

* Aggiunta del supporto per l'installazione di Azure AD Connect in Windows Server 2016 Standard o versione successiva.
* Aggiunta del supporto per l'uso di SQL Server 2016 come database remoto per Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Resa disponibile: Agosto 2016

**Problemi risolti:**

* Le modifiche apportate all'intervallo di sincronizzazione non vengono applicate fino al termine del ciclo di sincronizzazione successivo.
* La procedura guidata di Azure AD Connect non accetta un account di Azure AD il cui nome utente inizi con un carattere di sottolineatura (\_).
* La procedura guidata di Azure AD Connect non riesce ad autenticare l'account Azure AD specificato se la password dell'account contiene troppi caratteri speciali. Viene visualizzato il messaggio di errore "La convalida delle credenziali non è riuscita. Si è verificato un errore imprevisto".
* La disinstallazione del server di gestione temporanea disabilita la sincronizzazione delle password nel tenant Azure AD e rende impossibile la sincronizzazione delle password con il server attivo.
* In casi rari, la sincronizzazione delle password ha esito negativo quando non sono archiviati hash password per l'utente.
* Quando il server Azure AD Connect è abilitato per la modalità di gestione temporanea, il writeback delle password non viene temporaneamente disabilitato.
* La procedura guidata di Azure AD Connect non visualizza l'effettiva configurazione della sincronizzazione delle password e del writeback delle password quando il server è in modalità di gestione temporanea. Indica sempre le due funzionalità come disabilitate.
* Le modifiche apportate alla configurazione della sincronizzazione delle password e del writeback delle password non vengono salvate in modo permanente dalla procedura guidata di Azure AD Connect quando il server è in modalità di gestione temporanea.

**Miglioramenti:**

* Aggiornamento del cmdlet Start-ADSyncSyncCycle per indicare se può avviare o meno un nuovo ciclo di sincronizzazione.
* Aggiunta del cmdlet Stop-ADSyncSyncCycle per terminare il ciclo di sincronizzazione e l'operazione in corso.
* Aggiornamento del cmdlet Stop-ADSyncScheduler per terminare il ciclo di sincronizzazione e l'operazione in corso.
* Quando si configura [Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md) nella procedura guidata di Azure AD Connect, è ora possibile selezionare l'attributo Azure AD di tipo "Teletex string".

## <a name="111890"></a>1.1.189.0
Resa disponibile: Giugno 2016

**Problemi risolti e miglioramenti:**

* Ora Azure AD Connect può essere installato su un server conforme a FIPS.
  * Per la sincronizzazione della password, vedere [Sincronizzazione dell'hash delle password e FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Risolto un problema in cui un nome NetBIOS non poteva essere risolto nel FQDN in Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Resa disponibile: Maggio 2016

**Nuove funzionalità:**

* Visualizza avvisi e indicazioni riguardo alla verifica dei domini se la verifica non è stata effettuata prima di eseguire Azure AD Connect.
* Aggiunto il supporto per [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Aggiunto il supporto per la versione più recente dell'infrastruttura [cloud di Microsoft Azure per enti pubblici](reference-connect-instances.md#microsoft-azure-government) con nuovi requisiti per gli URL.

**Problemi risolti e miglioramenti:**

* Aggiunti i filtri all'editor delle regole di sincronizzazione per semplificare il reperimento delle regole.
* Miglioramento delle prestazioni quando si elimina uno spazio connettore.
* Risolto un problema che si verificava quando lo stesso oggetto veniva sia eliminato che aggiunto nella stessa esecuzione (elimina/aggiungi).
* Una regola di sincronizzazione disabilitata ora non riattiva più gli oggetti e gli attributi inclusi all'aggiornamento del sistema o dello schema della directory.

## <a name="111300"></a>1.1.130.0
Resa disponibile: aprile 2016

**Nuove funzionalità:**

* Aggiunto il supporto per gli attributi multivalore alle [estensioni della directory](how-to-connect-sync-feature-directory-extensions.md).
* Aggiunto il supporto per altre varianti di configurazione dell' [aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) da considerare idonee per l'aggiornamento.
* Aggiunti alcuni cmdlet per l' [utilità di pianificazione personalizzata](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Resa disponibile: marzo 2016

**Problemi risolti:**

* È stato verificato che non sia possibile usare l'installazione rapida in Windows Server 2008 (pre-R2), poiché la sincronizzazione delle password non è supportata in questo sistema operativo.
* L'aggiornamento da DirSync con una configurazione personalizzata del filtro non funzionava come previsto.
* Durante l'aggiornamento a una versione più recente, senza modifiche alla configurazione, è consigliabile non pianificare una importazione/sincronizzazione completa.

## <a name="111100"></a>1.1.110.0
Resa disponibile: Febbraio 2016

**Problemi risolti:**

* L'aggiornamento da versioni precedenti non funziona se l'installazione non viene eseguita nella cartella predefinita C:\Programmi.
* Se si esegue l'installazione e si deseleziona **Avvia il processo di sincronizzazione** al termine dell'Installazione guidata, una nuova esecuzione dell'Installazione guidata non abiliterà l'utilità di pianificazione.
* L'utilità di pianificazione non funzionerà come previsto nei server in cui il formato di data/ora non è US-en. `Get-ADSyncScheduler` non potrà restituire gli orari corretti.
* Se è stata installata una versione precedente di Azure AD Connect con AD FS come opzione di accesso e aggiornamento, non è possibile eseguire nuovamente l'installazione guidata.

## <a name="111050"></a>1.1.105.0
Resa disponibile: Febbraio 2016

**Nuove funzionalità:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) per i clienti con impostazioni rapide.
* Supporto per l'amministratore globale mediante Azure Multi-Factor Authentication e Privileged Identity Management nell'installazione guidata.
  * Se si usa Multi-Factor Authentication, è necessario impostare il proxy in modo che consenta il traffico per https://secure.aadcdn.microsoftonline-p.com.
  * Per il corretto funzionamento di Multi-Factor Authentication, è necessario aggiungere https://secure.aadcdn.microsoftonline-p.com all'elenco di siti attendibili.
* Possibilità di modificare il metodo di accesso dell'utente dopo l'installazione iniziale.
* Possibilità di usare i [filtri basati su dominio e unità organizzativa](how-to-connect-install-custom.md#domain-and-ou-filtering) nell'Installazione guidata. Ciò consente anche la connessione a foreste in cui non sono disponibili tutti i domini.
* L'[Utilità di pianificazione](how-to-connect-sync-feature-scheduler.md) è incorporata nel motore di sincronizzazione.

**Funzionalità passate dal livello di anteprima al livello di disponibilità generale:**

* [Writeback dei dispositivi](how-to-connect-device-writeback.md).
* [Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md).

**Nuove funzionalità di anteprima:**

* Il nuovo intervallo predefinito per i cicli di sincronizzazione è pari a 30 minuti. In tutte le versioni precedenti è pari a tre ore. Aggiunge il supporto per la modifica del comportamento dell' [utilità di pianificazione](how-to-connect-sync-feature-scheduler.md) .

**Problemi risolti:**

* La pagina di verifica dei domini DNS non riconosceva sempre i domini.
* Richiesta di credenziali di amministratore del dominio durante la configurazione di AD FS.
* Gli account AD locali non vengono riconosciuti dall'installazione guidata se si trovano in un dominio con un albero DNS diverso da quello del dominio radice.

## <a name="1091310"></a>1.0.9131.0
Resa disponibile: dicembre 2015

**Problemi risolti:**

* La sincronizzazione delle password potrebbe non funzionare quando si modificano le password in Active Directory Domain Services (AD DS), ma funziona quando si imposta la password.
* Quando si dispone di un server proxy, l'autenticazione ad Azure AD potrebbe non riuscire durante l'installazione o se viene annullato un aggiornamento nella pagina di configurazione.
* L'aggiornamento da una versione precedente di Azure AD Connect con un'istanza completa di SQL Server avrà esito negativo se non si è SA (amministratore di sistema) in SQL Server.
* L'aggiornamento da una versione precedente di Azure AD Connect con una versione remota di SQL Server mostrerà il messaggio di errore "Impossibile accedere al database SQL di ADSync".

## <a name="1091250"></a>1.0.9125.0
Resa disponibile: Novembre 2015

**Nuove funzionalità:**

* È possibile riconfigurare AD FS a trust AD Azure.
* È possibile aggiornare lo schema di Active Directory e rigenerare le regole di sincronizzazione.
* È possibile disattivare una regola di sincronizzazione.
* È possibile definire "AuthoritativeNull" come un nuovo valore letterale in una regola di sincronizzazione.

**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md).
* Supporto per la sincronizzazione della password dei [Servizi di dominio di Azure AD](../user-help/active-directory-passwords-update-your-own-password.md)

**Nuovo scenario supportato:**

* Supporta più organizzazioni di Exchange locali Per altre informazioni, vedere [Distribuzioni ibride con più foreste di Active Directory](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Problemi risolti:**

* Problemi sulla sincronizzazione delle password:
  * Un oggetto spostato dal all’esterno all’interno dell’ambito non avrà la password sincronizzata. Questo include unità Organizzativa e filtro attributi.
  * Selezione di una nuova unità Organizzativa da includere nella sincronizzazione non richiede una sincronizzazione completa della password.
  * Quando un utente disabilitato è abilitato la password non viene sincronizzata.
  * La coda di tentativi di password è infinita e il limite precedente di 5.000 oggetti da ritirare è stato rimosso.
* Impossibile connettersi ad Active Directory con livello di funzionalità foresta Windows Server 2016.
* Impossibile modificare il gruppo usato per il filtro di gruppo dopo l'installazione iniziale.
* Non si creerà un nuovo profilo utente nel server di Azure AD Connect per ogni utente che effettua una modifica della password con attivato il writeback delle password.
* Non è possibile usare valori Intero lungo negli ambiti di regole di sincronizzazione.
* La casella di controllo "writeback dispositivi" rimane disabilitata se sono presenti controller di dominio non raggiungibili.

## <a name="1086670"></a>1.0.8667.0
Resa disponibile: Agosto 2015

**Nuove funzionalità:**

* L'installazione guidata di Azure AD Connect è ora localizzata in tutte le lingue in cui è disponibile Windows Server.
* È stato aggiunto il supporto per lo sblocco dell'account quando si usa la gestione delle password di Azure AD.

**Problemi risolti:**

* Si verifica un arresto anomalo dell'installazione guidata di Azure AD Connect se l'installazione viene continuata da un utente diverso da quello che l'ha avviata.
* Se una disinstallazione precedente di Azure AD Connect non riesce a disinstallare completamente il servizio di sincronizzazione Azure AD Connect, non sarà possibile effettuare la reinstallazione.
* Non è possibile installare Azure AD Connect usando l'installazione rapida se l'utente non si trova nel dominio radice della foresta o se viene usata una versione di Active Directory in una lingua diversa dall'inglese.
* Se il nome di dominio completo dell'account utente di Active Directory non può essere risolto, verrà visualizzato un messaggio di errore fuorviante analogo a "Commit dello schema non riuscito".
* Se l'account usato in Active Directory Connector viene modificato all'esterno della procedura guidata, le esecuzioni successive della procedura guidata avranno esito negativo.
* A volte non è possibile installare Azure AD Connect in un controller di dominio.
* Non è possibile abilitare e disabilitare la "Modalità di gestione temporanea" se sono stati aggiunti attributi di estensione.
* Il writeback delle password ha esito negativo in alcune configurazioni a causa di una password non valida in Active Directory Connector.
* Non è possibile aggiornare DirSync se si usa un nome distinto (DN) nel filtro di attributi.
* Utilizzo eccessivo della CPU quando si utilizza la reimpostazione della password.

**Funzionalità di anteprima rimosse:**

* La funzionalità di anteprima [Utente writeback](how-to-connect-preview.md#user-writeback) è stata temporaneamente rimossa in base ai suggerimenti espressi dai clienti dell’anteprima. Verrà aggiunto di nuovo in un secondo momento, una volta valutato il feedback fornito.

## <a name="1086410"></a>1.0.8641.0
Resa disponibile: Giugno 2015

**Rilascio iniziale di Azure AD Connect.**

Il nome è stato modificato da Azure AD Sync ad Azure AD Connect.

**Nuove funzionalità:**

* Installazione mediante le [impostazioni rapide](how-to-connect-install-express.md)
* È possibile [configurare la farm AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* È possibile [aggiornare da DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedire eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Introdotta la [modalità di gestione temporanea](how-to-connect-sync-staging-server.md)

**Nuove funzionalità di anteprima:**

* [Writeback degli utenti](how-to-connect-preview.md#user-writeback)
* [Writeback dei dispositivi](how-to-connect-device-writeback.md)
* [Estensioni della directory](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Resa disponibile: Maggio 2015

**Nuovo requisito:**

* Azure AD Sync richiede ora .NET Framework versione 4.5.1 per l'installazione.

**Problemi risolti:**

* Il writeback delle password da Azure AD ha esito negativo con un errore di connettività del bus di servizio.

## <a name="104910413"></a>1.0.491.0413
Resa disponibile: Aprile 2015

**Problemi risolti e miglioramenti:**

* Active Directory Connector non elabora correttamente le eliminazioni se il Cestino è abilitato e sono presenti più domini nella foresta.
* Le prestazioni delle operazioni di importazione sono state migliorate per Azure Active Directory Connector.
* Quando un gruppo supera il limite per le appartenenze (per impostazione predefinita, il limite è impostato su 50.000 oggetti), viene eliminato in Azure Active Directory. Con il nuovo comportamento, il gruppo non viene eliminato, viene generato un errore e le nuove modifiche alle appartenenze non vengono esportate.
* Non è possibile effettuare il provisioning di un nuovo oggetto se nello spazio del connettore è già presente un'eliminazione a fasi con lo stesso nome di dominio.
* Alcuni oggetti sono contrassegnati per la sincronizzazione durante una sincronizzazione Delta, anche se non sono previste modifiche a fasi sull'oggetto.
* La forzatura della sincronizzazione delle password rimuove anche l'elenco dei controller di dominio preferiti.
* Si sono verificati problemi di CSExportAnalyzer con alcuni stati degli oggetti.

**Nuove funzionalità:**

* Un join può ora connettersi al tipo di oggetto "ANY" nel metaverse.

## <a name="104850222"></a>1.0.485.0222
Resa disponibile: Febbraio 2015

**Miglioramenti:**

* Prestazioni migliorate per l'importazione.

**Problemi risolti:**

* La sincronizzazione delle password rispetta l'attributo cloudFiltered usato dal filtro di attributi. Gli oggetti filtrati non sono più inclusi nell'ambito per la sincronizzazione delle password.
* Nelle rare situazioni in cui la topologia include un numero elevato di controller di dominio, la sincronizzazione delle password non funziona.
* Errore di tipo "server arrestato" durante l'importazione da Azure AD Connector dopo l'abilitazione della gestione dei dispositivi in Azure AD/Intune.
* L'aggiunta di entità di protezione esterne da più domini nella stessa foresta provoca un errore di join ambiguo.

## <a name="104751202"></a>1.0.475.1202
Resa disponibile: Dicembre 2014

**Nuove funzionalità:**

* È ora supportata l'esecuzione della sincronizzazione delle password con filtri basati sugli attributi. Per informazioni dettagliate, vedere [Sincronizzazione delle password con i filtri](how-to-connect-sync-configure-filtering.md).
* L'attributo ms-DS-ExternalDirectoryObjectID viene scritto di nuovo in Active Directory. Questa funzionalità aggiunge il supporto per applicazioni di Office 365. Usa OAuth2 per accedere alle cassette postali online e locali in una distribuzione ibrida di Exchange.

**Problemi di aggiornamento risolti:**

* Nel server è disponibile una versione più recente dell'Assistente per l'accesso.
* Per l'installazione di Azure AD Sync è stato usato un percorso di installazione personalizzato.
* Un criterio di join personalizzato non valido blocca l'aggiornamento.

**Altre correzioni:**

* I problemi dei modelli per Office Pro Plus sono stati risolti.
* I problemi di installazione provocati dai nomi utente che iniziano con un trattino sono stati risolti.
* Il problema relativo alla perdita dell'impostazione sourceAnchor durante la seconda esecuzione dell'Installazione guidata è stato risolto.
* Il problema relativo alla traccia ETW per la sincronizzazione delle password è stato risolto.

## <a name="104701023"></a>1.0.470.1023
Resa disponibile: Ottobre 2014

**Nuove funzionalità:**

* Sincronizzazione delle password da più istanze di Active Directory locali ad Azure AD.
* Interfaccia utente di installazione localizzata per tutte le lingue in cui è disponibile Windows Server.

**Aggiornamento da AADSync 1.0 GA**

Se è già stato installato Azure AD Sync, è necessario eseguire un'altra operazione nel caso in cui siano state modificate le regole di sincronizzazione predefinite. In seguito all'aggiornamento alla versione 1.0.470.1023, le regole di sincronizzazione modificate vengono duplicate. Per ogni regola di sincronizzazione modificata eseguire le operazioni seguenti:

1. Trovare la regola di sincronizzazione modificata e prendere nota delle modifiche.
1. Eliminare la regola di sincronizzazione.
1. Trovare la nuova regola di sincronizzazione creata da Azure AD Sync e riapplicare le modifiche.

**Autorizzazioni per l'account Active Directory**

Per poter leggere gli hash delle password da Active Directory, è necessario concedere autorizzazioni aggiuntive all'account di Active Directory. Le autorizzazioni da concedere sono "Replica modifiche directory" e "Replica di tutte le modifiche directory". Per poter leggere gli hash delle password, sono necessarie entrambe le autorizzazioni.

## <a name="104190911"></a>1.0.419.0911
Resa disponibile: Settembre 2014

**Rilascio iniziale di Azure AD Sync.**

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
