---
title: Risolvere i problemi di File di Azure in Windows
description: Risoluzione dei problemi di File di Azure in Windows. Vedere i problemi comuni relativi alle File di Azure quando ci si connette da client Windows e vedere le possibili soluzioni. Solo per le condivisioni SMB
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878511"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Risolvere i problemi di File di Azure in Windows (SMB)

Questo articolo elenca i problemi comuni correlati a File di Microsoft Azure quando si effettua la connessione da client Windows. L'articolo descrive anche le possibili cause e risoluzioni per tali problemi. Oltre ai passaggi di risoluzione dei problemi in questo articolo, si può anche usare [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) per verificare che l'ambiente client Windows abbia prerequisiti corretti. AzFileDiagnostics automatizza il rilevamento della maggior parte dei sintomi indicati in questo articolo e consente di configurare l'ambiente in modo da ottenere prestazioni ottimali.

> [!IMPORTANT]
> Il contenuto di questo articolo è valido solo per le condivisioni SMB. Per informazioni dettagliate sulle condivisioni NFS, vedere [risolvere i problemi relativi alle condivisioni file NFS di Azure](storage-troubleshooting-files-nfs.md).

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Errore 5 durante il montaggio di una condivisione file di Azure

Quando si tenta di montare una condivisione file, è possibile che si riceva l'errore seguente:

- Errore di sistema 5. Accesso negato.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canale di comunicazione non crittografato

Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dallo stesso data center in cui si trovano tali condivisioni. Le connessioni non crittografate all'interno dello stesso data center possono essere bloccate se l'impostazione [Trasferimento sicuro obbligatorio](../common/storage-require-secure-transfer.md) è abilitata nell'account di archiviazione. Un canale di comunicazione crittografato è disponibile solo se il sistema operativo del client dell'utente supporta la crittografia SMB.

Windows 8, Windows Server 2012 e le versioni successive di ciascuno dei due sistemi operativi negoziano richieste comprendenti SMB 3.0, che supporta la crittografia.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

1. Eseguire la connessione da un client che supporta la crittografia SMB (Windows 8, Windows Server 2012 o versioni successive) o da una macchina virtuale nello stesso data center dell'account di archiviazione di Azure usato per la condivisione file di Azure.
2. Verificare che l'impostazione [trasferimento sicuro richiesto](../common/storage-require-secure-transfer.md) sia disabilitata nell'account di archiviazione se il client non supporta la crittografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: la rete virtuale o le regole del firewall sono abilitate nell'account di archiviazione 

Se nell'account di archiviazione sono configurate regole di rete virtuale o del firewall, verrà negato l'accesso al traffico di rete a meno che all'indirizzo IP o alla rete virtuale client sia consentito l'accesso.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Motivo 3: le autorizzazioni a livello di condivisione non sono corrette quando si usa l'autenticazione basata su identità

Se gli utenti accedono alla condivisione file di Azure usando l'autenticazione Active Directory (AD) o Azure Active Directory Domain Services (Azure AD DS), l'accesso alla condivisione file avrà esito negativo con l'errore "accesso negato" se le autorizzazioni a livello di condivisione non sono corrette. 

### <a name="solution-for-cause-3"></a>Soluzione per la causa 3

Verificare che le autorizzazioni siano configurate correttamente:

