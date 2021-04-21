---
title: Tag di contenuto - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi alla funzione di applicazione di tag alle immagini dell'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3ef2d07b736d391fc3509266dea77fdc1b325ff2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778716"
---
# <a name="applying-content-tags-to-images"></a>Applicazione di tag di contenuto alle immagini

Visione artificiale restituisce tag basati su migliaia di oggetti, esseri viventi, panorami e azioni riconoscibili. Quando i tag sono ambigui o di conoscenza non comune, la risposta dell'API indica suggerimenti per chiarire il significato del tag nel contesto di un'impostazione nota. I tag non sono organizzati in una tassonomia e non esiste alcuna gerarchia di ereditarietà. Una raccolta di tag di contenuto costituisce la base per la descrizione di un'immagine visualizzata come linguaggio leggibile dall'utente e formattata in frasi complete. Si noti che a questo punto l'inglese è l'unica lingua supportata per la descrizione dell'immagine.

Dopo il caricamento di un'immagine o la definizione di un URL di immagine, gli algoritmi di Visione artificiale generano tag in base agli oggetti, alle azioni e agli esseri umani identificati nell'immagine. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via.

## <a name="image-tagging-example"></a>Esempio di assegnazione di tag a un'immagine

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando esegue l'assegnazione di tag alle caratteristiche visive rilevate nell'immagine di esempio.

![Casa azzurra con giardino](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Usare l'API

La funzionalità di assegnazione di tag fa parte dell'API [Analizza immagine.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Tags` nel parametro di query **visualFeatures.** Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"tags"` sezione.

* [Avvio rapido: API REST o librerie client di Visione artificiale](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti correlati relativi alla [categorizzazione delle immagini](concept-categorizing-images.md) e [alla descrizione delle immagini.](concept-describing-images.md)
