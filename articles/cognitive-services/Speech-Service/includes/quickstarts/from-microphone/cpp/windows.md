---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, C++ (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in C++ in Windows Desktop con Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 416c2893ebf07cd638f3f1c06c709db2586245d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925878"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire il file di origine **helloworld.cpp**.

1. Sostituire tutto il codice con il frammento di codice seguente:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con l'**identificatore di area** corrispondente all'[area](https://aka.ms/speech/sdkregion) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di prova gratuita.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione **helloworld**.

1. Pronunciare una frase o un'espressione in inglese. L'applicazione trasmette il parlato al servizio Voce, che effettua la trascrizione in testo e lo invia nuovamente all'applicazione per la visualizzazione.

   ![Output della console dopo il riconoscimento corretto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]