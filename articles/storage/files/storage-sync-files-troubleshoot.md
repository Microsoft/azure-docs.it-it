---
title: Risolvere i problemi di Sincronizzazione file di Azure | Microsoft Docs
description: Risolvere i problemi comuni in una distribuzione in Sincronizzazione file di Azure, che è possibile usare per trasformare Windows Server in una cache rapida della condivisione file di Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 1/13/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e2f0d62ae6882229cee3ee32e3b362f2b6593da7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199236"
---
# <a name="troubleshoot-azure-file-sync"></a>Risolvere i problemi di Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

L'obiettivo di questo articolo è aiutare l'utente a individuare e risolvere i problemi che si possono incontrare con la distribuzione di Sincronizzazione file di Azure. Viene inoltre spiegato come raccogliere i log importanti dal sistema per effettuare un'analisi più approfondita dei problemi. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. [Forum di archiviazione di Azure](/answers/products/azure?product=storage).
2. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida**, selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Si è verificato un problema di Sincronizzazione file di Azure nel server (sincronizzazione, suddivisione in livelli cloud e così via). È consigliabile rimuovere l'endpoint server e ricrearlo?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Installazione dell'agente e registrazione del server
<a id="agent-installation-failures"></a>**Risolvere gli errori di installazione dell'agente**  
Se l'installazione dell'agente Sincronizzazione file di Azure ha esito negativo, eseguire il comando seguente per abilitare la registrazione durante l'installazione dell'agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Esaminare il file installer.log per determinare la causa dell'errore di installazione.

<a id="agent-installation-on-DC"></a>**L'installazione dell'agente ha esito negativo nel controller di dominio di Active Directory**  
Se si tenta di installare l'agente di sincronizzazione in un controller di dominio di Active Directory in cui il proprietario di ruolo si trova in un Windows Server 2008 R2 o una versione precedente del sistema operativo, è possibile riscontrare il problema dell'esito negativo dell'installazione dell'agente.

Per risolverlo, trasferire il ruolo PDC in un altro controller di dominio su cui è in esecuzione Windows Server 2012 R2 o una versione più recente, quindi installare la sincronizzazione.

<a id="parameter-is-incorrect"></a>**L'accesso a un volume in Windows Server 2012 R2 ha esito negativo con errore: Parametro non corretto**  
Dopo aver creato un endpoint server in Windows Server 2012 R2, si verifica l'errore seguente durante l'accesso al volume:

unitàlettera:\ non accessibile.  
Parametro non corretto.

Per risolvere questo problema, installare [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) e riavviare il server. Se l'aggiornamento non viene installato perché è già installato un aggiornamento successivo, passare a Windows Update, installare gli ultimi aggiornamenti per Windows Server 2012 R2 e riavviare il server.

<a id="server-registration-missing-subscriptions"></a>**La registrazione del server non elenca tutte le sottoscrizioni di Azure**  
Quando si registra un server con ServerRegistration.exe, le sottoscrizioni risultano mancanti quando si fa clic sull'elenco a discesa delle sottoscrizioni di Azure.

Questo problema si verifica perché ServerRegistration.exe recupererà solo le sottoscrizioni dai primi 5 Azure AD tenant. 

Per aumentare il limite del tenant di registrazione server nel server, creare un valore DWORD denominato ServerRegistrationTenantLimit in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync con un valore maggiore di 5.

È anche possibile aggirare questo problema usando i comandi di PowerShell seguenti per registrare il server:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Registrazione server visualizza il messaggio seguente: "Prerequisiti mancanti"**  
Questo messaggio viene visualizzato se il modulo di PowerShell AZ o AzureRM non è installato in PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe non supporta PowerShell 6.x. Per registrare il server, è possibile usare il cmdlet Register-AzStorageSyncServer in PowerShell 6.x.

Per installare il modulo Az o AzureRM in PowerShell 5.1, eseguire i passaggi seguenti:

1. Digitare **powershell** da un prompt dei comandi con privilegi elevati e premere INVIO.
2. Installare il modulo Az o AzureRM più recente seguendo la documentazione:
    - [Modulo Az (richiede .NET 4.7.2)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [Modulo AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Eseguire il file ServerRegistration.exe e completare la registrazione guidata del server con un servizio di sincronizzazione archiviazione.

<a id="server-already-registered"></a>**Registrazione server visualizza il messaggio seguente: "Il server è già registrato con un altro insieme di credenziali"** 

![Schermata della finestra di dialogo Registrazione server con messaggio di errore che indica che il server è già registrato](media/storage-sync-files-troubleshoot/server-registration-1.png)

Questo messaggio viene visualizzato se il server è stato registrato in precedenza con un servizio di sincronizzazione archiviazione. Per annullare la registrazione del server con il servizio di sincronizzazione archiviazione corrente e registrarlo con un nuovo servizio di sincronizzazione archiviazione, seguire la procedura descritta in [Annullare la registrazione del server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se il server non è presente nell'elenco **Server registrati** del servizio di sincronizzazione archiviazione, nel server di cui si vuole annullare la registrazione eseguire i comandi PowerShell seguenti:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se il server fa parte di un cluster, è possibile usare il parametro facoltativo *Reset-StorageSyncServer -CleanClusterRegistration* per rimuovere anche la registrazione del cluster.

<a id="web-site-not-trusted"></a>**Perché quando si registra un server si ricevono numerose risposte indicanti che il sito Web non è attendibile. Perché?**  
Questo errore si verifica perché durante la registrazione del server sono abilitati i criteri **Sicurezza avanzata di Internet Explorer**. Per altre informazioni su come disabilitare correttamente i criteri **Sicurezza avanzata di Internet Explorer**, vedere [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Il server non è elencato in Server registrati nel portale di Azure**  
Se un server non è presente nell'elenco **Server registrati** per un servizio di sincronizzazione archiviazione, seguire questa procedura:
1. Accedere al server da registrare.
2. Aprire Esplora File e quindi passare alla directory di installazione dell'agente di sincronizzazione archiviazione (il percorso predefinito è C:\Program Files\Azure\StorageSyncAgent). 
3. Eseguire il file ServerRegistration.exe e completare la registrazione guidata del server con un servizio di sincronizzazione archiviazione.

## <a name="sync-group-management"></a>Gestione dei gruppi di sincronizzazione

### <a name="cloud-endpoint-creation-errors"></a>Errori di creazione dell'endpoint cloud

<a id="cloud-endpoint-using-share"></a>**La creazione dell'endpoint cloud ha esito negativo e viene restituito il messaggio di errore: "La condivisione file di Azure specificata è già usata da un endpoint cloud diverso"**  
Questo errore si verifica se la condivisione file di Azure è già in uso da parte di un altro endpoint cloud. 

Se viene visualizzato questo messaggio e la condivisione file di Azure non è attualmente in uso da alcun endpoint cloud, attenersi ai passaggi seguenti per cancellare i metadati di Sincronizzazione file di Azure nella condivisone file di Azure:

> [!Warning]  
> L'eliminazione dei metadati nella condivisione file di Azure attualmente in uso da un endpoint cloud impedisce il corretto funzionamento di Sincronizzazione file di Azure. 

1. Nel portale di Azure passare alla condivisione file di Azure.  
2. Fare clic con il pulsante destro del mouse sulla condivisione file di Azure e scegliere **Modifica metadati**.
3. Fare clic con il pulsante destro del mouse su **SyncService** e quindi fare clic su **Elimina**.

<a id="cloud-endpoint-authfailed"></a>**La creazione dell'endpoint cloud ha esito negativo e viene restituito il messaggio di errore: "AuthorizationFailed"**  
Questo errore si verifica se l'account utente non ha diritti sufficienti per creare un endpoint cloud. 

Per creare un endpoint cloud, l'account utente deve avere le autorizzazioni Microsoft seguenti:  
* Lettura: Ottenere la definizione del ruolo
* Scrittura: Crea o aggiorna la definizione del ruolo personalizzata
* Lettura: Ottenere l'assegnazione del ruolo
* Scrittura: Crea assegnazione ruolo

I seguenti ruoli predefiniti dispongono delle necessarie autorizzazioni Microsoft:  
* Proprietario
* Amministratore accessi utente

Per determinare se il ruolo dell'account utente in uso dispone delle autorizzazioni necessarie:  
1. Nel portale di Azure fare clic su **Gruppi di risorse**.
2. Selezionare il gruppo di risorse in cui si trova l'account di archiviazione e quindi selezionare **Controllo di accesso (IAM)** .
3. Selezionare la scheda **Assegnazioni di ruolo**.
4. Selezionare il **Ruolo** per l'account utente, ad esempio proprietario o collaboratore.
5. Nell'elenco **Provider di risorse** selezionare **Autorizzazione Microsoft**. 
    * In **Assegnazione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.
    * In **Definizione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Errori di creazione ed eliminazione dell'endpoint server

<a id="-2134375898"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2134375898 o 0x80c80226)**  
Questo errore si verifica se il percorso dell'endpoint server si trova sul volume di sistema ed è abilitata la suddivisione in livelli nel cloud. La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.

<a id="-2147024894"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2147024894 o 0x80070002)**  
Questo errore si verifica se il percorso dell'endpoint server specificato non è valido. Verificare che il percorso dell'endpoint server specificato sia un volume NTFS collegato in locale. Si noti che Sincronizzazione file di Azure non supporta le unità mappate come percorso dell'endpoint server.

<a id="-2134375640"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2134375640 o 0x80c80328)**  
Questo errore si verifica se il percorso dell'endpoint server specificato non è un volume NTFS. Verificare che il percorso dell'endpoint server specificato sia un volume NTFS collegato in locale. Si noti che Sincronizzazione file di Azure non supporta le unità mappate come percorso dell'endpoint server.

<a id="-2134347507"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2134347507 o 0x80c8710d)**  
Questo errore si verifica perché Sincronizzazione file di Azure non supporta gli endpoint server nei volumi che dispongono di una cartella System Volume Information compressa. Per risolvere il problema, decomprimere la cartella System Volume Information. Se la cartella System Volume Information è l'unica cartella compressa nel volume, seguire questa procedura:

1. Scaricare lo strumento [PsExec](/sysinternals/downloads/psexec).
2. Eseguire il comando seguente da un prompt dei comandi con privilegi elevati per avviare un prompt dei comandi in esecuzione con l'account di sistema: **PsExec.exe -i -s -d cmd**
3. Dal prompt dei comandi in esecuzione con l'account di sistema, digitare i comandi seguenti e premere INVIO:   
    **cd /d "lettera di unità:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2134376345 o 0x80C80067)**  
