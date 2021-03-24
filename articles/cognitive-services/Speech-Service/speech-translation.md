---
title: Panoramica della traduzione vocale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La traduzione vocale consente di aggiungere la traduzione vocale end-to-end, in tempo reale e multilingue per le applicazioni, gli strumenti e i dispositivi. La stessa API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale. In questo articolo viene illustrata una panoramica dei vantaggi e delle funzionalità del servizio di traduzione vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: traduzione vocale
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954961"
---
# <a name="what-is-speech-translation"></a>Che cos'è la traduzione vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In questa panoramica vengono illustrati i vantaggi e le funzionalità del servizio di traduzione vocale, che consente la traduzione vocale e vocale in tempo reale e [multilingue](language-support.md#speech-translation) dei flussi audio. Con l'SDK di riconoscimento vocale, le applicazioni, gli strumenti e i dispositivi hanno accesso a trascrizioni di origine e output di traduzione per l'audio fornito. I risultati della trascrizione e della traduzione provvisori vengono restituiti quando viene rilevato il riconoscimento vocale e i risultati finali possono essere convertiti in sintesi vocale.

## <a name="core-features"></a>Funzionalità di base

* Traduzione vocale con risultati di riconoscimento.
* Traduzione vocale.
* Supporto per la conversione a più lingue di destinazione.
* Risultati del riconoscimento provvisorio e della traduzione.

## <a name="get-started"></a>Introduzione 

Per iniziare a usare la traduzione vocale, vedere la [Guida introduttiva](get-started-speech-translation.md) . Il servizio di traduzione vocale è disponibile tramite l' [SDK vocale](speech-sdk.md) e l'interfaccia della riga di comando [vocale](spx-overview.md).

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per l'SDK di riconoscimento vocale è disponibile su GitHub. Questi esempi illustrano scenari comuni, ad esempio la lettura di audio da un file o un flusso, il riconoscimento e la traduzione continui e singoli e l'utilizzo di modelli personalizzati.

* [Esempi di sintesi vocale e traduzione (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guide alla migrazione

Se le applicazioni, gli strumenti o i prodotti usano la [API traduzione vocale](./how-to-migrate-from-translator-speech-api.md), sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.

* [Eseguire la migrazione dalla API Traduzione vocale al servizio riconoscimento vocale](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](./speech-sdk.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Passaggi successivi

* Completare la [Guida introduttiva](get-started-speech-translation.md) alla traduzione vocale
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
* [Ottenere Speech SDK](speech-sdk.md)