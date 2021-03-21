---
title: Panoramica del riconoscimento preventivo-servizio vocale
titleSuffix: Azure Cognitive Services
description: Il riconoscimento preventivo consente di riconoscere gli obiettivi utente già definiti. In questo articolo viene illustrata una panoramica dei vantaggi e delle funzionalità del servizio di riconoscimento preventivo.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: Riconoscimento delle finalità
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018702"
---
# <a name="what-is-intent-recognition"></a>Che cos'è il riconoscimento preventivo?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In questa panoramica vengono illustrati i vantaggi e le funzionalità di riconoscimento preventivo. I servizi cognitivi Speech SDK si integrano con il servizio Language Understanding Intelligent Service (LUIS) per fornire il riconoscimento finalità. Una finalità è qualcosa che l'utente desidera fare: prenotare un volo, controllare il meteo o effettuare una chiamata.
Utilizzando il riconoscimento preventivo, le applicazioni, gli strumenti e i dispositivi possono determinare le informazioni che l'utente desidera avviare o eseguire in base alle opzioni definite in LUIS.

## <a name="luis-key-required"></a>Chiave LUIS obbligatoria

* LUIS si integra con il servizio di riconoscimento vocale per distinguere le finalità dai contenuti vocali. Non è necessaria una sottoscrizione al servizio di riconoscimento vocale, LUIS è sufficiente.
* Il riconoscimento della finalità vocale è integrato con l'SDK. È possibile usare una chiave LUIS con il servizio di riconoscimento vocale.
* Il riconoscimento preventivo tramite Speech SDK è [disponibile in un sottoinsieme di aree supportate da Luis](./regions.md#intent-recognition).

## <a name="get-started"></a>Introduzione

Per iniziare a usare il riconoscimento preventivo, vedere la [Guida introduttiva](quickstarts/intent-recognition.md) .

## <a name="sample-code"></a>Codice di esempio

Codice di esempio per il riconoscimento preventivo:

* [Guida introduttiva: Usare l'app di domotica predefinita](../luis/luis-get-started-create-app.md)
* [Riconoscere le finalità dai contenuti vocali con Speech SDK per C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Riconoscimento preventivo e altri servizi vocali usando Unity in C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Riconoscere gli Intent usando l'SDK vocale per Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Riconoscimento preventivo e altri servizi vocali con l'SDK di riconoscimento vocale per C++ in Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Riconoscimento preventivo e altri servizi di riconoscimento vocale con l'SDK vocale per Java in Windows o Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Riconoscimento preventivo e altri servizi vocali con l'SDK di riconoscimento vocale per JavaScript in un Web browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>Passaggi successivi

* Completa la [Guida introduttiva](quickstarts/intent-recognition.md) al riconoscimento preventivo
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
* [Ottenere Speech SDK](speech-sdk.md)