Questo errore si verifica se viene raggiunto il limite di endpoint server per ogni server. Sincronizzazione file di Azure supporta attualmente fino a 30 endpoint server per ogni server. Per altre informazioni, vedere [Obiettivi di scalabilità di Sincronizzazione file di Azure](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2134376427 o 0x80c80015)**  
Questo errore si verifica se un altro endpoint server sta già sincronizzando il percorso dell'endpoint server specificato. Sincronizzazione file di Azure non supporta endpoint server multipli che sincronizzano la stessa directory o lo stesso volume.

<a id="-2160590967"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (codice errore: -2160590967 o 0x80c80077)**  
Questo errore si verifica se il percorso dell'endpoint server contiene file a livelli orfani. Se un endpoint server è stato rimosso di recente, attendere il completamento della pulizia dei file a livelli orfani. Un evento con ID 6662 viene registrato nel registro eventi di telemetria dopo l'avvio della pulizia dei file a livelli orfani. Un evento con ID 6661 viene registrato al termine della pulizia dei file a livelli orfani ed è possibile ricreare un endpoint server usando il percorso. Se la creazione dell'endpoint server ha esito negativo dopo il completamento della pulizia dei file a livelli o se non è possibile trovare l'ID evento 6661 nel registro eventi di telemetria a causa del rollover del registro eventi, rimuovere i file a livelli orfani eseguendo i passaggi illustrati nella sezione [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).

