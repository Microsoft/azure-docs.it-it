---
title: Pianificare processi con l'hub IoT di Azure (Node) | Microsoft Docs
description: Come pianificare un processo dell'hub IoT di Azure per richiamare un metodo diretto su più dispositivi. Usare Azure IoT SDK per Node.js per implementare le app per dispositivi simulati e un'app di servizio per eseguire il processo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81732259"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Pianificare e trasmettere processi (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

L'hub IoT di Azure è un servizio completamente gestito che consente a un'app back-end di creare e tenere traccia dei processi che pianificano e aggiornano milioni di dispositivi.  I processi possono essere usati per le azioni seguenti:

* Aggiornare le proprietà desiderate
* Aggiornare i tag
* Richiamare metodi diretti

Concettualmente, un processo esegue il wrapping di una di queste azioni e tiene traccia dell'avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query di dispositivi gemelli.  Grazie a un processo, ad esempio, un'app back-end può richiamare un metodo di riavvio in 10.000 dispositivi, specificato da una query di dispositivi gemelli e pianificato in un secondo momento. L'applicazione può quindi tenere traccia dell'avanzamento mentre ognuno dei dispositivi riceve ed esegue il metodo di riavvio.

Altre informazioni su queste funzionalità sono disponibili in questi articoli:

* Dispositivo gemello e proprietà: [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md) ed [esercitazione: Come usare le proprietà del dispositivo gemello](tutorial-device-twins.md)

* Metodi diretti: [Guida per gli sviluppatori dell'hub Internet-metodi diretti](iot-hub-devguide-direct-methods.md) ed [esercitazione: metodi diretti](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Questa esercitazione illustra come:

* Creare un'app Node.js per dispositivo simulato con un metodo diretto che abilita il metodo diretto **lockDoor** che può essere chiamato dal back-end della soluzione.

* Creare un'app console Node.js che chiama il metodo diretto **lockDoor** nell'app per dispositivo simulato tramite un processo e aggiorna le proprietà desiderate tramite un processo del dispositivo.

Al termine di questa esercitazione si avranno due app Node.js:

* **simDevice.js**, che si connette all'hub Internet delle cose con l'identità del dispositivo e riceve un metodo diretto **lockDoor** .

* **scheduleJobService.js**, che chiama un metodo diretto nell'app per dispositivo simulato e aggiorna le proprietà desiderate di un dispositivo gemello tramite un processo.

## <a name="prerequisites"></a>Prerequisiti

* Node.js 10.0.x o versione successiva. [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Preparare l'ambiente di sviluppo) descrive come installare Node.js per questa esercitazione in Windows o Linux.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app di dispositivo simulato

In questa sezione viene creata un'applicazione console Node.js che risponde a un metodo chiamato dal cloud, che attiva un metodo **lockDoor** simulato.

1. Creare una nuova cartella vuota chiamata **simDevice**.  Nella cartella **simDevice** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

   ```console
   npm init
   ```

2. Eseguire questo comando al prompt dei comandi nella cartella **simDevice** per installare il pacchetto SDK per dispositivi **azure-iot-device** e il pacchetto **azure-iot-device-mqtt**:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Con un editor di testo creare un nuovo file **simDevice.js** nella cartella **simDevice**.

4. Aggiungere le istruzioni "require" seguenti all'inizio del file **simDevice.js**:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Aggiungere una variabile **connectionString** e usarla per creare un'istanza **Client**. Sostituire il `{yourDeviceConnectionString}` valore del segnaposto con la stringa di connessione del dispositivo copiata in precedenza.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Aggiungere la funzione seguente per gestire il metodo **lockDoor** .

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Aggiungere il codice seguente per registrare il gestore per il metodo **lockDoor** .

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Salvare e chiudere il file **simDevice.js**.

> [!NOTE]
> Per semplicità, in questa esercitazione non si implementa alcun criterio di ripetizione dei tentativi. Nel codice di produzione è consigliabile implementare criteri di ripetizione dei tentativi, ad esempio un backoff esponenziale, come suggerito nell'articolo [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Pianificare i processi per chiamare un metodo diretto e aggiornare le proprietà dei dispositivi gemelli

In questa sezione si crea un'app console Node.js che avvia un **lockDoor** remoto su un dispositivo usando un metodo diretto e aggiorna le proprietà del dispositivo gemello.

1. Creare una nuova cartella vuota chiamata **scheduleJobService**.  Nella cartella **scheduleJobService** creare un file package.json eseguendo questo comando al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```console
    npm init
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **scheduleJobService** per installare il pacchetto SDK per dispositivi **azure-iothub** e il pacchetto **azure-iot-device-mqtt**:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Usando un editor di testo, creare un nuovo file **scheduleJobService.js** nella cartella **scheduleJobService**.

4. Aggiungere le istruzioni "require" seguenti all'inizio del file di **scheduleJobService.js** :

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Aggiungere le dichiarazioni di variabili seguenti. Sostituire il `{iothubconnectionstring}` valore del segnaposto con il valore copiato in [ottenere la stringa di connessione dell'hub Internet](#get-the-iot-hub-connection-string). Se è stato registrato un dispositivo diverso da **myDeviceId**, assicurarsi di modificarlo nella condizione di query.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente che verrà usata per monitorare l'esecuzione del processo:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Aggiungere il codice seguente per pianificare il processo che chiama il metodo del dispositivo:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Aggiungere il codice seguente per pianificare il processo che aggiorna il dispositivo gemello:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Salvare e chiudere il file **scheduleJobService.js**.

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella **simDevice** eseguire questo comando per iniziare l'ascolto del metodo diretto di riavvio.

    ```console
    node simDevice.js
    ```

2. Eseguire il comando riportato di seguito al prompt dei comandi nella cartella **scheduleJobService** per attivare i processi per bloccare la porta e aggiornare il dispositivo gemello

    ```console
    node scheduleJobService.js
    ```

3. Viene visualizzata la risposta del dispositivo al metodo diretto e lo stato del processo nella console di.

   Di seguito viene illustrata la risposta del dispositivo al metodo diretto:

   ![Output dell'app del dispositivo simulato](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Di seguito vengono illustrati i processi di pianificazione dei servizi per il metodo diretto e l'aggiornamento del dispositivo gemello e i processi in esecuzione fino al completamento:

   ![Eseguire un'app di dispositivo simulato](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un processo per pianificare un metodo diretto in un dispositivo e aggiornare le proprietà di un dispositivo gemello.

Per continuare a usare i modelli di gestione di hub e dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware aereo, vedere [esercitazione: come eseguire un aggiornamento del firmware](tutorial-firmware-update.md).

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Introduzione a IoT Edge di Azure](../iot-edge/tutorial-simulate-device-linux.md).
