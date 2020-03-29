---
title: Regioni - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Elenco delle aree e degli endpoint disponibili per il servizio di riconoscimento vocale, incluse la sintesi vocale, la sintesi vocale e la traduzione vocale.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220468"
---
# <a name="speech-service-supported-regions"></a>Aree supportate dal servizio vocale

Il servizio Voce consente alle applicazioni di eseguire operazioni di riconoscimento e sintesi vocale, ovvero conversione di audio in testo e viceversa, e anche di traduzione vocale. Il servizio è disponibile in più aree con endpoint univoci per Speech SDK e le API REST.

Il portale di riconoscimento vocale per eseguire configurazioni personalizzate per l'esperienza vocale per tutte le aree è disponibile qui:https://speech.microsoft.com

Per le chiamate del servizio di riconoscimento vocale, assicurarsi che la chiamata corrisponda all'area della sottoscrizione.

## <a name="speech-sdk"></a>Speech SDK

In [Speech SDK](speech-sdk.md) le aree vengono specificate sotto forma di stringa (ad esempio, come parametro `SpeechConfig.FromSubscription` in Speech SDK per C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Sintesi vocale, sintesi vocale e traduzione

Il portale di personalizzazione vocale è disponibile qui:https://speech.microsoft.com

Il servizio di riconoscimento vocale è disponibile in queste aree per il **riconoscimento vocale,** la **sintesi vocale**e la **traduzione:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se si utilizza [Speech SDK](speech-sdk.md), le aree vengono specificate `SpeechConfig.FromSubscription`dall'identificatore **Region** (ad esempio, come parametro per ). Assicurarsi che l'area corrisponda all'area della sottoscrizione.

### <a name="intent-recognition"></a>Riconoscimento finalità

Le aree disponibili per il **riconoscimento finalità** tramite Speech SDK sono le seguenti:

| Regione globale | Region           | Identificatore area |
| ------------- | ---------------- | -------------------- |
| Asia          | Asia orientale        | `eastasia`           |
| Asia          | Asia sud-orientale   | `southeastasia`      |
| Australia     | Australia orientale   | `australiaeast`      |
| Europa        | Europa settentrionale     | `northeurope`        |
| Europa        | Europa occidentale      | `westeurope`         |
| America del Nord | Stati Uniti orientali          | `eastus`             |
| America del Nord | Stati Uniti orientali 2        | `eastus2`            |
| America del Nord | Stati Uniti centro-meridionali | `southcentralus`     |
| America del Nord | Stati Uniti centro-occidentali  | `westcentralus`      |
| America del Nord | Stati Uniti occidentali          | `westus`             |
| America del Nord | Stati Uniti occidentali 2        | `westus2`            |
| America del Sud | Brasile meridionale     | `brazilsouth`        |

Questo è un sottoinsieme delle aree di pubblicazione supportate per il [servizio Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Assistenti vocali

Speech SDK supporta le funzionalità **dell'assistente vocale** nelle aree seguenti:The [Speech SDK](speech-sdk.md) supports voice assistant capabilities in these regions:

| Region         | Identificatore area |
| -------------- | -------------------- |
| Stati Uniti occidentali        | `westus`             |
| Stati Uniti occidentali 2      | `westus2`            |
| Stati Uniti orientali        | `eastus`             |
| Stati Uniti orientali 2      | `eastus2`            |
| Europa occidentale    | `westeurope`         |
| Europa settentrionale   | `northeurope`        |
| Asia sud-orientale | `southeastasia`      |

## <a name="rest-apis"></a>API REST

Il servizio Voce espone anche endpoint REST per le richieste di riconoscimento vocale e sintesi vocale.

### <a name="speech-to-text"></a>Riconoscimento vocale

Per la documentazione di riferimento vocale, vedere [API REST di sintesi vocale](rest-speech-to-text.md).

L'endpoint per l'API REST ha questo formato:The endpoint for the REST API has this format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Sostituire `<REGION_IDENTIFIER>` con l'identificatore corrispondente all'area della sottoscrizione da questa tabella:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Il parametro di lingua deve essere aggiunto all'URL per evitare di ricevere un errore 4xx HTTP. Ad esempio, la lingua impostata su inglese (Stati Uniti) usando l'endpoint per l'area Stati Uniti occidentali è: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Sintesi vocale

Per la documentazione di riferimento di sintesi vocale, vedere [API REST di sintesi vocale](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