<a id="-2134347757"></a>**L'eliminazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobExpired" (codice errore: -2134347757 o 0x80c87013)**  
Questo errore si verifica se il server è offline o non si dispone di connettività di rete. Se il server non è più disponibile, annullare la registrazione del server nel portale. In questo modo, si annullano gli endpoint server. Per eliminare gli endpoint server, seguire la procedura descritta in [Annullare la registrazione del server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Integrità endpoint server

<a id="server-endpoint-provisioningfailed"></a>**Impossibile aprire la pagina delle proprietà dell'endpoint server o aggiornare i criteri di suddivisione in livelli nel cloud**  
Questo problema può verificarsi se un'operazione di gestione nell'endpoint server ha esito negativo. Se la pagina delle proprietà dell'endpoint server non si apre nel portale di Azure, l'aggiornamento dell'endpoint server mediante i comandi di PowerShell dal server potrebbe risolvere il problema. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**L'endpoint server ha uno stato integrità "Nessuna attività" o "In sospeso" e lo stato del server nel pannello dei server registrati è "Risulta offline"**  

Questo problema può verificarsi se il processo di monitoraggio della sincronizzazione dell'archiviazione (AzureStorageSyncMonitor.exe) non è in esecuzione o se il server non è in grado di accedere al servizio Sincronizzazione file di Azure.

Sul server che viene visualizzato come "Risulta offline" nel portale, esaminare l'ID evento 9301 nel registro eventi di telemetria (che si trova in Applicazioni e servizi\Microsoft\FileSync\Agent in Visualizzatore eventi) per determinare il motivo per cui il server non è in grado di accedere al servizio Sincronizzazione file di Azure. 

- Se **GetNextJob completato con lo stato: 0** è registrato, il server può comunicare con il servizio Sincronizzazione file di Azure. 
    - Aprire Gestione attività sul server e verificare che il processo di monitoraggio della sincronizzazione dell'archiviazione (AzureStorageSyncMonitor.exe) sia in esecuzione. Se il processo non è in esecuzione, provare a riavviare il server. Se il riavvio del server non risolve il problema, aggiornare all'ultima[versione dell'agente](./storage-files-release-notes.md) della Sincronizzazione file di Azure. 

- Se **GetNextJob è stato completato con lo stato:-2134347756** viene registrato, il server non è in grado di comunicare con il servizio sincronizzazione file di Azure a causa di un firewall, un proxy o una configurazione dell'ordine del pacchetto di crittografia TLS. 
    - Se il server si trova dietro un firewall, verificare che la porta 443 in uscita sia consentita. Se il firewall limita il traffico a domini specifici, verificare che i domini elencati nella [documentazione](./storage-sync-files-firewall-and-proxy.md#firewall) del firewall siano accessibili.
    - Se il server si trova dietro un proxy, configurare le impostazioni del proxy a livello di computer o specifiche dell'app seguendo la procedura descritta nella [documentazione](./storage-sync-files-firewall-and-proxy.md#proxy) del proxy.
    - Usare il cmdlet Test-StorageSyncNetworkConnectivity per verificare la connettività di rete agli endpoint del servizio. Per altre informazioni, vedere [Testare la connettività di rete agli endpoint di servizio](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Per aggiungere pacchetti di crittografia nel server, usare i cmdlet di criteri di gruppo o TLS:
        - Per usare i criteri di gruppo, vedere [configurazione dell'ordine dei pacchetti di crittografia TLS con criteri di gruppo](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Per usare i cmdlet TLS, vedere [Configuring TLS cipher Suite order by using TLS PowerShell cmdlets](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets).
    
        Sincronizzazione file di Azure attualmente supporta i pacchetti di crittografia seguenti per il protocollo TLS 1,2:  
        - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256  
        - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256  
        - TLS_RSA_WITH_AES_256_GCM_SHA384  
        - TLS_RSA_WITH_AES_128_GCM_SHA256  
        - TLS_RSA_WITH_AES_256_CBC_SHA256  
        - TLS_RSA_WITH_AES_128_CBC_SHA256  

- Se **GetNextJob completato con lo stato:-2134347764** è registrato, il server non è in grado di comunicare con il servizio Sincronizzazione file di Azure a causa di un certificato eliminato o scaduto.  
    - Eseguire il comando di PowerShell seguente sul server per ripristinare il certificato usato per l'autenticazione:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Lo stato di integrità dell'endpoint server è "Nessuna attività" e lo stato del server nel pannello dei server registrati è "Online"**  

Se lo stato di integrità dell'endpoint server è impostato su "Nessuna attività" significa che, nelle ultime due ore, nell'endpoint server non sono state registrate attività di sincronizzazione.

Per verificare le attività di sincronizzazione attualmente in esecuzione su un server, vedere [Come monitorare lo stato di avanzamento di una sessione di sincronizzazione corrente?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Un endpoint server non può registrare l'attività di sincronizzazione per diverse ore a causa di un bug o di risorse di sistema insufficienti. Verificare che la [versione più recente dell'agente](./storage-files-release-notes.md) Sincronizzazione file di Azure sia installata. Se il problema persiste, aprire una richiesta di supporto.

> [!Note]  
> Se lo stato del server nel pannello dei server registrati è "Risulta offline", eseguire i passaggi documentati nella sezione [Lo stato di integrità dell'endpoint server è "nessuna attività" o "in sospeso" e lo stato del server nel pannello Server registrati è "offline"](#server-endpoint-noactivity).

## <a name="sync"></a>Sincronizzazione
<a id="afs-change-detection"></a>**Se è stato creato un file direttamente nella condivisione file di Azure su SMB o nel portale, quanto tempo richiede la sincronizzazione del file nei server nel gruppo di sincronizzazione?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**L'integrità dell'endpoint server è in sospeso per diverse ore**  
Questo errore è previsto se si crea un endpoint cloud e si usa una condivisione file di Azure contenente dati. Il processo di enumerazione di modifiche che esegue l'analisi per rilevare modifiche nella condivisione file di Azure deve essere completato prima che i file possano eseguire la sincronizzazione tra gli endpoint server e cloud. Il tempo necessario per il complemento del processo dipende dalle dimensioni dello spazio dei nomi nella condivisione file di Azure. L'integrità dell'endpoint server deve aggiornarsi dopo aver completato il processo di enumerazione di modifiche.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Come si monitorano le risorse di integrità?
# <a name="portal"></a>[Portale](#tab/portal1)
All'interno di ogni gruppo di sincronizzazione è possibile risalire ai relativi endpoint server singoli per visualizzare lo stato delle ultime sessioni di sincronizzazione completate. Una colonna di integrità verde e un valore per i file che non eseguono la sincronizzazione pari a 0 indicano che la sincronizzazione funziona come previsto. Se ciò non avviene, vedere di seguito un elenco di errori di sincronizzazione comuni e come gestire i file che non eseguono la sincronizzazione. 

![Schermata del portale di Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Passare ai log dei dati di telemetria del server che si trovano in Visualizzatore eventi in `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. L'evento 9102 corrisponde a una sessione di sincronizzazione completata; per lo stato più recente del servizio di sincronizzazione, cercare l'evento più recente con ID 9102. SyncDirection indica se la sessione rappresenta un caricamento o un download. Se il valore HResult è 0, la sessione di sincronizzazione è riuscita. Un valore HResult diverso da zero indica che si è verificato un errore durante la sincronizzazione; per un elenco di errori comuni, vedere di seguito. Se PerItemErrorCount è maggiore di 0, significa che alcuni file o cartelle non sono stati sincronizzati correttamente. È possibile ricevere un valore HResult pari a 0, ma al contempo un PerItemErrorCount maggiore di 0.

Di seguito, un esempio di caricamento corretto. Per ragioni di brevità, solo alcuni dei valori contenuti in ciascun evento 9102 sono elencati di seguito. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Al contrario, un'operazione di caricamento riuscita potrebbe essere simile alla seguente:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

In alcuni casi le sessioni di sincronizzazione hanno un esito completamente negativo o hanno un PerItemErrorCount diverso da zero, ma compiono ancora progressi, ovvero alcuni file eseguono correttamente la sincronizzazione. Si può notare nei campi Applied* (AppliedFileCount AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), che indicano quanti file della sessione vengono completati. Se vengono visualizzate più sessioni di sincronizzazione in una riga con esito negativo, ma con un numero crescente di Applied*, è necessario assegnare l'ora della sincronizzazione per tentare nuovamente l'operazione prima di aprire un ticket di supporto.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Come monitorare lo stato di avanzamento di una sessione di sincronizzazione corrente?
# <a name="portal"></a>[Portale](#tab/portal1)
All'interno del gruppo di sincronizzazione, accedere all'endpoint server in questione ed esaminare la sezione delle attività di sincronizzazione per visualizzare il numero di file caricati o scaricati nella sessione di sincronizzazione corrente. Si noti che questo stato ritarderà di circa 5 minuti e, se la sessione di sincronizzazione è sufficientemente ridotta da essere completata entro questo periodo, potrebbe non essere segnalata nel portale. 

# <a name="server"></a>[Server](#tab/server)
Cercare gli eventi 9302 più recenti nei dati di telemetria del log eventi sul server (nel Visualizzatore eventi, passare ad Registri applicazioni e servizi\Microsoft\FileSync\Agente\Telemetria). Questo evento indica lo stato della sessione di sincronizzazione corrente. TotalItemCount denota il numero di file che devono essere sincronizzati, AppliedItemCount il numero di file che sono stati sincronizzati finora e PerItemErrorCount il numero di file che hanno esito negativo per la sincronizzazione (vedere di seguito per la procedura risolvere questo problema).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Come si capisce se i server sono sincronizzati tra loro?
# <a name="portal"></a>[Portale](#tab/portal1)
Per ogni server in un gruppo di sincronizzazione specificato, assicurarsi che:
- I timestamp dell'ultimo tentativo di sincronizzazione per il caricamento e il download siano recenti.
- Lo stato sia verde per il caricamento e per il download.
- Nel campo di attività di sincronizzazione venga visualizzato un numero molto ridotto o non esistano file rimanenti per la sincronizzazione.
- Il campo di file che non eseguono la sincronizzazione sia 0 per il caricamento e il download.

# <a name="server"></a>[Server](#tab/server)
Esaminare le sessioni di sincronizzazione completate che sono contrassegnate da eventi 9102 nel registro eventi di telemetria per ogni server (nel Visualizzatore eventi, passare a `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. In qualsiasi server specificato, si desidera assicurarsi che il caricamento più recente e le sessioni di download siano completati correttamente. A questo scopo, verificare che il valore HResult e PerItemErrorCount sia 0 per il caricamento e per il download (il campo SyncDirection indica se una sessione specifica è una sessione di caricamento o di download). Si noti che se non è possibile visualizzare una sessione di sincronizzazione completata di recente, è probabile che una sessione di sincronizzazione sia attualmente in corso, presumibilmente se è stata appena aggiunta o modificata una grande quantità di dati.
2. Quando un server è completamente aggiornato con il cloud e non include modifiche per la sincronizzazione in entrambe le direzioni, è possibile visualizzare le sessioni di sincronizzazione vuote. Queste sono indicate da eventi di caricamento e download in cui tutti i camp Sync* (SyncFileCount SyncDirCount, SyncTombstoneCount e SyncSizeBytes) sono zero, ovvero non erano presenti file da sincronizzare. Si noti che queste sessioni di sincronizzazione vuote potrebbero non verificarsi sui server di varianza elevata poiché vi sono sempre file nuovi da sincronizzare. Se è assente l'attività di sincronizzazione, le sessioni dovranno essere eseguite ogni 30 minuti. 
3. Se tutti i server sono aggiornati con il cloud, vale a dire le loro sessioni di caricamento e di download recenti sono sessioni di sincronizzazione vuote, è possibile affermare con ragionevole certezza che il sistema nel suo complesso è sincronizzato. 
    
Si noti che se sono state apportate modifiche direttamente nella condivisione file di Azure, Sincronizzazione file di Azure non rileverà questa modifica fino all'esecuzione dell'enumerazione di modifiche, che avviene una volta ogni 24 ore. È possibile che un server venga indicato come aggiornato con il cloud quando in realtà non contiene modifiche recenti apportate direttamente nella condivisione file di Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Come capire se sono presenti determinati file o cartelle che non eseguono la sincronizzazione?
Se il conteggio di PerItemErrorCount nel server o dei file che non eseguono la sincronizzazione nel portale è maggiore a 0 per qualsiasi sessione di sincronizzazione specificato, significa che non è possibile sincronizzare alcuni elementi. I file e le cartelle possono presentare caratteristiche che ne impediscono la sincronizzazione. Queste caratteristiche possono essere persistenti e richiedono un'azione esplicita per ripristinare la sincronizzazione, ad esempio la rimozione di caratteri non supportati dal nome del file o della cartella. Possono anche avere caratteristiche temporanee per cui la sincronizzazione del file o della cartella verrà ripristinata automaticamente, ad esempio file con handle aperti riprenderanno automaticamente la sincronizzazione alla chiusura dell'handle di file. Quando il motore di Sincronizzazione file di Azure rileva un problema di questo tipo, viene generato un log degli errori che può essere analizzato per elencare gli elementi attualmente non sincronizzati correttamente.

Per visualizzare questi errori, eseguire lo script **FileSyncErrorsReport.ps1** di PowerShell (che si trova nella directory di installazione dell'agente Sincronizzazione file di Azure) per identificare i file che non sono stati sincronizzati a causa di handle aperti, caratteri non supportati o altri problemi. Il campo ItemPath indica il percorso del file in relazione alla cartella per la sincronizzazione della radice. Vedere l'elenco degli errori di sincronizzazione comuni di seguito per la procedura di correzione.

> [!Note]  
> Se lo script FileSyncErrorsReport.ps1 restituisce "Non sono stati trovati errori di file" o non elenca gli errori per elemento per il gruppo di sincronizzazione, la causa è:
>
>- Causa 1: L'ultima sessione di sincronizzazione completata non contiene errori per elemento. Il portale deve essere aggiornato a breve per visualizzare 0 file non sincronizzati. 
>    - Verificare l'[ID evento 9102](?tabs=server%252cazure-portal#broken-sync) nel registro eventi di telemetria per confermare che PerItemErrorCount sia 0. 
>
>- Causa 2: Il registro eventi ItemResults sul server è stato sottoposto a wrapping a causa di un numero eccessivo di errori per elemento e il registro eventi non contiene più errori per questo gruppo di sincronizzazione.
>    - Per evitare questo problema, aumentare le dimensioni del registro eventi ItemResults. Il registro eventi ItemResults si trova in "Registri applicazioni e servizi\Microsoft\FileSync\Agent" nel Visualizzatore eventi. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Risoluzione dei problemi per gli errori di sincronizzazione di file e directory
**Log ItemResults - errori di sincronizzazione per ogni elemento**  

| HRESULT | HRESULT (decimale) | Stringa di errore | Problema | Correzione |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Il file a livelli sul server non è accessibile. Questo problema si verifica se il file a livelli non è stato richiamato prima di eliminare un endpoint server. | Per risolvere questo problema, vedere [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Non è ancora possibile sincronizzare la modifica del file o della directory perché una cartella dipendente non è ancora stata sincronizzata. Questo elemento verrà sincronizzato dopo la sincronizzazione delle modifiche dipendenti. | Non è necessaria alcuna azione. Se l'errore persiste per diversi giorni, usare lo script di PowerShell FileSyncErrorsReport.ps1 per determinare il motivo per cui la cartella dipendente non è ancora sincronizzata. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Non è ancora possibile sincronizzare la modifica del file o della directory perché una cartella dipendente non è ancora stata sincronizzata. Questo elemento verrà sincronizzato dopo la sincronizzazione delle modifiche dipendenti. | Non è necessaria alcuna azione. Se l'errore persiste per diversi giorni, usare lo script di PowerShell FileSyncErrorsReport.ps1 per determinare il motivo per cui la cartella dipendente non è ancora sincronizzata. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Non è ancora possibile sincronizzare la modifica del file o della directory perché una cartella dipendente non è ancora stata sincronizzata e la sessione di sincronizzazione ha avuto esito negativo. Questo elemento verrà sincronizzato dopo la sincronizzazione delle modifiche dipendenti. | Non è necessaria alcuna azione. Se l'errore persiste, esaminare l'errore della sessione di sincronizzazione. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Il nome della directory o dei file non è valido. | Rinominare il file o la directory in questione. Vedere [Gestione dei caratteri non supportati](?tabs=portal1%252cazure-portal#handling-unsupported-characters) per altre informazioni. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Il nome della directory o dei file non è valido. | Rinominare il file o la directory in questione. Vedere [Gestione dei caratteri non supportati](?tabs=portal1%252cazure-portal#handling-unsupported-characters) per altre informazioni. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Il file non può essere sincronizzato perché è in uso. Il file verrà sincronizzato quando non sarà più in uso. | Non è necessaria alcuna azione. Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Il file è stato modificato, ma la modifica non è ancora stata rilevata dalla sincronizzazione. La sincronizzazione verrà ripristinata dopo il rilevamento di questa modifica. | Non è necessaria alcuna azione. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Il file è stato eliminato e la sincronizzazione non ha rilevato la modifica. | Non è necessaria alcuna azione. La sincronizzazione interromperà la registrazione dell'errore una volta che il rilevamento delle modifiche avrà rilevato che il file è stato eliminato. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | L'eliminazione di un file o di una directory non può essere sincronizzata perché l'elemento è già stato eliminato nella destinazione e la sincronizzazione non ha rilevato la modifica. | Non è necessaria alcuna azione. La sincronizzazione interromperà la registrazione dell'errore dopo l'esecuzione del rilevamento delle modifiche nella destinazione e una volta che la sincronizzazione avrà rilevato che l'elemento è stato eliminato. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Il file o la directory sono stati ignorati ma verranno sincronizzati durante la sessione di sincronizzazione successiva. Se questo errore viene segnalato durante il download dell'elemento, il nome del file o della directory molto probabilmente non è valido. | Non è richiesta alcuna azione se questo errore viene segnalato durante il caricamento del file. Se l'errore viene segnalato durante il download del file, rinominare il file o la directory in questione. Vedere [Gestione dei caratteri non supportati](?tabs=portal1%252cazure-portal#handling-unsupported-characters) per altre informazioni. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Non è possibile sincronizzare la creazione di un file o di una directory perché l'elemento esiste già nella destinazione e la sincronizzazione non ha rilevato la modifica. | Non è necessaria alcuna azione. La sincronizzazione arresterà la registrazione dell'errore una volta eseguito il rilevamento delle modifiche nella destinazione e rileverà il nuovo elemento. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Il file non può essere sincronizzato perché è stato raggiunto il limite di condivisione file di Azure. | Per risolvere questo problema, vedere la sezione [È stato raggiunto il limite di archiviazione di condivisione file di Azure](?tabs=portal1%252cazure-portal#-2134351810) nella Guida alla risoluzione dei problemi. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Il file è crittografato da una soluzione non supportata, ad esempio NTFS EFS. | Decrittografare il file e usare una soluzione di crittografia supportata. Per un elenco delle soluzioni di supporto, vedere la sezione [Soluzioni di crittografia](./storage-sync-files-planning.md#encryption) nella Guida alla pianificazione. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Il file si trova in una cartella di replica di sola lettura DFS-R. | Il file si trova in una cartella di replica di sola lettura DFS-R. Sincronizzazione file di Azure non supporta gli endpoint server nelle cartelle di replica di sola lettura di DFS-R. Per altre informazioni, vedere la [guida di piano](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Il file ha uno stato di eliminazione in sospeso. | Non è necessaria alcuna azione. Il file verrà eliminato una volta che tutti gli handle di file aperti saranno chiusi. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Il file non può essere sincronizzato perché le impostazioni del firewall e della rete virtuale nell'account di archiviazione sono abilitate e il server non ha accesso all'account di archiviazione. | Aggiungere l'indirizzo IP del server o la rete virtuale seguendo i passaggi descritti nella sezione [Configurare le impostazioni del firewall e della rete virtuale](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) nella Guida alla distribuzione. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Il file non può essere sincronizzato perché le dimensioni del descrittore di protezione superano il limite di 64 KiB. | Per risolvere questo problema, rimuovere le voci di controllo di accesso (ACE) nel file per ridurre le dimensioni del descrittore di sicurezza. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Il file non può essere sincronizzato a causa di un errore imprevisto. | Se l'errore persiste per diversi giorni, aprire un caso di supporto. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Il file non può essere sincronizzato perché è in uso. Il file verrà sincronizzato quando non sarà più in uso. | Non è necessaria alcuna azione. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Il file è stato modificato durante la sincronizzazione ed è quindi necessario sincronizzarlo di nuovo. | Non è necessaria alcuna azione. |
| 0x80070017 | -2147024873 | ERROR_CRC | Il file non può essere sincronizzato a causa di un errore CRC. Questo errore si potrebbe verificare se un file a livelli non è stato richiamato prima di eliminare un endpoint server oppure se il file è danneggiato. | Per risolvere questo problema, vedere [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) per rimuovere i file a livelli orfani. Se l'errore persiste dopo la rimozione dei file a livelli orfani, eseguire [chkdsk](/windows-server/administration/windows-commands/chkdsk) sul volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Il file non può essere sincronizzato perché è stato raggiunto il numero massimo di file in conflitto. Sincronizzazione file di Azure supporta 100 file in conflitto per file. Per altre informazioni sui conflitti di file, vedere le [domande frequenti](./storage-files-faq.md#afs-conflict-resolution) su Sincronizzazione file di Azure. | Per risolvere il problema, ridurre il numero di file in conflitto. Il file verrà sincronizzato quando il numero di file in conflitto sarà inferiore a 100. |

#### <a name="handling-unsupported-characters"></a>Gestione dei caratteri non supportati
Se lo script di **FileSyncErrorsReport.ps1** PowerShell Mostra gli errori di sincronizzazione per elemento a causa di caratteri non supportati (codice errore 0x8007007b o 0x80c80255), è necessario rimuovere o rinominare i caratteri in errore dai rispettivi nomi file. PowerShell probabilmente stamperà i caratteri come punti interrogativi o rettangoli vuoti poiché la maggior parte di questi caratteri non ha alcuna codifica visiva standard. 
> [!Note]  
> È possibile usare lo [strumento di valutazione](storage-sync-files-planning.md#evaluation-cmdlet) per identificare i caratteri non supportati. Se il set di dati contiene più file con caratteri non validi, usare lo script [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) per rinominare i file che contengono caratteri non supportati.

La tabella seguente contiene tutti i caratteri unicode che Sincronizzazione file di Azure non supporta ancora.

| Set di caratteri | Conteggio di caratteri |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operating system command)</li><li>0x00000090 (dcs device control string)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (high octet preset)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (Arabic presentation forms-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (caso speciale) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (non carattere)</li><li>0x0002FFFE - 0x0002FFFF = 2 (non carattere)</li><li>0x0003FFFE - 0x0003FFFF = 2 (non carattere)</li><li>0x0004FFFE - 0x0004FFFF = 2 (non carattere)</li><li>0x0005FFFE - 0x0005FFFF = 2 (non carattere)</li><li>0x0006FFFE - 0x0006FFFF = 2 (non carattere)</li><li>0x0007FFFE - 0x0007FFFF = 2 (non carattere)</li><li>0x0008FFFE - 0x0008FFFF = 2 (non carattere)</li><li>0x0009FFFE - 0x0009FFFF = 2 (non carattere)</li><li>0x000AFFFE - 0x000AFFFF = 2 (non carattere)</li><li>0x000BFFFE - 0x000BFFFF = 2 (non carattere)</li><li>0x000CFFFE - 0x000CFFFF = 2 (non carattere)</li><li>0x000DFFFE - 0x000DFFFF = 2 (non carattere)</li><li>0x000EFFFE - 0x000EFFFF = 2 (non definito)</li><li>0x000FFFFE - 0x000FFFFF = 2 (supplementary private use area)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Errori di sincronizzazione comuni
<a id="-2147023673"></a>**La sessione di sincronizzazione è stata annullata.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimale)** | -2147023673 | 
| **Stringa di errore** | ERROR_CANCELLED |
| **Rimedio necessario** | No |

Le sessioni di sincronizzazione potrebbero non avvenire per vari motivi, tra cui il server che viene riavviato o aggiornato, gli snapshot VSS e così via. Anche se questo errore sembra richiedere un promemoria, è possibile ignorarlo a meno che non venga mantenuto per un periodo di diverse ore.

<a id="-2147012889"></a>**Non è stato possibile stabilire una connessione al servizio.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimale)** | -2147012889 | 
| **Stringa di errore** | WININET_E_NAME_NOT_RESOLVED |
| **Rimedio necessario** | Sì |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Una richiesta dell'utente è stata limitata dal servizio.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimale)** | -2134376372 |
| **Stringa di errore** | ECS_E_USER_REQUEST_THROTTLED |
| **Rimedio necessario** | No |

Non è necessaria alcuna azione, il server tenterà nuovamente. Se l'errore persiste per più ore, creare una richiesta di supporto.

<a id="-2134364043"></a>**La sincronizzazione è bloccata fino al completamento del rilevamento delle modifiche dopo il ripristino**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimale)** | -2134364043 |
| **Stringa di errore** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Rimedio necessario** | No |

Non è richiesta alcuna azione. Quando viene ripristinato un file o una condivisione file (endpoint cloud) usando Backup di Azure, la sincronizzazione viene bloccata fino al completamento del rilevamento delle modifiche nella condivisione file di Azure. Il rilevamento delle modifiche viene eseguito immediatamente dopo il completamento del ripristino e la durata si basa sul numero di file nella condivisione file.

<a id="-2147216747"></a>**Sincronizzazione non riuscita perché il database di sincronizzazione è stato scaricato.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimale)** | -2147216747 |
| **Stringa di errore** | SYNC_E_METADATA_INVALID_OPERATION |
| **Rimedio necessario** | No |

Questo errore si verifica in genere quando un'applicazione di backup crea uno snapshot VSS e il database di sincronizzazione viene scaricato. Se l'errore persiste per più ore, creare una richiesta di supporto.

<a id="-2134364065"></a>**La sincronizzazione non può accedere alla condivisione file di Azure specificata nell'endpoint cloud.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimale)** | -2134364065 |
| **Stringa di errore** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché l'agente Sincronizzazione file di Azure non accede alla condivisione di file di Azure, possibilmente poiché la condivisione file di Azure o l'account di archiviazione che lo ospita non è più presente. È possibile risolvere questo errore eseguendo questa procedura:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Garantire l'esistenza della condivisione file di Azure.](#troubleshoot-azure-file-share)
3. [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione.](#troubleshoot-rbac)
4. [Verificare che le impostazioni del firewall e della rete virtuale nell'account di archiviazione siano configurate correttamente (se abilitate)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Sincronizzazione non riuscita perché la richiesta non è autorizzata a eseguire questa operazione.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimale)** | -2134351804 |
| **Stringa di errore** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché l'agente Sincronizzazione file di Azure non è autorizzato ad accedere alla condivisione file di Azure. È possibile risolvere questo errore eseguendo questa procedura:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Garantire l'esistenza della condivisione file di Azure.](#troubleshoot-azure-file-share)
3. [Verificare che le impostazioni del firewall e della rete virtuale nell'account di archiviazione siano configurate correttamente (se abilitate)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Non è stato possibile risolvere il nome dell'account di archiviazione usato.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimale)** | -2134364064 |
| **Stringa di errore** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Rimedio necessario** | Sì |

1. Verificare che sia possibile risolvere il nome DNS di archiviazione dal server.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
3. [Verificare che le impostazioni del firewall e della rete virtuale nell'account di archiviazione siano configurate correttamente (se abilitate)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Si è verificato un errore sconosciuto durante l'accesso all'account di archiviazione.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimale)** | -2134364022 |
| **Stringa di errore** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Rimedio necessario** | Sì |

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Verificare che le impostazioni del firewall e della rete virtuale nell'account di archiviazione siano configurate correttamente (se abilitate)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**La sincronizzazione non è riuscita perché l'account di archiviazione è bloccato.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimale)** | -2134364014 |
| **Stringa di errore** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché l'account di archiviazione ha un [blocco risorsa](../../azure-resource-manager/management/lock-resources.md) di sola lettura. Per risolvere questo problema, rimuovere il blocco di risorse di sola lettura nell'account di archiviazione. 

<a id="-1906441138"></a>**La sincronizzazione non è riuscita a causa di un problema con il database di sincronizzazione.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimale)** | -1906441138 |
| **Stringa di errore** | JET_errWriteConflict |
| **Rimedio necessario** | Sì |

Questo errore si verifica quando è presente un problema con il database interno usato da Sincronizzazione file di Azure. Quando si verifica questo problema, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

<a id="-2134364053"></a>**La versione dell'agente di Sincronizzazione file di Azure installata nel server non è supportata.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimale)** | -2134364053 |
| **Stringa di errore** | ECS_E_AGENT_VERSION_BLOCKED |
| **Rimedio necessario** | Sì |

Questo errore si verifica se la versione dell'agente di Sincronizzazione file di Azure installata nel server non è supportata. Per risolvere questo problema, eseguire l'[aggiornamento]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) a una [versione dell'agente supportata]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**È stato raggiunto il limite di archiviazione per le condivisioni file di Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimale)** | -2134351810 |
| **Stringa di errore** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Rimedio necessario** | Sì |

Questo errore si verifica quando il limite di archiviazione di condivisione file di Azure è stato raggiunto, il che può succedere se viene applicata una quota per una condivisione file di Azure o se l'utilizzo supera i limiti per una condivisione file di Azure. Per altre informazioni, vedere i [limiti attuali per una condivisione file di Azure](storage-files-scale-targets.md).

1. Passare al gruppo di sincronizzazione all'interno del servizio di sincronizzazione archiviazione.
2. Selezionare l'endpoint cloud all'interno del gruppo di sincronizzazione.
3. Si noti il nome della condivisione file di Azure nel riquadro aperto.
4. Selezionare l'account di archiviazione collegato. Se questo collegamento ha esito negativo, l'account di archiviazione a cui viene fatto riferimento è stato rimosso.

    ![Schermata che mostra il riquadro dei dettagli dell'endpoint cloud con un collegamento all'account di archiviazione.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selezionare **File** per visualizzare l'elenco delle condivisioni file.
6. Fare clic sui tre puntini alla fine della riga per la condivisione file di Azure a cui fa riferimento l'endpoint cloud.
7. Verificare che l'**Utilizzo** si trovi sotto a **Quota**. Nota: a meno che non sia stata specificata una quota alternativa, la quota corrisponderà alle [dimensioni massime della condivisione file di Azure](storage-files-scale-targets.md).

    ![Schermata delle proprietà della condivisione file di Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se la condivisione è piena e non è impostata una quota, uno dei possibili metodi per correggere il problema è inserire ogni sottocartella dell'endpoint server corrente nel relativo endpoint server all'interno degli specifici gruppi di sincronizzazione. In questo modo ogni sottocartella verrà sincronizzata con le singole condivisioni di file di Azure.

<a id="-2134351824"></a>**Impossibile trovare la condivisione file di Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimale)** | -2134351824 |
| **Stringa di errore** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Rimedio necessario** | Sì |

Questo errore si verifica quando la condivisione file di Azure non è accessibile. Per risolvere i problemi:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Garantire l'esistenza della condivisione file di Azure.](#troubleshoot-azure-file-share)

Se la condivisione file di Azure è stata eliminata, creare una nuova condivisione file e quindi ricreare il gruppo di sincronizzazione. 

<a id="-2134364042"></a>**La sincronizzazione è in pausa durante la sospensione della sottoscrizione di Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimale)** | -2134364042 |
| **Stringa di errore** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Rimedio necessario** | Sì |

Questo errore si verifica quando viene sospesa la sottoscrizione di Azure. La sincronizzazione verrà riabilitata quando verrà ripristinata la sottoscrizione di Azure. Per altre informazioni, vedere [Perché la sottoscrizione di Azure è disabilitata e cosa occorre fare per riattivarla?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134375618"></a>**Per l'account di archiviazione sono configurati un firewall o reti virtuali.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (decimale)** | -2134375618 |
| **Stringa di errore** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Rimedio necessario** | Sì |

Questo errore si verifica quando la condivisione file di Azure è inaccessibile a causa di un firewall di account di archiviazione o perché l'account di archiviazione appartiene a una rete virtuale. Verificare che le impostazioni del firewall e della rete virtuale nell'account di archiviazione siano configurate correttamente. Per altre informazioni, vedere [Configurare le impostazioni del firewall e della rete virtuale](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**La sincronizzazione non è riuscita a causa di un problema con il database di sincronizzazione.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimale)** | -2134375911 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Rimedio necessario** | No |

Questo errore in genere si risolve automaticamente. Può verificarsi in presenza di:

* Un numero elevato di modifiche ai file tra i server nel gruppo di sincronizzazione.
* Un numero elevato di errori in singoli file e directory.

Se il problema persiste per più di due ore, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

<a id="-2146762487"></a>**Il server non è riuscito a stabilire una connessione sicura. Il servizio cloud ha ricevuto un certificato imprevisto.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimale)** | -2146762487 |
| **Stringa di errore** | CERT_E_UNTRUSTEDROOT |
| **Rimedio necessario** | Sì |

Questo errore può verificarsi se l'organizzazione usa un proxy di terminazione TLS o se un'entità dannosa intercetta il traffico tra il server e il servizio Sincronizzazione file di Azure. Se si è certi che questo comportamento sia previsto (perché l'organizzazione usa un proxy di terminazione TLS), ignorare la verifica dei certificati con un override del Registro di sistema.

1. Creare il valore del registro SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Riavviare il servizio di sincronizzazione nel server registrato.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Impostando questo valore del Registro di sistema, l'agente di Sincronizzazione file di Azure accetterà qualsiasi certificato TLS/SSL attendibile locale durante il trasferimento dei dati tra il server e il servizio cloud.

<a id="-2147012894"></a>**Non è stato possibile stabilire una connessione al servizio.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimale)** | -2147012894 |
| **Stringa di errore** | WININET_E_TIMEOUT |
| **Rimedio necessario** | Sì |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**La sincronizzazione non è riuscita a causa di un problema di autenticazione.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimale)** | -2134375680 |
| **Stringa di errore** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Rimedio necessario** | Sì |

Questo errore si verifica in genere perché l'ora del server non è corretta. Se il server è in esecuzione in una macchina virtuale, verificare che l'ora dell'host sia corretta.

<a id="-2134364040"></a>**Sincronizzazione non riuscita a causa della scadenza del certificato.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimale)** | -2134364040 |
| **Stringa di errore** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché il certificato usato per l'autenticazione è scaduto.

Per verificare che il certificato è scaduto, seguire questa procedura:  
1. Aprire lo snap-in MMC dei certificati, selezionare l'account del computer e passare a certificati (Computer locale)\Personale\Certificati.
2. Controllare se il certificato di autenticazione del client è scaduto.

Se il certificato di autenticazione client è scaduto, attenersi alla procedura seguente per risolvere il problema:

1. Verificare che sia installata la versione 4.0.1.0 o versione successiva dell'agente Sincronizzazione file di Azure.
2. Eseguire il comando di PowerShell seguente sul server:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Sincronizzazione non riuscita a causa di un certificato di autenticazione non trovato.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimale)** | -2134375896 |
| **Stringa di errore** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché non è stato trovato il certificato usato per l'autenticazione.

Per risolvere il problema, eseguire la procedura seguente:

1. Verificare che sia installata la versione 4.0.1.0 o versione successiva dell'agente Sincronizzazione file di Azure.
2. Eseguire il comando di PowerShell seguente sul server:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Sincronizzazione non riuscita a causa di un'identità autenticazione non trovata.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimale)** | -2134364039 |
| **Stringa di errore** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché l'eliminazione dell'endpoint server non è riuscita e l'endpoint si trova ora in uno stato di parziale eliminazione. Per risolvere il problema, provare a eliminare di nuovo l'endpoint server.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Lo spazio su disco del volume in cui risiede l'endpoint server è insufficiente.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimale)** | -1906441711 |
| **Stringa di errore** | JET_errLogDiskFull |
| **Rimedio necessario** | Sì |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimale)** | -2134375654 |
| **Stringa di errore** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché il volume è esaurito. Questo errore si verifica generalmente perché i file all'esterno di endpoint del server usano spazio nel volume. Liberare spazio nel volume aggiungendo altri endpoint server, spostando i file in un altro volume o aumentando le dimensioni del volume in cui l'endpoint server è attivo.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Il servizio non è ancora pronto per la sincronizzazione con questo endpoint server.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimale)** | -2134364145 |
| **Stringa di errore** | ECS_E_REPLICA_NOT_READY |
| **Rimedio necessario** | No |

Questo errore si verifica perché l'endpoint cloud è stato creato con contenuto già esistente nella condivisione file di Azure. Sincronizzazione file di Azure deve cercare tutto il contenuto nella condivisione file di Azure prima di consentire all'endpoint server di procedere con la sincronizzazione iniziale.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Sincronizzazione non riuscita a causa di problemi con svariati file singoli.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimale)** | -2134375877 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Rimedio necessario** | Sì |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimale)** | -2134375908 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Rimedio necessario** | Sì |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimale)** | -2134375853 |
| **Stringa di errore** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Rimedio necessario** | Sì |

