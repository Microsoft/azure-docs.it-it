---
title: Connettere MXChip IoT DevKit a Monitoraggio remoto dell’hub IoT di Azure
description: In questa esercitazione si apprende come inviare lo stato dei sensori di IoT DevKit AZ3166 all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785017"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Connettere MXChip IoT DevKit all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT

In questa esercitazione si apprende come eseguire un'app di esempio in DevKit per inviare i dati dei sensori all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Per la scheda esiste un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](./iot-hub-arduino-iot-devkit-az3166-get-started.md) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di prova gratuita di 30 giorni dell'account di Microsoft Azure](https://azure.microsoft.com/free/)

* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Creare un acceleratore di soluzioni di monitoraggio remoto di Azure IoT

1. Passare al [sito degli acceleratori di soluzione Azure IoT](https://www.azureiotsolutions.com/) e fare clic su **Creare una nuova soluzione**.

   ![Selezionare il tipo di acceleratore di soluzioni di Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Per impostazione predefinita, questo esempio crea un hub IoT S2 dopo aver creato un acceleratore di soluzioni di monitoraggio remoto IoT. Se questo hub IoT non viene usato con un numero elevato di dispositivi, è consigliabile effettuare il downgrade da S2 a S1 ed eliminare l'acceleratore di soluzioni di monitoraggio remoto IoT per poter eliminare anche l'hub IoT correlato quando non sarà più necessario. 

2. Selezionare **Monitoraggio remoto**.

3. Immettere il nome della soluzione, selezionare una sottoscrizione e un'area e quindi fare clic su **Crea soluzione**. Il provisioning della soluzione può richiedere tempo.
  
   ![Creare una soluzione](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Al termine del provisioning, fare clic su **Avvia**. Durante il processo di provisioning, vengono creati alcuni dispositivi simulati per la soluzione. Fare clic su **DISPOSITIVI** per estrarli.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Fare clic su **AGGIUNGI UN DISPOSITIVO**.

6. Fare clic su **Aggiungi nuovo** per **Dispositivo personalizzato**.
  
   ![Aggiungere un nuovo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Fare clic su **Desidero definire il mio ID dispositivo**, immettere `AZ3166` e quindi fare clic su **Crea**.
  
   ![Creare il dispositivo con l'ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Annotare il valore di **Nome host hub IoT** e fare clic su **Fine**.

## <a name="open-the-remotemonitoring-sample"></a>Aprire l'esempio RemoteMonitoring

1. Disconnettere il dispositivo DevKit dal computer, se è connesso.

2. Avviare Visual Studio Code.

3. Connettere il dispositivo DevKit al computer. Visual Studio Code rileva automaticamente il dispositivo DevKit e apre le pagine seguenti:

   * Pagina introduttiva di DevKit.
   * Esempi Arduino: esempi pratici per iniziare con il dispositivo DevKit.

4. Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **RemoteMonitoring**. Verrà visualizzata una nuova finestra di Visual Studio Code con una cartella di progetto all'interno.

   > [!NOTE]
   > Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="provision-required-azure-services"></a>Eseguire il provisioning dei servizi di Azure necessari

Nella finestra della soluzione eseguire l'attività premendo `Ctrl+P` (macOS: `Cmd+P`) e immettendo `task cloud-provision` nella casella di testo visualizzata.

Nel terminale di VS Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari.

![Effettuare il provisioning delle risorse di Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

1. Usare `Ctrl+P` (macOS: `Cmd + P`) e digitare **task config-device-connection**.

2. Il terminale chiede se si vuole usare una stringa di connessione per il recupero dal passaggio `task cloud-provision`. È anche possibile immettere una stringa di connessione del dispositivo personalizzata facendo clic su "Crea nuovo".

3. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza l'ID DevKit e la voce 'Configuration' (Configurazione).

   ![Immettere la stringa di connessione](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Al termine di `task config-device-connection`, fare clic su `F1` per caricare i comandi di Visual Studio Code e selezionare `Arduino: Upload`. Visual Studio Code inizia a verificare e a caricare lo sketch Arduino.
  
   ![Verifica e caricamento della definizione di Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

## <a name="test-the-project"></a>Verificare il progetto

Quando viene eseguita l'app di esempio, DevKit invia i dati dei sensori tramite Wi-Fi all'acceleratore di soluzioni di monitoraggio remoto Azure IoT. Per visualizzare il risultato, seguire questi passaggi:

1. Passare all'acceleratore di soluzioni di monitoraggio remoto Azure IoT e fare clic su **DASHBOARD**.

2. Nella console della soluzione di monitoraggio remoto verrà visualizzato lo stato dei sensori di DevKit.

   ![Dati dei sensori nell'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Modificare l'ID dispositivo

Se si vuole modificare il dispositivo **AZ3166** hardcoded in base all'ID dispositivo personalizzato nel codice, modificare la riga di codice visualizzata nell'[esempio di monitoraggio remoto](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2).

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti su IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare i canali seguenti per ricevere assistenza:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come connettere un dispositivo DevKit all'acceleratore di soluzioni di monitoraggio remoto Azure IoT e visualizzare i dati dei sensori, ecco i passaggi successivi consigliati:

* [Panoramica degli acceleratori di soluzioni Azure IoT](/azure/iot-suite/)

* [Kit per sviluppatori IoT](https://microsoft.github.io/azure-iot-developer-kit/)