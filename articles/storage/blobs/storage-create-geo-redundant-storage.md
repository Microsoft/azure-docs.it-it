---
title: "Esercitazione: Creare un'applicazione a disponibilità elevata con l'archivio BLOB"
titleSuffix: Azure Storage
description: Usare l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS) per applicare la disponibilità elevata ai dati delle applicazioni.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, tracking-python
ms.subservice: blobs
ms.openlocfilehash: f7c3ebb1a68d671f63e3239794266c8c24f5906a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553192"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Esercitazione: Compilare un'applicazione a disponibilità elevata con l'archivio BLOB

Questa è la prima di una serie di esercitazioni. Si apprenderà come applicare la disponibilità elevata ai dati delle applicazioni in Azure.

Dopo aver completato l'esercitazione, si avrà un'applicazione console che carica e recupera un BLOB da un account di archiviazione [con ridondanza geografica della zona e accesso in lettura](../common/storage-redundancy.md) (RA-GZRS).

La ridondanza geografica in Archiviazione di Azure replica le transazioni in modo asincrono da un'area primaria a una secondaria a centinaia di chilometri di distanza. Il processo di replica garantisce che i dati nell'area secondaria abbiano coerenza finale. L'applicazione console usa il criterio [interruttore](/azure/architecture/patterns/circuit-breaker) per determinare l'endpoint a cui connettersi, passando automaticamente da un endpoint a un altro quando vengono simulati errori e ripristini.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Impostare la stringa di connessione
> * Eseguire l'applicazione console

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

# <a name="net"></a>[.NET](#tab/dotnet)