Le sessioni di sincronizzazione hanno esito negativo con uno di questi errori quando sono presenti molti file che non riescono a eseguire la sincronizzazione con errori per elemento. Eseguire i passaggi illustrati nella [ricerca per categorie verificare se sono presenti file o cartelle specifici che non sono sincronizzati?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) sezione per risolvere gli errori per elemento. Per ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED di errore di sincronizzazione, aprire un caso di supporto.

> [!NOTE]
> Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti.

<a id="-2134376423"></a>**La sincronizzazione non è riuscita a causa di un problema con il percorso dell'endpoint server.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimale)** | -2134376423 |
| **Stringa di errore** | ECS_E_SYNC_INVALID_PATH |
| **Rimedio necessario** | Sì |

Assicurarsi che il percorso esista, che si trovi in un volume NTFS locale e che non sia un reparse point o un endpoint server esistente.

<a id="-2134375817"></a>**La sincronizzazione non è riuscita perché la versione del driver di filtro non è compatibile con la versione dell'agente**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimale)** | -2134375817 |
| **Stringa di errore** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché la versione del driver cloud a livelli caricata (StorageSync.sys) non è compatibile con il servizio agente di sincronizzazione archiviazione (FileSyncSvc). Se l'agente Sincronizzazione file di Azure è stato aggiornato, riavviare il server per completare l'installazione. Se l'errore persiste, disinstallare l'agente, riavviare il server e reinstallare l'agente Sincronizzazione file di Azure.

