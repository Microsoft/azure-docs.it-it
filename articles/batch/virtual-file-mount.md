---
title: Montare un file system virtuale in un pool
description: Informazioni su come montare un file system virtuale in un pool Batch.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dc5fbdf9ca0df8362a8999856c3f7163dd5e59b9
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626028"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montare un file system virtuale in un pool Batch

Azure Batch supporta il montaggio dell'archiviazione cloud o di un file system esterno nei nodi di calcolo Windows o Linux nei pool di batch. Quando un nodo di calcolo viene aggiunto a un pool, il file system virtuale viene montato e considerato come un'unità locale in tale nodo. È possibile montare diversi tipi di file system, ad esempio File di Azure, archiviazione BLOB di Azure, NFS (Network File System) o anche una [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) o il servizio CIFS (Common Internet file System).

In questo articolo si apprenderà come montare un file system virtuale in un pool di nodi di calcolo usando la [libreria di gestione Batch per .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Il montaggio di un file system virtuale è supportato solo nei pool di batch creati a seguito dell'8 agosto 2019. I pool di batch creati prima di tale data non supportano questa funzionalità.

## <a name="benefits-of-mounting-on-a-pool"></a>Vantaggi del montaggio in un pool

Il montaggio del file system nel pool, anziché consentire alle attività di recuperare i propri dati da un set di dati di grandi dimensioni, rende più semplice e più efficiente l'accesso ai dati necessari da parte delle attività.

Si consideri uno scenario con più attività che richiedono l'accesso a un set di dati comune, ad esempio il rendering di un film. Ogni attività esegue il rendering di uno o più fotogrammi alla volta dai file della scena. Montando un'unità che contiene i file della scena, è più facile per i nodi di calcolo accedere ai dati condivisi.

Inoltre, il file system sottostante può essere scelto e ridimensionato in modo indipendente in base alle prestazioni e alla scalabilità (velocità effettiva e IOPS) richieste dal numero di nodi di calcolo che accedono contemporaneamente ai dati. Ad esempio, è possibile usare una cache [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) in memoria distribuita per supportare il rendering di immagini di movimento di grandi dimensioni con migliaia di nodi di rendering simultanei, accedendo ai dati di origine che risiedono in locale. In alternativa, per i dati che già risiedono nell'archivio BLOB basato sul cloud, è possibile usare [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) per montare questi dati come file system locale. Blobfuse è disponibile solo nei nodi Linux, ma [file di Azure](../storage/files/storage-files-introduction.md) fornisce un flusso di lavoro simile ed è disponibile sia in Windows che in Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montare un file system virtuale in un pool  

Il montaggio di un file system virtuale in un pool rende disponibile il file system per ogni nodo di calcolo del pool. Il file system viene configurato quando un nodo di calcolo viene aggiunto a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Per montare un file system in un pool, creare un oggetto `MountConfiguration`. Scegliere l'oggetto più adatto al file system virtuale in uso: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` o `CifsMountConfiguration`.

Per tutti gli oggetti di configurazione di montaggio sono necessari i parametri di base seguenti. Alcune configurazioni di montaggio hanno parametri specifici per il file system in uso, che verranno descritti in modo più dettagliato negli esempi di codice.

- **Nome account o origine**: per montare una condivisione file virtuale, è necessario indicare il nome dell'account di archiviazione o la relativa origine.
- **Percorso di montaggio relativo o origine**: posizione del file system montato nel nodo di calcolo, relativo alla directory `fsmounts` standard accessibile nel nodo via `AZ_BATCH_NODE_MOUNTS_DIR`. La posizione esatta varia a seconda del sistema operativo usato nel nodo. Ad esempio, la posizione fisica in un nodo Ubuntu viene mappata a `mnt\batch\tasks\fsmounts` e in un nodo CentOS viene mappata a `mnt\resources\batch\tasks\fsmounts`.
- **Opzioni di montaggio o opzioni blobfuse**: queste opzioni descrivono parametri specifici per il montaggio di un file system.

Dopo aver creato l'oggetto `MountConfiguration`, assegnare l'oggetto alla proprietà `MountConfigurationList` quando si crea il pool. Il file system viene montato quando si aggiunge un nodo a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Quando viene montato il file system, viene creata una variabile di ambiente `AZ_BATCH_NODE_MOUNTS_DIR` che punta alla posizione dei file system montati, nonché ai file di log, utili per la risoluzione dei problemi e il debug. I file di log sono descritti più in dettaglio nella sezione [Diagnosticare gli errori di montaggio](#diagnose-mount-errors).  

> [!IMPORTANT]
> Il numero massimo di file system montati in un pool è 10. Per dettagli e altri limiti, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md#other-limits).

## <a name="examples"></a>Esempi

Gli esempi di codice seguenti illustrano il montaggio di diverse condivisioni file in un pool di nodi di calcolo.

### <a name="azure-files-share"></a>Condivisione di File di Azure

File di Azure è l'offerta standard di file system cloud di Azure. Per altre informazioni su come ottenere uno dei parametri dell'esempio di codice per la configurazione di montaggio, vedere l'articolo sull'[uso di una condivisione di File di Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>File system di BLOB di Azure

Un'altra opzione è usare l'archiviazione BLOB di Azure con [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Il montaggio di un file system BLOB richiede un `AccountKey` o `SasKey` per l'account di archiviazione. Per informazioni su come ottenere queste chiavi, vedere [gestire le chiavi di accesso all'account di archiviazione](../storage/common/storage-account-keys-manage.md) o [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md). Per ulteriori informazioni e suggerimenti sull'utilizzo di blobfuse, vedere blobfuse.

Per ottenere l'accesso predefinito alla directory blobfuse montata, eseguire l'attività come **amministratore**. Blobfuse monta la directory nello spazio utente e al momento della creazione del pool viene montata come radice. In Linux tutte le attività di **amministratore** sono radice. Tutte le opzioni per il modulo FUSE sono descritte nella [pagina di riferimento di fuse](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Per altre informazioni e suggerimenti sull'uso di blobfuse, vedere le [domande frequenti sulla risoluzione dei problemi](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) . È anche possibile esaminare [i problemi di GitHub nel repository blobfuse](https://github.com/Azure/azure-storage-fuse/issues) per verificare i problemi e le risoluzioni correnti di blobfuse.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS (Network File System)

È possibile montare i file System di rete (NFS) nei nodi del pool, in modo da consentire l'accesso ai file System tradizionali Azure Batch. Si può usare un singolo server NFS distribuito nel cloud o un server NFS locale a cui si accede attraverso una rete virtuale. In alternativa, è possibile usare la soluzione per la cache in memoria distribuita [vFXT](../avere-vfxt/avere-vfxt-overview.md) per le attività HPC (High Performance Computing) con utilizzo intensivo di dati.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>CIFS (Common Internet File System)

Il montaggio di [Common Internet file System (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) in nodi del pool è un altro modo per fornire accesso ai file System tradizionali. CIFS è un protocollo di condivisione file che offre un meccanismo aperto e multipiattaforma per la richiesta di file e servizi del server di rete. CIFS è basato sulla versione migliorata del protocollo [SMB (Server Message Block)](/windows-server/storage/file-server/file-server-smb-overview) per la condivisione di file Internet e Intranet e può essere usato per montare file System esterni nei nodi Windows.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticare gli errori di montaggio

Se una configurazione di montaggio ha esito negativo, il nodo di calcolo nel pool avrà esito negativo e lo stato del nodo verrà impostato su `unusable` . Per diagnosticare un errore di configurazione di montaggio, esaminare la proprietà [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) per informazioni dettagliate sull'errore.

Per recuperare i file di log per il debug, usare [OutputFiles](batch-task-output-files.md) per caricare i file di `*.log`. I file di `*.log` contengono informazioni sul montaggio del file system nella posizione `AZ_BATCH_NODE_MOUNTS_DIR`. Il formato dei file di log di montaggio è `<type>-<mountDirOrDrive>.log` per ogni montaggio. Ad esempio, un montaggio `cifs` in una directory di montaggio denominata `test` avrà un file di log di montaggio denominato `cifs-test.log`.

## <a name="supported-skus"></a>SKU supportati

| Editore | Offerta | SKU | Condivisione di File di Azure | Blobfuse | Montaggio NFS | Montaggio CIFS |
|---|---|---|---|---|---|---|
| o batch | rendering-centos73 | rendering | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Nota: Supporta l'archiviazione A_8 o 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>Requisiti di rete

Quando si usano i montaggi di file virtuali con [Azure batch pool in una rete virtuale](batch-virtual-network.md), tenere presenti i requisiti seguenti e assicurarsi che non sia bloccato il traffico necessario.

- **File di Azure**:
  - Richiede che la porta TCP 445 sia aperta per il traffico verso/dal tag di servizio "archiviazione". Per altre informazioni, vedere [usare una condivisione file di Azure con Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Blobfuse**:
  - Richiede che la porta TCP 443 sia aperta per il traffico verso/dal tag di servizio "archiviazione".
  - Le macchine virtuali devono poter accedere a per https://packages.microsoft.com scaricare i pacchetti blobfuse e gpg. A seconda della configurazione, potrebbe essere necessario accedere anche ad altri URL per scaricare pacchetti aggiuntivi.
- **NFS (Network File System)**:
  - Richiede l'accesso alla porta 2049 (per impostazione predefinita, la configurazione potrebbe avere altri requisiti).
  - Per scaricare il pacchetto nfs-common (per Debian o Ubuntu) o nfs-utils (per CentOS), le macchine virtuali devono avere accesso a gestione pacchetti appropriato. Questo URL può variare in base alla versione del sistema operativo. A seconda della configurazione, potrebbe essere necessario accedere anche ad altri URL per scaricare pacchetti aggiuntivi.
- **Common Internet file System (CIFS)**:
  - Richiede l'accesso alla porta TCP 445.
  - Per scaricare il pacchetto cifs-utils, le macchine virtuali devono avere accesso a gestione pacchetti appropriati. Questo URL può variare in base alla versione del sistema operativo.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul montaggio di una condivisione di File di Azure con [Windows](../storage/files/storage-how-to-use-files-windows.md) o [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Informazioni sull'uso e sul montaggio di file system virtuali [blobfuse](https://github.com/Azure/azure-storage-fuse).
- Per informazioni su NFS e sulle relative applicazioni, vedere [Panoramica di NFS (Network File System)](/windows-server/storage/nfs/nfs-overview).
- Per altre informazioni su CIFS, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview).
