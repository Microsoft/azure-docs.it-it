---
title: 'Guida introduttiva: Eseguire una ricerca Web con Node.js - API REST Ricerca Web Bing'
titleSuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per inviare richieste all'API REST Ricerca Web Bing usando Node.js e ricevere una risposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: d0a57a2d7d6a7898ae530108b8aabc2f31c97df6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076606"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Guida introduttiva: Effettuare ricerche sul Web con l'API REST Ricerca Web Bing e Node.js

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questa guida di avvio rapido per effettuare la prima chiamata all'API Ricerca Web Bing. Questa applicazione Node.js invia una richiesta di ricerca all'API e visualizza la risposta JSON. Anche se l'applicazione è scritta in JavaScript, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i passaggi illustrati in questa guida introduttiva, verificare di avere quanto segue:

* [Node.js 6](https://nodejs.org/en/download/) o versione successiva
* Una chiave di sottoscrizione

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Creare un progetto e dichiarare i moduli necessari

Creare un nuovo progetto Node.js nell'IDE o nell'editor preferito. Copiare quindi il frammento di codice seguente nel progetto all'interno di un file denominato search.js:

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Impostare la chiave di sottoscrizione

Questo frammento di codice usa la variabile di ambiente `AZURE_SUBSCRIPTION_KEY` per archiviare la chiave di sottoscrizione, evitando così l'esposizione accidentale delle chiavi durante la distribuzione del codice.

Se non si ha familiarità con l'uso delle variabili di ambiente o se si vuole eseguire l'app al massimo della velocità, sostituire `process.env['AZURE_SUBSCRIPTION_KEY']` con la chiave di sottoscrizione impostata come stringa.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Creare una funzione per effettuare la richiesta

Questa funzione effettua una richiesta GET sicura e salva la query di ricerca come parametro di query nel percorso. 

1. Per il valore di `hostname`, è possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.  

2. Usare `encodeURIComponent` per eseguire l'escape dei caratteri non validi. La chiave di sottoscrizione viene passata in un'intestazione. 

3. Il callback riceve una [risposta](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse) che sottoscrive l'`data`evento per aggregare il corpo JSON, l'`error` evento per registrare eventuali problemi e l'`end` evento per sapere quando il messaggio deve essere considerato completo. 

4. Una volta completata, l'app visualizza le intestazioni e il corpo del messaggio pertinenti. È possibile modificare i colori e impostare la profondità in base alle preferenze. Una profondità di `1` fornisce un buon riepilogo della risposta.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>Impostare la query

Esaminiamo gli argomenti del programma per trovare la query. Il primo argomento è il percorso del nodo, il secondo è il nome file e il terzo è la query. Se la query è assente, viene utilizzata una query predefinita dei "Servizi cognitivi Microsoft".

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Eseguire una richiesta e stampare la risposta

Ora che è stato tutto definito, è possibile chiamare la funzione.

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nell'eseguire il codice con il comando `node search.js "<your query>"`.

Se si vuole confrontare il proprio codice con quello già disponibile, ecco il programma completo:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="example-json-response"></a>Risposta JSON di esempio

Le risposte dell'API Ricerca Web Bing vengono restituite in formato JSON. Questa risposta di esempio è stata troncata in modo da visualizzare un singolo risultato.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina dell'API Ricerca Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