<a id="-2134376373"></a>**Il servizio non è attualmente disponibile.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimale)** | -2134376373 |
| **Stringa di errore** | ECS_E_SERVICE_UNAVAILABLE |
| **Rimedio necessario** | No |

Questo errore si verifica perché il servizio di Sincronizzazione file di Azure è disponibile. Questo errore si risolve automaticamente quando il servizio Sincronizzazione file di Azure torna disponibile.

<a id="-2146233088"></a>**Sincronizzazione non riuscita a causa di un'eccezione.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimale)** | -2146233088 |
| **Stringa di errore** | COR_E_EXCEPTION |
| **Rimedio necessario** | No |

Questo errore si verifica perché la sincronizzazione non è riuscita a causa di un'eccezione. Se l'errore persiste per diverse ore, creare una richiesta di supporto.

<a id="-2134364045"></a>**La sincronizzazione non è riuscita perché è stato eseguito il failover dell'account di archiviazione in un'altra area.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimale)** | -2134364045 |
| **Stringa di errore** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché è stato eseguito il failover dell'account di archiviazione in un'altra area. Sincronizzazione file di Azure non supporta la funzionalità di failover dell'account di archiviazione. È consigliabile non effettuare il failover degli account di archiviazione contenenti condivisioni file di Azure che vengono usate come endpoint cloud in Sincronizzazione file di Azure. Il failover causerebbe l'arresto della sincronizzazione e potrebbe causare inoltre una perdita di dati imprevista nel caso di file appena disposti su livelli. Per risolvere questo problema, spostare l'account di archiviazione nell'area primaria.

