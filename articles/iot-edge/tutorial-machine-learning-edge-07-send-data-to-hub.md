---
title: 'Esercitazione: Inviare i dati dei dispositivi tramite un gateway trasparente - Machine Learning in Azure IoT Edge'
description: Questa esercitazione mostra come usare il computer di sviluppo come dispositivo IoT Edge simulato per inviare i dati all'hub IoT passando attraverso un dispositivo configurato come gateway trasparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a830c2cbbc16d5d5e93e6f2283af012e4412c0be
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310649"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Esercitazione: Inviare i dati tramite un gateway trasparente

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In questo articolo si usa di nuovo la macchina virtuale di sviluppo come dispositivo simulato. Tuttavia, invece di inviare i dati direttamente all'hub IoT, il dispositivo li invia al dispositivo IoT Edge configurato come gateway trasparente.

Il funzionamento del dispositivo IoT Edge viene monitorato mentre il dispositivo simulato invia i dati. Al termine dell'esecuzione del dispositivo, i dati nell'account di archiviazione vengono esaminati per verificare che tutto abbia funzionato come previsto.

Questo passaggio viene in genere eseguito da uno sviluppatore per il cloud o per il dispositivo.

In questa sezione dell'esercitazione si apprende come:

> [!div class="checklist"]
>
> * Creare ed eseguire un dispositivo foglia.
> * Verificare che i dati generati siano archiviati nell'archiviazione BLOB di Azure.
> * Verificare che il modello di Machine Learning abbia classificato i dati del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Ogni articolo della serie si basa sulle attività di quello precedente. Se questo articolo è stato aperto direttamente, vedere il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie.

## <a name="review-device-harness"></a>Esaminare l'harness del dispositivo

Riutilizzare il [progetto DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) per simulare il dispositivo downstream (o foglia). Per la connessione al gateway trasparente è necessario eseguire altre due operazioni:

* Registrare il certificato in modo che il dispositivo IoT downstream consideri attendibile l'autorità di certificazione usata dal runtime IoT Edge. In questo caso, il dispositivo downstream è la macchina virtuale di sviluppo.
* Aggiungere il nome di dominio completo (FQDN) del gateway perimetrale alla stringa di connessione del dispositivo.

Osservare il codice per vedere come vengono implementati questi due elementi.

1. Nel computer di sviluppo aprire Visual Studio Code.

1. Usare **File** > **Apri cartella** per aprire C:\\source\\IoTEdgeAndMlSample\\DeviceHarness.

1. Osservare il metodo InstallCertificate() in Program.cs.

1. Se il codice trova il percorso del certificato, chiama il metodo CertificateManager.InstallCACert per installare il certificato nel computer.

1. Osservare ora il metodo GetIotHubDevice nella classe TurbofanDevice.

1. Quando l'utente specifica il nome FQDN del gateway usando l'opzione "-g", tale valore viene passato a questo metodo come variabile `gatewayFqdn`, che viene accodata alla stringa di connessione del dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Creare ed eseguire il dispositivo foglia

1. Con il progetto DeviceHarness ancora aperto in Visual Studio Code, creare il progetto. Dal menu **Terminale** scegliere **Esegui attività di compilazione** e selezionare **Compila**.

1. Trovare il nome di dominio completo (FQDN) del gateway perimetrale passando al dispositivo IoT Edge (VM Linux) nel portale di Azure e copiando il valore di **Nome DNS** dalla pagina di panoramica.

1. Avviare il dispositivo IoT (VM Linux) se non è già in esecuzione.

1. Aprire il terminale di Visual Studio Code. Dal menu **Terminale** scegliere **Nuovo terminale**  ed eseguire il comando seguente, sostituendo `<edge_device_fqdn>` con il nome DNS copiato dal dispositivo IoT Edge (VM Linux):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. L'applicazione prova a installare il certificato nel computer di sviluppo. Quando ci riesce, accettare l'avviso di sicurezza.

1. Quando viene richiesta la stringa di connessione per l'hub IoT, fare clic sui puntini di sospensione ( **...** ) nel pannello dei dispositivi dell'hub Azure IoT e scegliere **Copy IoT Hub Connection String** (Copia stringa di connessione hub IoT). Incollare il valore nel terminale.

1. L'output sarà simile al seguente:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Notare l'aggiunta di "GatewayHostName" alla stringa di connessione del dispositivo. In questo modo il dispositivo comunica con l'hub IoT tramite il gateway trasparente IoT Edge.

## <a name="check-output"></a>Verificare l'output

