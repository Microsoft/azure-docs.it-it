---
title: Procedura per distribuire File di Azure | Microsoft Docs
description: Informazioni su come distribuire di File di Azure dall'inizio alla fine.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77598783"
---
# <a name="how-to-deploy-azure-files"></a>Come distribuire i file di Azure
[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud accessibili tramite il protocollo SMB standard di settore. Questo articolo illustra come distribuire praticamente File di Azure all'interno dell'organizzazione.

È consigliabile leggere [Planning for an Azure Files deployment](storage-files-planning.md) (Pianificazione della distribuzione di File di Azure) prima di seguire la procedura descritta in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente abbia già completato i passaggi seguenti:

- Creazione di un account di Archiviazione di Azure con le opzioni di resilienza e crittografia desiderate, nell'area desiderata. Vedere [Creare un account di archiviazione](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per istruzioni dettagliate su come creare un account di archiviazione.
- Creazione di una condivisione file di Azure con la quota desiderata nell'account di archiviazione. Vedere [Creare una condivisione file](storage-how-to-create-file-share.md) per istruzioni dettagliate su come creare una condivisione file.

## <a name="transfer-data-into-azure-files"></a>Trasferire i dati in File di Azure
A volte può essere necessario eseguire la migrazione di condivisioni file esistenti, come quelle archiviate in locale, nella nuova condivisione file di Azure. In questa sezione viene illustrato come spostare i dati in una condivisione file di Azure tramite diversi metodi comuni descritti in dettaglio nella [Guida alla pianificazione](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) ed è possibile scegliere tutte le cache necessarie in tutto il mondo.

Sincronizzazione file di Azure può essere usato per eseguire la migrazione di dati in una condivisione file di Azure, anche se il meccanismo di sincronizzazione non è necessario per l'uso a lungo termine. Altre informazioni su come usare Sincronizzazione file di Azure per trasferire dati nella condivisione file di Azure sono disponibili in [Pianificazione di una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importazione/Esportazione di Azure
Il servizio Importazione/Esportazione di Azure permette di trasferire in tutta sicurezza grandi quantità di dati in una condivisione file di Azure tramite la spedizione di dischi rigidi a un data center di Azure. Per altre informazioni sul servizio vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione di Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!Note]  
> Il servizio Importazione/Esportazione di Azure attualmente non supporta l'esportazione dei file da una condivisione file di Azure.

La procedura seguente importerà dati da un percorso locale nella condivisione file di Azure.

1. Ottenere il numero di dischi rigidi richiesto da inviare ad Azure. I dischi rigidi possono avere qualsiasi dimensione, ma devono avere un'unità SSD o HDD di 2,5" o 3,5" che supporti lo standard SATA II o III. 

2. Collegare e montare ciascun disco nel server o nel PC eseguendo il trasferimento dei dati. Per prestazioni ottimali, è consigliabile eseguire il processo di esportazione locale nel server locale che contiene i dati. In alcuni casi, ad esempio quando il file server che invia i dati è un dispositivo NAS, potrebbe non essere possibile eseguire questa operazione. In tal caso, è accettabile montare ciascun disco su un PC.

