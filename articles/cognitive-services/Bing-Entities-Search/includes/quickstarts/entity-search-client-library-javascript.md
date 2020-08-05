---
title: 'Avvio rapido: Libreria client di Ricerca entità Bing per JavaScript'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: f5a3b48fd6be48d468b66009a77de100962be10d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405068"
---
Seguire questo argomento di avvio rapido per iniziare a cercare entità con la libreria client di Ricerca entità Bing per JavaScript. Anche se Ricerca entità Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, la libreria client offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).

* L'[SDK di Ricerca entità Bing per Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Per installare l'SDK di Ricerca entità Bing:

1. Eseguire `npm install ms-rest-azure` nell'ambiente di sviluppo.
2. Eseguire `npm install @azure/cognitiveservices-entitysearch` nell'ambiente di sviluppo.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'ambiente di sviluppo integrato o nell'editor preferito e aggiungere i requisiti seguenti.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Creare un'istanza di `CognitiveServicesCredentials` usando la chiave della sottoscrizione. Quindi usarla per creare un'istanza del client di ricerca.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Inviare una richiesta e ricevere una risposta

1. Inviare una richiesta di ricerca entità con `entitiesOperations.search()`. Dopo aver ricevuto una risposta, stampare `queryContext`, il numero di risultati restituiti e la descrizione del primo risultato.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../../overview.md)