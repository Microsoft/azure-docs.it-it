---
title: 'Guida introduttiva: Configurazione della piattaforma per .NET Framework (Windows) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per C# in .NET Framework per Windows con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: b47fd90a3fc92234d95ddc9d9f4f14b5af67d757
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926664"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per .NET Framework (Windows). Se si vuole solo il nome del pacchetto per iniziare autonomamente, eseguire `Install-Package Microsoft.CognitiveServices.Speech` nella console NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Creare un progetto di Visual Studio e installare Speech SDK

Sarà necessario procedere all'installazione del [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) per potervi fare riferimento nel proprio codice. A tale scopo, è prima di tutto necessario creare un progetto **HelloWorld**. Se si dispone già di un progetto con il carico di lavoro **.NET desktop development** disponibile, è possibile usare quel progetto e passare a [Usare NuGet Package Manager per installare Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Creazione di un progetto HelloWorld

1. Aprire Visual Studio 2019.

1. Nella finestra di avvio selezionare **Crea un nuovo progetto**. 

1. Nella finestra **Crea un nuovo progetto** scegliere **App console (.NET Framework)** e quindi fare clic su **Avanti**.

1. Nella finestra **Configura il nuovo progetto** immettere *helloworld* in **Nome progetto** scegliere o creare il percorso della directory in **Percorso** e quindi selezionare **Crea**.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

1. Assicurarsi che il carico di lavoro di **Sviluppo per desktop .NET** sia disponibile. Se il carico di lavoro non è stato installato, selezionare la casella di controllo accanto ad esso e quindi fare clic su **Modifica** per avviare l'installazione. Il download e l'installazione può richiedere alcuni minuti.

   Se la casella di controllo accanto a **Sviluppo per desktop .NET** è già selezionata, fare clic su **Chiudi** per chiudere la finestra di dialogo.

   ![Abilitare lo sviluppo per desktop .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Usare NuGet Package Manager per installare Speech SDK

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **helloworld** e quindi scegliere **Gestisci pacchetti NuGet** per visualizzare Gestione pacchetti NuGet.

   ![Gestione pacchetti NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Nell'angolo in alto a destra individuare la casella a discesa **Origine dei pacchetti** e assicurarsi che sia selezionata l'opzione **`nuget.org`** .

1. Nell'angolo in alto a sinistra fare clic su **Sfoglia**.

1. Nella casella di ricerca digitare *Microsoft.CognitiveServices.Speech* e premere **INVIO**.

1. Nei risultati della ricerca selezionare il pacchetto **Microsoft.CognitiveServices.Speech** e quindi selezionare **Installa** per installare la versione stabile più recente.

   ![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella finestra della **Console di Gestione pacchetti**.

### <a name="choose-target-architecture"></a>Scegliere l'architettura di destinazione

Per compilare ed eseguire l'applicazione console, creare una configurazione della piattaforma corrispondente all'architettura del computer.

1. Nella barra dei menu selezionare **Compila** > **Gestione configurazione**. Verrà visualizzata finestra di dialogo **Gestione configurazione**.

   ![Finestra di dialogo Gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Nella casella a discesa **Piattaforma soluzione attiva** selezionare **Nuova**. Verrà visualizzata la finestra di dialogo **Nuova piattaforma soluzione**.

1. Nella casella a discesa **Digitare o selezionare la nuova piattaforma**:
   - Se è in esecuzione Windows a 64 bit, selezionare **x64**.
   - Se è in esecuzione Windows a 32 bit, selezionare **x86**.

1. Fare clic su **OK** e quindi su **Chiudi**.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]
