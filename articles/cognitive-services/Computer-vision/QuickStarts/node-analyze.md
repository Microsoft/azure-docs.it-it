---
title: "Guida introduttiva: Analizzare un'immagine remota con l'API REST e Node.js"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si analizzerà un'immagine usando l'API Visione artificiale con Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019
ms.openlocfilehash: 0fada9129d3b05111b2988a9debb55c88414ae3f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685124"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Guida introduttiva: Analizzare un'immagine remota usando l'API REST di Visione artificiale con Node.js

In questa guida di avvio rapido si analizzerà un'immagine archiviata in remoto per estrarre caratteristiche visive usando l'API REST Visione artificiale con Node.js. Con il metodo [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) è possibile estrarre caratteristiche visive in base al contenuto di un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver installato [Node.js](https://nodejs.org) 4.x o versione successiva.
- È necessario aver installato [npm](https://www.npmjs.com/).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. È possibile ottenere una chiave della versione di valutazione gratuita nella pagina [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Oppure seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Visione artificiale e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Installare il pacchetto [`request`](https://www.npmjs.com/package/request) npm.
   1. Aprire una finestra del prompt dei comandi come amministratore.
   1. Eseguire il comando seguente:

      ```console
      npm install request
      ```

   1. Dopo l'installazione del pacchetto, chiudere la finestra del prompt dei comandi.

1. Copiare il codice seguente in un editor di testo.
1. Facoltativamente, sostituire il valore di `imageUrl` con l'URL di un'altra immagine da analizzare.
1. Facoltativamente, sostituire il valore del parametro `language` della richiesta con un'altra lingua.
1. Salvare il codice in un file con estensione `.js`. Ad esempio: `analyze-image.js`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `node` per eseguire il file. Ad esempio: `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.0/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. L'esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, eliminare il file e quindi disinstallare il pacchetto `request` npm. Per disinstallare il pacchetto NuGet, seguire questa procedura:

1. Aprire una finestra del prompt dei comandi come amministratore.
2. Eseguire il comando seguente:

   ```console
   npm uninstall request
   ```

3. Dopo la disinstallazione del pacchetto, chiudere la finestra del prompt dei comandi.

## <a name="next-steps"></a>Passaggi successivi

Esplorare ora le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo stampato e scritto a mano.

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
