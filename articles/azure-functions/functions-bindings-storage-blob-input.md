---
title: Binding di input di archiviazione BLOB di Azure per funzioni di Azure
description: Informazioni su come fornire dati di binding di input di archiviazione BLOB di Azure a una funzione di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 191722d02b493cfe0197c3e45771543fd8c5926a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961048"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Binding di input di archiviazione BLOB di Azure per funzioni di Azure

L'associazione di input consente di leggere i dati di archiviazione BLOB come input per una funzione di Azure.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-storage-blob.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente è una [funzione in C#](functions-dotnet-class-library.md) che usa un trigger della coda e un'associazione di input BLOB. Il messaggio della coda contiene il nome del BLOB e la funzione registra la dimensione del BLOB.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [script C# (.csx)](functions-reference-csharp.md) che usa le associazioni. La funzione crea una copia di un BLOB di testo. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Questa sezione contiene gli esempi seguenti:

* [Trigger HTTP, ricerca del nome BLOB da una stringa di query](#http-trigger-look-up-blob-name-from-query-string)
* [Trigger di coda, ricezione del nome BLOB da un messaggio in coda](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Trigger HTTP, ricerca del nome BLOB da una stringa di query

 L'esempio seguente mostra una funzione Java che usa l'annotazione `HttpTrigger` per ricevere un parametro che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Trigger di coda, ricezione del nome BLOB da un messaggio in coda

 L'esempio seguente mostra una funzione Java che usa l'annotazione `QueueTrigger` per ricevere un messaggio che contiene il nome di un file in un contenitore di archiviazione BLOB. L'annotazione `BlobInput` legge quindi il file e passa il relativo contenuto alla funzione come `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare `@BlobInput` l'annotazione per i parametri il cui valore deriva da BLOB.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [JavaScript](functions-reference-node.md) che usa le associazioni. La funzione crea una copia di un BLOB. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Nell'esempio seguente viene illustrata un'associazione di input BLOB, definita nella _function.jssu_ file, che rende disponibili i dati BLOB in ingresso per la funzione di [PowerShell](functions-reference-powershell.md) .

Ecco la configurazione JSON:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Ecco il codice della funzione:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

L'esempio seguente mostra associazioni di input e di output di BLOB in un file *function.json* e codice [Python](functions-reference-python.md) che usa le associazioni. La funzione crea una copia di un BLOB. La funzione viene attivata da un messaggio della coda che contiene il nome del BLOB da copiare. Il nuovo BLOB è denominato *{originalblobname}-Copy*.

Nel file *function.json* viene usata la proprietà dei metadati `queueTrigger` per specificare il nome del BLOB nelle proprietà `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

La `dataType` proprietà determina quale associazione viene utilizzata. Per supportare strategie di associazione diverse sono disponibili i valori seguenti:

| Valore di binding | Predefinito | Descrizione | Esempio |
| --- | --- | --- | --- |
| `string` | N | Usa un'associazione generica e esegue il cast del tipo di input come `string` | `def main(input: str)` |
| `binary` | N | Usa l'associazione generica e esegue il cast del BLOB di input come `bytes` oggetto Python | `def main(input: bytes)` |

Se la `dataType` proprietà non è definita in function.json, il valore predefinito è `string` .

Ecco il codice Python:

```python
import logging
import azure.functions as func


# The type func.InputStream is not supported for blob input binding.
# The input binding field inputblob can either be 'bytes' or 'str' depends
# on dataType in function.json, 'binary' or 'string'.
def main(queuemsg: func.QueueMessage, inputblob: bytes) -> bytes:
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Il costruttore dell'attributo accetta il percorso del BLOB e un parametro `FileAccess` che indica la lettura o la scrittura, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere [trigger-attributi e annotazioni](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="java"></a>[Java](#tab/java)

L' `@BlobInput` attributo consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary` . Per informazioni dettagliate, vedere l' [esempio di input](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Gli attributi non sono supportati da PowerShell.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Blob`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `blob`. |
|**direction** | n/d | Il valore deve essere impostato su `in`. Le eccezioni sono indicate nella sezione [usage](#usage). |
|**nome** | n/d | Nome della variabile che rappresenta il BLOB nel codice della funzione.|
|**path** |**BlobPath** | Percorso del BLOB. |
|**connection** |**Connection**| Nome di un'impostazione dell'app che contiene la [stringa di connessione di archiviazione](../storage/common/storage-configure-connection-string.md) da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome. Se ad esempio si imposta `connection` su "Storage", il runtime di funzioni Cerca un'impostazione dell'app denominata "AzureWebJobsMyStorage". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>La stringa di connessione deve essere relativa a un account di archiviazione di uso generico, non a un [account di archiviazione solo BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Se si usa [la versione 5. x o una versione successiva dell'estensione](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), anziché una stringa di connessione, è possibile fornire un riferimento a una sezione di configurazione che definisce la connessione. Vedere [connessioni](./functions-reference.md#connections).|
|**dataType**| n/d | Per i linguaggi tipizzati in modo dinamico, specifica il tipo di dati sottostante. I valori possibili sono `string`, `binary` o `stream`. Per ulteriori dettagli, fare riferimento ai [concetti relativi a trigger e associazioni](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions). |
|n/d | **Accesso** | Indica se eseguire la lettura o la scrittura. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

L' `@BlobInput` attributo consente di accedere al BLOB che ha attivato la funzione. Se si usa una matrice di byte con l'attributo, impostare `dataType` su `binary` . Per informazioni dettagliate, vedere l' [esempio di input](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accedere ai dati BLOB usando `context.bindings.<NAME>` Where `<NAME>` corrisponde al valore definito in *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Accedere ai dati BLOB tramite un parametro che corrisponda al nome designato dal parametro Name del binding nel _function.jssu_ file.

# <a name="python"></a>[Python](#tab/python)

Accedere ai dati BLOB tramite il parametro tipizzato come [InputStream](/python/api/azure-functions/azure.functions.inputstream). Per informazioni dettagliate, vedere l' [esempio di input](#example) .

---

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando i dati dell'archivio BLOB cambiano](./functions-bindings-storage-blob-trigger.md)
- [Scrivere dati di archiviazione BLOB da una funzione](./functions-bindings-storage-blob-output.md)
