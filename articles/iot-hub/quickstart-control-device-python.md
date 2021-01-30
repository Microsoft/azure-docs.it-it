---
title: "Guida introduttiva: controllare un dispositivo dalla Guida introduttiva all'hub Internet Azure (Python) | Microsoft Docs"
description: In questa guida introduttiva vengono eseguite due applicazioni Python di esempio. Una è un'applicazione back-end che può controllare in remoto i dispositivi connessi all'hub. L'altra applicazione simula un dispositivo connesso all'hub che può essere controllato in remoto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: d7e7eb80d452620cdbb0f6688d6137ad34951dbe
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070722"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Guida introduttiva: Controllare un dispositivo connesso a un hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In questa guida di avvio rapido si usa un metodo diretto per controllare un dispositivo simulato connesso all'hub IoT di Azure. L'hub IoT è un servizio di Azure che consente di gestire i dispositivi IoT dal cloud e inserire volumi elevati di dati di telemetria dei dispositivi nel cloud per l'archiviazione o l'elaborazione. È possibile usare metodi diretti per modificare in remoto il comportamento di un dispositivo connesso all'hub IoT. Questa guida di avvio rapido usa due applicazioni Python: un'applicazione del dispositivo simulato che risponde ai metodi diretti chiamati da un'applicazione back-end e un'applicazione back-end che chiama i metodi diretti sul dispositivo simulato.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7+](https://www.python.org/downloads/). Per informazioni sulle altre versioni di Python supportate, vedere [Funzionalità per i dispositivi IoT di Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Un progetto Python di esempio](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Porta 8883 aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md), ignorare questo passaggio.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: Inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md), ignorare questo passaggio.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    **MyPythonDevice**: nome del dispositivo da registrare. È consigliabile usare **MyPythonDevice**, come illustrato. Se si sceglie un altro nome per il dispositivo, è necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

3. È necessaria anche una _stringa di connessione del servizio_ per consentire all'applicazione back-end di connettersi all'hub IoT dell'utente e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

    **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Annotare la stringa di connessione del servizio, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva. Questa stringa di connessione del servizio è diversa dalla stringa di connessione del dispositivo annotata nel passaggio precedente.

## <a name="listen-for-direct-method-calls"></a>Ascoltare le chiamate dei metodi diretti

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT, invia dati di telemetria simulati e ascolta le chiamate dei metodi diretti dall'hub. In questa guida introduttiva la chiamata dei metodi diretti dall'hub indica al dispositivo di modificare l'intervallo di invio dei dati di telemetria. Il dispositivo simulato invia un acknowledgement all'hub dopo l'esecuzione del metodo diretto.

1. In una finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\simulated-device-2**.

1. Aprire il file **SimulatedDevice.py** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `CONNECTION_STRING` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche nel file **SimulatedDevice.py**.

1. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione del dispositivo simulato:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La schermata seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Chiamare il metodo diretto

L'applicazione back-end si connette a un endpoint sul lato servizio nell'IoT Hub dell'utente. L'applicazione effettua chiamate dei metodi diretti a un dispositivo tramite l'hub IoT e rimane in ascolto degli acknowledgement. Un'applicazione back-end dell'hub IoT in genere viene eseguita nel cloud.

1. In un'altra finestra del terminale locale passare alla cartella radice del progetto Python di esempio. Passare quindi alla cartella **iot-hub\Quickstarts\back-end-application**.

1. Aprire il file **BackEndApplication.py** in un editor di testo di propria scelta.

    Sostituire il valore della variabile `CONNECTION_STRING` con la stringa di connessione del servizio annotata in precedenza. Salvare quindi le modifiche nel file **BackEndApplication.py**.

1. Nella finestra del terminale locale eseguire i comandi seguenti per installare le librerie necessarie per l'applicazione del dispositivo simulato:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Nella finestra del terminale locale eseguire i comandi seguenti per eseguire l'applicazione back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    La schermata seguente mostra l'output visualizzato quando l'applicazione effettua una chiamata del metodo diretto al dispositivo e riceve un acknowledgement:

    ![Eseguire l'applicazione back-end](./media/quickstart-control-device-python/backend-application.png)

    Dopo l'esecuzione dell'applicazione back-end, viene visualizzato un messaggio nella finestra della console che esegue il dispositivo simulato e cambia la frequenza di invio dei messaggi:

    ![Modifica nel client simulato](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato chiamato un metodo diretto su un dispositivo da un'applicazione back-end ed è stata inviata una risposta alla chiamata del metodo diretto in un'applicazione del dispositivo simulato.

Per informazioni su come indirizzare messaggi da dispositivo a cloud a diverse destinazioni nel cloud, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: indirizzare i dati di telemetria a endpoint diversi per l'elaborazione](tutorial-routing.md)
