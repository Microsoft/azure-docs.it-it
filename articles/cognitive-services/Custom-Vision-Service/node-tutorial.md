---
title: 'Guida introduttiva: Creare un progetto di classificazione immagini con Custom Vision SDK per Node.js'
titleSuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto ed effettuare una stima usando Node.js SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 2a7da34238af6d1936c680cd5a063e9ad49ee889
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403609"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>Guida introduttiva: Creare un progetto di classificazione immagini con Custom Vision SDK per Node.js

Questo articolo mostra come iniziare a usare Custom Vision SDK con Node.js per creare un modello di classificazione immagini. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint di stima pubblicato del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la compilazione dell'applicazione Node.js. Se si preferisce eseguire la procedura di compilazione e utilizzo di un modello di classificazione _senza_ codice, vedere le [indicazioni basate su browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisiti

- [Node.js 8](https://www.nodejs.org/en/download/) o versioni successive installato.
- [npm](https://www.npmjs.com/) installato.
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Installare Custom Vision SDK

Per installare Custom Vision SDK per Node.js, usare il comando seguente in PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Creare un nuovo file denominato *sample.js* nella directory del progetto preferita.

### <a name="create-the-custom-vision-service-project"></a>Creare il progetto di Servizio visione artificiale personalizzato

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. Inserire le chiavi della sottoscrizione nelle definizioni appropriate e impostare il valore del percorso sampleDataRoot sul percorso della cartella di immagini. Verificare che il valore dell'endpoint corrisponda agli endpoint di training e di stima creati in [Customvision.ai](https://www.customvision.ai/). Tenere presente che la differenza tra la creazione di un progetto di rilevamento di oggetti e uno di classificazione di immagini è data dal dominio specificato nella chiamata **createProject**.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const trainer = new TrainingApi.TrainingAPIClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>Creare tag nel progetto

Per creare i tag di classificazione per il progetto, aggiungere il codice seguente alla fine del file *sample.js*:

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica ogni immagine con il tag corrispondente. È possibile caricare fino a 64 immagini in un singolo batch.

> [!NOTE]
> Occorrerà modificare anche *sampleDataRoot* indicando il percorso delle immagini in base a dove è stato scaricato il progetto Cognitive Services Node.js SDK Samples.

```javascript
console.log("Adding images...");
let fileUploadPromises = [];

const hemlockDir = `${sampleDataRoot}/Hemlock`;
const hemlockFiles = fs.readdirSync(hemlockDir);
hemlockFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
});

const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
const japaneseCherryFiles = fs.readdirSync(cherryDir);
japaneseCherryFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>Training del classificatore e pubblicazione

Questo codice crea la prima iterazione del modello di previsione e quindi la pubblica nell'endpoint di previsione. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Ottenere e usare l'iterazione pubblicata nell'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

```javascript
    const predictor = new PredictionApi.PredictionAPIClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *sample.js*.

```shell
node sample.js
```

L'output dell'applicazione dovrebbe essere simile al testo seguente:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Si può quindi verificare che all'immagine di test (disponibile in **<url_immagine_base>/Images/Test/** ) siano stati applicati i tag appropriati. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come è possibile eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