- **Active Directory (ad)** vedere [assegnare autorizzazioni a livello di condivisione a un'identità](./storage-files-identity-ad-ds-assign-permissions.md).

    Le assegnazioni delle autorizzazioni a livello di condivisione sono supportate per i gruppi e gli utenti che sono stati sincronizzati dal Active Directory (AD) al Azure Active Directory (Azure AD) utilizzando Azure AD Connect.  Verificare che i gruppi e gli utenti a cui vengono assegnate le autorizzazioni a livello di condivisione non siano gruppi "solo cloud" non supportati.
- **Azure Active Directory Domain Services (Azure AD DS)** vedere [assegnare le autorizzazioni di accesso a un'identità](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Errore 53, Errore 67 o Errore 87 quando si prova a montare o smontare una condivisione file di Azure

Quando si prova a montare una condivisione file da locale o da un data center diverso, potrebbero verificarsi gli errori seguenti:

- Errore di sistema 53. Impossibile trovare il percorso di rete.
- Errore di sistema 67. Impossibile trovare il nome della rete.
- Errore di sistema 87. Parametro non corretto.

### <a name="cause-1-port-445-is-blocked"></a>Motivo 1: la porta 445 è bloccata

L'errore di sistema 53 o 67 può verificarsi se la comunicazione in uscita dalla porta 445 verso un data center di File di Azure è bloccata. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

È possibile verificare se la porta 445 è bloccata dal firewall o dai vincoli dell'ISP con lo strumento [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) o il cmdlet `Test-NetConnection`. 

Per usare il `Test-NetConnection` cmdlet, è necessario installare il modulo Azure PowerShell. per altre informazioni, vedere [Install Azure PowerShell Module](/powershell/azure/install-Az-ps) . Ricordarsi di sostituire `<your-storage-account-name>` e `<your-resource-group-name>` con i nomi pertinenti per il proprio account di archiviazione.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Se la connessione ha avuto esito positivo, verrà visualizzato l'output seguente:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> Il comando precedente restituisce l'indirizzo IP corrente dell'account di archiviazione. Non è garantito che tale indirizzo IP rimanga invariato. Potrebbe cambiare in qualsiasi momento. Non includere l'indirizzo IP come hardcoded in script o nella configurazione di un firewall.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Soluzione 1: usare Sincronizzazione file di Azure
Sincronizzazione file di Azure possibile trasformare l'istanza locale di Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Sincronizzazione file di Azure usa la porta 443 e pertanto può essere usato come soluzione alternativa per accedere a File di Azure dai client in cui la porta 445 bloccata. [Informazioni su come configurare sincronizzazione file di Azure](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Soluzione 2: usare VPN
Impostando una VPN per l'account di archiviazione specifico, il traffico passerà attraverso un tunnel sicuro anziché tramite Internet. Seguire le [istruzioni per configurare la VPN](storage-files-configure-p2s-vpn-windows.md) per accedere a File di Azure da Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Soluzione 3: sbloccare la porta 445 con l'aiuto dell'amministratore IT/ISP
Collaborare con il reparto IT o l'ISP per aprire la porta 445 in uscita per gli [intervalli IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Soluzione 4: usare strumenti basati sull'API REST come Storage Explorer/PowerShell
File di Azure supporta anche REST oltre a SMB. L'accesso a REST viene eseguito sulla porta 443, ovvero su TCP standard. Sono diversi gli strumenti scritti con l'API REST che consentono un'esperienza avanzata dell'interfaccia utente. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) è uno di essi. [Scaricare e installare Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e connettersi alla condivisione file supportata da File di Azure. È anche possibile usare [PowerShell](./storage-how-to-use-files-powershell.md) che è anche l'API REST dell'utente.

### <a name="cause-2-ntlmv1-is-enabled"></a>Motivo 2: NTLMv1 è abilitato

L'errore di sistema 53 o 87 può verificarsi se sul client è abilitata la comunicazione NTLMv1. File di Azure supporta solo l'autenticazione NTLMv2. Con la comunicazione NTLMv1 abilitata, il client è meno sicuro. Di conseguenza, la comunicazione viene bloccata per File di Azure. 

Per determinare se questa è la cause dell'errore, verificare che la seguente sottochiave del registro di sistema non sia impostata su un valore inferiore a 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Per altre informazioni, vedere l'argomento [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) su TechNet.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Ripristinare **LmCompatibilityLevel** sul valore predefinito 3 nella sottochiave seguente del Registro di sistema:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Errore 1816: la quota disponibile per l'elaborazione del comando non è sufficiente

### <a name="cause"></a>Causa

L'errore 1816 si verifica quando si raggiunge il limite massimo di handle aperti simultanei consentiti per un file o una directory nella condivisione file di Azure. Per altre informazioni, vedere [Obiettivi di scalabilità per File di Azure](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Soluzione

Chiudere alcuni degli handle aperti simultaneamente per ridurne il numero e quindi riprovare. Per ulteriori informazioni, vedere [elenco di controllo delle prestazioni e della scalabilità archiviazione di Microsoft Azure](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Per visualizzare gli handle aperti per una condivisione file, una directory o un file, usare il cmdlet di PowerShell [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) .  

Per chiudere gli handle aperti per una condivisione file, una directory o un file, usare il cmdlet di PowerShell [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> I cmdlet Get-AzStorageFileHandle e Close-AzStorageFileHandle sono inclusi nel modulo AZ PowerShell versione 2,4 o successiva. Per installare la versione più recente del modulo AZ PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Errore di "nessun accesso" quando si tenta di accedere o eliminare una condivisione file di Azure  
Quando si tenta di accedere o eliminare una condivisione file di Azure nel portale, è possibile che venga visualizzato l'errore seguente:

Nessun accesso  
Codice errore: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: le regole della rete virtuale o del firewall sono abilitate nell'account di archiviazione

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: l'account utente non ha accesso all'account di archiviazione

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Passare all'account di archiviazione in cui si trova la condivisione file di Azure, fare clic su **Controllo di accesso (IAM)** e verificare che l'account utente abbia accesso all'account di archiviazione. Per altre informazioni, vedere [come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Impossibile modificare, spostare, rinominare o eliminare un file o una directory
Uno degli scopi chiave di una condivisione file è che più utenti e applicazioni possono interagire simultaneamente con i file e le directory nella condivisione. Per supportare questa interazione, le condivisioni file forniscono diversi modi per mediare l'accesso a file e directory.

Quando si apre un file da una condivisione file di Azure montata su SMB, l'applicazione o il sistema operativo richiede un handle di file, ovvero un riferimento al file. Tra le altre cose, l'applicazione specifica una modalità di condivisione file quando richiede un handle di file, che specifica il livello di esclusività dell'accesso al file applicato da File di Azure: 

- `None`: si dispone di accesso esclusivo. 
- `Read`: altri utenti possono leggere il file mentre è aperto.
- `Write`: altri utenti possono scrivere sul file mentre è aperto. 
- `ReadWrite`: combinazione di entrambe le `Read` modalità di `Write` condivisione e.
- `Delete`: altri possono eliminare il file mentre è aperto. 

Sebbene come protocollo senza stato, il protocollo filerest non ha un concetto di handle di file, fornisce un meccanismo simile per mediare l'accesso a file e cartelle che possono essere usati dallo script, dall'applicazione o dal servizio: lease di file. Quando un file viene concesso in lease, viene considerato equivalente a un handle di file con una modalità di condivisione file di `None` . 

Sebbene gli handle di file e i lease richiedano uno scopo importante, a volte gli handle di file e i lease potrebbero essere orfani. In tal caso, è possibile che si verifichino problemi durante la modifica o l'eliminazione di file. È possibile che vengano visualizzati messaggi di errore simili ai seguenti:

- Il processo non può accedere al file perché è in uso da un altro processo.
- Non è possibile completare l'azione perché il file è aperto in un altro programma.
- Il documento è bloccato per la modifica da parte di un altro utente.
- La risorsa specificata è stata contrassegnata per l'eliminazione da un client SMB.

La risoluzione di questo problema dipende dal fatto che questo sia causato da un lease o da un handle di file orfano. 

### <a name="cause-1"></a>Causa 1
Un handle di file impedisce la modifica o l'eliminazione di un file o di una directory. È possibile usare il cmdlet di PowerShell [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) per visualizzare gli handle aperti. 

Se tutti i client SMB hanno chiuso i rispettivi handle aperti su un file o una directory e il problema continua a verificarsi, è possibile forzare la chiusura di un handle di file.

### <a name="solution-1"></a>Soluzione 1
Per forzare la chiusura di un handle di file, usare il cmdlet di PowerShell [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) . 

> [!Note]  
> I cmdlet Get-AzStorageFileHandle e Close-AzStorageFileHandle sono inclusi nel modulo AZ PowerShell versione 2,4 o successiva. Per installare la versione più recente del modulo AZ PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Causa 2
Un lease del file impedisce la modifica o l'eliminazione di un file. È possibile verificare se un file ha un lease di file con il seguente PowerShell, sostituendo `<resource-group>` ,, `<storage-account>` `<file-share>` e `<path-to-file>` con i valori appropriati per l'ambiente:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Se un file ha un lease, l'oggetto restituito deve contenere le proprietà seguenti:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Soluzione 2
Per rimuovere un lease da un file, è possibile rilasciare o interrompere il lease. Per rilasciare il lease, è necessario il valore di LeaseId impostato durante la creazione del lease. Il valore di LeaseId non è necessario per interrompere il lease.

Nell'esempio seguente viene illustrato come interrompere il lease per il file indicato nella ragione 2 (questo esempio continua con le variabili di PowerShell dalla cause 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Rallentamento della copia del file da e verso File di Azure in Windows

Si potrebbe verificare un rallentamento delle prestazioni quando si prova a trasferire file nel servizio File di Azure.

- In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MiB per assicurare prestazioni ottimali.
-   Se si conosce la dimensione finale del file che si vuole estendere con operazioni di scrittura e il software non ha problemi di compatibilità se la parte finale del file non ancora scritta contiene zeri, impostare le dimensioni del file in fase preliminare anziché lasciare che ogni operazione di scrittura venga considerata un'estensione.
-   Usare il metodo di copia corretto:
    -   Usare [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per i trasferimenti tra due condivisioni file.
    -   Usare [Robocopy](./storage-how-to-create-file-share.md) tra condivisioni file in un computer locale.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerazioni per Windows 8.1 o Windows Server 2012 R2

Per i client che eseguono Windows 8.1 o Windows Server 2012 R2, assicurarsi che sia installata la hotfix [KB3114025](https://support.microsoft.com/help/3114025). Questa hotfix consente di migliorare le prestazioni durante la creazione e la chiusura degli handle.

Per verificare se la hotfix è stata installata, è possibile eseguire lo script seguente:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se la hotfix è installata, viene visualizzato l'output seguente:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Da dicembre 2015 la hotfix KB3114025 è installata per impostazione predefinita nelle immagini di Windows Server 2012 R2 presenti in Azure Marketplace.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nessuna cartella con una lettera di unità in "Computer locale" o "questo PC"

Se si esegue il mapping di una condivisione di file di Azure come amministratore tramite il comando net use, la condivisione risulta mancante.

### <a name="cause"></a>Causa

Per impostazione predefinita, Esplora file di Windows non viene eseguito come amministratore. Se si esegue net use da un prompt dei comandi amministrativo, il mapping dell'unità di rete viene eseguito come amministratore. Poiché le unità mappate sono incentrate sull'utente, l'account utente usato per la connessione non ne consente la visualizzazione se tali unità sono state montate con un account utente diverso.

### <a name="solution"></a>Soluzione
Montare la condivisione da una riga di comando senza privilegi di amministratore. In alternativa, è possibile seguire [questo argomento TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) per configurare il valore del registro di sistema **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Il comando net use non viene eseguito se l'account di archiviazione contiene una barra

### <a name="cause"></a>Causa

Il comando net use interpreta una barra (/) come un'opzione della riga di comando. Se il nome dell'account utente inizia con una barra, il mapping dell'unità ha esito negativo.

### <a name="solution"></a>Soluzione

Per risolvere il problema, è possibile attenersi a una delle procedure seguenti:

- Eseguire il comando PowerShell seguente:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Da un file batch, si può eseguire il comando in questo modo:

  `Echo new-smbMapping ... | powershell -command –`

- Racchiudere la chiave tra virgolette doppie, a meno che la barra non sia il primo carattere. Se è il primo carattere, usare la modalità interattiva e immettere la password separatamente oppure rigenerare le chiavi per ottenere una chiave che non inizi con una barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>L'applicazione o il servizio non è in grado di accedere a un'unità di File di Azure montata

### <a name="cause"></a>Causa

Le unità vengono montate per utente. Se l'applicazione o il servizio viene eseguito con un account utente diverso da quello che ha montato l'unità, l'unità non sarà visibile.

### <a name="solution"></a>Soluzione

Usare una delle soluzioni seguenti:

-   Montare l'unità dallo stesso account utente che contiene l'applicazione. Si può usare uno strumento come PsExec.
- Passare il nome e la chiave dell'account di archiviazione nei parametri di nome utente e password del comando net use.
- Usare il comando cmdkey per aggiungere le credenziali in Gestione credenziali. Eseguire questa operazione da una riga di comando nel contesto dell'account del servizio tramite un account di accesso interattivo o utilizzando `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappare direttamente la condivisione, senza usare una lettera di unità mappata. Alcune applicazioni potrebbero non riconnettersi alla lettera di unità in modo corretto, pertanto l'uso del percorso UNC completo è più affidabile. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Dopo aver seguito le istruzioni, è possibile che venga visualizzato il messaggio di errore seguente quando si esegue net use per l'account del servizio di sistema/rete: "Errore di sistema 1312. Una sessione di accesso specificata non esiste. Potrebbe essere già stata terminata." In questo caso, assicurarsi che il nome utente passato a net use includa informazioni di dominio (ad esempio: "[nome account archiviazione].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Errore "You are copying a file to a destination that does not support encryption" (La destinazione in cui si sta copiando il file non supporta la crittografia)

Quando un file viene copiato tramite la rete, viene decrittografato nel computer di origine, trasmesso in testo non crittografato e crittografato nuovamente nella destinazione. Tuttavia, quando si prova a copiare un file crittografato potrebbe essere visualizzato l'errore seguente: "La destinazione in cui si sta copiando il file non supporta la crittografia."

### <a name="cause"></a>Causa
Questo problema può verificarsi se si usa EFS (Encrypting File System). I file crittografati con BitLocker possono essere copiati in File di Azure. Tuttavia, File di Azure non supporta NTFS EFS.

### <a name="workaround"></a>Soluzione alternativa
Per copiare un file tramite rete, è necessario prima decrittografarlo. Utilizzare una delle seguenti modalità:

- Usare il comando **copy /d**. Questo consente di salvare i file crittografati come file decrittografati nella destinazione.
- Impostare la chiave del Registro di sistema seguente:
  - Percorso = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo valore = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valore = 1

Si noti che l'impostazione della chiave del Registro di sistema influisce su tutte le operazioni di copia eseguite nelle condivisioni di rete.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumerazione lenta di file e cartelle

### <a name="cause"></a>Causa

Questo problema può verificarsi se nel computer client non è disponibile una quantità di cache sufficiente per le directory di grandi dimensioni.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, modificare il valore del Registro di sistema **DirectoryCacheEntrySizeMax** per consentire la memorizzazione nella cache di elenchi di directory di maggiori dimensioni nel computer client:

- Percorso: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Nome valore: DirectoryCacheEntrySizeMax 
- Tipo di valore: DWORD
 
 
È ad esempio possibile impostare questo valore su 0x100000 e verificare se si riscontra un miglioramento delle prestazioni.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Errore AadDsTenantNotFound durante l'abilitazione dell'autenticazione del servizio Azure Active Directory dominio (Azure AD DS) per File di Azure "Impossibile individuare i tenant attivi con ID tenant AAD-Tenant-ID"

### <a name="cause"></a>Causa

L'errore AadDsTenantNotFound si verifica quando si tenta di [abilitare l'autenticazione Azure Active Directory Domain Services (Azure AD DS) in file di Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) in un account di archiviazione in cui [Azure ad servizio del dominio (Azure AD DS)](../../active-directory-domain-services/overview.md) non viene creato nel tenant Azure ad della sottoscrizione associata.  

### <a name="solution"></a>Soluzione

Abilitare Azure AD DS nel tenant Azure AD della sottoscrizione in cui è distribuito l'account di archiviazione. Per creare un dominio gestito, è necessario disporre dei privilegi di amministratore del tenant Azure AD. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [creare e configurare un dominio gestito Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Non è possibile montare File di Azure con le credenziali di Active Directory 

### <a name="self-diagnostics-steps"></a>Passaggi di diagnostica automatica
Prima di tutto, assicurarsi di aver seguito tutti e quattro i passaggi per [abilitare l'autenticazione file di Azure ad](./storage-files-identity-auth-active-directory-enable.md).

Secondo, provare [a montare la condivisione file di Azure con la chiave dell'account di archiviazione](./storage-how-to-use-files-windows.md). Se non è stato possibile eseguire il montaggio, scaricare [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) per convalidare l'ambiente in cui viene eseguito il client, rilevare la configurazione del client incompatibile che potrebbe causare un errore di accesso per file di Azure, fornire indicazioni dettagliate sulla correzione automatica e raccogliere le tracce di diagnostica.

In terzo luogo, è possibile eseguire il cmdlet Debug-AzStorageAccountAuth per eseguire un set di controlli di base sulla configurazione di Active Directory con l'utente di Active Directory connesso. Questo cmdlet è supportato nella [versione AzFilesHybrid v 0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). È necessario eseguire questo cmdlet con un utente di AD che disponga dell'autorizzazione di proprietario per l'account di archiviazione di destinazione.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Il cmdlet esegue questi controlli in sequenza e fornisce indicazioni sugli errori:
1. CheckADObjectPasswordIsCorrect: assicurarsi che la password configurata nell'identità di Active Directory che rappresenta l'account di archiviazione corrisponda a quella della chiave kerb1 o kerb2 dell'account di archiviazione. Se la password non è corretta, è possibile eseguire [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) per reimpostare la password. 
2. CheckADObject: verificare che nel Active Directory sia presente un oggetto che rappresenta l'account di archiviazione e abbia il nome SPN corretto (nome dell'entità servizio). Se il nome SPN non è configurato correttamente, eseguire il cmdlet Set-AD restituito nel cmdlet debug per configurare il nome SPN.
3. CheckDomainJoined: verificare che il computer client sia aggiunto a un dominio di Active Directory. Se il computer non è aggiunto a un dominio ad, fare riferimento a questo [articolo](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) per l'istruzione di aggiunta al dominio.
4. CheckPort445Connectivity: verificare che la porta 445 sia aperta per la connessione SMB. Se la porta richiesta non è aperta, consultare lo strumento per la risoluzione dei problemi [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) per problemi di connettività con file di Azure.
5. CheckSidHasAadUser: verificare che l'utente di Active Directory connesso venga sincronizzato con Azure AD. Se si vuole cercare se un utente di Active Directory specifico viene sincronizzato con Azure AD, è possibile specificare il nome utente e il dominio nei parametri di input. 
6. CheckGetKerberosTicket: tentativo di ottenere un ticket Kerberos per connettersi all'account di archiviazione. Se non è disponibile un token Kerberos valido, eseguire il cmdlet klist Get CIFS/storage-account-name. file. Core. Windows. NET ed esaminare il codice di errore per la radice, causando l'errore di recupero del ticket.
7. CheckStorageAccountDomainJoined: verificare se l'autenticazione di Active Directory è stata abilitata e le proprietà di Active Directory dell'account sono popolate. In caso contrario, fare riferimento all' [istruzione seguente](./storage-files-identity-ad-ds-enable.md) per abilitare l'autenticazione di servizi di dominio Active directory su file di Azure. 
8. CheckUserRbacAssignment: verificare se l'utente di Active Directory ha l'assegnazione di ruolo RBAC appropriata per fornire l'autorizzazione a livello di condivisione per accedere File di Azure. In caso contrario, fare riferimento all'istruzione [qui](./storage-files-identity-ad-ds-assign-permissions.md) per configurare l'autorizzazione a livello di condivisione. (Supportato in AzFilesHybrid v 0.2.3 + versione)
9. CheckUserFileAccess: verificare se l'utente di Active Directory dispone dell'autorizzazione file/directory (ACL Windows) appropriata per accedere File di Azure. In caso contrario, fare riferimento all'istruzione [qui](./storage-files-identity-ad-ds-configure-permissions.md) per configurare l'autorizzazione a livello di directory/file. (Supportato in AzFilesHybrid v 0.2.3 + versione)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Impossibile configurare le autorizzazioni a livello di directory/file (ACL di Windows) con Esplora file di Windows

### <a name="symptom"></a>Sintomo

Quando si tenta di configurare gli ACL di Windows con Esplora file in una condivisione file montata, è possibile che si verifichino i sintomi seguenti:
- Dopo aver fatto clic sull'autorizzazione di modifica nella scheda sicurezza, la procedura guidata di autorizzazione non viene caricata. 
- Quando si tenta di selezionare un nuovo utente o gruppo, il percorso del dominio non Visualizza il dominio di servizi di dominio Active Directory appropriato. 

### <a name="solution"></a>Soluzione

È consigliabile usare [lo strumento icacls](/windows-server/administration/windows-commands/icacls) per configurare le autorizzazioni a livello di directory/file come soluzione alternativa. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Errori durante l'esecuzione di Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Errore: "il servizio directory non è stato in grado di allocare un identificatore relativo"

Questo errore può verificarsi se un controller di dominio che dispone del ruolo FSMO master RID non è disponibile o è stato rimosso dal dominio e ripristinato dal backup.  Verificare che tutti i controller di dominio siano in esecuzione e disponibili.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Errore: "Cannot bind positional parameters because no names were given" (Non è possibile associare i parametri posizionali perché non è stato indicato alcun nome)

Probabilmente questo errore viene attivato da un errore di sintassi nel comando join-AzStorageAccountforAuth.  Controllare il comando per rilevare errori di ortografia o di sintassi e verificare che sia installata la versione più recente del modulo AzFilesHybrid https://github.com/Azure-Samples/azure-files-samples/releases) .  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>File di Azure il supporto dell'autenticazione AD DS locale per la crittografia Kerberos AES 256

È stato introdotto il supporto per la crittografia Kerberos AES 256 per File di Azure l'autenticazione di servizi di dominio Active Directory locale con [AzFilesHybrid Module v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Se è stata abilitata l'autenticazione di servizi di dominio Active Directory con una versione del modulo inferiore a v 0.2.2, sarà necessario scaricare il modulo AzFilesHybrid più recente (v 0.2.2 +) ed eseguire PowerShell di seguito. Se l'autenticazione di servizi di dominio Active Directory non è ancora stata abilitata nell'account di archiviazione, è possibile seguire questa [Guida](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) per l'abilitazione. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
