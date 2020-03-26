---
title: "Guida introduttiva: Creare un'app iOS con Unity"
description: In questa guida introduttiva verrà illustrato come creare un'app iOS con Unity usando Ancoraggi nello spazio.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215529"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app iOS in Unity con gli ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app iOS in Unity usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app ARKit per iOS compilata con Unity in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Preparare le impostazioni di compilazione di Unity
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Esportare il progetto Xcode
> * Distribuire ed eseguire l'app in un dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer macOS in cui sono installati <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 o 2019.2</a>, la versione più recente di <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git installato tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Eseguire quindi `brew install git` e `brew install git-lfs`.
- Un dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatibile con ARKit</a> abilitato per lo sviluppo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Scaricare e aprire il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchors.Examples/Scenes` e aprire il file della scena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salvare la scena selezionando **File** -> **Salva**.

## <a name="export-the-xcode-project"></a>Esportare il progetto Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Seguire le istruzioni nell'app per posizionare e richiamare un ancoraggio.

Al termine, arrestare l'app premendo **Stop** (Arresta) in Xcode.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="rendering-issues"></a>Problemi di rendering

Se durante l'esecuzione dell'app non si vede la videocamera sullo sfondo, ma si vede invece, ad esempio, uno sfondo vuoto, blu o un'altra trama, è probabilmente necessario reimportare gli asset in Unity. Arrestare l'app. Nel menu in alto in Unity scegliere **Assets -> Reimport all** (Asset -> Reimporta tutto). Quindi eseguire di nuovo l'app.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemi di CocoaPods in macOS Catalina (10.15)

Se di recente è stato eseguito l'aggiornamento a macOS Catalina (10.15) e CocoaPods è stato installato in precedenza, CocoaPods potrebbe interrompersi e non essere in grado di configurare correttamente i pod e i file di progetto `.xcworkspace`. Per risolvere questo problema, è necessario reinstallare CocoaPods eseguendo i comandi seguenti:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Unity 2019.3

A causa di modifiche che causano un'interruzione, Unity 2019.3 non è attualmente supportato. Usare Unity 2019.1 o 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
