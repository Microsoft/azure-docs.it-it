---
title: Uso della classificazione per visualizzare le risposte - Ricerca entità Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la classificazione per visualizzare le risposte restituite dall'API Ricerca entità Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94365636"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Uso della classificazione per visualizzare i risultati della ricerca di entità  

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

La risposta di ogni ricerca di entità include una risposta [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) che specifica come devono essere visualizzati i risultati della ricerca restituiti dall'API Ricerca entità Bing. La risposta di classificazione raggruppa i risultati in contenuto prominente, principale e della barra laterale. Il risultato prominente è il risultato più importante o significativo e deve essere visualizzato per primo. Se i risultati rimanenti non vengono visualizzati in un formato di contenuto principale e della barra laterale tradizionale, è necessario dare al contenuto principale una maggiore visibilità rispetto al contenuto della barra laterale. 
  
All'interno di ogni gruppo, la matrice [Items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica l'ordine in cui deve essere visualizzato il contenuto. Ogni elemento offre due modi per identificare il risultato all'interno di una risposta.  
 

|Campo | Descrizione  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType` identifica la risposta (Entity o Place) e `resultIndex` identifica un risultato all'interno di tale risposta (ad esempio, un'entità). L'indice è in base 0.|
|`value`    | `value` Contiene un ID che corrisponde all'ID di una risposta o di un risultato all'interno della risposta. L'ID è incluso nel riscontro o nei risultati, ma non in entrambi. |
  
L'uso di `answerType` e `resultIndex` è un processo in due passaggi. Innanzitutto, usare `answerType` per identificare la risposta contenente i risultati da visualizzare. Usare quindi `resultIndex` per indicizzare i risultati della risposta e ottenere il risultato da visualizzare. Il `answerType` valore è il nome del campo nell'oggetto [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) . Se si suppone che tutti i risultati della risposta vengano visualizzati insieme, l'elemento di risposta di rango non include il `resultIndex` campo.

Per usare l'ID è necessario che l'ID di classificazione corrisponda all'ID di una risposta o a uno dei risultati. Se un oggetto riscontro include un campo `id`, tutti i risultati del riscontro vengono visualizzati insieme. Ad esempio, se l'oggetto `Entities` include il campo `id`, gli articoli di tutte le entità vengono visualizzati insieme. Se l'oggetto `Entities` non include il campo `id`, ogni entità conterrà un campo `id` e la risposta di classificazione combinerà le entità con i risultati di Places.  
  
## <a name="ranking-response-example"></a>Esempio di risposta di classificazione

Di seguito viene illustrato un esempio di [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

In base alla risposta di classificazione, la barra laterale visualizza i due risultati di entità correlati a Jimi Hendrix.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](tutorial-bing-entities-search-single-page-app.md)