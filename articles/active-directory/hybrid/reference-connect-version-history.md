---
title: 'Azure AD Connect: Cronologia del rilascio delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni di Azure AD Connect e Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576364"
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
Non tutte le versioni di Azure AD Connect saranno disponibili per l'aggiornamento automatico. Lo stato della versione indicherà se una versione sarà disponibile per l'aggiornamento automatico o solo per il download. Se l'aggiornamento automatico è stato abilitato nel server di Azure AD Connect, tale server eseguirà automaticamente l'aggiornamento per la versione più recente di Azure AD Connect, rilasciata per l'aggiornamento automatico. Si noti che non tutte le configurazioni di Azure AD Connect sono idonee per l'aggiornamento automatico. 

Per chiarire l'uso dell'aggiornamento automatico, è necessario eseguire il push di tutti gli aggiornamenti importanti e le correzioni critiche. Questa non è necessariamente la versione più recente perché non tutte le versioni richiedono o includono una correzione a un problema di sicurezza critico (solo un esempio di molti). Un problema simile a questo verrebbe risolto con una nuova versione fornita tramite l'aggiornamento automatico. Se non si verificano problemi di questo tipo, non è possibile eseguire il push degli aggiornamenti usando l'aggiornamento automatico e, in generale, se si usa la versione più recente dell'aggiornamento automatico, è consigliabile eseguire il push.
Tuttavia, se si desidera che tutte le funzionalità e gli aggiornamenti più recenti, il modo migliore per verificare se sono presenti è controllare questa pagina e installarli nel modo più adatto. 

Seguire questo collegamento per altre informazioni sull'[aggiornamento automatico](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partire dal 1° aprile 2024 verranno ritirate le versioni di Azure AD Connect rilasciate prima del 1° maggio 2018 - versione 1.1.751.0 e precedenti. 
>
> È necessario assicurarsi di eseguire una versione recente di Azure AD Connect per ricevere un'esperienza di supporto ottimale. 
>
>Se si esegue una versione ritirata di Azure AD Connect è possibile che non siano disponibili le correzioni di sicurezza più recenti, i miglioramenti delle prestazioni, gli strumenti di risoluzione dei problemi e di diagnostica e i miglioramenti dei servizi e, se è necessario, il supporto potrebbe non essere in grado di fornire il livello di servizio necessario all'organizzazione.
>

