---
title: Identità e modulo gemello dell'hub IoT di Azure (Python)Azure IoT Hub module identity and module twin (Python)
description: Come creare l'identità del modulo e aggiornare il modulo gemello usando gli SDK per IoT per Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: e18d448d9aee0137f1167d23a2bbf53486d0c764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953856"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Introduzione all'identità del modulo Hub IoT e al modulo gemello (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Le identità del modulo e i moduli gemelli](iot-hub-devguide-module-twins.md) sono simili alle identità del dispositivo e ai dispositivi gemelli dell'hub IoT di Azure, ma offrono una granularità superiore. Mentre l'identità del dispositivo e il dispositivo gemello dell'hub IoT di Azure consentono all'applicazione back-end di configurare un dispositivo e forniscono visibilità sulle condizioni del dispositivo, l'identità del modulo e il modulo gemello forniscono queste funzionalità per i singoli componenti di un dispositivo. Nei dispositivi con più componenti, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware, è possibile isolare la configurazione e le condizioni di ogni componente.
>

Al termine di questa esercitazione si hanno due app Python:

* **CreateIdentities**, che crea un'identità del dispositivo, un'identità del modulo e una chiave di sicurezza associata per connettere i client dispositivo e modulo.

* **UpdateModuleTwinReportedProperties**, che invia le proprietà segnalate aggiornate del modulo gemello all'hub IoT.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub IoTGet the IoT hub connection string

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Creare un'identità del dispositivo e un'identità del modulo nell'hub IoT

In questa sezione si scriverà un'app Python che crea un'identità del dispositivo e un'identità del modulo nel registro delle identità dell'hub IoT. Un dispositivo o un modulo non può connettersi all'hub IoT se non ha una voce nel registro delle identità. Per ulteriori informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-identity-registry.md). Quando si esegue questa app console vengono generati un ID e una chiave univoci sia per il dispositivo che per il modulo. Il dispositivo e il modulo usano questi valori per identificarsi quando inviano messaggi da dispositivo a cloud all'hub IoT. Negli ID viene fatta distinzione tra maiuscole e minuscole.

Aggiungere il codice seguente al file Python:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Questa app crea un'identità del dispositivo con ID **myFirstDevice** e un'identità del modulo con ID **myFirstModule** per il dispositivo **myFirstDevice**. Se tale ID modulo esiste già nel registro delle identità, il codice recupera semplicemente le informazioni sul modulo esistente. L'app visualizza quindi la chiave primaria per tale identità. Questa chiave verrà usata dall'app per modulo simulato per connettersi all'hub IoT.

> [!NOTE]
> Il registro delle identità dell'hub IoT archivia solo le identità del dispositivo e del modulo per abilitare l'accesso sicuro all'hub. Il registro delle identità archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza. Il registro delle identità archivia anche un flag di abilitazione/disabilitazione per ogni dispositivo che consente di disabilitare l'accesso per un dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Non esiste alcun flag abilitato/disabilitato per le identità del modulo. Per altre informazioni, vedere [La guida per gli sviluppatori dell'hub IoT](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Aggiornare il modulo gemello usando l'SDK per dispositivi Python

In questa sezione, nel dispositivo simulato viene creata un'app Python che aggiorna le proprietà restituite del modulo gemello.

1. **Ottenere la stringa** di connessione del modulo, ora se si accede al portale di [Azure](https://portal.azure.com/). Passare all'hub IoT e fare clic su Dispositivi IoT. Trovare myFirstDevice e aprirlo. Si vedrà che myFirstModule è stato creato correttamente. Copiare la stringa di connessione del modulo. Sarà necessaria nel prossimo passaggio.

   ![Dettagli del modulo nel portale di Azure](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Creare l'app UpdateModuleTwinReportedPropertiesCreate UpdateModuleTwinReportedProperties app**

   Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Questo esempio di codice mostra come recuperare il modulo gemello e aggiornare le proprietà segnalate con il protocollo AMQP.

## <a name="get-updates-on-the-device-side"></a>Ottenere aggiornamenti sul lato dispositivo

Oltre al codice precedente, è possibile aggiungere il blocco di codice seguente per ottenere il messaggio di aggiornamento del modulo gemello nel dispositivo.

```python
import time
import threading
from azure.iot.device import IoTHubModuleClient

CONNECTION_STRING = "{deviceConnectionString}"


def twin_update_listener(client):
    while True:
        patch = client.receive_twin_desired_properties_patch()  # blocking call
        print("")
        print("Twin desired properties patch received:")
        print(patch)

def iothub_client_sample_run():
    try:
        module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

        twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
        twin_update_listener_thread.daemon = True
        twin_update_listener_thread.start()

        while True:
            time.sleep(1000000)

    except KeyboardInterrupt:
        print("IoTHubModuleClient sample stopped")


if __name__ == '__main__':
    print ( "Starting the IoT Hub Python sample..." )
    print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

    iothub_client_sample_run()
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT e per esplorare altri scenari IoT, vedere:

* [Introduzione alla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md)

* [Introduzione a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)