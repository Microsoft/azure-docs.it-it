---
title: 'Guida introduttiva: Libreria di Archiviazione BLOB di Azure v12 - Python'
description: In questa guida di avvio rapido verrà illustrato come usare la libreria client di Archiviazione BLOB di Azure versione 12 per Python per creare un contenitore e un BLOB nell'archiviazione (oggetto) BLOB. Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061346"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Guida introduttiva: Gestire i BLOB con Python v12 SDK

In questo argomento di avvio rapido viene illustrato come gestire i BLOB con Python. I BLOB sono oggetti che possono contenere grandi quantità di dati di testo o binari, tra cui immagini, documenti, flussi multimediali e dati di archiviazione. Verranno caricati, scaricati ed elencati i BLOB e verranno creati ed eliminati i contenitori.

[Documentazione di riferimento delle API](/python/api/azure-storage-blob) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-storage-blob/) | [Esempi](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un account dell'Archiviazione di Azure. [Creare un account di archiviazione](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva

> [!NOTE]
> Per iniziare a usare la versione precedente dell'SDK, vedere [Avvio rapido: Gestire i BLOB con Python v2.1 SDK](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione BLOB di Azure v12 per Python.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione Python denominata *blob-quickstart-v12*.

1. In una finestra della console, ad esempio cmd, PowerShell o Bash, creare una nuova directory per il progetto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Passare alla directory *blob-quickstart-v12* appena creata.

    ```console
    cd blob-quickstart-v12
    ```

1. Nella directory *blob-quickstart-v12* creare un'altra directory denominata *data*. Qui verranno creati e archiviati i file di dati BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory dell'applicazione installare la libreria client di Archiviazione BLOB di Azure per il pacchetto Python usando il comando `pip install`.

```console
pip install azure-storage-blob
```

Questo comando installa il pacchetto della libreria client di Archiviazione BLOB di Azure per Python e tutte le librerie da cui dipende. In questo caso, si tratta solo della libreria principale di Azure per Python.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un nuovo file di testo nell'editor del codice
1. Aggiungere le istruzioni `import`
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

    Ecco il codice:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Salvare il nuovo file come *blob-quickstart-v12.py* nella directory *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blobs-introduction/blob1.png)

Per interagire con queste risorse, usare le classi Python seguenti:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): la classe `ContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le operazioni seguenti con la libreria client di Archiviazione BLOB di Azure per Python:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del blocco `try`:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore UUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chiamando il metodo [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Infine, chiamare il metodo [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) per creare effettivamente il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine del blocco `try`:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Creare un file di testo nella directory locale.
1. Ottenere un riferimento a un oggetto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chiamando il metodo [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) su [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare il file di testo locale nel BLOB chiamando il metodo [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Aggiungere questo codice alla fine del blocco `try`:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB presenti nel contenitore chiamando il metodo [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice alla fine del blocco `try`:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). Il codice di esempio aggiunge il suffisso "DOWNLOAD" al nome del file in modo da consentire la visualizzazione di entrambi i file nel file system locale.

Aggiungere questo codice alla fine del blocco `try`:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente pulisce le risorse create dall'app eliminando l'intero contenitore tramite il metodo [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). È anche possibile eliminare i file locali, se si vuole.

L'app viene sospesa per l'input dell'utente chiamando `input()` prima dell'eliminazione di BLOB, contenitore e file locali. Si tratta di una valida opportunità per verificare che le risorse siano state create correttamente, prima che vengano eliminate.

Aggiungere questo codice alla fine del blocco `try`:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea un file di test nella cartella locale e lo carica nell'archiviazione BLOB. L'esempio elenca quindi i BLOB presenti nel contenitore e scarica il file con un nuovo nome per consentire il confronto tra i nuovi file e quelli precedenti.

Passare alla directory contenente il file *blob-quickstart-v12.py*, quindi usare questo comando `python` per eseguire l'app.

```console
python blob-quickstart-v12.py
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Prima di iniziare il processo di pulizia, controllare che nella cartella *data* siano presenti due file. È possibile aprirli e verificare che sono identici.

Dopo aver verificato i file, premere **INVIO** per eliminare i file di test e completare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come caricare, scaricare ed elencare i BLOB con Python.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Esempi dell'SDK di Archiviazione BLOB di Azure v12 per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Per altre informazioni, vedere [Azure SDK per Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Per esercitazioni, esempi, guide di avvio rapido e altre documentazioni, vedere [Azure per sviluppatori Python](/azure/python/).
