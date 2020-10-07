---
title: 'Guida introduttiva: Libreria client di archiviazione BLOB di Azure v10 per JavaScript'
description: Creare, caricare ed eliminare BLOB e contenitori in Node.js con la libreria client di archiviazione di Azure v10 per JavaScript
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1f47c35997fe060fd3c318602bcad17de83b530c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91249621"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Guida introduttiva: Gestire i BLOB con JavaScript v10 SDK in Node.js

In questo argomento di avvio rapido viene illustrato come gestire i BLOB con Node.js. I BLOB sono oggetti che possono contenere grandi quantità di dati di testo o binari, tra cui immagini, documenti, flussi multimediali e dati di archiviazione. Verranno caricati, scaricati, elencati ed eliminati i BLOB e verranno gestiti i contenitori.

> [!NOTE]
> In questo argomento di avvio rapido si usa una versione legacy della libreria client di Archiviazione BLOB di Azure. Per un'introduzione alla versione più recente, vedere [Avvio rapido: Gestire i BLOB con JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un account dell'Archiviazione di Azure. [Creare un account di archiviazione](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) usata in questa guida introduttiva è una semplice applicazione console Node.js. Per iniziare, clonare il repository nel computer in uso con il comando seguente:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Modificare quindi le cartelle per l'applicazione:

```bash
cd azure-storage-js-v10-quickstart
```

Aprire ora la cartella nell'ambiente di modifica del codice preferito.

## <a name="configure-your-storage-credentials"></a>Configurare le credenziali di archiviazione

Prima di eseguire l'applicazione, è necessario specificare le credenziali di sicurezza per l'account di archiviazione. Il repository di esempio include un file denominato *. env.example*. Rinominare il file rimuovendo l'estensione *.example*, per ottenere un file denominato *.env*. Nel file *ENV* aggiungere i valori del nome account e della chiave di accesso dopo le chiavi *AZURE_STORAGE_ACCOUNT_NAME* e *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>Installare i pacchetti necessari

Nella directory dell'applicazione eseguire il comando *npm install* per installare i pacchetti richiesti per l'applicazione.

```bash
npm install
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Ora che le dipendenze sono installate, è possibile eseguire l'esempio tramite il comando seguente:

```bash
npm start
```

L'output di questa app sarà simile all'esempio seguente:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Se si usa un nuovo account di archiviazione per questo argomento di avvio rapido, potrebbe essere visibile solo il contenitore *demo* sotto l'etichetta "*Contenitori*".

## <a name="understanding-the-code"></a>Informazioni sul codice

L'esempio inizia importando alcune classi e funzioni dallo spazio dei nomi di archiviazione BLOB di Azure. Ogni elemento importato viene illustrato nel contesto quando viene usato nell'esempio.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Le credenziali vengono lette dalle variabili di ambiente in base al contesto appropriato.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Il modulo *dotenv* carica le variabili di ambiente quando esegue l'app in locale per il debug. Le variabili di ambiente vengono definite in un file denominato *ENV* e caricate nel contesto di esecuzione corrente. Nell'ambiente di produzione la configurazione del server fornisce questi valori ed è per tale motivo che questo codice viene eseguito solo quando lo script *non* è in esecuzione in un ambiente di "produzione".

Il blocco successivo di moduli viene importato per consentire di interfacciarsi con il file system.

```javascript
const fs = require('fs');
const path = require('path');
```

Di seguito è descritto lo scopo di questi moduli: 

- *fs* è il modulo Node.js nativo usato per lavorare con il file system

- *path* è necessario per determinare il percorso assoluto del file, che viene usato quando si carica un file nell'archivio BLOB

I valori delle variabili di ambiente vengono quindi letti e copiati nelle costanti.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
Il set successivo di costanti consente di rivelare la finalità dei calcoli delle dimensioni del file durante le operazioni di caricamento.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Le richieste effettuate dall'API possono essere impostate per raggiungere il timeout dopo un intervallo specifico. La classe [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy) determina come le richieste raggiungono il timeout e la costante seguente viene usata per definire i timeout usati in questo esempio.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Codice della chiamata

Per il supporto della sintassi JavaScript *async/await*, viene eseguito il wrapping per tutto il codice chiamante in una funzione denominata *execute*. La funzione *execute* viene quindi chiamata e gestita come promessa.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Tutto il codice seguente viene eseguito all'interno della funzione execute in cui si trova il commento `// commands...`.

In primo luogo, le variabili rilevanti vengono dichiarate per assegnare nomi e contenuto di esempio e in modo che puntino al file locale da caricare nell'archivio BLOB.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Le credenziali dell'account vengono usate per creare una pipeline, che determina come le richieste vengono inviate all'API REST. Le pipeline sono thread-safe e specificano la logica per criteri di ripetizione, registrazione, regole di deserializzazione della risposta HTTP e altro ancora.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

In questo blocco di codice vengono usate le classi seguenti:

- La classe [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy) è responsabile del wrapping delle credenziali dell'account di archiviazione per fornirle a una pipeline di richieste.

- La classe [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy) è responsabile della creazione di una nuova pipeline.

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy) modella un URL usato nell'API REST. Le istanze di questa classe consentono di eseguire azioni come elencare i contenitori e fornire informazioni di contesto per generare gli URL dei contenitori.

L'istanza di *ServiceURL* viene usata con le istanze di [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy) e [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy) per gestire contenitori e BLOB nell'account di archiviazione.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