>
>Per altre informazioni su come aggiornare Azure AD Connect alla versione più recente, vedere [questo articolo](./how-to-upgrade-previous-version.md).
>
>Per informazioni sulla cronologia delle versioni ritirate, Azure AD Connect archivio della cronologia [delle versioni](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> L Azure AD Connect'API dell'endpoint di sincronizzazione V2 è ora disponibile in questi ambienti di Azure:
> - Azure Commercial
> - Azure Cina cloud
> - Cloud di Azure us government Non sarà disponibile nel cloud tedesco di Azure

### <a name="release-status"></a>Stato della versione
31/03/2021: rilasciato solo per il download, non disponibile per l'aggiornamento automatico

### <a name="bug-fixes"></a>Correzioni di bug
- Questa versione corregge un bug nella versione 1.6.2.4 in cui, dopo l'aggiornamento a tale versione, la funzionalità Azure AD Connect Health non è stata registrata correttamente e non funzionava. Ai clienti che hanno distribuito la build 1.6.2.4 viene richiesto di aggiornare il server Azure AD Connect con questa build, che registrerà correttamente la funzionalità Integrità. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> Aggiornamento al 30 marzo 2021: è stato rilevato un problema in questa build. Dopo l'installazione di questa build, i servizi di integrità non vengono registrati. È consigliabile non installare questa build. A breve verrà rilasciato un hotfix.
> Se questa build è già stata installata, è possibile registrare manualmente i servizi di integrità usando il cmdlet , come illustrato in [questo articolo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Questa versione verrà resa disponibile solo per il download.
> - L'aggiornamento a questa versione richiederà una sincronizzazione completa a causa di modifiche alle regole di sincronizzazione.
> - In questa versione il server AADConnect viene predefinito per il nuovo punto finale V2. Si noti che questo punto finale non è supportato nel cloud nazionale tedesco e [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) se è necessario distribuire questa versione in questo ambiente è necessario seguire queste istruzioni per tornare all'end point V1. In caso negativo, si verificano errori di sincronizzazione.

### <a name="release-status"></a>Stato della versione
19/03/2021: rilasciato per il download, non disponibile per l'aggiornamento automatico

### <a name="functional-changes"></a>Modifiche funzionali

 - Aggiornamento delle regole di sincronizzazione predefinite per limitare l'appartenenza ai gruppi di back-back a 50.000 membri.
   - Sono state aggiunte nuove regole di sincronizzazione predefinite per limitare il numero di appartenenze nel writeback dei gruppi (Out to AD - Group Writeback Member Limit) e la sincronizzazione dei gruppi nei gruppi di Azure Active Directory (Out to AAD - Group Writeup Member Limit).
   - Aggiunta dell'attributo member alla regola 'Out to AD - Group SOAInAAD - Exchange' per limitare a 50.000 i membri nei gruppi di back-back
 - Aggiornamento delle regole di sincronizzazione per supportare il writeback dei gruppi v2: se la regola "In from AAD - Group SOAInAAD" (In da AAD - Gruppo SOAInAAD) è clonata e AADConnect viene aggiornato.
     -La regola aggiornata verrà disabilitata per impostazione predefinita e quindi targetWritebackType sarà Null.
     - AADConnect scriverà il writeback di tutti i gruppi cloud (inclusi Azure Active Directory di sicurezza abilitati per il writeback) come gruppi di distribuzione.
   -Se la regola "Out to AD - Group SOAInAAD" è clonata e AADConnect viene aggiornato.
     - La regola aggiornata verrà disabilitata per impostazione predefinita. Tuttavia, verrà abilitata una nuova regola di sincronizzazione "Out to AD - Group SOAInAAD - Exchange" (Out to AD - Group SOAInAAD - Exchange) aggiunta.
     - A seconda della precedenza della regola di sincronizzazione personalizzata clonata, AADConnect invierà gli attributi di Posta ed Exchange.
     - Se la regola di sincronizzazione personalizzata clonata non scorre alcuni attributi di Posta ed Exchange, la nuova regola di sincronizzazione Exchange aggiungerà tali attributi.
 - Aggiunta del supporto per la sincronizzazione [selettiva dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Aggiunta del nuovo [cmdlet Single Object Sync](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync). Usare questo cmdlet per risolvere i problemi di configurazione Azure AD Connect sincronizzazione. 
 -  Azure AD Connect ora supporta il ruolo Amministratore identità ibrida per la configurazione del servizio.
 - Aggiornamento dell'agente AADConnectHealth alla versione 3.1.83.0
 - Nuova versione del modulo [PowerShell ADSyncTools,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)che include diversi cmdlet nuovi o migliorati. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Aggiornamento della registrazione degli errori per gli errori di acquisizione del token.
 - Sono stati aggiornati i collegamenti "Altre informazioni" nella pagina di configurazione per fornire altri dettagli sulle informazioni collegate.
 - Rimozione della colonna Explicit dalla pagina CS Search nell'interfaccia utente di sincronizzazione precedente
 - È stata aggiunta un'interfaccia utente aggiuntiva al flusso di writeback dei gruppi per richiedere all'utente le credenziali o per configurare le proprie autorizzazioni usando il modulo ADSyncConfig se le credenziali non sono già state fornite in un passaggio precedente.
 - Creare automaticamente l'account del servizio MsA per l'account del servizio ADSync in un controller di dominio. 
 -  Aggiunta della possibilità di impostare e ottenere Azure Active Directory funzionalità DirSync Group Writeback V2 nei cmdlet esistenti:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Aggiunta di 2 cmdlet per leggere la versione dell'API AWS
    - Get-ADSyncAADConnectorImportApiVersion: per ottenere la versione dell'API AWS di importazione
    - Get-ADSyncAADConnectorExportApiVersion: per ottenere la versione dell'API AWS di esportazione

 - Le modifiche apportate alle regole di sincronizzazione vengono ora rilevate per facilitare la risoluzione dei problemi delle modifiche nel servizio. Il cmdlet "Get-ADSyncRuleAudit" recupererà le modifiche rilevate.
 - Aggiornamento del cmdlet Add-ADSyncADDSConnectorAccount nel modulo [di PowerShell ADSyncConfig](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) per consentire a un utente del gruppo ADSyncAdmin di modificare l'account del connettore di Ad DS. 

### <a name="bug-fixes"></a>Correzioni di bug
 - Aggiornamento del colore di primo piano disabilitato per soddisfare i requisiti di luminosità su uno sfondo bianco. Aggiunta di altre condizioni per l'albero di navigazione per impostare il colore del testo in primo piano sul bianco quando viene selezionata una pagina disabilitata per soddisfare i requisiti di luminosità.
 - Aumentare la granularità Set-ADSyncPasswordHashSyncPermissions cmdlet : aggiornamento dello script delle autorizzazioni PHS (Set-ADSyncPasswordHashSyncPermissions) per includere un parametro facoltativo "ADobjectDN". 
 - Correzione di bug di accessibilità. L'utilità per la lettura dello schermo descrive ora l'elemento UX che contiene l'elenco di foreste come "**Elenco** foreste " anziché "**Elenco foreste**"
 - Aggiornamento dell'output dell'utilità per la lettura dello schermo per alcuni elementi della Azure AD Connect guidata. Aggiornamento del colore del passaggio del mouse del pulsante per soddisfare i requisiti di contrasto. Aggiornamento del Synchronization Service Manager del titolo per soddisfare i requisiti di contrasto.
 - Risolto un problema relativo all'installazione di AADConnect dalla configurazione esportata con attributi di estensione personalizzati: aggiunta di una condizione per ignorare il controllo degli attributi di estensione nello schema di destinazione durante l'applicazione della regola di sincronizzazione.
 - Le autorizzazioni appropriate vengono aggiunte durante l'installazione se è abilitata la funzionalità writeback di gruppo.
 - Correzione della precedenza della regola di sincronizzazione predefinita duplicata durante l'importazione
 - È stato risolto un problema che causava un errore di gestione temporanea durante l'importazione differenziale dell'API V2 per un oggetto in conflitto che è stato ripristinato tramite il portale integrità.
 - È stato risolto un problema nel motore di sincronizzazione che causava uno stato di collegamento incoerente per gli oggetti cs
 - Sono stati aggiunti contatori di importazione Get-ADSyncConnectorStatistics output.
 - Risolto un problema di de-selezione del dominio non raggiungibile (selezionato in precedenza) in alcuni casi angolari durante la procedura guidata pass2.
 - L'importazione e l'esportazione dei criteri modificati hanno esito negativo se la regola personalizzata ha la precedenza duplicata 
 - Correzione di un bug nella logica di selezione del dominio.
 - Correzione di un problema con la build 1.5.18.0 se si usa mS-DS-ConsistencyGuid come ancoraggio di origine e si clona la regola In da AD - Group Join.
 - Le nuove installazioni di AADConnect useranno la soglia di eliminazione dell'esportazione archiviata nel cloud, se disponibile e non ne viene passata una diversa.
 - È stato risolto un problema per cui AADConnect non legge le modifiche di AD displayName dei dispositivi aggiunti all'ambiente ibrido

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Stato della versione
29/07/2020: rilasciato per il download

### <a name="functional-changes"></a>Modifiche funzionali
Si tratta di una versione di correzione di bug. In questa versione non sono state apportate modifiche funzionali.

### <a name="fixed-issues"></a>Problemi risolti

- Risolto un problema a causa del quale l'amministratore non può abilitare "Seamless Single Sign On" se l'account computer AZUREADSSOACC è già presente in "Active Directory".
- Correzione di un problema che causava un errore di gestione temporanea durante l'importazione differenziale dell'API V2 per un oggetto in conflitto che è stato corretto tramite il portale per l'integrità.
- Correzione di un problema nella configurazione di importazione/esportazione per cui la regola personalizzata disabilitata è stata importata come abilitata.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Stato della versione
10/07/2020: rilasciato per il download

### <a name="functional-changes"></a>Modifiche funzionali
Questa versione include un'anteprima pubblica della funzionalità per esportare la configurazione di un server Azure AD Connect esistente in un . File JSON che può quindi essere usato quando si installa un nuovo server Azure AD Connect per creare una copia del server originale.

Una descrizione dettagliata di questa nuova funzionalità è disponibile in [questo articolo](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Problemi risolti
- Correzione di un bug per cui durante l'aggiornamento verrebbe visualizzato un falso avviso relativo alle dimensioni del database locale nelle build localizzate.
- Correzione di un bug per cui si verificava un errore falso negli eventi dell'app per lo scambio di nome account/nome di dominio.
- Correzione di un errore per cui Azure AD Connect'installazione non riesce in un controller di dominio, con l'errore "Membro non trovato".


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
- La sincronizzazione dell'hash delle password è stata aggiornata per Azure AD Domain Services per tenere in considerazione correttamente la spaziatura interna negli hash Kerberos.  Ciò fornirà un miglioramento delle prestazioni durante la sincronizzazione delle password da Azure AD a Azure AD Domain Services.
- È stato aggiunto il supporto per le sessioni affidabili tra l'agente di autenticazione e il bus di servizio.
- Questa versione applica TLS 1.2 per la comunicazione tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta una cache DNS per le connessioni WebSocket tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta la possibilità di indirizzare un agente specifico dal cloud per testare la connettività degli agenti.

### <a name="fixed-issues"></a>Problemi risolti
- La versione 1.4.18.0 presentava un bug in cui il cmdlet di PowerShell per DSSO usava le credenziali di accesso di Windows anziché le credenziali di amministratore fornite durante l'esecuzione di PS. Di conseguenza, non è stato possibile abilitare L'accesso DSSO in più foreste tramite l'Azure AD Connect utente. 
- È stata apportata una correzione per abilitare DSSO simultaneamente in tutte le foreste tramite l Azure AD Connect'interfaccia utente

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
- Aggiunta del supporto per i cloud nazionali nello script Azure AD Connect risoluzione dei problemi.
- I clienti devono essere informati che gli endpoint WMI deprecati per MIIS_Service ora sono stati rimossi. Tutte le operazioni WMI dovrebbero ora essere eseguite tramite i cmdlet di PS.
- Miglioramento della sicurezza reimpostando la delega vincolata nell'oggetto AZUREADSSOACC.
- Quando si aggiunge o modifica una regola di sincronizzazione, se nello schema del connettore sono presenti attributi usati nella regola, ma non aggiunti al connettore, gli attributi vengono aggiunti automaticamente al connettore. Lo stesso vale per il tipo di oggetto su cui la regola ha effetto. Se viene aggiunto un elemento al connettore, il connettore verrà contrassegnato per l'importazione completa al successivo ciclo di sincronizzazione.
- L'uso di un amministratore Enterprise o di dominio come account connettore non è più supportato nelle nuove distribuzioni di Azure AD Connect. Le Azure AD Connect correnti che usano un amministratore dell'organizzazione o di dominio come account del connettore non saranno interessate da questa versione.
- In Gestione sincronizzazione viene eseguita una sincronizzazione completa per la creazione/modifica/eliminazione di regole. Per qualsiasi modifica della regola, viene visualizzata una finestra popup che informa l'utente se verrà eseguita l'importazione completa o la sincronizzazione completa.
- Aggiunta della procedura di mitigazione degli errori di password alla pagina "Connettori > proprietà > connettività".
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
- Correzione di un bug per rendere più affidabile lo script di connettività di Active Directory.
- Correzione di un bug per rendere Azure AD Connect'installazione in un computer usando un servizio WCF Named Pipes esistente più affidabile.
- Diagnostica migliorata e risoluzione dei problemi relativi a criteri di gruppo che non consentono l'avvio del servizio ADSync al momento dell'installazione iniziale.
- Correzione di un bug per cui il nome visualizzato per un computer Windows non era scritto correttamente.
- Correzione di un bug in cui il tipo di sistema operativo per un computer Windows non era scritto correttamente.
- Correzione di un bug per cui i computer non Windows 10 venivano sincronizzati in modo imprevisto. In conseguenza a questa modifica, i computer non Windows 10 sincronizzati in precedenza verranno ora eliminati. Questa operazione non influisce sulle funzionalità perché la sincronizzazione dei computer Windows viene usata solo per l'aggiunta a un dominio Azure AD ibrido, che funziona solo per i dispositivi Windows 10.
- Sono stati aggiunti diversi nuovi cmdlet (interni) al modulo PowerShell ADSync.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Esiste un problema noto relativo all'aggiornamento di Azure AD Connect da una versione precedente alla versione 1.3.21.0 in cui il portale Microsoft 365 non riflette la versione aggiornata anche se Azure AD Connect è stato aggiornato correttamente.
>
> Per risolvere questo problema, è necessario importare il **modulo AdSync** e quindi eseguire il `Set-ADSyncDirSyncConfiguration` cmdlet di PowerShell nel server Azure AD Connect.  È possibile seguire la procedura seguente:
>
>1. Aprire PowerShell in modalità amministratore.
>2. Eseguire `Import-Module "ADSync"`.
>3. Eseguire `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stato della versione 

14/05/2019: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti 

- Correzione di una vulnerabilità di elevazione dei privilegi presente nella versione 1.3.20.0 di Microsoft Azure Active Directory Connect.  Questa vulnerabilità, in determinate condizioni, può consentire a un utente malintenzionato di eseguire due cmdlet di PowerShell nel contesto di un account con privilegi ed eseguire azioni con privilegi.  Questa patch di sicurezza risolve il problema disabilitando questi cmdlet. Per altre informazioni, vedere [Patch di sicurezza](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