3. Verificare che ogni unità sia online, inizializzata e che le sia stata assegnata una lettera di unità. Per portare un'unità online, inizializzare e assegnare una lettera di unità, aprire lo snap-in di MMC di Gestione disco, ovvero diskmgmt.msc.

    - Per portare un disco online, se non lo è già, fare clic con il pulsante destro del mouse sul disco nel riquadro in basso della MMC di Gestione disco e selezionare "Online".
    - Per inizializzare un disco, fare clic con il pulsante destro del mouse sul disco nel riquadro in basso, quando il disco è online, e selezionare "Inizializza". Assicurarsi di selezionare "GPT" quando viene richiesto.

        ![Una schermata del menu Inizializza disco in MMC di Gestione disco](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Per assegnare una lettera di unità al disco, fare clic con il pulsante destro del mouse sullo spazio "non allocato" del disco online e inizializzato e fare clic su "Nuovo volume semplice". In questo modo sarà possibile assegnare una lettera di unità. Si noti che non è necessario formattare il volume perché questa operazione verrà eseguita in un secondo momento.

        ![Una schermata della procedura guidata Nuovo volume semplice in MMC di Gestione disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Creare il file CSV dataset. Il file CSV dataset è un mapping tra il percorso dei dati in locale e la condivisione file di Azure desiderata in cui copiare i dati. Ad esempio, il file CSV dataset seguente esegue il mapping di una condivisione file locale ("F:\shares\scratch") a una condivisione file di Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    È possibile specificare più condivisioni con un account di archiviazione. Per altre informazioni vedere [Preparare il file CSV dataset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

5. Creare il file CSV driveset. Il file CSV driveset elenca i dischi disponibili per l'agente di esportazione locale. Ad esempio, il seguente file CSV driveset elenca le unità `X:`, `Y:` e `Z:` da usare nel processo di esportazione locale:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Per altre informazioni vedere [Preparare il file CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

6. Usare lo [strumento WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) per copiare i dati in uno o più dischi rigidi.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Non modificare i dati sulle unità disco rigido o sul file journal dopo aver completato la preparazione del disco.

7. [Creare un processo di importazione](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy è un noto strumento di copia incluso in Windows e Windows Server. Robocopy può essere usato per trasferire i dati in File di Azure montando la condivisione file in locale e quindi usando il percorso montato come destinazione del comando Robocopy. L'uso di Robocopy è piuttosto semplice:

1. [Montare la condivisione file di Azure](storage-how-to-use-files-windows.md). Per ottenere prestazioni ottimali, è consigliabile montare la condivisione file di Azure in locale nel server che contiene i dati. In alcuni casi, ad esempio quando il file server che invia i dati è un dispositivo NAS, potrebbe non essere possibile eseguire questa operazione. In questo caso, è perfettamente accettabile montare la condivisione file di Azure in un PC. In questo esempio `net use` viene usato nella riga di comando per montare la condivisione file:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Usare `robocopy` nella riga di comando per spostare i dati nella condivisione file di Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy dispone di un numero significativo di opzioni per modificare il comportamento di copia in base alle esigenze. Per altre informazioni, vedere la pagina di manuale di [Robocopy](https://technet.microsoft.com/library/cc733145.aspx).

### <a name="azcopy"></a>AzCopy
AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso File di Azure, oltre ad Archiviazione BLOB di Azure usando semplici comandi con prestazioni ottimali. Usare AzCopy è semplice:

1. Scaricare la [versione più recente di AzCopy in Windows](https://aka.ms/downloadazcopy) o in [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Usare `azcopy` nella riga di comando per spostare i dati nella condivisione file di Azure. La sintassi in Windows è la seguente: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    In Linux, la sintassi del comando è leggermente diversa:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy dispone di un numero significativo di opzioni per modificare il comportamento di copia in base alle esigenze. Per altre informazioni, vedere [AzCopy in Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy in Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montare automaticamente nei PC o nei server necessari
Per sostituire una condivisione file locale, è utile per pre-montare le condivisioni nei computer su cui verrà usata. Questa operazione può essere eseguita automaticamente in un elenco di computer.

> [!Note]  
> Il montaggio di una condivisione file di Azure richiede l'uso della chiave dell'account di archiviazione come password e di conseguenza è consigliabile montarla solo in ambienti attendibili. 

### <a name="windows"></a>Windows
PowerShell può essere usato eseguire il comando di montaggio in più PC. Nell'esempio seguente, `$computers` viene compilato manualmente, ma è possibile generare l'elenco dei computer da montare automaticamente. Ad esempio, è possibile compilare questa variabile con i risultati di Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Un semplice script bash combinato con SSH può restituire lo stesso risultato nell'esempio seguente. La variabile `$computer` viene lasciata allo stesso modo alla compilazione dell'utente:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
