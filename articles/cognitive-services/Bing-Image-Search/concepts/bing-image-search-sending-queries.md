---
title: Personalizzare e suggerire query di ricerca immagini-API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Informazioni sulla personalizzazione delle query di ricerca inviate all'API Ricerca immagini Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 6fb1bdbad4455b55c3f6cc3b395526f637339847
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592159"
---
# <a name="customize-and-suggest-image-search-queries"></a>Personalizzare e suggerire query di ricerca immagini

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questo articolo per informazioni su come personalizzare le query e suggerire i termini di ricerca da inviare al API Ricerca immagini Bing.

## <a name="suggest-search-terms"></a>Suggerisci termini di ricerca

Se l'app ha una casella di ricerca in cui immettere i termini di ricerca, è possibile usare l'[API Suggerimenti automatici Bing](../../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. L'API può visualizzare i termini di ricerca suggeriti in tempo reale. L'API restituisce le stringhe di query suggerite in base a termini di ricerca parziali e a Servizi cognitivi.

## <a name="pivot-the-query"></a>Segmentare la query in pivot

Se Bing può segmentare la query di ricerca originale, l'oggetto [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) restituito contiene `pivotSuggestions`. I suggerimenti di pivot possono essere visualizzati come termini di ricerca facoltativi per l'utente. Se ad esempio la query originale fosse *Microsoft Surface* , Bing potrebbe segmentare la query in *Microsoft* e *Surface* o fornire pivot suggeriti per ciascuno. Questi suggerimenti possono essere visualizzati come termini facoltativi della query per l'utente.

L'esempio seguente illustra i suggerimenti di pivot per *Microsoft Surface* :  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Il campo `pivotSuggestions` contiene l'elenco di segmenti (pivot) in cui è stata suddivisa la query originale. Per ogni pivot, la risposta contiene un elenco di oggetti [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) contenenti le query suggerite. Il campo `text` contiene la query suggerita. Il campo `displayText` contiene il termine che sostituisce il pivot nella query originale, ad esempio la data di rilascio di Surface.

Se la stringa di query pivot è effettivamente ciò l'utente sta cercando, usare i campi `text` e `thumbnail` per visualizzare le stringhe di query pivot. Rendere selezionabili l'anteprima e il testo usando l'URL `webSearchUrl` o `searchLink`. Usare `webSearchUrl` per consentire all'utente di visualizzare i risultati della ricerca di Bing. Se si fornisce una pagina dei risultati personalizzata, usare `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Espandere la query

Se Bing può espandere la query per restringere la ricerca originale, l'oggetto [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) contiene il campo `queryExpansions`. Se ad esempio la query è *Microsoft Surface* , le query espanse possono essere:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

L'esempio seguente illustra le query espanse per *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Il campo `queryExpansions` contiene un elenco di oggetto [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). Il campo `text` contiene la query espansa. Il campo `displayText` contiene il termine espanso. Se la stringa di query espansa è effettivamente ciò l'utente sta cercando, usare i campi `text` e `thumbnail` per visualizzare le stringhe di query espanse. Rendere selezionabili l'anteprima e il testo usando l'URL `webSearchUrl` o `searchLink`. Usare `webSearchUrl` per consentire all'utente di visualizzare i risultati della ricerca di Bing. Se si fornisce una pagina dei risultati personalizzata, usare `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Se non si è ancora provato l'API Ricerca immagini Bing, consultare una [Guida introduttiva](../quickstarts/csharp.md). Se si cercano contenuti più complessi, provare l'esercitazione che consente di creare un'[app Web a pagina singola](../tutorial-bing-image-search-single-page-app.md).