<a id="-2134375922"></a>**La sincronizzazione non è riuscita a causa di un problema temporaneo con il database di sincronizzazione.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimale)** | -2134375922 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Rimedio necessario** | No |

Questo errore si verifica a causa di un problema interno con il database di sincronizzazione. Questo errore si risolve automaticamente quando viene ritentata la sincronizzazione. Se il problema persiste per un periodo di tempo prolungato, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

<a id="-2134364024"></a>**Sincronizzazione non riuscita a causa di modifiche nel tenant di Azure Active Directory**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimale)** | -2134364024 | 
| **Stringa di errore** | ECS_E_INVALID_AAD_TENANT |
| **Rimedio necessario** | Sì |

Assicurarsi di disporre della versione più recente dell'agente di Sincronizzazione file di Azure. A partire dall'agente V10, Sincronizzazione file di Azure supporta il trasferimento della sottoscrizione a un tenant di Azure Active Directory diverso.
 
Dopo aver ottenuto la versione più recente dell'agente, è necessario concedere all'applicazione Microsoft.StorageSync l'accesso all'account di archiviazione (vedere [Verificare che Sincronizzazione file di Azure abbia accesso all'account di archiviazione](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Sincronizzazione non riuscita a causa di un'eccezione del firewall e della rete virtuale non configurata**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimale)** | -2134364010 | 
| **Stringa di errore** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Rimedio necessario** | Sì |

Questo errore si verifica se le impostazioni del firewall e della rete virtuale sono abilitate nell'account di archiviazione e l'eccezione "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione" non è selezionata. Per risolvere questo problema, seguire la procedura illustrata nella sezione [Configurare le impostazioni del firewall e della rete virtuale](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) nella Guida alla distribuzione.

<a id="-2147024891"></a>**La sincronizzazione non è riuscita perché le autorizzazioni per la cartella System Volume Information non sono corrette.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimale)** | -2147024891 |
| **Stringa di errore** | ERROR_ACCESS_DENIED |
| **Rimedio necessario** | Sì |

Questo errore può verificarsi se l'account NT AUTHORITY\SYSTEM non dispone delle autorizzazioni per accedere alla cartella System Volume Information nel volume in cui si trova l'endpoint server. Se i singoli file non possono eseguire la sincronizzazione con ERROR_ACCESS_DENIED, eseguire i passaggi descritti nella sezione [Risoluzione dei problemi per gli errori di sincronizzazione di file e directory](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors).

Per risolvere il problema, eseguire la procedura seguente:

1. Scaricare lo strumento [Psexec](/sysinternals/downloads/psexec).
2. Eseguire il comando seguente da un prompt dei comandi con privilegi elevati per avviare un prompt dei comandi con l'account di sistema: **PsExec.exe -i -s -d cmd** 
3. Dal prompt dei comandi in esecuzione nell'account di sistema, eseguire il comando seguente per verificare che l'account NT AUTHORITY\SYSTEM non abbia accesso alla cartella System Volume Information: **cacls "drive letter:\system volume information" /T /C**
4. Se l'account NT AUTHORITY\SYSTEM non dispone dell'accesso alla cartella System Volume Information, eseguire il comando seguente: **cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Se il passaggio 4 ha esito negativo e l'accesso viene negato, eseguire il comando seguente per assumere la proprietà della cartella System Volume Information, quindi ripetere il passaggio 4: **takeown /A /R /F "drive letter:\System Volume Information"**

<a id="-2134375810"></a>**La sincronizzazione non è riuscita perché la condivisione file di Azure è stata eliminata e ricreata.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimale)** | -2134375810 |
| **Stringa di errore** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché Sincronizzazione file di Azure non supporta l'eliminazione e la ricreazione di una condivisione file di Azure nello stesso gruppo di sincronizzazione. 

Per risolvere il problema, eliminare e ricreare il gruppo di sincronizzazione attenendosi alla procedura seguente:

1. Eliminare tutti gli endpoint server nel gruppo di sincronizzazione.
2. Eliminare l'endpoint cloud. 
3. Eliminare il gruppo di sincronizzazione.
4. Se il cloud a livelli è stato abilitato in un endpoint server, eliminare i file a livelli orfani sul server eseguendo i passaggi illustrati nella sezione [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
5. Ricreare il gruppo di sincronizzazione.

<a id="-2145844941"></a>**Sincronizzazione non riuscita perché la richiesta HTTP è stata reindirizzata**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimale)** | -2145844941 |
| **Stringa di errore** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Rimedio necessario** | Sì |

Questo errore si verifica perché Sincronizzazione file di Azure non supporta il reindirizzamento HTTP (codice di stato 3xx). Per risolvere questo problema, disabilitare il reindirizzamento HTTP sul server proxy o sul dispositivo di rete.

<a id="-2134364027"></a>**Si è verificato un timeout durante il trasferimento di dati offline, ma è ancora in corso.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimale)** | -2134364027 |
| **Stringa di errore** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Rimedio necessario** | No |

