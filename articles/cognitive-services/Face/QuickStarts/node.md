---
title: "Avvio rapido: Rilevare i visi in un'immagine con l'API REST di Azure e Node.js"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con Node.js per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: a245dae4405706c37b3c142963d325925d089830
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001440"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Avvio rapido: Rilevare i visi in un'immagine con l'API REST Viso e Node.js

In questa guida di avvio rapido si userà l'API REST Viso di Azure con Node.js per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="set-up-the-node-environment"></a>Configurare l'ambiente Node

Passare alla cartella in cui si vuole creare il progetto e creare un nuovo file denominato *facedetection.js*. Installare quindi il modulo `axios` nel progetto. Questo consente agli script di effettuare richieste HTTP.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Scrivere lo script Node.js

Incollare il codice seguente in *facedetection.js*. Questi campi specificano come connettersi al servizio Viso e dove ottenere i dati di input. [Creare le variabili di ambiente](../../cognitive-services-apis-create-account.md?tabs=multiservice%252cwindows#configure-an-environment-variable-for-authentication) e aggiungervi l'endpoint e la chiave di sottoscrizione di Viso. Potrebbe essere opportuno modificare il campo `imageUrl` per puntare alla propria immagine di input.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="environment":::

Aggiungere quindi il codice seguente per chiamare l'API Viso e ottenere i dati degli attributi del viso dall'immagine di input. Il campo `returnFaceAttributes` specifica gli attributi del viso da recuperare. È possibile modificare questa stringa in base all'uso previsto.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="main":::

## <a name="save-and-run-the-script"></a>Salvare ed eseguire lo script

Dopo aver apportato le modifiche, aprire un prompt dei comandi ed eseguire il file con il comando `node`.

```
node facedetection.js
```

Una risposta con esito positivo visualizzerà i dati sui volti in formato JSON facilmente leggibile. Ad esempio:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>Estrarre gli attributi del viso
 
Per estrarre gli attributi del viso, usare il modello di rilevamento 1 e aggiungere il parametro di query `returnFaceAttributes`. Modificare i parametri come indicato di seguito:

```javascript
    params : {
        detectionModel: 'detection_01',
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
        returnFaceId: true
    },
```

La risposta includerà gli attributi del viso. Ad esempio:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è scritto uno script Node.js che chiama il servizio Viso di Azure per rilevare i visi in un'immagine e restituirne gli attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 