---
title: 'Avvio rapido: Estrarre testo stampato - REST, Node.js'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si estrarrà testo stampato da un'immagine usando l'API Visione artificiale con Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 64ca75645d1a9e6d638af5128a6c690c47985cdd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974533"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-nodejs"></a>Avvio rapido: Estrarre testo stampato (OCR) usando l'API REST di Visione artificiale e Node.js

> [!NOTE]
> Se si estrae testo in lingua inglese, valutare la possibilità di usare la nuova [operazione di lettura](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text).

In questa guida di avvio rapido si estrarrà testo stampato da un'immagine con il riconoscimento ottico dei caratteri (OCR) usando l'API REST Visione artificiale. Con il metodo [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) è possibile rilevare il testo stampato in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4.x o versione successiva 
* [npm](https://www.npmjs.com/) 
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* [Creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominati rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

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
1. Facoltativamente, sostituire il valore di `imageUrl` con l'URL di un'altra immagine da cui si vuole estrarre il testo stampato.
1. Salvare il codice in un file con estensione `.js`. Ad esempio: `get-printed-text.js`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `node` per eseguire il file. Ad esempio: `node get-printed-text.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.1/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
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
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
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
