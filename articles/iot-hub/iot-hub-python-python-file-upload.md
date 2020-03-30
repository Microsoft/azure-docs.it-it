---
title: Caricare file da dispositivi nell'hub IoT di Azure con Python | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Python. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110740"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Caricare file dal dispositivo al cloud con IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questo articolo mostra come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file in una risorsa di [archiviazione BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

* Fornire in modo sicuro un contenitore di archiviazione per il caricamento di un file.

* Usare il client Python per caricare un file tramite l'hub IoT.

La guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md) illustra la funzionalità di base di messaggistica da dispositivo a cloud dell'hub IoT.The Send telemetry from a device to an IoT hub quickstart demonstrates the basic device-to-cloud messaging functionality of IoT Hub. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

> [!NOTE]
> Python SDK per hub IoT attualmente supporta solo il caricamento dei file basati sui caratteri come i file **.txt**.

Al termine di questa esercitazione eseguire l'app console Python:

* **FileUpload.py**, che carica un file nella risorsa di archiviazione tramite Python SDK per dispositivi.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Questa guida usa l'SDK Python per V1 deprecato, poiché la funzionalità di caricamento dei file non è ancora stata implementata nel nuovo SDK V2.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene creata l'app del dispositivo per caricare un file nell'hub IoT.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iothub-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Con un editor di testo, creare un file di test da caricare nella risorsa di archiviazione BLOB.

    > [!NOTE]
    > Python SDK per hub IoT attualmente supporta solo il caricamento dei file basati sui caratteri come i file **.txt**.

3. Con un editor di testo, creare un file **FileUpload.py** nella cartella di lavoro.

4. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **FileUpload.py**. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Nel file sostituire `[Device Connection String]` con la stringa di connessione del dispositivo dell'hub IoT. Sostituire `[Full path to file]` con il percorso del file di test creato o di qualsiasi file nel dispositivo che si vuole caricare. Sostituire `[File name for storage]` con il nome che si vuole assegnare al file dopo che è stato caricato nella risorsa di archiviazione BLOB. 

6. Creare un callback per la funzione **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Aggiungere il codice seguente per connettere il client e caricare il file. Includere anche la routine `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Salvare e chiudere il file **UploadFile.py**.

## <a name="run-the-application"></a>Eseguire l'applicazione

A questo punto è possibile eseguire l'applicazione.

1. Al prompt dei comandi nella cartella di lavoro eseguire il comando seguente:

    ```cmd/sh
    python FileUpload.py
    ```

2. Lo screenshot seguente mostra l'output dell'app **FileUpload**:

    ![Output dell'app simulated-device](./media/iot-hub-python-python-file-upload/1.png)

3. Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

    ![File caricato](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDKs](iot-hub-devguide-sdks.md) (SDK di IoT di Azure)