Le variabili *containerURL* e *blockBlobURL* vengono riutilizzate in tutto l'esempio per eseguire operazioni relative all'account di archiviazione. 

A questo punto, il contenitore non esiste nell'account di archiviazione. L'istanza di *ContainerURL* rappresenta un URL su cui è possibile eseguire operazioni. Usando questa istanza, è possibile creare ed eliminare il contenitore. La posizione di questo contenitore sarà simile alla seguente:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

*blockBlobURL* viene usato per gestire i singoli BLOB, consentendo di caricare, scaricare ed eliminare il contenuto dei BLOB. L'URL rappresentato qui è simile a questa posizione:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Come il contenitore, il BLOB in blocchi non esiste ancora. La variabile *blockBlobURL* viene usata più avanti per creare il BLOB caricando il contenuto.

### <a name="using-the-aborter-class"></a>Uso della classe Aborter

Le richieste effettuate dall'API possono essere impostate per raggiungere il timeout dopo un intervallo specifico. La classe *Aborter* determina come le richieste raggiungono il timeout. Il codice seguente crea un contesto in cui a un set di richieste vengono concessi 30 minuti per l'esecuzione.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Le classi Aborter consentono di controllare le richieste consentendo di:

- stabilire l'intervallo di tempo concesso per un batch di richieste
- stabilire la durata dell'esecuzione di una singola richiesta nel batch
- consentire di annullare le richieste
- usare il membro statico *Aborter.none* per evitare che tutte le richieste raggiungano il timeout contemporaneamente

### <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore, viene usato il metodo *create* di *ContainerURL*.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Poiché il nome del contenitore viene definito quando si chiama *ContainerURL.fromServiceURL(serviceURL, containerName)* , per creare il contenitore è sufficiente chiamare il metodo *create*.

### <a name="show-container-names"></a>Visualizzare i nomi dei contenitori

Gli account possono archiviare un numero elevato di contenitori. Il codice seguente illustra come elencare i contenitori in modo segmentato, per poter scorrere un numero elevato di contenitori. Alla funzione *showContainerNames* vengono passate le istanze di *ServiceURL* e *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

La funzione *showContainerNames* usa il metodo *listContainersSegment* per richiedere batch di nomi di contenitori all'account di archiviazione.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Quando la risposta viene restituita, viene eseguita l'iterazione di *containerItems* per registrare il nome nella console. 

### <a name="upload-text"></a>Caricare un file di testo

Per caricare il testo nel BLOB, usare il metodo *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Il testo e la lunghezza vengono passati al metodo.

### <a name="upload-a-local-file"></a>Caricare un file locale

Per caricare un file locale nel contenitore, sono necessari un URL contenitore e il percorso del file.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

La funzione *uploadLocalFile* chiama la funzione *uploadFileToBlockBlob*, che usa il percorso del file e un'istanza della destinazione del BLOB in blocchi come argomenti.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Caricare un flusso

È supportato anche il caricamento dei flussi. Questo esempio apre un file locale come flusso per passare al metodo di caricamento.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

La funzione *uploadStream* chiama *uploadStreamToBlockBlob* per caricare il flusso nel contenitore di archiviazione.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Durante un caricamento, *uploadStreamToBlockBlob* alloca i buffer per memorizzare nella cache i dati del flusso qualora fosse necessario eseguire un nuovo tentativo. Il valore *maxBuffers* stabilisce il numero massimo di buffer che è possibile usare dal momento che ogni buffer crea una richiesta di caricamento separata. Idealmente, più buffer equivalgono a maggiore velocità, ma anche a un utilizzo più elevato di memoria. La velocità di caricamento si stabilizza quando il numero di buffer è abbastanza elevato che il collo di bottiglia passa alla rete o al disco invece che al client.

### <a name="show-blob-names"></a>Visualizzare i nomi dei BLOB

Come gli account possono includere più contenitori, ogni contenitore può potenzialmente includere una grande quantità di BLOB. L'accesso a ogni BLOB in un contenitore è disponibile tramite un'istanza della classe *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

La funzione *showBlobNames* chiama *listBlobFlatSegment* per richiedere i batch di BLOB al contenitore.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Scaricare un BLOB

Dopo la creazione di un BLOB, è possibile scaricare il contenuto usando il metodo *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

La risposta viene restituita come flusso. In questo esempio il flusso viene convertito in una stringa tramite la funzione helper *streamToString* seguente.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Eliminare un BLOB

Il metodo *delete* da un'istanza di *BlockBlobURL* elimina un BLOB dal contenitore.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Eliminare un contenitore

Il metodo *delete* da un'istanza di *ContainerURL* elimina un contenitore dall'account di archiviazione.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Pulire le risorse

Tutti i dati scritti nell'account di archiviazione vengono automaticamente eliminati alla fine del codice di esempio. 

## <a name="next-steps"></a>Passaggi successivi

Questa guida introduttiva illustra come gestire i BLOB e i contenitori nell'archivio BLOB di Azure usando Node.js. Per altre informazioni sull'uso di questo SDK, vedere il repository di GitHub.

> [!div class="nextstepaction"]
> [Repository Azure Storage v10 SDK per JavaScript](https://github.com/Azure/azure-storage-js)
> [Informazioni di riferimento sull'API JavaScript di Archiviazione di Azure](/javascript/api/overview/azure/storage-overview)
