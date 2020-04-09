---
title: 'Guida introduttiva: Eseguire la sintesi vocale, C# (.NET Core) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in C# per .NET Core in Windows con Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 87fe6b49ce98b3fcb2b16c6573f81aa25fac7f5c
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671689"
---
> [!NOTE]
> .NET core è una multipiattaforma open source, multipiattaforma di .NET che implementa la specifica [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `Program.cs` e sostituire tutto il codice in questo file con quanto segue.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio con l'opzione Compila soluzione evidenziata](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio con l'opzione Avvia debug evidenziata](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Avviare l'app nel debug")

1. Viene visualizzata una finestra della console che chiede di digitare un testo. Digitare alcune parole o una frase. Il testo digitato viene trasmesso al servizio Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

    ![Screenshot dell'output della console dopo la sintesi riuscita](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Output della console dopo la sintesi riuscita")

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
