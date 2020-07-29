---
title: Introduzione ai dispositivi gemelli dell'hub IoT di Azure (Node) | Documentazione Microsoft
description: Come usare i dispositivi gemelli dell'hub IoT di Azure per aggiungere tag e quindi usare una query dell'hub IoT. Usare Azure IoT SDK per Node.js per implementare l'app per dispositivo simulato e un'app di servizio che aggiunge i tag ed esegue la query dell'hub IoT.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt
ms.openlocfilehash: e65c781bd5cb62bdaa693b854caafd5f91fd497e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732288"
---
# <a name="get-started-with-device-twins-nodejs"></a>Introduzione ai dispositivi gemelli (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al termine di questa esercitazione si avranno due app console Node.js:

* **AddTagsAndQuery.js**, un'app back-end di Node.js, che aggiunge tag ed esegue query sui dispositivi gemelli.

* **TwinSimulatedDevice.js**, un'app Node.js che simula un dispositivo che si connette all'hub IoT con l'identità del dispositivo creata prima e segnala la condizione della connettività.

> [!NOTE]
> L'articolo [Azure IoT SDK](iot-hub-devguide-sdks.md) riporta informazioni sui componenti Azure IoT SDK che consentono di compilare le app back-end e per dispositivi.
>

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* Node.js 10.0.x o versione successiva.

* Un account Azure attivo. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Creare l'app di servizio

In questa sezione si crea a un'app console Node.js che aggiunge i metadati della posizione al dispositivo gemello associato a **myDeviceId**. Viene quindi effettuata una query dei dispositivi gemelli archiviati nell'hub IoT selezionando i dispositivi situati negli Stati Uniti e quindi quelli che segnalano una rete cellulare.

1. Creare una nuova cartella vuota denominata **addtagsandqueryapp**. Nella cartella **addtagsandqueryapp** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Il parametro `--yes` accetta tutti i valori predefiniti.

    ```cmd/sh
    npm init --yes
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **addtagsandqueryapp** per installare il pacchetto **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando un editor di testo, creare un nuovo file **AddTagsAndQuery.js** nella cartella **addtagsandqueryapp**.

4. Aggiungere il codice seguente al file **AddTagsAndQuery.js**. Sostituire `{iot hub connection string}` con la stringa di connessione dell'hub IoT copiata in [Ottenere la stringa di connessione dell'hub IoT](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    L'oggetto **Registry** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal servizio. Il codice precedente inizializza prima l'oggetto **Registry**, quindi recupera il dispositivo gemello per **myDeviceId** e infine ne aggiorna i tag con le informazioni sulla posizione desiderate.

    Dopo avere aggiornato i tag, chiama la funzione **queryTwins**.

5. Aggiungere il codice seguente alla fine di **AddTagsAndQuery.js** per implementare la funzione **queryTwins**:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Il codice precedente esegue due query: la prima seleziona solo i dispositivi gemelli tra quelli situati nello stabilimento **Redmond43** e la seconda affina la query per selezionare solo i dispositivi che sono anche connessi tramite la rete cellulare.

    Quando il codice crea l’oggetto **query** , specifica il numero massimo di documenti restituiti nel secondo parametro. L'oggetto **query** contiene una proprietà booleana **hasMoreResults** che è possibile usare per richiamare i metodi **nextAsTwin** più volte per recuperare tutti i risultati. Un metodo chiamato **next** è disponibile per i risultati che non sono dispositivi gemelli, ad esempio i risultati delle query di aggregazione.

6. Eseguire l'applicazione con:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Nei risultati si noterà un dispositivo per la query che cerca tutti i dispositivi situati in **Redmond43** e nessuno per la query che limita i risultati ai dispositivi che usano una rete cellulare.

   ![Vedere l'unico dispositivo nei risultati della query](media/iot-hub-node-node-twin-getstarted/service1.png)

Nella sezione successiva si crea un'app per dispositivo che segnala le informazioni sulla connettività e modifica il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare l'app per dispositivo

In questa sezione si crea un'app console Node.js che si connette all'hub come **myDeviceId** e quindi aggiorna le proprietà segnalate del dispositivo gemello per poter contenere le informazioni relative alla connessione usando una rete cellulare.

1. Creare una nuova cartella vuota denominata **reportconnectivity**. Nella cartella **reportconnectivity** creare un nuovo file package.json immettendo il comando seguente al prompt dei comandi. Il parametro `--yes` accetta tutti i valori predefiniti.

    ```cmd/sh
    npm init --yes
    ```

2. Eseguire questo comando al prompt dei comandi nella cartella **reportconnectivity** per installare i pacchetti **azure-iot-device** e **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando un editor di testo, creare un nuovo file **ReportConnectivity.js** nella cartella **reportconnectivity**.

4. Aggiungere il codice seguente al file **ReportConnectivity.js**. Sostituire `{device connection string}` con la stringa di connessione del dispositivo copiata durante la creazione dell’identità del dispositivo **myDeviceId** in [Registrare un nuovo dispositivo nell’hub IoT](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    L'oggetto **Client** espone tutti i metodi necessari per interagire con i dispositivi gemelli dal dispositivo. Il codice precedente, dopo avere inizializzato l'oggetto **Client**, recupera il dispositivo gemello per **myDeviceId** e aggiorna le proprietà segnalate con le informazioni sulla connettività.

5. Eseguire l'app per dispositivo

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Dovrebbe essere visualizzato il messaggio `twin state reported`.

6. Ora che il dispositivo ha segnalato le informazioni sulla connettività, verrà visualizzato in entrambe le query. Tornare alla cartella **addtagsandqueryapp** ed eseguire di nuovo le query:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Questa volta **myDeviceId** verrà visualizzato nei risultati di entrambe le query.

    ![Mostra myDeviceId in entrambi i risultati della query](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT nel Portale di Azure ed è stata quindi creata un'identità del dispositivo nel registro di identità dell'hub IoT. Sono stati aggiunti i metadati del dispositivo come tag da un'app back-end ed è stata scritta un'app per dispositivo simulato per segnalare le informazioni sulla connettività del dispositivo nel dispositivo gemello. Si è anche appreso come effettuare una query di queste informazioni usando il linguaggio di query simile a SQL dell'hub IoT.

Per altre informazioni, vedere le risorse seguenti:

* per inviare dati di telemetria dai dispositivi, vedere l'esercitazione [Introduzione all'hub IoT](quickstart-send-telemetry-node.md),

* per configurare i dispositivi usando le proprietà desiderate del dispositivo gemello, vedere l'esercitazione [Usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md);

* per controllare i dispositivi in modo interattivo, ad esempio per attivare un ventilatore da un'app controllata dall'utente, vedere l'esercitazione [Usare i metodi diretti](quickstart-control-device-node.md).
