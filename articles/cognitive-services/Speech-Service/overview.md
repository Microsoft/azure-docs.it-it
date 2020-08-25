---
title: Informazioni sul Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il servizio Voce di Azure combina il riconoscimento vocale, la sintesi vocale e la traduzione vocale in un'unica sottoscrizione di Azure. Aggiungere il riconoscimento vocale ad applicazioni, strumenti e dispositivi con Speech SDK, Speech Devices SDK o API REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 82099172a933496f015ae8fc575c1919a879e1f9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167743"
---
# <a name="what-is-the-speech-service"></a>Informazioni sul Servizio di riconoscimento vocale

Il servizio Voce di Azure combina il riconoscimento vocale, la sintesi vocale e la traduzione vocale in un'unica sottoscrizione di Azure. Con l'[interfaccia della riga di comando di Voce](spx-overview.md), [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart), [Speech Studio](https://speech.microsoft.com/) o le [API REST](rest-apis.md), è facile abilitare il riconoscimento vocale per applicazioni, strumenti e dispositivi.

> [!IMPORTANT]
> Il servizio Voce ha sostituito l'API Riconoscimento vocale Bing e Traduzione vocale. Vedere _Guide alle procedure > Migrazione_ per istruzioni relative alla migrazione.

Queste funzionalità costituiscono il servizio Voce. Per altre informazioni sui casi d'uso comuni per ogni funzionalità, usare i collegamenti in questa tabella o esplorare il riferimento API.

| Service | Funzionalità | Descrizione | SDK | REST |
|---------|---------|-------------|-----|------|
| [Riconoscimento vocale](speech-to-text.md) | Riconoscimento vocale in tempo reale | Il riconoscimento vocale trascrive o traduce in tempo reale flussi audio o file locali in testo da utilizzare o visualizzare in applicazioni, dispositivi o strumenti. È possibile usare il riconoscimento vocale con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) per derivare le finalità dell'utente dal parlato trascritto e agire sui comandi vocali. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Riconoscimento vocale in batch](batch-transcription.md) | Il riconoscimento vocale in batch consente la trascrizione asincrona in testo di una quantità elevata di dati audio vocali memorizzati in Archiviazione BLOB di Azure. Oltre a convertire l'audio vocale in testo, il riconoscimento vocale in batch consente anche di eseguire la diarizzazione e l'analisi del sentiment. | No | [Sì](https://westus.cris.ai/swagger/ui/index) |
| | [Conversazione per più dispositivi](multi-device-conversation.md) | Connessione di più dispositivi o client in una conversazione per l'invio di messaggi vocali o di testo, con il supporto semplificato per la trascrizione e la traduzione| Sì | No |
| | [Trascrizione conversazione](conversation-transcription-service.md) | Abilita il riconoscimento vocale in tempo reale, l'identificazione voce e la diarizzazione. È ideale per la trascrizione di riunioni dal vivo grazie alla capacità di distinguere i parlanti. | Sì | No |
| | [Creare modelli conversione voce/testo personalizzati](#customize-your-speech-experience) | Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore. | No | [Sì](https://westus.cris.ai/swagger/ui/index) |
| [Sintesi vocale](text-to-speech.md) | Sintesi vocale | Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano usando [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). È possibile scegliere tra voci standard e voci neurali (vedere [Supporto per le lingue](language-support.md)). | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creare voci personalizzate](#customize-your-speech-experience) | Creazione di caratteri voce personalizzati univoci per il proprio marchio o prodotto. | No | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduzione vocale](speech-translation.md) | Traduzione vocale | La traduzione vocale consente di attivare la traduzione vocale end-to-end in tempo reale e in più lingue in applicazioni, strumenti e dispositivi. È possibile usare questo servizio per il riconoscimento vocale e la traduzione vocale. | [Sì](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No |
| [Assistenti vocali](voice-assistants.md) | Assistenti vocali | Gli assistenti vocali che usano il servizio Voce consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per applicazioni ed esperienze. Il servizio di assistente vocale offre un'interazione rapida e affidabile tra un dispositivo e l'implementazione di un assistente che usa il canale Direct Line Speech di Bot Framework o il servizio integrato Comandi personalizzati (anteprima) per completare le attività. | [Sì](voice-assistants.md) | No |
| [Riconoscimento del parlante](speaker-recognition-overview.md) | Identificazione e verifica voce | Il servizio Riconoscimento del parlante fornisce algoritmi che verificano e identificano i parlanti in base alle relative caratteristiche vocali univoche. Il servizio Riconoscimento del parlante consente di rispondere alla domanda "chi parla?" | Sì | [Sì](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Provare il servizio Voce

Sono disponibili modelli di avvio rapido nei linguaggi di programmazione più diffusi, ognuno progettato per consentire all'utente di disporre dell'esecuzione di codice in meno di 10 minuti. Questa tabella contiene i modelli di avvio rapido più popolari per ogni funzionalità. Usare il riquadro di spostamento a sinistra per esplorare altri linguaggi e piattaforme.

| Riconoscimento vocale (SDK) | API Sintesi vocale (SDK) | Traduzione vocale (SDK) |
|----------------------|----------------------|-------------------|
| [Riconoscimento vocale da un file audio](quickstarts/speech-to-text-from-file.md) | [Sintesi vocale in un file audio](quickstarts/text-to-speech-audio-file.md) | [Traduzione vocale in testo](quickstarts/translate-speech-to-text.md) |
| [Riconoscimento vocale con un microfono](quickstarts/speech-to-text-from-microphone.md) | [Sintesi vocale in un altoparlante](quickstarts/text-to-speech.md) | [Tradurre la voce in più lingue di destinazione](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Riconoscimento della voce archiviata in archivio BLOB](quickstarts/from-blob.md) | [Sintesi asincrona per audio lungo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Conversione della voce in voce](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Per il riconoscimento vocale e la sintesi vocale sono disponibili anche endpoint REST e guide di avvio rapido associate.

Dopo avere avuto la possibilità di usare il servizio Voce, provare le esercitazioni che illustrano come risolvere diversi scenari.

- [Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS per C#](how-to-recognize-intents-from-speech-csharp.md)
- [Esercitazione: Abilitare il servizio voce per il bot con Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Esercitazione: Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare il testo tradotto in parlato, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Ottenere il codice di esempio

Il codice di esempio per il servizio Voce è disponibile in GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

- [Esempi di riconoscimento vocale, sintesi vocale e traduzione vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))
- [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)

## <a name="customize-your-speech-experience"></a>Personalizzare l'esperienza di riconoscimento vocale

Il servizio Voce funziona bene con i modelli predefiniti, tuttavia, è possibile personalizzare e ottimizzare ulteriormente l'esperienza in base al prodotto o all'ambiente in uso. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio.

| Servizio di riconoscimento vocale | Piattaforma | Descrizione |
| -------------- | -------- | ----------- |
| Riconoscimento vocale | [Riconoscimento vocale personalizzato](https://aka.ms/customspeech) | Personalizzare i modelli di riconoscimento vocale in base alle esigenze e ai dati disponibili. Superare gli ostacoli al riconoscimento vocale, come il modo di parlare, il vocabolario e il rumore di fondo. |
| Sintesi vocale | [Voce personalizzata](https://aka.ms/customvoice) | Creare un'unica voce riconoscibile per le app di sintesi vocale con i dati della lingua disponibili. È possibile ottimizzare ulteriormente gli output vocali regolando un set di parametri vocali. |

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: Riconoscimento vocale](rest-speech-to-text.md)
- [API REST: Sintesi vocale](rest-text-to-speech.md)
- [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
