---
title: Guida introduttiva di Azure - Creare un BLOB nell'archivio oggetti con Ruby | Microsoft Docs
description: In questa guida introduttiva si creano un account di archiviazione e un contenitore nell'archivio oggetti (BLOB). Si usa quindi la libreria client di archiviazione per Ruby per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8c24c5f043d17b5f0e54ca1c2c6cf41a0d3fe9bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "68726361"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Ruby

Questa guida rapida mostra come usare Ruby per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verificare che siano installati i prerequisiti aggiuntivi seguenti:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [Libreria di Archiviazione di Azure per Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage) usando il pacchetto rubygem: 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio
L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) usata in questa guida rapida è un'applicazione Ruby di base.  

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire l'applicazione Ruby di esempio, cercare la cartella storage-blobs-ruby-quickstart e aprire il file example.rb.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Nell'applicazione è necessario inserire il nome dell'account di archiviazione e la chiave dell'account per creare l'istanza di `BlobService` per l'applicazione. Aprire il file `example.rb` da Esplora soluzioni nell'IDE. Sostituire i valori **accountname** e **accountkey** con il nome e la chiave dell'account. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Eseguire l'esempio
Questo esempio consente di creare un file di test nella cartella "Documenti". Il programma di esempio consente di caricare il file di test nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome. 

Eseguire l'esempio. Il seguente output è un esempio dell'output ricevuto durante l'esecuzione dell'applicazione:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando si preme un tasto qualsiasi per continuare, il programma di esempio elimina il contenitore di archiviazione e i file. Prima di continuare, controllare che nella cartella "Documenti" siano presenti due file. È possibile aprirli e vedere che sono identici.

È anche possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file example.rb per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione
La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlobService** di Archiviazione di Azure per configurare le credenziali di connessione. 
* Creare l'oggetto **Container** che rappresenta il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato CloudBlobContainer, è possibile creare l'oggetto BLOB **Block** che punti al BLOB specifico a cui si è interessati ed esegua operazioni di caricamento, download e copia.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione è possibile configurare un'istanza del client di Archiviazione di Azure, creare istanze dell'oggetto del servizio BLOB, creare un nuovo contenitore e quindi impostare le autorizzazioni sul contenitore in modo che i BLOB siano pubblici. Il contenitore è denominato **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequente e vengono usati in questa guida rapida.  

Per caricare un file in un BLOB, ottenere il percorso completo del file unendo il nome della directory e il nome del file nell'unità locale. È quindi possibile caricare il file nel percorso desiderato usando il metodo **create\_block\_blob()** . 

Il codice di esempio crea un file locale da usare per il caricamento e il download, per archiviare il file da caricare come **file\_path\_to\_file** e il nome del BLOB nel **local\_file\_name**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Per eseguire un aggiornamento parziale del contenuto di un BLOB in blocchi, usare il metodo **create\_block\_list()** . I BLOB in blocchi possono avere dimensioni pari a 4,7 TB e possono essere qualsiasi tipo di file, da fogli di calcolo di Excel ai file video di grandi dimensioni. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. Il BLOB di accodamento deve essere usato in un modello a singolo writer. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere un elenco di file nel contenitore usando il metodo **list\_blobs()** . Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando i nomi dei BLOB trovati in un contenitore.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Scaricare i BLOB

Scaricare i BLOB nel disco locale usando il metodo **get\_blob()** . Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente. Il suffisso "_DOWNLOADED" viene aggiunto al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo **delete\_container()** . È possibile eliminare anche i file creati se non sono più necessari usando il metodo **delete\_blob()** .

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Ruby con BLOB

Per lo sviluppo Ruby con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

- Visualizzare e scaricare il [codice sorgente della libreria client Ruby](https://github.com/Azure/azure-storage-ruby) per Archiviazione di Azure su GitHub.
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) scritti con la libreria client Ruby.

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e Archiviazione BLOB di Azure con Ruby. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](./storage-ruby-how-to-use-blob-storage.md)


Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
