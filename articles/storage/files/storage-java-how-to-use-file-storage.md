---
title: Sviluppare per File di Azure con Java | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi Java che usano File di Azure per archiviare i dati dei file.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837467"
---
# <a name="develop-for-azure-files-with-java"></a>Sviluppare per File di Azure con Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra le nozioni di base per l'uso di Java per sviluppare applicazioni o servizi che usano File di Azure per archiviare i dati dei file. In questa esercitazione verrà creata un'applicazione console e verrà mostrato come eseguire le azioni di base con Java e File di Azure:

* Creare ed eliminare condivisioni file di Azure
* Creare ed eliminare directory
* Enumerare file e directory in una condivisione file di Azure
* Caricare, scaricare ed eliminare un file

> [!Note]  
> Poiché File di Azure è accessibile tramite SMB, è possibile scrivere semplici applicazioni che accedono alla condivisione file di Azure usando le classi I/O standard di Java. Questo articolo illustra come scrivere applicazioni che usano l'SDK Java di Archiviazione di Azure, che usa l'[API REST di File di Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) per comunicare con File di Azure.

## <a name="create-a-java-application"></a>Creare un'applicazione Java
Per compilare gli esempi, sono necessari Java Development Kit (JDK) e [Azure Storage SDK per Java.](https://github.com/Azure/azure-storage-java) È inoltre necessario aver creato un account di archiviazione di Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurare l'applicazione per usare File di Azure
Per utilizzare le API di archiviazione di Azure, aggiungere le seguenti istruzioni all'inizio del file Java da cui si desidera accedere al servizio di archiviazione.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Impostare una stringa di connessione di archiviazione di Azure
Per usare File di Azure, è necessario connettersi all'account di archiviazione di Azure. Il primo passaggio consiste nel configurare una stringa di connessione che verrà usata per connettersi all'account di archiviazione. È importante definire una variabile statica a tale scopo.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Sostituire your_storage_account_name e your_storage_account_key con i valori effettivi dell'account di archiviazione.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Connessione a un account di archiviazione di Azure
Per connettersi all'account di archiviazione, è necessario usare l'oggetto **CloudStorageAccount**, passando una stringa di connessione al relativo metodo **parse**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** genera un'eccezione InvalidKeyException, sarà quindi necessario inserirlo in un blocco Try-Catch.

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Tutti i file e le directory in File di Azure si trovano in un contenitore denominato **Condivisione**. L'account di archiviazione può disporre del numero di condivisioni consentite dalla capacità dell'account. Per ottenere l'accesso a una condivisione e ai relativi contenuti, è necessario usare un client di File di Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

In questo modo è possibile ottenere un riferimento a una condivisione.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Per creare effettivamente la condivisione, utilizzare il metodo **createIfNotExists** dell'oggetto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

A questo punto, **share** contiene un riferimento a una condivisione denominata **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Eliminare una condivisione file di Azure
L'eliminazione di una condivisione viene eseguita chiamando il metodo **deleteIfExists** in un oggetto CloudFileShare. Ecco il codice di esempio che esegue tale operazione.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Creare una directory
È possibile anche organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. In File di Azure è possibile creare tutte le directory consentite dall'account. Il codice seguente creerà una sottodirectory denominata **sampledir** nella directory radice.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Eliminare una directory
Eliminare una directory è un'attività semplice, anche se occorre tenere presente che non è possibile eliminare una directory che contiene ancora file o altre directory.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure
Ottenere un elenco di file e directory all'interno di una condivisione è facile chiamando **listFilesAndDirectories** in un riferimento CloudFileDirectory. Il metodo restituisce un elenco di oggetti ListFileItem che è possibile scorrere. Ad esempio, il seguente codice elencherà i file e le directory all'interno della directory radice.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Caricare un file
In questa sezione verrà illustrato come caricare un file dall'archiviazione locale nella directory radice di una condivisione.

Il primo passaggio del caricamento di un file consiste nell'ottenere un riferimento alla directory in cui risiederà. È possibile eseguire questa operazione chiamando il metodo **getRootDirectoryReference** dell'oggetto condivisione.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Ora che si dispone di un riferimento alla directory radice della condivisione, è possibile caricarvi un file mediante il codice seguente.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Scaricare un file
Una delle operazioni più frequenti che verranno eseguite in File di Azure consiste nello scaricare i file. Nell'esempio seguente, il codice scarica SampleFile.txt e ne visualizza il contenuto.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Eliminare un file
Un'altra operazione comunemente eseguita in File di Azure è l'eliminazione dei file. Il codice seguente elimina un file denominato SampleFile.txt memorizzato all'interno di una directory denominata **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su altre API di archiviazione di Azure, seguire i collegamenti seguenti.

* [Azure per sviluppatori Java](/java/azure)/)
* [Azure Storage SDK per Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK per Android](https://github.com/azure/azure-storage-android)
* [Riferimento all'SDK del client di archiviazione di Azure](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [API REST di Servizi di archiviazione di AzureAzure Storage Services REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog del team di Archiviazione di AzureAzure Storage Team Blog](https://blogs.msdn.com/b/windowsazurestorage/)
* [Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md)
* [Risoluzione dei problemi di File di Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
