---
title: 'Esercitazione: Creare un modulo Python personalizzato con Azure IoT Edge'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice Python e distribuirlo in un dispositivo perimetrale.
services: iot-edge
author: kgremban
manager: philmea
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d6a95dae91ef3e6aa7d39cf8af51c355a87ea73a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462729"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>Esercitazione: sviluppare e distribuire un modulo di IoT Edge Python con i contenitori Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Usare Visual Studio Code per sviluppare codice Python e distribuirlo in un dispositivo che esegue Azure IoT Edge.

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore nel dispositivo IoT Edge configurato nell'argomento di avvio rapido. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Usare Visual Studio Code per creare un modulo Python per IoT Edge.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come sviluppare un modulo in **Python** usando **Visual Studio Code** e come distribuirlo in un dispositivo IOT Edge.

IoT Edge non supporta i moduli Python che usano i contenitori di Windows.

Usare la tabella seguente per comprendere le opzioni per lo sviluppo e la distribuzione di moduli Python con i contenitori Linux:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Usare Visual Studio Code per i moduli Python su Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Usare Visual Studio Code per i moduli Python su Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Prima di iniziare questa esercitazione, è necessario eseguire l'esercitazione precedente per configurare l'ambiente di sviluppo per lo sviluppo di contenitori Linux: [sviluppare IOT Edge moduli con i contenitori Linux](tutorial-develop-for-linux.md). Completando tale esercitazione, saranno soddisfatti i prerequisiti seguenti:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un dispositivo che esegue Azure IoT Edge. È possibile usare le guide introduttive per configurare un [dispositivo Linux](quickstart-linux.md) o un [dispositivo Windows](quickstart.md).
* Un registro contenitori, ad esempio [Registro Azure Container](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per sviluppare un modulo IoT Edge in Python, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo:

* [Estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code.
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) per l'installazione di pacchetti Python (generalmente incluso con l'installazione di Python).

>[!Note]
>Assicurarsi che la cartella `bin` sia nel percorso per la piattaforma, in genere `~/.local/` per UNIX e macOS o `%APPDATA%\Python` in Windows.

## <a name="create-a-module-project"></a>Creare un progetto di modulo

La procedura seguente consente di creare un modulo Python per IoT Edge tramite Visual Studio Code e gli strumenti di Azure IoT.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare un modello di soluzione Python da poter personalizzare con il proprio codice.

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code.

2. Nel riquadro comandi immettere ed eseguire il comando **Azure: Accedere** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt e specificare le informazioni seguenti per creare la soluzione:

   | Campo | valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Modulo Python**. |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **PythonModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore di **Server di accesso** del registro contenitori di Azure. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br>Il repository di immagini finale sarà simile a \<registry name\>.azurecr.io/pythonmodule. |

   ![Specificare il repository di immagini Docker](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il repository di contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge.

L'estensione IoT Edge cerca di eseguire il pull delle credenziali del Registro Container da Azure, per inserirle nel file di ambiente. Verificare se le credenziali sono già incluse. In caso contrario, aggiungerle:

1. Nello strumento di esplorazione di VS Code aprire il file con estensione **env**.
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal Registro Azure Container.
3. Salvare il file con estensione env.

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code può sviluppare moduli Python per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. L'impostazione predefinita è Linux AMD64.

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra.

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

In ogni modello è incluso il codice di esempio, che accetta i dati del sensore simulato dal modulo **SimulatedTemperatureSensor** e li indirizza all'hub IoT. In questa sezione aggiungere il codice che espande **PythonModule** per analizzare i messaggi prima di inviarli.

1. Nello strumento di esplorazione di VS Code aprire **modules** > **PythonModule** > **main.py**.

2. All'inizio del file **main.py** importare la libreria **json**:

    ```python
    import json
    ```

3. Aggiungere le variabili **TEMPERATURE_THRESHOLD** e **TWIN_CALLBACKS** nei contatori globali. La soglia della temperatura imposta il valore che la temperatura del computer misurata deve superare per inviare i dati all'hub IoT.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Sostituire la funzione **input1_listener** con il codice seguente:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Aggiornare i **listener** per restare in ascolto anche di aggiornamenti dei dispositivi gemelli.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Salvare il file main.py.

7. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge.

8. Aggiungere il modulo gemello **PythonModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **moduleContent** dopo il modulo gemello **$edgeHub**:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-python-module/module-twin.png)

9. Salvare il file deployment.template.json.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto a PythonModule il codice per filtrare i messaggi in cui la temperatura del computer segnalata è compresa nei limiti accettabili. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**.

2. Accedere a Docker immettendo il comando seguente nel terminale. Accedere con il nome utente, la password e il server di accesso del Registro Azure Container. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

3. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente.

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Usare Esplora risorse di Visual Studio Code e l'estensione Azure IoT Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.amd64.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. Nello strumento di esplorazione di Visual Studio Code espandere **Dispositivi** nella sezione **Azure IoT Hub** (Hub IoT di Azure) per visualizzare l'elenco dei dispositivi IoT.

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Selezionare il file **deployment.amd64.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

4. Nel dispositivo espandere **Moduli** per visualizzare un elenco dei moduli distribuiti e in esecuzione. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **PythonModule** in esecuzione insieme al modulo **SimulatedTemperatureSensor** e a **$edgeAgent** e **$edgeHub**.

    L'avvio dei moduli potrebbe richiedere alcuni minuti. Il runtime IoT Edge deve ricevere il nuovo manifesto della distribuzione, eseguire il pull delle immagini dei moduli dal runtime del contenitore e quindi avviare ogni nuovo modulo.

## <a name="view-the-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati.

È possibile visualizzare lo stato del dispositivo IoT Edge tramite la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. Espandere i dettagli del dispositivo per visualizzare un elenco dei moduli distribuiti e in esecuzione.

1. Nella finestra di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

2. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo del messaggio potrebbe richiedere del tempo. Il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. In seguito, le modifiche apportate al codice fanno sì che PythonModule attenda che la temperatura della macchina raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**.

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Si è usato il modulo gemello PythonModule nel manifesto della distribuzione per impostare la soglia di temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio Code espandere i dettagli relativi al dispositivo IoT Edge per vedere i moduli in esecuzione.

2. Fare clic con il pulsante destro del mouse su **PythonModule** e scegliere **Edit module twin** (Modifica il modulo gemello).

3. Trovare **TemperatureThreshold** nelle proprietà desiderate. Sostituire il valore con una nuova temperatura più alta di 5-10 gradi rispetto all'ultima temperatura segnalata.

4. Salvare il file del modulo gemello.

5. Fare clic con il pulsante destro del mouse in qualsiasi punto del riquadro di modifica del modulo gemello e scegliere **Update module twin** (Aggiorna modulo gemello).

6. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge contenente il codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge.

Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Funzioni](tutorial-deploy-function.md)
> [Analisi di flusso](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)