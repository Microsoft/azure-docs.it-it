---
title: Traduzione vocale con servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio riconoscimento vocale consente di aggiungere la traduzione vocale end-to-end, in tempo reale e multilingue per le applicazioni, gli strumenti e i dispositivi. La stessa API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 670d7f160285283bd44371b893c63904b2685926
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934498"
---
# <a name="what-is-speech-translation"></a>Che cos'è la traduzione vocale?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La traduzione vocale da parte del servizio riconoscimento vocale consente la conversione in tempo reale e vocale in più lingue dei flussi audio. Con l'SDK di riconoscimento vocale, le applicazioni, gli strumenti e i dispositivi hanno accesso a trascrizioni di origine e output di traduzione per l'audio fornito. I risultati della trascrizione e della traduzione provvisori vengono restituiti quando viene rilevato un riconoscimento vocale e i risultati finali possono essere convertiti in sintesi vocale.

Il motore di traduzione Microsoft è basato su due approcci diversi: statistical machine translation (SMT) e Neural Machine Translation (NMT). SMT usa l'analisi statistica avanzata per stimare le migliori traduzioni possibili in base al contesto di alcune parole. Con NMT, le reti neurali vengono usate per fornire traduzioni più accurate e con suono naturale usando il contesto completo delle frasi per tradurre le parole.

Oggi Microsoft usa NMT per la traduzione in lingue più diffuse. Tutte le [lingue disponibili per la traduzione vocale](language-support.md#speech-translation) usano la tecnologia basata sulla traduzione automatica neurale. La traduzione con riconoscimento vocale può usare la traduzione automatica statistica o la traduzione automatica neurale a seconda della coppia di lingue. Quando la lingua di destinazione è supportata da NMT, la traduzione completa è basata su NMT. Quando la lingua di destinazione non è supportata da NMT, la traduzione è ibrida di NMT e SMT, usando la lingua inglese come "pivot" tra le due lingue.

## <a name="core-features"></a>Funzionalità di base

Di seguito sono riportate le funzionalità disponibili tramite l'SDK vocale e le API REST:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Traduzione vocale con risultati di riconoscimento. | Sì | No |
| Traduzione vocale. | Sì | No |
| Risultati del riconoscimento provvisorio e della traduzione. | Sì | No |

## <a name="get-started-with-speech-translation"></a>Introduzione alla traduzione vocale

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per la traduzione vocale organizzate in base alla lingua.

| Avvio rapido | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Sfoglia](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Sfoglia](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per l'SDK di riconoscimento vocale è disponibile su GitHub. Questi esempi illustrano scenari comuni, ad esempio la lettura di audio da un file o un flusso, il riconoscimento e la traduzione continui e singoli e l'utilizzo di modelli personalizzati.

* [Esempi di sintesi vocale e traduzione (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guide alla migrazione

Se le applicazioni, gli strumenti o i prodotti usano la [API traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), sono state create guide che consentono di eseguire la migrazione al servizio di riconoscimento vocale.

* [Eseguire la migrazione dalla API Traduzione vocale al servizio riconoscimento vocale](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere Speech SDK](speech-sdk.md)