### <a name="iot-edge-device-output"></a>Output del dispositivo IoT Edge

L'output proveniente dal modulo avroFileWriter può essere osservato guardando il dispositivo IoT Edge.

1. Usare SSH per connettersi alla macchina virtuale IoT Edge.

1. Cercare i file scritti su disco.

   ```bash
   find /data/avrofiles -type f
   ```

1. L'output del comando sarà simile all'esempio seguente:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Può accadere che sia presente più di un singolo file a seconda dell'ora di esecuzione.

1. Prestare attenzione ai timestamp. Il modulo avroFileWriter carica i file nel cloud quando l'ultima ora di modifica è trascorsa da più di 10 minuti (vedere MODIFIED\_FILE\_TIMEOUT in uploader.py nel modulo avroFileWriter).

1. Trascorsi 10 minuti, il modulo dovrebbe caricare i file. Se il caricamento ha esito positivo, elimina i file dal disco.

### <a name="azure-storage"></a>Archiviazione di Azure

È possibile osservare i risultati dell'invio dei dati da parte del dispositivo foglia guardando gli account di archiviazione in cui si prevede che vengano instradati i dati.

1. Nel computer di sviluppo aprire Visual Studio Code.

1. Nel pannello "ARCHIVIAZIONE DI AZURE", nella finestra di esplorazione, scorrere l'albero per trovare il proprio account di archiviazione.

1. Espandere il nodo **Contenitori BLOB**.

1. In base al lavoro svolto nella parte precedente dell'esercitazione, si prevede che il contenitore **ruldata** includa i messaggi con la vita utile rimanente. Espandere il nodo **ruldata**.

1. Verranno visualizzati uno o più file BLOB denominati come segue: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

1. Fare clic con il pulsante destro del mouse su uno dei file e scegliere **Scarica BLOB** per salvare il file nel computer di sviluppo.

1. Espandere quindi il nodo **uploadturbofanfiles**. Nell'articolo precedente è stata impostata questa posizione come destinazione per i file caricati dal modulo avroFileWriter.

1. Fare clic con il pulsante destro del mouse sui file e scegliere **Scarica BLOB** per salvarlo nel computer di sviluppo.

### <a name="read-avro-file-contents"></a>Leggere il contenuto del file Avro

È stata inclusa una semplice utilità da riga di comando per leggere un file Avro e restituire una stringa JSON dei messaggi nel file. In questa sezione verrà installata ed eseguita.

1. Aprire una finestra del terminale in Visual Studio Code (**Terminale** > **Nuovo terminale**).

1. Installare hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Usare hubavroreader per leggere il file Avro scaricato da **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Notare che il corpo del messaggio ha l'aspetto atteso con l'ID dispositivo e la vita utile rimanente prevista.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Eseguire lo stesso comando passando il file Avro scaricato da **uploadturbofanfiles**.

1. Come previsto, questi messaggi contengono tutti i dati dei sensori e le impostazioni operative del messaggio originale. Questi dati potrebbero essere usati per migliorare il modello relativo alla vita utile rimanente nel dispositivo perimetrale.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende esplorare le risorse usate da questa esercitazione end-to-end, attendere fino alla fine per pulire le risorse create. In caso contrario, seguire questa procedura per eliminarle:

1. Eliminare i gruppi di risorse creati per contenere la macchina virtuale di sviluppo, la macchina virtuale IoT Edge, l'hub IoT, l'account di archiviazione, l'area di lavoro del servizio Machine Learning (e le risorse create, ovvero registro contenitori, informazioni dettagliate sull'applicazione, insieme di credenziali delle chiavi e account di archiviazione).

1. Eliminare il progetto di Machine Learning in [Azure Notebooks](https://notebooks.azure.com).

1. Se il repository è stato clonato localmente, chiudere le eventuali finestre di PowerShell o VS Code che fanno riferimento al repository locale e quindi eliminare la directory del repository.

1. Se i certificati sono stati creati localmente, eliminare la cartella c:\\edgeCertificates.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata usata la VM di sviluppo per simulare un dispositivo foglia che invia i dati dei sensori e i dati operativi al dispositivo IoT Edge. È stato verificato che i moduli nel dispositivo abbiano instradato, classificato, salvato in modo permanente e caricato i dati prima esaminando il funzionamento in tempo reale del dispositivo perimetrale e quindi controllando i file caricati nell'account di archiviazione.

Per approfondire la conoscenza delle funzionalità di IoT Edge, provare a eseguire l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creare una gerarchia di dispositivi IoT Edge](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)