Questo errore si verifica quando un'operazione di inserimento di dati supera il timeout. Questo errore può essere ignorato se la sincronizzazione sta procedendo (AppliedItemCount è maggiore di 0). Vedere [Come monitorare lo stato di avanzamento di una sessione di sincronizzazione corrente?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Normale procedura di risoluzione dei problemi
<a id="troubleshoot-storage-account"></a>**Verificare l'esistenza dell'account di archiviazione.**  
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Passare al gruppo di sincronizzazione all'interno del servizio di sincronizzazione archiviazione.
2. Selezionare l'endpoint cloud all'interno del gruppo di sincronizzazione.
3. Si noti il nome della condivisione file di Azure nel riquadro aperto.
4. Selezionare l'account di archiviazione collegato. Se questo collegamento ha esito negativo, l'account di archiviazione a cui viene fatto riferimento è stato rimosso.
    ![Schermata che mostra il riquadro dei dettagli dell'endpoint cloud con un collegamento all'account di archiviazione.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Garantire l'esistenza della condivisione file di Azure.**  
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Fare clic su **Panoramica** nel sommario a sinistra per tornare alla pagina dell'account di archiviazione principale.
2. Selezionare **File** per visualizzare l'elenco delle condivisioni file.
3. Verificare che la condivisione file a cui fa riferimento l'endpoint cloud venga visualizzata nell'elenco delle condivisioni di file (come si può vedere dal passaggio 1 in alto).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione.**  
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Fare clic su **Controllo di accesso (IAM)** nel sommario a sinistra.
1. Fare clic sulla scheda **Assegnazioni del ruolo** per passare all'elenco di utenti e applicazioni (*entità servizio*) che possono accedere all'account di archiviazione.
1. Verificare che **Microsoft.StorageSync** o il **servizio Sincronizzazione file ibrida** (nome dell'applicazione precedente) venga visualizzato nell'elenco con il ruolo **Lettore e accesso ai dati**. 

    ![Una schermata dell'entità servizio del servizio Sincronizzazione file ibrida nella scheda di controllo di accesso dell'account di archiviazione](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se **Microsoft. StorageSync** o il **servizio Sincronizzazione file ibrida** non è visualizzato nell'elenco, eseguire i passaggi seguenti:

    - Scegliere **Aggiungi**.
    - Nel campo **Ruolo** selezionare **Lettore e accesso ai dati**.
    - Nel campo **Seleziona** digitare **Microsoft.StorageSync**, selezionare il ruolo e fare clic su **Salva**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Come impedire agli utenti di creare i file contenenti caratteri non supportati nel server?
È possibile usare [screening dei file di Gestione risorse File server](/windows-server/storage/fsrm/file-screening-management) per impedire ai file con caratteri non supportati nei nomi di essere creati nel server. È possibile eseguire questa operazione tramite PowerShell, poiché la maggior parte dei caratteri non supportati non è stampabile e pertanto è necessario prima di tutto eseguire il cast nelle rispettive rappresentazioni esadecimali come caratteri.

Prima di tutto creare un gruppo di file di Gestione risorse file server usando il [cmdlet New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Questo esempio definisce il gruppo affinché contenga solo due dei caratteri non supportati, ma è possibile includere il numero dei caratteri in base alle esigenze nel gruppo di file.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Dopo aver definito un gruppo di file di Gestione risorse file server, è possibile creare uno screening dei file di Gestione risorse file server usando il cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Si noti che gli screening dei file devono essere usati solo per bloccare la creazione di caratteri non supportati da Sincronizzazione file di Azure. Se gli screening dei file vengono usati in altri scenari, la sincronizzazione tenterà continuamente di scaricare i file dalla condivisione file di Azure nel server e verrà bloccata a causa dello screening dei file, comportando un elevato traffico in uscita dei dati. 

## <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
Nell'archiviazione a livelli nel cloud possono prendere forma due percorsi di errore:

- È possibile che i file non possano essere archiviati a livelli, ovvero che Sincronizzazione file di Azure non riesca ad archiviare a livelli i file in File di Azure.
- È possibile che i file non possano essere richiamati, ovvero che il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non possa scaricare dati quando un utente tenta di accedere a un file a livelli.

Dai questi due percorsi di errore possono essere generate due principali classi di errore:

- Errori di archiviazione cloud
    - *Problemi di disponibilità del servizio di archiviazione temporanea*. Per altre informazioni, vedere [Contratto di servizio per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Condivisione file di Azure non accessibile*. Questo errore si verifica in genere quando si elimina una condivisione file di Azure che si trova ancora in un endpoint cloud di un gruppo di sincronizzazione.
    - *Account di archiviazione inaccessibile*. Questo errore si verifica in genere quando si elimina un account di archiviazione che ha ancora una condivisione file di Azure costituita da un endpoint cloud in un gruppo di sincronizzazione. 
- Errori del server 
  - *Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non è caricato*. Per rispondere alle richieste di archiviazione a livelli/richiamo, è necessario che il filtro del file system di Sincronizzazione file di Azure sia caricato. È possibile che un filtro non risulti caricato per vari motivi, ma la causa più comune è che un amministratore lo abbia scaricato manualmente. Il filtro del file system di Sincronizzazione file di Azure deve risultare sempre caricato per garantire il corretto funzionamento di Sincronizzazione file di Azure.
  - *Reparse point mancante, danneggiato o non funzionante*. Un reparse point è una speciale struttura di dati in un file costituita da due parti:
    1. Un tag di reparse, che indica al sistema operativo che per eseguire alcune operazioni di I/O sul file è possibile che sia necessario il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). 
    2. I dati di reparse, che indicano al filtro del file system l'URI del file nell'endpoint cloud associato (la condivisione file di Azure). 
        
       Nella maggior parte dei casi, un reparse point viene danneggiato quando un amministratore tenta di modificarne il tag o i dati. 
  - *Problemi di connettività di rete*. Per archiviare a livelli o richiamare un file, il server deve avere la connettività Internet.

Le sezioni seguenti indicano come risolvere problemi di archiviazione a livelli nel cloud e determinare se si tratta di un problema di archiviazione cloud o di un problema del server.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Come monitorare l'attività di suddivisione in livelli in un server  
Per monitorare l'attività di suddivisione in livelli in un server, usare l'ID evento 9003, 9016 e 9029 nel registro eventi di telemetria (che si trova in Applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi).

- L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio, Total Error Count, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.
- L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio, percentuale di spazio disponibile, numero di file fantasma nella sessione, numero di file in cui non è stato possibile eseguire il ghosting e così via.
- L'ID evento 9029 fornisce informazioni sulla sessione di ghosting per un endpoint server. Ad esempio, numero di file che hanno eseguito un tentativo nella sessione, numero di file archiviati a livelli nella sessione, numero di file già archiviati a livelli e così via.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Come monitorare l'attività di richiamo in un server
Per monitorare l'attività di richiamo in un server, usare l'ID evento 9005, 9006 e 9009 e 9059 nel registro eventi di telemetria (che si trova in Registri applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi).

- L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio, numero totale di file univoci a cui si ha avuto accesso, numero totale di file univoci con accessi non riusciti e così via.
- L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio, Totale richieste non riuscite, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.
- L'ID evento 9009 fornisce informazioni sulla sessione di richiamo per un endpoint server. Ad esempio, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed e così via.
- L'ID evento 9059 fornisce la distribuzione di richiamo delle applicazioni per un endpoint server. Ad esempio, ShareId, nome dell'applicazione e TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Come risolvere i problemi dei file che non è possibile archiviare a livelli
Se non è possibile archiviare a livelli i file in File di Azure:

1. Nel Visualizzatore eventi esaminare i log i dati di telemetria e gli eventi operativi e diagnostici, i quali si trovano in Registri applicazioni e servizi\Microsoft\FileSync\Agente. 
   1. Verificare che i file esistano nella condivisione di file di Azure.

      > [!NOTE]
      > È necessario che un file venga sincronizzato con una condivisione di file di Azure per poter essere archiviato a livelli.

   2. Verificare che il server disponga della connettività Internet. 
   3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
       - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

> [!NOTE]
> L'ID evento 9003 viene registrato dopo un'ora nel registro eventi di telemetria, se un file non riesce a eseguire la suddivisione in livelli (per ogni codice di errore viene registrato un evento). Controllare la sezione [Errori di suddivisione in livelli e correzione](#tiering-errors-and-remediation) per verificare se sono elencati i passaggi correttivi per il codice errore.

### <a name="tiering-errors-and-remediation"></a>Errori di suddivisione in livelli e correzione

| HRESULT | HRESULT (decimale) | Stringa di errore | Problema | Correzione |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Il file non è stato in grado di eseguire il livello perché è in corso il caricamento iniziale. | Non è necessaria alcuna azione. Al termine del caricamento iniziale, il file verrà suddiviso in livelli. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | La suddivisione in livelli del file non è riuscita perché il file è in uso. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli quando non sarà più in uso. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | La suddivisione in livelli del file non è riuscita perché il file è escluso dalla sincronizzazione. | Non è necessaria alcuna azione. I file nell'elenco di esclusione della sincronizzazione non possono essere archiviati a livelli. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | La suddivisione in livelli del file non è riuscita perché il file non è stato trovato sul server. | Non è necessaria alcuna azione. Se l'errore persiste, controllare se il file esiste nel server. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | La suddivisione in livelli del file non è riuscita perché il file è stato eliminato nella condivisione file di Azure. | Non è necessaria alcuna azione. Il file deve essere eliminato nel server quando viene eseguita la successiva sessione di sincronizzazione del download. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | La suddivisione in livelli del file non è riuscita a causa di un problema di rete. | Non è necessaria alcuna azione. Se l'errore persiste, controllare la connettività di rete alla condivisione file di Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | La suddivisione in livelli del file non è riuscita a causa di un problema di rete. | Non è necessaria alcuna azione. Se l'errore persiste, controllare la connettività di rete alla condivisione file di Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | La suddivisione in livelli del file non è riuscita a causa di un errore di accesso negato. Questo errore si verifica se il file si trova in una cartella di replica di sola lettura di DFS-R. | Sincronizzazione file di Azure non supporta gli endpoint server nelle cartelle di replica di sola lettura di DFS-R. Per altre informazioni, vedere la [guida di piano](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | La suddivisione in livelli del file non è riuscita a causa di un problema di rete. | Non è necessaria alcuna azione. Se l'errore persiste, controllare la connettività di rete alla condivisione file di Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | La suddivisione in livelli del file non è riuscita perché le dimensioni del file sono minori delle dimensioni supportate. | Se la versione dell'agente è inferiore a 9.0, la dimensione del file minima supportata è 64 KB. Se la versione dell'agente è 9.0 e più recente, la dimensione del file minima supportata si basa sulle dimensioni del cluster del file system (raddoppia le dimensioni del cluster del file system). Se, ad esempio, le dimensioni del cluster del file system sono 4 KB, le dimensioni minime del file sono 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | La suddivisione in livelli del file non è riuscita a causa di un problema di archiviazione di Azure. | Se l'errore persiste, aprire una richiesta di supporto. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | La suddivisione in livelli del file non è riuscita perché il file è stato richiamato contemporaneamente. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli al termine del richiamo, quando non sarà più in uso. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | La suddivisione in livelli del file non è riuscita perché il file non è stato sincronizzato nella condivisione file di Azure. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli dopo essere stato sincronizzato con la condivisione file di Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | La suddivisione in livelli del file non è riuscita perché il driver filtro per il cloud a livelli (storagesync.sys) non è in esecuzione. | Per risolvere il problema, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: `fltmc load storagesync`<br>Se non è possibile caricare il driver filtro storagesync quando si esegue il comando fltmc, disinstallare l'agente Sincronizzazione file di Azure, riavviare il server e reinstallare l'agente Sincronizzazione file di Azure. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | La suddivisione in livelli del file non è riuscita a causa di spazio su disco insufficiente nel volume in cui si trova l'endpoint server. | Per risolvere il problema, liberare almeno 100 MB di spazio su disco nel volume in cui si trova l'endpoint server. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | La suddivisione in livelli del file non è riuscita perché il file non è stato sincronizzato nella condivisione file di Azure. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli dopo essere stato sincronizzato con la condivisione file di Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | La suddivisione in livelli del file non è riuscita perché il file è un reparse point non supportato. | Se il file è un reparse point di deduplicazione dati, seguire la procedura descritta nella [guida alla pianificazione](./storage-sync-files-planning.md#data-deduplication) per abilitare il supporto della deduplicazione dati. I file con reparse point diversi dalla deduplicazione dei dati non sono supportati e non verranno archiviati a livelli.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | La suddivisione in livelli del file non è riuscita perché il file è stato modificato. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli dopo che il file modificato è stato sincronizzato con la condivisione file di Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | La suddivisione in livelli del file non è riuscita perché il file non è stato sincronizzato nella condivisione file di Azure. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli dopo essere stato sincronizzato con la condivisione file di Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | La suddivisione in livelli del file non è riuscita a causa di un problema di rete. | Non è necessaria alcuna azione. Se l'errore persiste, controllare la connettività di rete alla condivisione file di Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | La suddivisione in livelli del file non è riuscita perché il file è stato modificato. | Non è necessaria alcuna azione. Il file verrà archiviato a livelli dopo che il file modificato è stato sincronizzato con la condivisione file di Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | La suddivisione in livelli del file non è riuscita a causa di risorse del sistema insufficienti. | Se l'errore persiste, individuare l'applicazione o il driver in modalità kernel che esaurisce le risorse di sistema. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Impossibile eseguire il livello del file a causa di un errore di I/O durante la scrittura nel database di suddivisione in livelli cloud. | Se l'errore è permanente, eseguire chkdsk sul volume e controllare l'hardware di archiviazione. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Impossibile eseguire il livello del file perché il database di suddivisione in livelli cloud non è in esecuzione. | Per risolvere questo problema, riavviare il servizio o il server di FileSyncSvc. Se l'errore è permanente, eseguire chkdsk sul volume e controllare l'hardware di archiviazione. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Come risolvere i problemi dei file che non è possibile richiamare  
Se il richiamo di file ha esito negativo:
1. Nel Visualizzatore eventi esaminare i log i dati di telemetria e gli eventi operativi e diagnostici, i quali si trovano in Registri applicazioni e servizi\Microsoft\FileSync\Agente.
    1. Verificare che i file esistano nella condivisione di file di Azure.
    2. Verificare che il server disponga della connettività Internet. 
    3. Aprire lo snap-in Servizi di MMC e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.
    4. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
        - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

> [!NOTE]
> L'ID evento 9006 viene registrato una volta all'ora nel registro eventi di telemetria se un file non riesce a eseguire il richiamo (per ogni codice di errore viene registrato un evento). Controllare la sezione [Richiama errori e correzione](#recall-errors-and-remediation) per verificare se sono elencati passaggi correttivi per il codice errore.

### <a name="recall-errors-and-remediation"></a>Errori di richiamo e correzione

| HRESULT | HRESULT (decimale) | Stringa di errore | Problema | Correzione |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Impossibile richiamare il file a causa di un timeout di I/O. Questo problema può verificarsi per diversi motivi: vincoli delle risorse del server, connettività di rete scarsa o un problema di archiviazione di Azure (ad esempio, limitazione). | Non è necessaria alcuna azione. Se l'errore persiste per diverse ore, aprire un caso di supporto. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Impossibile richiamare il file a causa di un problema di rete.  | Se l'errore persiste, controllare la connettività di rete alla condivisione file di Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Impossibile richiamare il file perché l'endpoint server è stato eliminato. | Per risolvere questo problema, vedere [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Impossibile richiamare il file a causa di un errore di accesso negato. Questo problema può verificarsi se le impostazioni del firewall e della rete virtuale nell'account di archiviazione sono abilitate e il server non ha accesso all'account di archiviazione. | Per risolvere il problema, aggiungere l'indirizzo IP del server o la rete virtuale seguendo i passaggi descritti nella sezione [Configurare le impostazioni del firewall e della rete virtuale](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) nella Guida alla distribuzione. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Impossibile richiamare il file perché non è accessibile nella condivisione file di Azure. | Per risolvere il problema, verificare che il file esista nella condivisione file di Azure. Se il file è presente nella condivisione file di Azure, eseguire l'aggiornamento alla [versione più recente dell'agente](./storage-files-release-notes.md#supported-versions) di Sincronizzazione file di Azure. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Impossibile richiamare il file a causa di un errore di autorizzazione per l'account di archiviazione. | Per risolvere questo problema, verificare che [Sincronizzazione file di Azure abbia accesso all'account di archiviazione](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Impossibile richiamare il file perché la condivisione file di Azure non è accessibile. | Verificare che la condivisione file esista e che sia accessibile. Se la condivisione file è stata eliminata e ricreata, eseguire i passaggi illustrati nella sezione [La sincronizzazione non è riuscita perché la condivisione file di Azure è stata eliminata e ricreata](?tabs=portal1%252cazure-portal#-2134375810) per eliminare e ricreare il gruppo di sincronizzazione. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Impossibile richiamare il file a causa di risorse del sistema insufficienti. | Se l'errore persiste, individuare l'applicazione o il driver in modalità kernel che esaurisce le risorse di sistema. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Impossibile richiamare il file a causa di memoria insufficiente. | Se l'errore persiste, individuare l'applicazione o il driver in modalità kernel che causa la condizione di memoria insufficiente. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Impossibile richiamare il file a causa di spazio su disco insufficiente. | Per risolvere questo problema, liberare spazio sul volume spostando i file in un altro volume, aumentando le dimensioni del volume oppure forzando l'archiviazione per livelli dei file con il cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server
I file a livelli su un server diventano inaccessibili se non vengono richiamati prima di eliminare un endpoint server.

Errori registrati se i file a livelli non sono accessibili
- Quando si sincronizza un file, il codice errore -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) viene registrato nel registro eventi ItemResults
- Quando si richiama un file, il codice errore -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) viene registrato nel registro eventi RecallResults

Il ripristino dell'accesso ai file a livelli è possibile se vengono soddisfatte le condizioni seguenti:
- L'endpoint server è stato eliminato negli ultimi 30 giorni
- L'endpoint cloud non è stato eliminato 
- La condivisione file non è stata eliminata
- Il gruppo di sincronizzazione non è stato eliminato

Se vengono soddisfatte le condizioni precedenti, è possibile ripristinare l'accesso ai file nel server ricreando l'endpoint server nello stesso percorso del server all'interno dello stesso gruppo di sincronizzazione entro 30 giorni. 

Se le condizioni precedenti non sono soddisfatte, non è possibile ripristinare l'accesso perché i file a livelli nel server sono ora orfani. Per rimuovere i file a livelli orfani, seguire le istruzioni riportate di seguito.

**Note**
- Quando i file a livelli non sono accessibili sul server, il file completo dovrebbe essere ancora accessibile se si accede direttamente alla condivisione file di Azure.
- Per impedire la creazione di file a livelli orfani in futuro, seguire i passaggi descritti in [Rimuovere un endpoint server](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) quando si elimina un endpoint server.

<a id="get-orphaned"></a>**Come ottenere l'elenco dei file a livelli orfani** 

1. Verificare che sia installata la versione 5.1 o successiva dell'agente Sincronizzazione file di Azure.
2. Eseguire i comandi di PowerShell seguenti per elencare i file a livelli orfani:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Salvare il file di output OrphanTieredFiles.txt nel caso in cui i file debbano essere ripristinati dal backup dopo l'eliminazione.

<a id="remove-orphaned"></a>**Come rimuovere file a livelli orfani** 

*Opzione 1: Eliminare i file a livelli orfani*

Questa opzione consente di eliminare i file a livelli orfani sul server Windows, ma richiede la rimozione dell'endpoint server, se presente, a causa della ricreazione dopo 30 giorni o se è connesso a un gruppo di sincronizzazione diverso. I conflitti di file si verificheranno se i file vengono aggiornati sul server Windows o nella condivisione file di Azure prima che l'endpoint server venga ricreato.

1. Verificare che sia installata la versione 5.1 o successiva dell'agente Sincronizzazione file di Azure.
2. Eseguire il backup della condivisione file di Azure e del percorso dell'endpoint server.
3. Rimuovere l'endpoint server nel gruppo di sincronizzazione (se esiste) attenendosi ai passaggi descritti in [Rimuovere un endpoint server](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Se l'endpoint server non viene rimosso prima di usare il cmdlet Remove-StorageSyncOrphanedTieredFiles, l'eliminazione del file a livelli orfano sul server eliminerà il file completo nella condivisione file di Azure. 

4. Eseguire i comandi di PowerShell seguenti per elencare i file a livelli orfani:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Salvare il file di output OrphanTieredFiles.txt nel caso in cui i file debbano essere ripristinati dal backup dopo l'eliminazione.
6. Eseguire i comandi di PowerShell seguenti per eliminare i file a livelli orfani:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Note** 
- I file a livelli modificati sul server che non vengono sincronizzati con la condivisione file di Azure verranno eliminati.
- I file a livelli accessibili (non orfani) non verranno eliminati.
- I file non a livelli rimarranno sul server.

7. Facoltativo: Ricreare l'endpoint server se eliminato nel passaggio 3.

*Opzione 2: Montare la condivisione file di Azure e copiare localmente i file che sono orfani sul server*

Questa opzione non richiede la rimozione dell'endpoint server, ma richiede spazio su disco sufficiente per copiare localmente i file completi.

1. [Montare](./storage-how-to-use-files-windows.md) la condivisione file di Azure sul server Windows che contiene file a livelli orfani.
2. Eseguire i comandi di PowerShell seguenti per elencare i file a livelli orfani:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Usare il file di output OrphanTieredFiles.txt per identificare i file a livelli orfani sul server.
4. Sovrascrivere i file a livelli orfani copiando il file completo dalla condivisione file di Azure sul server Windows.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Come risolvere i problemi di file richiamati in modo imprevisto su un server  
Antivirus, backup e altre applicazioni che leggono quantità elevate di file causano richiami indesiderati a meno che non rispettino l'attributo per ignorare i file offline e ignorare la lettura del contenuto di tali file. Se si ignorano i file offline per i prodotti che supportano questa opzione, è possibile evitare richiami imprevisti durante operazioni come le analisi antivirus o i processi di backup.

Per informazioni su come configurare la soluzione in modo che non legga i file offline, rivolgersi al produttore del software.

I richiami imprevisti possono verificarsi anche in altri scenari, ad esempio quando si visualizzano file in Esplora File. Se si apre una cartella contenente file archiviati a livelli nel cloud in Esplora File nel server, è possibile che si verifichino richiami non previsti. Questa situazione si verifica con ancora maggiore probabilità se nel server è abilitata una soluzione antivirus.

> [!NOTE]
>Usare l'ID evento 9059 nel registro eventi di telemetria per determinare quali applicazioni stanno causando i richiami. Questo evento fornisce la distribuzione di richiamo delle applicazioni per un endpoint server e viene registrato ogni ora.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 obbligatorio per Sincronizzazione file di Azure

È possibile visualizzare le impostazioni TLS sul server esaminando le [impostazioni del Registro di sistema](/windows-server/security/tls/tls-registry-settings). 

Se si usa un proxy, consultare la documentazione del proxy e verificare che sia configurato per l'uso di TLS 1.2.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali
Se si verificano problemi con Sincronizzazione file di Azure in un server, per prima cosa eseguire questi passaggi:
1. Nel Visualizzatore eventi esaminare i dati di telemetria e i registri eventi operativi e diagnostici.
    - I problemi di sincronizzazione, archiviazione a livelli e richiamo vengono registrati nei dati di telemetria nella sezione dei log eventi operativi e diagnostici in Registri applicazioni e servizi\Microsoft\FileSync\Agent.
    - I problemi correlati alla gestione di un server (ad esempio le impostazioni di configurazione) vengono registrati nei log eventi operativi e diagnostici in Applications e Services\Microsoft\FileSync\Management.
2. Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server:
    - Aprire lo snap-in di MMC di Servizi e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.
3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
    - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

Se il problema non è risolto, eseguire lo strumento AFSDiag e inviare l'output del file con estensione zip al tecnico del supporto assegnato al caso per un'ulteriore diagnosi.

Per eseguire AFSDiag, seguire questa procedura.

Per la versione dell'agente V11 e versioni successive:
1. Aprire una finestra di PowerShell con privilegi elevati ed eseguire i comandi seguenti, premendo INVIO dopo ogni comando:

    > [!NOTE]
    >AFSDiag creerà la directory di output e una cartella temporanea al suo interno prima di raccogliere i log ed eliminerà la cartella temporanea dopo l'esecuzione. Specificare un percorso di output che non contenga dati.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Riprodurre il problema. Al termine immettere **D**.
3. Nella directory di output specificata verrà salvato un file con estensione zip contenente i log e i file di traccia. 

Per Agent versione V10 e versioni precedenti:
1. Creare una directory in cui verranno salvati i risultati di AFSDiag, ad esempio C:\Output.
    > [!NOTE]
    >AFSDiag eliminerà tutto il contenuto nella directory di output prima di raccogliere i log. Specificare un percorso di output che non contenga dati.
2. Aprire una finestra di PowerShell con privilegi elevati ed eseguire i comandi seguenti, premendo INVIO dopo ogni comando:

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Per il livello di traccia nella modalità kernel di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
4. Per il livello di traccia nella modalità utente di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
5. Riprodurre il problema. Al termine immettere **D**.
6. Nella directory di output specificata verrà salvato un file con estensione zip contenente i log e i file di traccia.


## <a name="see-also"></a>Vedere anche
- [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)
- [Domande frequenti su File di Azure](storage-files-faq.md)
- [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
