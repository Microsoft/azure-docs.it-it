---
title: Esempi di informazioni dettagliate su Bing - Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene esempi di come Bing Visual Search potrebbe usare e visualizzare informazioni dettagliate sulle immagini in Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111646"
---
# <a name="examples-of-bing-insights-usage"></a>Esempi di utilizzo delle informazioni dettagliate di Bing

Questo articolo contiene esempi del modo in cui Bing potrebbe usare e visualizzare informazioni dettagliate sulle immagini su Bing.com.

## <a name="pagesincluding-insight-example"></a>Esempio di informazioni dettagliate PagesIncluding

Di seguito viene visualizzato un collegamento alla prima pagina Web e consente all'utente di espandere e comprimere l'elenco delle altre pagine Web che includono l'immagine:

![Pagine espanse che includono l'immagine](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Esempio di informazioni dettagliate ShoppingSources

Di seguito viene illustrato come Bing potrebbe visualizzare le fonti di acquisto per i prodotti visualizzati nell'immagine:

![Fonti per l'acquisto](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Esempio di informazioni dettagliate VisualSearch

Di seguito viene illustrato come Bing potrebbe visualizzare immagini visivamente simili (vedere Immagini correlate nell'esempio):The following shows how Bing might display visually similar images (see **Related images** in the example):

![Immagini visivamente simili](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Esempio di informazioni dettagliate Recipes

L'esempio seguente mostra in che modo Bing potrebbe visualizzare ricette per l'alimento mostrato nell'immagine. L'esempio consente all'utente di sapere che sono disponibili ricette:

![Ricette e pagine che includono l'immagine](./media/recipes-pages-including.PNG)

 E fornisce il link alle ricette quando l'utente espande l'elenco:

![Pagine di ricette espanse che includono l'immagine](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Esempio di informazioni dettagliate RelatedSearches

L'esempio seguente mostra in che modo Bing potrebbe visualizzare le ricerche correlate di immagini eseguite da altri utenti. Se l'utente fa clic sull'immagine, viene visualizzata la pagina dei risultati della ricerca Bing.com/images per la query correlata.

![Ricerche correlate per le immagini](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Esempio di informazioni dettagliate Entity

L'esempio seguente mostra in che modo Bing potrebbe visualizzare informazioni sull'entità (persona, luogo o cosa) mostrata nell'immagine. Se l'utente fa clic sul collegamento dell'entità, viene indirizzato alla pagina dei risultati di ricerca Bing.com per l'entità:

![Entità visualizzata in un'immagine](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visualizzazione di altre informazioni dettagliate che l'utente potrebbe esplorare

L'esempio seguente mostra in che modo Bing potrebbe visualizzare altre informazioni sull'immagine che l'utente può esplorare.

![Esplorare altre informazioni dettagliate sull'immagine](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Rettangoli di selezione e aree sensibili

I tag non predefiniti includono il rettangolo di selezione che identifica l'area di interesse nell'immagine a cui è applicato il tag. Se il rettangolo di selezione non identifica l'intera immagine, usarlo per creare un'area sensibile sull'immagine. L'utente può fare clic sull'area sensibile per ottenere informazioni relative al contenuto trovato sotto l'area sensibile (o il rettangolo). Ad esempio, se l'immagine è un'immagine di alta moda, i risultati possono contenere tag (e rettangoli di selezione) per gli accessori mostrati nell'immagine, ad esempio una borsa, gioielli, sciarpe e così via. L'esempio seguente mostra un rettangolo di area sensibile per gli occhiali da sole mostrati nell'immagine:

![Rettangolo di selezione e area sensibile](./media/click-to-search.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con la prima richiesta, vedere le guide introduttive: [C'è](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





