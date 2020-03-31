---
title: "Guida introduttiva: Ottenere informazioni dettagliate sulle immagini con l'API REST e Node.js - Ricerca visiva Bing"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379709"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Guida introduttiva: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Node.js

Usare questo argomento di avvio rapido per eseguire la prima chiamata all'API Ricerca visiva Bing e visualizzare i risultati della ricerca. Questa semplice applicazione JavaScript carica un'immagine nell'API e visualizza le informazioni restituite. L'applicazione è scritta in JavaScript, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/)
* Modulo di richiesta per JavaScript. È possibile usare il comando `npm install request` per installare il modulo.
* Modulo form-data. È possibile usare il comando `npm install form-data` per installare il modulo. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inizializzare l'applicazione

1. Creare un file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e impostare i requisiti seguenti:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e il percorso dell'immagine. `baseUri` può essere l'endpoint globale seguente o l'endpoint di [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Creare una funzione denominata `requestCallback()` per stampare la risposta dall'API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Costruire e inviare la richiesta di ricerca

Quando si carica un'immagine locale, i dati del modulo devono includere l'intestazione `Content-Disposition`. È necessario impostare il parametro `name` su "image" e il parametro `filename` su qualsiasi stringa. Il contenuto del modulo include i dati binari dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Creare un nuovo oggetto **FormData** usando `FormData()` e accodare il percorso dell'immagine con `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Usare la libreria di richieste per caricare l'immagine e chiamare `requestCallback()` per stampare la risposta. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione della richiesta:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola con Ricerca visiva](../tutorial-bing-visual-search-single-page-app.md)
