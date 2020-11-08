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
ms.openlocfilehash: cdbbd6afeedc1c8808e02aefa268be4fe0de5f9f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363375"
---
# <a name="suggesting-query-terms"></a>Termini della query suggeriti

> [!WARNING]
> API di ricerca Bing passano da servizi cognitivi a Ricerca Bing Services. A partire dal **30 ottobre 2020** , le nuove istanze di ricerca Bing devono essere sottoposte a provisioning in base al processo documentato [qui](https://aka.ms/cogsvcs/bingmove).
> API di ricerca Bing provisioning con servizi cognitivi sarà supportato per i prossimi tre anni o fino alla fine del Enterprise Agreement, a seconda di quale evento si verifichi per primo.
> Per istruzioni sulla migrazione, vedere [ricerca Bing Services](https://aka.ms/cogsvcs/bingmigration).

L'API Suggerimenti automatici Bing viene generalmente chiamata ogni volta che un utente digita un nuovo carattere nella casella di ricerca dell'applicazione. La completezza della stringa di query influisce sulla pertinenza dei termini di query suggeriti che vengono restituiti dall'API. Più completa è la stringa di query, più rilevante è l'elenco dei termini di query suggeriti. Ad esempio, i suggerimenti che l'API può restituire per `s` sono probabilmente meno pertinenti di quelli restituiti dalla query per `sailing dinghies`.

## <a name="example-request"></a>Richiesta di esempio

L'esempio seguente illustra una richiesta che restituisce le stringhe di query suggerite per *sail*. Ricordarsi di codificare nell'URL il termine della query parziale dell'utente quando si imposta il parametro della query [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Se ad esempio l'utente ha immesso *sailing les* , impostare `q` su `sailing+les` o `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La risposta seguente contiene un elenco di oggetti [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) contenenti i termini della query suggerita.

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

Se l'utente seleziona una query suggerita dall'elenco a discesa, è possibile usare il termine di query nel campo `query` per chiamare l'[API Ricerca Web Bing](../../bing-web-search/overview.md) e visualizzare i risultati. Oppure è possibile usare l'URL nel campo `url` per inviare l'utente alla pagina dei risultati della ricerca di Bing.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Suggerimenti automatici Bing](../get-suggested-search-terms.md)