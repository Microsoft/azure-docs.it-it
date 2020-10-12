---
title: Connettere gli DevKit alla soluzione di monitoraggio remoto-Azure | Microsoft Docs
description: In questa guida procedurale viene descritto come inviare i dati di telemetria dai sensori del dispositivo IoT DevKit AZ3166 all'acceleratore della soluzione di monitoraggio remoto per il monitoraggio e la visualizzazione.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: ed14d4cb79cb5794fa666f1bb96e062d69128d0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337671"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Connettere un dispositivo IoT DevKit all'acceleratore della soluzione di monitoraggio remoto

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Questa guida procedurale illustra come eseguire un'applicazione di esempio in un dispositivo IoT DevKit. Il codice di esempio invia i dati di telemetria dai sensori del dispositivo DevKit all'acceleratore della soluzione.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per tale scheda con [Azure IoT Device Workbench](https://aka.ms/iot-workbench) oppure il pacchetto di estensione [Azure IoT Tools](https://aka.ms/azure-iot-tools) in Visual Studio Code. Il [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contiene applicazioni di esempio che consentono di creare prototipi di soluzioni IoT.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura di questa esercitazione, effettuare le attività seguenti:

* Preparare il DevKit seguendo i passaggi descritti in [Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Aprire il progetto di esempio

Per aprire l'esempio della soluzione di monitoraggio remoto in VS Code:

1. Assicurarsi che IoT DevKit non sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples** (Azure IoT Device Workbench: Apri esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Trovare **Remote Monitoring** (Monitoraggio remoto) e fare clic su **Open Sample** (Apri esempio). Si apre una nuova finestra di Visual Studio Code che visualizza la cartella del progetto:

   ![IoT Workbench, selezionare l'esempio della soluzione di monitoraggio remoto](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Configurare il dispositivo

Per configurare la stringa di connessione del dispositivo hub IoT al dispositivo DevKit:

1. Passa alla **modalità di configurazione**DevKit:

    * Tenere premuto il pulsante **A**.
    * Premere e rilasciare il pulsante **Reimposta** .

1. La schermata visualizza l'ID DevKit e `Configuration`.

    ![Selezionare la modalità di configurazione di IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Premere **F1** per aprire il riquadro comandi, digitare e selezionare **Azure Internet Azure per dispositivi Workbench: configurare le impostazioni del dispositivo... > configurazione della stringa di connessione del dispositivo**.

1. Incollare la stringa di connessione copiata in precedenza e premere **Invio** per configurare il dispositivo.

## <a name="build-the-code"></a>Compilare il codice

Per compilare e caricare il codice del dispositivo:

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure Internet Azure per dispositivi Workbench: caricare il codice del dispositivo**:

1. Visual Studio Code compila e carica il codice nel dispositivo DevKit:

    ![IoT Workbench: Device - > Uploaded (Workbench IoT: Dispositivo > Caricato)](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Il dispositivo DevKit viene riavviato ed esegue il codice caricato.

## <a name="test-the-sample"></a>Testare l'esempio

Per verificare che l'applicazione di esempio caricata nel dispositivo DevKit funzioni correttamente, completare i passaggi seguenti:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Visualizzare i dati di telemetria inviati alla soluzione di monitoraggio remoto

Quando viene eseguita l'app di esempio, il dispositivo DevKit invia le informazioni di telemetria dai dati raccolti dai sensori all'acceleratore della soluzione tramite Wi-Fi. Per visualizzare le informazioni di telemetria:

1. Passare al dashboard della soluzione e fare clic su **Device Explorer**.

1. Fare clic sul nome del dispositivo DevKit. Nella scheda a destra è possibile visualizzare i dati di telemetria dal DevKit in tempo reale:

    ![Dati dei sensori in Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Controllare il dispositivo DevKit

L'acceleratore della soluzione di monitoraggio remoto consente di controllare il dispositivo in remoto. Il codice di esempio implementa tre metodi che è possibile visualizzare nella sezione **Method** quando si seleziona il dispositivo nella pagina **Device Explorer** :

![Metodi di IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Per cambiare il colore di uno dei LED del dispositivo DevKit, usare il metodo **LedColor**:

1. Selezionare il nome del dispositivo dall'elenco dei dispositivi e fare clic su **Processi**:

    ![Creare un processo](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configurare i processi con i valori seguenti e fare clic su **Applica**:

   * Select Job: **Run method** (Selezione processo: Esegui metodo)
   * Method name: **LedColor** (Nome del metodo: LedColor)
   * Job Name: **ChangeLedColor** (Nome del processo: ChangeLedColor)

     ![Impostazioni del processo](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Dopo qualche secondo, il DevKit cambierà il colore del LED RGB (sotto il pulsante A):

    ![LED rosso di IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare alle esercitazioni, lasciare l'acceleratore della soluzione di monitoraggio remoto distribuito.

Se l'acceleratore di soluzione non è più necessario, eliminarlo dalla pagina Soluzioni di cui è stato effettuato il provisioning selezionandolo e facendo clic su Elimina soluzione:

![Eliminare la soluzione](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti su IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare i canali seguenti per ricevere assistenza:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come connettere un dispositivo DevKit all'acceleratore della soluzione di monitoraggio remoto, sono consigliati i passaggi successivi seguenti:

* [Panoramica degli acceleratori di soluzioni Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Personalizzare l'interfaccia utente](iot-accelerators-remote-monitoring-customize.md)
