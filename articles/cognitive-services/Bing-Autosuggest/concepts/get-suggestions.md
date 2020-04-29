---
title: Suggerire termini di ricerca con l'API Suggerimenti automatici Bing
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato il concetto di suggerire i termini di query usando il API Suggerimenti automatici Bing e l'effetto della lunghezza della query sulla pertinenza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74072882"
---
# <a name="suggesting-query-terms"></a>Termini della query suggeriti

L'API Suggerimenti automatici Bing viene generalmente chiamata ogni volta che un utente digita un nuovo carattere nella casella di ricerca dell'applicazione. La completezza della stringa di query influisce sulla pertinenza dei termini di query suggeriti che vengono restituiti dall'API. Più completa è la stringa di query, più rilevante è l'elenco dei termini di query suggeriti. Ad esempio, i suggerimenti che l'API può restituire per `s` sono probabilmente meno pertinenti di quelli restituiti dalla query per `sailing dinghies`.

## <a name="example-request"></a>Richiesta di esempio

L'esempio seguente illustra una richiesta che restituisce le stringhe di query suggerite per *sail*. Ricordarsi di codificare nell'URL il termine della query parziale dell'utente quando si imposta il parametro della query [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Se ad esempio l'utente ha immesso *sailing les*, impostare `q` su `sailing+les` o `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La risposta seguente contiene un elenco di oggetti [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) contenenti i termini della query suggerita.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Uso di termini di query suggeriti

Ogni suggerimento include i campi `displayText`, `query` e `url`. Il campo `displayText` contiene la query suggerita che si usa per popolare l'elenco a discesa della casella di ricerca. È necessario visualizzare tutti i suggerimenti inclusi nella risposta, nell'ordine indicato.

L'esempio seguente mostra una casella di ricerca a discesa con i termini di query suggeriti dall'API Suggerimenti automatici Bing.

![Elenco di casella di ricerca con elenco a discesa di suggerimenti automatici](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se l'utente seleziona una query suggerita dall'elenco a discesa, è possibile usare il termine di query nel campo `query` per chiamare l'[API Ricerca Web Bing](../../bing-web-search/search-the-web.md) e visualizzare i risultati. Oppure è possibile usare l'URL nel campo `url` per inviare l'utente alla pagina dei risultati della ricerca di Bing.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Suggerimenti automatici Bing](../get-suggested-search-terms.md)