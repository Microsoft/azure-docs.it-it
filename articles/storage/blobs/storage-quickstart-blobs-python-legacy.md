---
title: 'Avvio rapido: Libreria client di Archiviazione BLOB di Azure versione 2.1 per Python'
description: In questa guida introduttiva si creano un account di archiviazione e un contenitore nell'archivio oggetti (BLOB). La libreria client di archiviazione viene usata quindi per Python per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: a01b6e644ce3afda451d94d3e00eda278a645adf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87851325"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Avvio rapido: Gestire i BLOB con Python v2.1 SDK

In questo argomento di avvio rapido viene illustrato come gestire i BLOB con Python. I BLOB sono oggetti che possono contenere grandi quantità di dati di testo o binari, tra cui immagini, documenti, flussi multimediali e dati di archiviazione. Verranno caricati, scaricati ed elencati i BLOB e verranno creati ed eliminati i contenitori.

> [!NOTE]
> In questo argomento di avvio rapido si usa una versione legacy della libreria client di Archiviazione BLOB di Azure. Per un'introduzione alla versione più recente, vedere [Avvio rapido: Gestire i BLOB con Python v12 SDK](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un account dell'Archiviazione di Azure. [Creare un account di archiviazione](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [SDK di Archiviazione di Azure per Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) in questa guida rapida è un'applicazione Python di base.  

Usare il comando [git](https://git-scm.com/) seguente per scaricare l'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Per esaminare il programma Python, aprire il file *example.py* nella radice del repository.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Nell'applicazione, inserire il nome dell'account di archiviazione e la chiave dell'account per creare un oggetto `BlockBlobService`.

1. Aprire il file *example.py* da Esplora soluzioni nell'IDE.

1. Sostituire i valori `accountname` e `accountkey` con il nome e la chiave dell'account di archiviazione:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Salvare e chiudere il file.

## <a name="run-the-sample"></a>Eseguire l'esempio

Il programma di esempio consente di creare un file di test nella cartella *Documenti*, caricare il file nell'archiviazione BLOB, elencare i BLOB nel file e quindi scaricare il file con un nuovo nome.

1. Installare le dipendenze:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Passare all'applicazione di esempio:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Eseguire l'esempio:

    ```console
    python example.py
    ```

    Verranno visualizzati messaggi simili all'output seguente:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Prima di continuare, controllare che nella cartella *Documenti* siano presenti due file.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. È possibile aprirli e vedere che sono identici.

    È anche possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com) per visualizzare i file nell'archivio BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

1. Dopo aver esaminato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test.

## <a name="learn-about-the-sample-code"></a>Informazioni sul codice di esempio

Ora che si conosce il risultato dell'esempio, aprire il file *example.py* per esaminare il codice.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici. Si chiamerà il contenitore `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Prima di tutto, creare i riferimenti agli oggetti usati per accedere all'archivio BLOB e gestirlo. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlockBlobService** che punti al servizio BLOB nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il contenitore a cui si accede. Per organizzare i BLOB, il sistema usa i contenitori in modo analogo a come si usano le cartelle nel computer per organizzare i file.

Una volta creato il contenitore cloud di BLOB, creare un'istanza dell'oggetto **CloudBlockBlob** che punta allo specifico BLOB a cui è interessati. È possibile quindi caricare, scaricare e copiare il BLOB in base alle proprie esigenze.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi possono avere dimensioni pari a 4,7 TB e possono essere qualsiasi tipo di file, da fogli di calcolo di Excel ai file video di grandi dimensioni. È possibile usare i BLOB di accodamento per la registrazione quando si vuole scrivere in un file e quindi continuare ad aggiungere altre informazioni. I BLOB di pagine vengono usati principalmente per i file di disco rigido virtuale che supportano le macchine virtuali dell'infrastruttura distribuita come servizio (IaaS). I BLOB in blocchi sono i BLOB usati più frequentemente. In questa guida di avvio rapido si usano i BLOB in blocchi.

Per caricare un file in un BLOB, ottenere il percorso completo del file unendo il nome della directory con il nome del file nell'unità locale. È quindi possibile caricare il file nel percorso specificato con il metodo `create_blob_from_path`. 

Il codice di esempio crea un file locale che viene usato dal sistema per il caricamento e il download, archiviando il file caricato dal sistema come *full_path_to_file* e il nome del BLOB come *local_file_name*. In questo esempio il file viene caricato in un contenitore denominato `quickstartblobs`:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Nel caso di un flusso di memoria, ad esempio, è possibile usare il metodo `create_blob_from_stream` anziché il metodo `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Il codice seguente crea un `generator` per il metodo `list_blobs`. Il codice scorre l'elenco di BLOB nel contenitore e stampa i relativi nomi nella console.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Scaricare i BLOB


Scaricare i BLOB nel disco locale usando il metodo `get_blob_to_path`.
Il codice seguente consente di scaricare il BLOB caricato in precedenza. Il sistema aggiunge il suffisso *_DOWNLOADED* al nome del BLOB in modo da poter visualizzare entrambi i file nel disco locale.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo `delete_container`. Per eliminare singoli file, usare invece il metodo `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Python con BLOB

Per altre informazioni sullo sviluppo di applicazioni Python con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare, scaricare e installare il [codice sorgente della libreria client Python](https://github.com/Azure/azure-storage-python) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client Python, vedere le [Librerie di Archiviazione di Azure per Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) scritti con la libreria client per Python.

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida introduttiva si è appreso come trasferire file tra il disco locale e un archivio BLOB di Azure con Python. 

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
