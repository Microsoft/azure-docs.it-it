---
title: Introduzione alla gestione dei dispositivi dell'hub IoT di Azure (Node) | Documentazione Microsoft
description: Come usare la gestione dei dispositivi dell'hub IoT per riavviare un dispositivo remoto. Usare Azure IoT SDK per Node.js per implementare un'app per dispositivo simulato che include un metodo diretto e un'app di servizio che richiama il metodo diretto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: a7814970d4ffb051ab54c965d27394f925d79e59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732337"
---
# <a name="get-started-with-device-management-nodejs"></a>Introduzione alla gestione dei dispositivi (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Questa esercitazione illustra come:

* Creare un hub IoT nel [portale di Azure](https://portal.azure.com) e un'identità del dispositivo nell'hub IoT.

* Creare un'app di dispositivo simulato contenente un metodo diretto per il riavvio del dispositivo. I metodi diretti vengono richiamati dal cloud.

* Creare un'app console Node.js che chiama il metodo diretto di riavvio nell'app per dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione si avranno due app console Node.js:

* **dmpatterns_getstarted_device.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza, riceve un metodo diretto per il riavvio, simula un riavvio fisico e segnala il tempo impiegato per l'ultimo riavvio.

* **dmpatterns_getstarted_service.js**, che chiama un metodo diretto nel dispositivo simulato, visualizza la risposta e le proprietà segnalate aggiornate.

## <a name="prerequisites"></a>Prerequisiti

* Node.js 10.0.x o versione successiva. [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione verrà illustrato come:

* Creare un'app console Node.js che risponde a un metodo diretto chiamato dal cloud

* Attivare un riavvio del dispositivo simulato

* Usare le proprietà segnalate per abilitare le query nei dispositivi gemelli in modo da identificare i dispositivi e l'ora dell'ultimo riavvio

1. Creare una cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **manageddevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Con un editor di testo creare un file **dmpatterns_getstarted_device.js** nella cartella **manageddevice**.

4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_device.js**:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**.  Sostituire il valore segnaposto `{yourdeviceconnectionstring}` con la stringa di connessione del dispositivo copiata in precedenza nella sezione [Registrare un nuovo dispositivo nell’hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Aggiungere la funzione seguente per implementare il metodo diretto nel dispositivo

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Aprire la connessione all'hub IoT e avviare il listener del metodo diretto:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Salvare e chiudere il file **dmpatterns_getstarted_device.js**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Attivare un riavvio remoto nel dispositivo con un metodo diretto

In questa sezione viene creata un'app console Node.js che attiva un riavvio remoto in un dispositivo usando un metodo diretto. L'app esegue query nel dispositivo gemello per ottenere l'ora dell'ultimo riavvio del dispositivo in questione.

1. Creare una cartella vuota denominata **triggerrebootondevice**. Nella cartella **triggerrebootondevice** creare un file package.json eseguendo questo comando al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```cmd/sh
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **triggerrebootondevice** per installare il pacchetto SDK per dispositivi **azure-iothub** e il pacchetto **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Con un editor di testo creare un file **dmpatterns_getstarted_service.js** nella cartella **triggerrebootondevice**.

4. Aggiungere le istruzioni "require" seguenti all'inizio del file **dmpatterns_getstarted_service.js**:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Aggiungere le dichiarazioni di variabile seguenti e sostituire il valore segnaposto `{iothubconnectionstring}` con la stringa di connessione dell'hub IoT copiata in precedenza in [Ottenere la stringa di connessione dell’hub IoT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Aggiungere la funzione seguente per richiamare il metodo per riavviare il dispositivo di destinazione:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Aggiungere la funzione seguente per eseguire una query per il dispositivo e ottenere l'ora dell'ultimo riavvio:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Aggiungere il codice seguente per chiamare le funzioni che attivano il metodo diretto per il riavvio ed eseguire una query per ottenere l'ora dell'ultimo riavvio:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Salvare e chiudere il file **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Eseguire le app

A questo punto è possibile eseguire le app.

1. Al prompt dei comandi nella cartella **manageddevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Al prompt dei comandi nella cartella **triggerrebootondevice** eseguire questo comando per attivare il riavvio remoto e cercare il dispositivo gemello per trovare l'ora dell'ultimo riavvio.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Viene visualizzata la risposta del dispositivo al metodo diretto di riavvio e lo stato di riavvio nella console.

   Di seguito viene illustrata la risposta del dispositivo al metodo diretto di riavvio inviata dal servizio:

   ![manageddevice app output](./media/iot-hub-node-node-device-management-get-started/device.png)

   Di seguito viene illustrato il servizio che attiva il riavvio e il polling del dispositivo gemello per lo stato di ultimo riavvio:

   ![triggerrebootondevice app output](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
