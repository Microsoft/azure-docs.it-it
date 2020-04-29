---
title: Assistenti vocali-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per gli assistenti vocali che usano il Software Development Kit (SDK) di riconoscimento vocale.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79369897"
---
# <a name="what-is-a-voice-assistant"></a>Che cos'è un assistente vocale?

Gli assistenti vocali che usano il servizio riconoscimento vocale consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per le proprie applicazioni ed esperienze.

Il servizio Voice Assistant fornisce un'interazione rapida e affidabile tra un dispositivo e un'implementazione di Assistant che usa (1) il canale di sintesi vocale diretta di bot Framework o (2) il servizio integrato di comandi personalizzati (anteprima) per il completamento delle attività.

Le applicazioni si connettono al servizio Voice Assistant con Speech Software Development Kit (SDK).

   ![Diagramma concettuale del flusso del servizio di orchestrazione di Assistente vocale](media/voice-assistants/overview.png "Il flusso di Assistente vocale")

## <a name="choosing-an-assistant-solution"></a>Scelta di una soluzione di assistente

Il primo passaggio per la creazione di un assistente vocale è quello di decidere cosa fare. Il servizio di riconoscimento vocale offre più soluzioni complementari per la creazione di interazioni con gli assistenti. Che tu voglia ottenere la flessibilità e la versatilità offerte dal canale di [sintesi vocale diretta](direct-line-speech.md) di bot Framework o la semplicità dei [comandi personalizzati (anteprima)](custom-commands.md) per scenari semplici, la selezione degli strumenti più appropriati ti consente di iniziare.

| Per... | Quindi, prendere in considerazione... | Per esempio... |
|-------------------|------------------|----------------|
|Conversazione aperta con Solid Skills Integration e controllo completo della distribuzione | Canale di [sintesi vocale diretta](direct-line-speech.md) di bot Framework | <ul><li>"Devo andare a Seattle"</li><li>"Che tipo di pizza è possibile ordinare?"</li></ul>
|Gestione dei comandi e delle conversazioni orientate alle attività con la creazione e l'hosting semplificate | [Comandi personalizzati (anteprima)](custom-commands.md) | <ul><li>"Accendere la luce del sovraccarico"</li><li>"Renderlo 5 gradi più caldo"</ul>

Se non si è certi di cosa si vuole che l'assistente debba gestire, è consigliabile usare [Direct Line Speech](direct-line-speech.md) come la scelta migliore. Offre l'integrazione con un set completo di strumenti e strumenti per la creazione, ad esempio la [soluzione Virtual Assistant e il modello Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , e il [servizio QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) per creare modelli comuni e usare le origini delle informazioni esistenti.

I [comandi personalizzati (anteprima)](custom-commands.md) offrono un'esperienza di creazione e hosting semplificata, appositamente adattata per gli scenari di comando e controllo del linguaggio naturale.

   ![Confronto tra soluzioni di assistente](media/voice-assistants/assistant-solution-comparison.png "Confronto tra soluzioni di assistente")

## <a name="core-features"></a>Funzionalità di base

Indipendentemente dal fatto che si scelga [Direct Line Speech](direct-line-speech.md) o [Custom Commands (anteprima)](custom-commands.md) per creare le interazioni con gli assistenti, è possibile usare un set completo di funzionalità di personalizzazione per personalizzare l'assistente per il marchio, il prodotto e la personalità.

| Category | Caratteristiche |
|----------|----------|
|[Parola chiave Custom](speech-devices-sdk-create-kws.md) | Gli utenti possono avviare conversazioni con gli assistenti con una parola chiave personalizzata, ad esempio "Hey contoso". Un'app esegue questa operazione con un motore di parole chiave personalizzato nell'SDK di riconoscimento vocale, che può essere configurato con una parola chiave personalizzata [che è possibile generare qui](speech-devices-sdk-create-kws.md). Gli assistenti vocali possono usare la verifica delle parole chiave sul lato servizio per migliorare l'accuratezza dell'attivazione della parola chiave (rispetto al dispositivo da solo).
|[Riconoscimento vocale](speech-to-text.md) | Gli assistenti vocali convertono l'audio in tempo reale in testo riconosciuto usando il [riconoscimento vocale](speech-to-text.md) dal servizio di riconoscimento vocale. Questo testo è disponibile, in quanto viene trascritto, sia per l'implementazione dell'assistente che per l'applicazione client.
|[Sintesi vocale](text-to-speech.md) | Le risposte testuali dell'Assistente vengono sintetizzate tramite sintesi [vocale](text-to-speech.md) dal servizio di riconoscimento vocale. Questa sintesi viene quindi resa disponibile all'applicazione client come flusso audio. Microsoft offre la possibilità di creare una voce personalizzata neurale e di alta qualità che fornisce una voce al proprio marchio. Per altre informazioni, [Contattaci](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Introduzione agli assistenti vocali

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per Assistente vocale, organizzate in base alla lingua.

| Avvio rapido | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| C#, UWP | Windows | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Sfoglia](https://aka.ms/csspeech/javaref) |
| Java | Android | [Sfoglia](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale è disponibile su GitHub. Questi esempi coprono l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione più diffusi.

* [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)
* [Esercitazione: abilitare l'Assistente vocale con l'SDK di riconoscimento vocale, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Esercitazione

Esercitazione su come abilitare l' [Assistente vocale usando l'SDK di riconoscimento vocale e il canale di sintesi vocale diretta](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati con il servizio di riconoscimento vocale possono usare l'intera gamma di opzioni di personalizzazione disponibili per la scelta [vocale](speech-to-text.md) [, la sintesi vocale e](text-to-speech.md)la [selezione di parole chiave personalizzate](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere Speech SDK](speech-sdk.md)
* [Altre informazioni sui comandi personalizzati (anteprima)](custom-commands.md)
* [Scopri di più su Direct Line Speech](direct-line-speech.md)
