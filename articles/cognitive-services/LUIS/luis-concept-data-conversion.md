---
title: Conversione dati-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su come modificare le espressioni prima delle stime in Language Understanding (LUIS)
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b305be693f59b65a62570f656a0132f4f03cf099
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541799"
---
# <a name="convert-data-format-of-utterances"></a>Convertire il formato dati delle espressioni
LUIS fornisce le seguenti conversioni di un enunciato utente prima della stima "

* Riconoscimento vocale tramite il servizio di [riconoscimento vocale di servizi cognitivi](../Speech-Service/overview.md) .

## <a name="speech-to-text"></a>Riconoscimento vocale

Il riconoscimento vocale al testo viene fornito come integrazione con LUIS.

### <a name="intent-conversion-concepts"></a>Concetti relativi alla conversione Intent
La conversione del riconoscimento vocale in LUIS consente di inviare espressioni vocali a un endpoint e ricevere una risposta di stima da LUIS. Il processo è un'integrazione del servizio [Voce](https://docs.microsoft.com/azure/cognitive-services/Speech) di LUIS. Per altre informazioni sul riconoscimento finalità voce, eseguire un'[esercitazione](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Requisiti chiave
Per questa integrazione non è necessario creare una chiave **API Riconoscimento vocale Bing **. È sufficiente la chiave **Language Understanding** creata nel portale di Azure. Non usare la chiave di avvio LUIS.

### <a name="pricing-tier"></a>Piano tariffario
Questa integrazione usa un modello [prezzi](luis-limits.md#key-limits) diverso rispetto ai normali piani tariffari di Language Understanding.

### <a name="quota-usage"></a>Uso delle quote
Per informazioni, vedere [Key limits](luis-limits.md#key-limits) (Limiti chiave).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrazione di dati](luis-concept-data-extraction.md)

