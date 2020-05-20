---
title: Tipo di entità semplice-LUIS
titleSuffix: Azure Cognitive Services
description: Un'entità semplice descrive un singolo concetto dal contesto di machine learning. Aggiungere un elenco di frasi quando si usa un'entità semplice per migliorare i risultati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1b5754be3c9941101a53f332841ace93caf9acdd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684566"
---
# <a name="simple-entity"></a>Entità semplice

Una semplice entità è un'entità generica che descrive un singolo concetto ed è appresa dal contesto di machine learning. Poiché le entità semplici sono in genere nomi, come nomi di società, nomi di prodotto o altre categorie di nomi, è consigliabile aggiungere un [elenco di frasi](luis-concept-feature.md) quando si usa un'entità semplice per migliorare il segnale dei nomi usati.

**Questa entità è idonea quando:**

* I dati non sono formattati in modo coerente ma indicano la stessa cosa.

![entità semplice](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON di esempio

`Bob Jones wants 3 meatball pho`

Nell'espressione precedente `Bob Jones` è etichettata come entità `Customer` semplice.

I dati restituiti dall'endpoint includono il nome dell'entità, il testo individuato nell'espressione, la posizione del testo individuato e il punteggio:

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Oggetto dati|Nome dell'entità|Valore|
|--|--|--|
|Entità semplice|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Sintassi del modello Learn](reference-pattern-syntax.md)