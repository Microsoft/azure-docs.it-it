---
title: 'Guida introduttiva: Archiviazione BLOB di Azure libreria v12 - .NET'
description: In questa guida introduttiva si apprenderà come usare la libreria client Archiviazione BLOB di Azure versione 12 per .NET per creare un contenitore e un BLOB nell'archiviazione BLOB (oggetto). Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: twooley
ms.author: twooley
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f913b33d0bea425a24d2fd336c9d065978606e82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869258"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Guida introduttiva: Archiviazione BLOB di Azure libreria client v12 per .NET

Introduzione alla libreria Archiviazione BLOB di Azure client v12 per .NET. Archiviazione BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati.

Usare la Archiviazione BLOB di Azure client v12 per .NET per:

* Creare un contenitore
* Caricare un oggetto BLOB in Archiviazione di Azure
* Elencare tutti i BLOB in un contenitore
* Scaricare il BLOB nel computer locale
* Eliminare un contenitore

Risorse aggiuntive:

* [Documentazione di riferimento delle API](/dotnet/api/azure.storage.blobs)
* [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Esempi](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](../common/storage-account-create.md)
* Versione aggiornata di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso. Assicurarsi di ottenere l'SDK e non il runtime.

## <a name="setting-up"></a>Configurazione

In questa sezione viene illustrata la preparazione di un progetto per l'Archiviazione BLOB di Azure della libreria client v12 per .NET.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione .NET Core denominata *BlobQuickstartV12*.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando `dotnet new` per creare una nuova app console con il nome *BlobQuickstartV12*. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Passare alla directory *BlobQuickstartV12* creata.

   ```console
   cd BlobQuickstartV12
   ```

1. Nella directory *BlobQuickstartV12* creare un'altra directory denominata *data*. Qui verranno creati e archiviati i file di dati BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory dell'applicazione, installare la libreria client di Archiviazione BLOB di Azure per il pacchetto .NET usando il comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire il file *Program.cs* nell'editor.
1. Rimuovere l'istruzione `Console.WriteLine("Hello World!");` .
1. Aggiungere `using` direttive .
1. Aggiornare la `Main` dichiarazione del metodo per supportare async.

    Ecco il codice:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Il servizio Archiviazione BLOB di Azure è ottimizzato per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blobs-introduction/blob1.png)

Per interagire con queste risorse, usare le classi .NET seguenti:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): la classe `BlobContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): la classe `BlobDownloadInfo` rappresenta le proprietà e il contenuto restituiti dal download di un BLOB.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le operazioni seguenti con la Archiviazione BLOB di Azure client per .NET:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore GUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient). Chiamare quindi il metodo [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) per creare il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Creare un file di testo nella directory *data* locale.
1. Ottenere un riferimento a un oggetto [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) chiamando il metodo [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) sul contenitore dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare il file di testo locale nel BLOB chiamando il metodo [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_). Questo metodo crea il BLOB se non esiste o lo sovrascrive se esiste già.

Aggiungere questo codice alla fine del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore chiamando il metodo [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice alla fine del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync). Il codice di esempio aggiunge il suffisso "DOWNLOADED" al nome del file in modo da consentire la visualizzazione di entrambi i file nel file system locale.

Aggiungere questo codice alla fine del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente elimina le risorse create dall'app eliminando l'intero contenitore usando [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync). Elimina anche i file locali creati dall'app.

L'app viene sospesa per l'input dell'utente chiamando `Console.ReadLine` prima dell'eliminazione di BLOB, contenitore e file locali. Si tratta di una valida opportunità per verificare che le risorse siano state effettivamente create correttamente, prima che vengano eliminate.

Aggiungere questo codice alla fine del metodo `Main`:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea un file di test nella cartella *data* locale e lo carica nell'archiviazione BLOB. L'esempio elenca quindi i BLOB presenti nel contenitore e scarica il file con un nuovo nome per consentire il confronto tra i nuovi file e quelli precedenti.

Passare alla directory dell'applicazione, quindi compilarla ed eseguirla.

```console
dotnet build
```

```console
dotnet run
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Prima di iniziare il processo di pulizia, controllare che nella cartella *data* siano presenti due file. È possibile aprirli e verificare che sono identici.

Dopo aver verificato i file, premere **INVIO** per eliminare i file di test e completare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come caricare, scaricare ed elencare i BLOB con .NET.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Archiviazione BLOB di Azure SDK v12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Per esercitazioni, esempi, guide introduttive e altra documentazione, vedere [Azure per gli sviluppatori .NET e .NET Core](/dotnet/azure/).
* Per altre informazioni su .NET Core, vedere [Get started with .NET in 10 minutes](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro) (Introduzione a .NET in 10 minuti).