* Installare [Visual Studio 2019](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo di Azure**.

  ![Sviluppo di Azure (in Web e cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* Installare [Python](https://www.python.org/downloads/)
* Scaricare e installare [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Installare [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti.

Seguire questa procedura per creare un account di archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS):

1. Selezionare il pulsante **Crea una risorsa** nel portale di Azure.
2. Selezionare **Account di archiviazione - BLOB, file, tabella, coda** nella pagina **Nuovo**.
4. Compilare il modulo dell'account di archiviazione con le informazioni seguenti, come illustrato nell'immagine seguente e selezionare **Crea**:

   | Impostazione       | Valore di esempio | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Sottoscrizione** | *Sottoscrizione personale* | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.azure.com/Subscriptions). |
   | **ResourceGroup** | *myResourceGroup* | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |
   | **Nome** | *mystorageaccount* | Un nome univoco per l'account di archiviazione. |
   | **Posizione** | *Stati Uniti orientali* | Scegliere un paese. |
   | **Prestazioni** | *Standard* | Le prestazioni standard sono un'opzione valida per lo scenario di esempio. |
   | **Tipo di account** | *StorageV2* | È consigliabile usare un account di archiviazione per utilizzo generico v2. Per altre informazioni sui tipi di account di archiviazione di Azure, vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md). |
   | **Replica**| *Archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS)* | L'area primaria è con ridondanza della zona e viene replicata in un'area secondaria, con l'accesso in lettura all'area secondaria abilitato. |
   | **Livello di accesso**| *Livello di archiviazione ad accesso frequente* | Usare il livello ad accesso frequente per i dati a cui si accede spesso. |

    ![Creare un account di archiviazione](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Scaricare l'esempio

# <a name="net"></a>[.NET](#tab/dotnet)

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) ed estrarre (decomprimere) il file storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. È anche possibile usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Il progetto di esempio contiene un'applicazione console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) ed estrarre (decomprimere) il file storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. È anche possibile usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Il progetto di esempio contiene un'applicazione Python di base.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) e decomprimere il file. È anche possibile usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Il progetto di esempio contiene un'applicazione Node.js di base.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configurare l'esempio

# <a name="net"></a>[.NET](#tab/dotnet)

Nell'applicazione è necessario inserire la stringa di connessione per l'account di archiviazione. È possibile archiviare questa stringa di connessione in una variabile di ambiente nel computer locale che esegue l'applicazione. Seguire uno degli esempi riportati più avanti, in base al sistema operativo, per creare la variabile di ambiente.

Nel portale di Azure passare all'account di archiviazione. Nell'account di archiviazione selezionare **Chiavi di accesso** in **Impostazioni**. Copiare la **stringa di connessione** dalla chiave primaria o secondaria. Eseguire uno dei comandi seguenti in base al sistema operativo in uso, sostituendo \<yourconnectionstring\> con la stringa di connessione effettiva. Il comando salva una variabile di ambiente nel computer locale. In Windows, la variabile di ambiente non è disponibile finché non si ricarica il **prompt dei comandi** o la shell in uso.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

Nell'applicazione è necessario specificare le credenziali dell'account di archiviazione. È possibile archiviare queste informazioni nelle variabili di ambiente del computer locale che esegue l'applicazione. Seguire uno degli esempi seguenti, in base al sistema operativo, per creare le variabili di ambiente.

Nel portale di Azure passare all'account di archiviazione. Nell'account di archiviazione selezionare **Chiavi di accesso** in **Impostazioni**. Incollare i valori di **Nome dell'account di archiviazione** e **Chiave** nei comandi seguenti, sostituendo i segnaposto \<youraccountname\> e \<youraccountkey\>. Questo comando salva le variabili di ambiente nel computer locale. In Windows, la variabile di ambiente non è disponibile finché non si ricarica il **prompt dei comandi** o la shell in uso.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Per eseguire l'esempio, è necessario aggiungere le credenziali dell'account di archiviazione nel file `.env.example` e rinominarlo in `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Per trovare queste informazioni nel portale di Azure, accedere all'account di archiviazione e selezionare **Chiavi di accesso** nella sezione **Impostazioni**.

Installare le dipendenze richieste. A questo scopo, aprire un prompt dei comandi, passare alla cartella dell'esempio e quindi immettere `npm install`.

---

## <a name="run-the-console-application"></a>Eseguire l'applicazione console

# <a name="net"></a>[.NET](#tab/dotnet)

In Visual Studio premere **F5** o selezionare **Avvia** per avviare il debug dell'applicazione. Visual Studio ripristina automaticamente i pacchetti NuGet mancanti, se sono stati configurati. Per altre informazioni, vedere l'articolo relativo a [installazione e reinstallazione di pacchetti con Ripristino pacchetto](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Si apre una finestra della console e l'applicazione avvia l'esecuzione. L'applicazione carica l'immagine **HelloWorld.png** dalla soluzione nell'account di archiviazione. L'applicazione verifica che l'immagine sia stata replicata nell'endpoint RA-GZRS secondario. Si avvia quindi il download dell'immagine fino a 999 volte. Ogni lettura è rappresentata da una **P** o una **S**. **P** rappresenta l'endpoint primario e **S** rappresenta l'endpoint secondario.

![App console in esecuzione](media/storage-create-geo-redundant-storage/figure3.png)

Nell'esempio di codice l'attività `RunCircuitBreakerAsync` nel file `Program.cs` viene usata per scaricare un'immagine dall'account di archiviazione con il metodo [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Prima del download viene definita una classe [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext). Il contesto dell'operazione definisce i gestori dell'evento, generati quando il download viene completato correttamente o se un download ha esito negativo ed esegue un nuovo tentativo.

# <a name="python"></a>[Python](#tab/python)

Per eseguire l'applicazione in un terminale o al prompt dei comandi, passare alla directory **circuitbreaker.py** e quindi immettere `python circuitbreaker.py`. L'applicazione carica l'immagine **HelloWorld.png** dalla soluzione nell'account di archiviazione. L'applicazione verifica che l'immagine sia stata replicata nell'endpoint RA-GZRS secondario. Si avvia quindi il download dell'immagine fino a 999 volte. Ogni lettura è rappresentata da una **P** o una **S**. **P** rappresenta l'endpoint primario e **S** rappresenta l'endpoint secondario.

![App console in esecuzione](media/storage-create-geo-redundant-storage/figure3.png)

Nel codice di esempio, il metodo `run_circuit_breaker` nel file `circuitbreaker.py` viene usato per scaricare un'immagine dall'account di archiviazione con il metodo [get_blob_to_path](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice?view=azure-python-previous#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-).

La funzione di ripetizione dei tentativi dell'oggetto di archiviazione è impostata su un criterio di ripetizione lineare. La funzione di ripetizione dei tentativi determina se eseguire o meno un nuovo tentativo per una richiesta e specifica il numero di secondi di attesa prima del nuovo tentativo. Impostare il valore di **retry\_to\_secondary** su true se la richiesta dovrà essere ripetuta all'endpoint secondario in caso di esito negativo della richiesta iniziale all'endpoint primario. Nell'applicazione di esempio, nella funzione `retry_callback` dell'oggetto di archiviazione viene definito un criterio di ripetizione dei tentativi personalizzato.

Prima del download vengono definite le funzioni [retry_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) e [response_callback](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient?view=azure-python) dell'oggetto del servizio, che definiscono i gestori eventi che vengono attivati quando un download viene completato oppure ha esito negativo e viene eseguito un nuovo tentativo.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Per eseguire l'esempio, aprire un prompt dei comandi, passare alla cartella dell'esempio e quindi immettere `node index.js`.

L'esempio crea un contenitore nell'account di archiviazione BLOB, carica **HelloWorld.png** nel contenitore, quindi verifica ripetutamente se il contenitore e l'immagine sono stati replicati nell'area secondaria. Dopo la replica, chiede di immettere **D** o **Q** (seguito da INVIO) per scaricare (Download) o uscire (Quit). L'output dovrebbe essere simile all'esempio seguente:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Riprovare il gestore dell'evento

Quando il download dell'immagine non riesce ed è impostata la ripetizione dei tentativi, viene chiamato il gestore eventi `OperationContextRetrying`. Se viene raggiunto il numero massimo di tentativi definito nell'applicazione, il valore di [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) della richiesta viene modificato in `SecondaryOnly`. Questa impostazione forza l'applicazione a tentare di scaricare l'immagine dall'endpoint secondario. Questa configurazione riduce il tempo necessario per richiedere che l'immagine non venga ripetuta all'infinito come endpoint primario.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Gestore dell'evento per la richiesta completata

Quando il download dell'immagine riesce viene chiamato il gestore dell'evento `OperationContextRequestCompleted`. Se l'applicazione usa l'endpoint secondario, continua a usarlo fino a 20 volte. Dopo 20 volte, l'applicazione reimposta [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) su `PrimaryThenSecondary` ed esegue un nuovo tentativo con l'endpoint primario. Se una richiesta ha esito positivo, l'applicazione continua a eseguire la lettura dall'endpoint primario.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Riprovare il gestore dell'evento

Quando il download dell'immagine non riesce ed è impostata la ripetizione dei tentativi, viene chiamato il gestore eventi `retry_callback`. Se viene raggiunto il numero massimo di tentativi definito nell'applicazione, il valore di [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) della richiesta viene modificato in `SECONDARY`. Questa impostazione forza l'applicazione a tentare di scaricare l'immagine dall'endpoint secondario. Questa configurazione riduce il tempo necessario per richiedere che l'immagine non venga ripetuta all'infinito come endpoint primario.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Gestore dell'evento per la richiesta completata

Quando il download dell'immagine riesce viene chiamato il gestore dell'evento `response_callback`. Se l'applicazione usa l'endpoint secondario, continua a usarlo fino a 20 volte. Dopo 20 volte, l'applicazione reimposta [LocationMode](https://docs.microsoft.com/python/api/azure-storage-common/azure.storage.common.models.locationmode?view=azure-python) su `PRIMARY` ed esegue un nuovo tentativo con l'endpoint primario. Se una richiesta ha esito positivo, l'applicazione continua a eseguire la lettura dall'endpoint primario.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Con l'SDK Node.js V10, i gestori di callback non sono necessari. Al contrario, l'esempio crea una pipeline configurata con opzioni di ripetizione e un endpoint secondario. In questo modo l'applicazione passa automaticamente alla pipeline secondaria se non riesce ad accedere ai dati tramite quella primaria.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie è stato descritto come applicare la disponibilità elevata a un'applicazione con gli account di archiviazione RA-GZRS.

Passare alla seconda parte della serie per informazioni su come simulare un errore e forzare l'applicazione a usare l'endpoint RA-GZRS secondario.

> [!div class="nextstepaction"]
> [Simulare un errore durante la lettura dall'area primaria](simulate-primary-region-failure.md)
