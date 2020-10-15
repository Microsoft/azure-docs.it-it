---
title: 'Esercitazione: Distribuire un classificatore di Visione personalizzata in un dispositivo con Azure IoT Edge'
description: In questa esercitazione si configurerà un modello di Visione artificiale in modo che venga eseguito come un contenitore tramite il Servizio visione artificiale personalizzato e IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4854e61b646c0ca2a2070d676e3efc5cfbac6b9b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044582"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Esercitazione: Eseguire la classificazione delle immagini nella rete perimetrale con il Servizio visione artificiale personalizzato

Azure IoT Edge può rendere più efficienti le soluzioni IoT spostando i carichi di lavoro dal cloud alla rete perimetrale. Questa funzionalità è particolarmente utile per i servizi che elaborano grandi quantità di dati, come i modelli di visione artificiale. Il [Servizio visione artificiale personalizzato](../cognitive-services/custom-vision-service/overview.md) consente di compilare classificatori di immagini personalizzate e di distribuirli ai dispositivi come contenitori. Insieme, questi due servizi consentono di trovare informazioni dettagliate nelle immagini e nei flussi video senza dover prima trasferire tutti i dati in remoto. Il Servizio visione artificiale personalizzato fornisce un classificatore che confronta un'immagine con un modello con training per generare informazioni dettagliate.

In un dispositivo IoT Edge, ad esempio, il Servizio visione artificiale personalizzato potrebbe determinare se un'autostrada sta registrando un traffico superiore o inferiore al normale o se in un garage ci sono posti disponibili in una fila. Queste informazioni dettagliate possono essere condivise con un altro servizio in modo da intraprende l'azione appropriata.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Compilare un classificatore di immagini con il Servizio visione artificiale personalizzato.
> * Sviluppare un modulo IoT Edge che esegua query sul server Web del Servizio visione artificiale personalizzato nel dispositivo.
> * Inviare i risultati del classificatore di immagini all'hub IoT.

<center>

![Diagramma - Architettura dell'esercitazione, staging e classificatore della distribuzione](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

>[!TIP]
>Questa esercitazione è una versione semplificata del progetto di esempio [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (Visione personalizzata e Azure IoT Edge in un Raspberry Pi 3). È stata progettata per l'esecuzione in una macchina virtuale cloud e usa immagini statiche per eseguire il training e il test del classificatore di immagini, utile per chi inizia a valutare Visione personalizzata in IoT Edge. Il progetto di esempio usa hardware fisico e configura un feed di videocamera dal vivo per eseguire il training e il test del classificatore di immagini, utile per chi vuole provare uno scenario reale più dettagliato.

Prima di iniziare questa esercitazione, è necessario aver completato l'esercitazione precedente per configurare l'ambiente per lo sviluppo di contenitori Linux: [Sviluppare moduli IoT Edge per i dispositivi Linux](tutorial-develop-for-linux.md). Completando tale esercitazione, saranno soddisfatti i prerequisiti seguenti:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Linux che esegue Azure IoT Edge](quickstart-linux.md)
* Un registro contenitori, ad esempio [Registro Azure Container](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per sviluppare un modulo IoT Edge con il servizio Visione personalizzata, installare i prerequisiti aggiuntivi seguenti nel computer di sviluppo:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Compilare un classificatore di immagini con il Servizio visione artificiale personalizzato

Per compilare un classificatore di immagini, occorre creare un progetto Visione personalizzata e fornire immagini per il training. Per altre informazioni sulla procedura da eseguire in questa sezione, vedere [Come creare un classificatore con Visione personalizzata](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Dopo aver compilato ed eseguito il training del classificatore di immagini, è possibile esportarlo come contenitore Docker e distribuirlo in un dispositivo IoT Edge.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Nel Web browser passare alla [pagina web Visione personalizzata](https://customvision.ai/).

2. Selezionare **Sign in** (Accedi) ed eseguire l'accesso con lo stesso account che si usa per accedere alle risorse di Azure.

3. Selezionare **New project** (Nuovo progetto).

4. Creare il progetto con i valori seguenti:

   | Campo | valore |
   | ----- | ----- |
   | Nome | Specificare un nome per il progetto, ad esempio **EdgeTreeClassifier**. |
   | Descrizione | Descrizione facoltativa del progetto. |
   | Risorsa | Selezionare uno dei gruppi di risorse di Azure che include una risorsa del servizio Visione personalizzata o **crearne uno nuovo** se non ne è stato ancora aggiunto uno. |
   | Tipi di progetto | **Classificazione** |
   | Tipi di classificazione | **Multiclass (single tag per image)** (Multiclasse - singolo tag per immagine) |
   | Domini | **General (compact)** (Generale - compatto) |
   | Funzionalità di esportazione | **Piattaforme di base (Tensorflow, CoreML, ONNX, ...)** |

5. Selezionare **Create project** (Crea progetto).

### <a name="upload-images-and-train-your-classifier"></a>Caricare le immagini ed eseguire il training del classificatore

Per creare un classificatore di immagini sono necessari un set di immagini di training e un set di immagini di test.

1. Clonare o scaricare immagini di esempio dal repository [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) nel computer di sviluppo locale.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Tornare al progetto Visione personalizzata e selezionare **Add images** (Aggiungi immagini).

3. Individuare il repository Git clonato in locale e passare alla prima cartella di immagini, **Cognitive-CustomVision-Windows/Samples/Images/Hemlock**. Selezionare tutte le 10 immagini contenute nella cartella e scegliere **Open** (Apri).

4. Aggiungere il tag **hemlock** a questo gruppo di immagini e premere **INVIO** per applicarlo.

5. Selezionare **Upload 10 files** (Carica 10 file).

   ![Caricare file hemlock contenenti tag di Servizio visione artificiale personalizzato](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Al termine del caricamento, selezionare **Done** (Fine).

7. Selezionare di nuovo **Add images** (Aggiungi immagini).

8. Passare alla seconda cartella di immagini, **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry**. Selezionare tutte le 10 immagini contenute nella cartella e scegliere **Open** (Apri).

9. Aggiungere il tag **japanese cherry** a questo gruppo di immagini e premere **INVIO** per applicarlo.

10. Selezionare **Upload 10 files** (Carica 10 file). Al termine del caricamento, selezionare **Done** (Fine).

11. Dopo aver applicato il tag a entrambi i set di immagini e averli caricati, selezionare **Train** (Esegui il training) per eseguire il training del classificatore.

### <a name="export-your-classifier"></a>Esportare il classificatore

1. Completato il training del classificatore, selezionare **Export** (Esporta) nella pagina delle prestazioni del classificatore.

   ![Esportare la funzione di classificazione immagini di cui si è eseguito il training](./media/tutorial-deploy-custom-vision/export.png)

2. Selezionare **DockerFile** per la piattaforma. 

3. Selezionare **Linux** per la versione.  

4. Selezionare **Export** (Esporta). 

   ![Esportazione come DockerFile con contenitori Linux](./media/tutorial-deploy-custom-vision/export-2.png)

5. Al termine dell'esportazione, selezionare **Download** (Scarica) e salvare il pacchetto ZIP nel computer locale. Estrarre tutti i file dal pacchetto. Si useranno questi file per creare un modulo IoT Edge contenente il server di classificazione delle immagini. 

A questo punto la creazione e il training del progetto Visione personalizzata sono terminati. I file esportati verranno usati nella sezione successiva, ma non occorre più usare la pagina Web Visione personalizzata. 

## <a name="create-an-iot-edge-solution"></a>Creare una soluzione IoT Edge

I file per una versione contenitore del classificatore di immagini sono ora disponibili nel computer di sviluppo. In questa sezione il contenitore del classificatore di immagini verrà configurato in modo da essere eseguito come un modulo IoT Edge. Si creerà anche un secondo modulo da distribuire insieme al classificatore di immagini. Il secondo modulo pubblica le richieste nel classificatore e invia i risultati come messaggi all'hub IoT. 

### <a name="create-a-new-solution"></a>Creare una nuova soluzione

Una soluzione è un modo logico per sviluppare e organizzare più moduli per una singola distribuzione IoT Edge. Una soluzione contiene il codice per uno o più moduli e il manifesto di distribuzione che dichiara come configurarli in un dispositivo IoT Edge. 

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code. 

1. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Nel riquadro comandi immettere le informazioni seguenti per creare la soluzione: 

   | Campo | valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione, come **CustomVisionSolution**, oppure accettare l'impostazione predefinita. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Modulo Python**. |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **classifier**.<br><br>È importante che il nome del modulo sia in lettere minuscole. IoT Edge fa distinzione tra maiuscole e minuscole nei moduli e questa soluzione usa una libreria che formatta tutte le richieste in minuscolo. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata dall'ultimo passaggio. Sostituire **localhost:5000** con il valore del **server di accesso** nel registro contenitori di Azure. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure.<br><br>La stringa finale sarà simile a **\<registry name\>.azurecr.io/classifier**. |
 
   ![Specificare il repository di immagini Docker](./media/tutorial-deploy-custom-vision/repository.png)

La finestra di Visual Studio Code carica l'area di lavoro della soluzione IoT Edge.

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge.

L'estensione IoT Edge cerca di eseguire il pull delle credenziali del Registro Container da Azure, per inserirle nel file di ambiente. Verificare se le credenziali sono già incluse. In caso contrario, aggiungerle:

1. Nello strumento di esplorazione di Visual Studio Code aprire il file con estensione env.
2. Aggiornare i campi con i valori di **nome utente** e **password** copiati dal Registro Azure Container.
3. Salvare questo file.

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code consente di sviluppare moduli per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. Il valore predefinito è Linux AMD64, che verrà usato per questa esercitazione. 

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra. 

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**. 

### <a name="add-your-image-classifier"></a>Aggiungere il classificatore di immagini

Il modello di modulo Python in Visual Studio Code contiene del codice di esempio che può essere eseguito per testare IoT Edge. Questo codice non verrà usato in questo scenario. Usare invece la procedura descritta in questa sezione per sostituire il codice di esempio con il contenitore del classificatore di immagini esportato in precedenza. 

1. In Esplora file passare al pacchetto di Visione personalizzata precedentemente scaricato ed estratto. Copiare tutto il contenuto del pacchetto estratto. Dovrebbe essere costituito da due cartelle, **app** e **azureml**, e due file, **Dockerfile** e **README**. 

2. In Esplora file passare alla directory in cui si è indicato a Visual Studio Code di creare la soluzione IoT Edge. 

3. Aprire la cartella del modulo classifier. Se sono stati usati i nomi suggeriti nella sezione precedente, la struttura delle cartelle è simile a **CustomVisionSolution/modules/classifier**. 

4. Incollare i file nella cartella **classifier**. 

5. Tornare alla finestra di Visual Studio Code. Ora l'area di lavoro della soluzione dovrebbe mostrare i file del classificatore di immagini nella cartella dei moduli. 

   ![Area di lavoro della soluzione con i file del classificatore di immagini](./media/tutorial-deploy-custom-vision/workspace.png)

6. Aprire il file **module.json** nella cartella classifier. 

7. Aggiornare il parametro **platforms** in modo che punti al nuovo Dockerfile aggiunto e rimuovere tutte le opzioni tranne AMD64, che è l'unica architettura usata per questa esercitazione. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Salvare le modifiche. 

### <a name="create-a-simulated-camera-module"></a>Creare un modulo di videocamera simulata

Una distribuzione reale di Visione personalizzata includerebbe una videocamera che fornisce immagini o flussi video dal vivo. Per questo scenario si simulerà la videocamera compilando un modulo che invia un'immagine di test al classificatore di immagini. 

#### <a name="add-and-configure-a-new-module"></a>Aggiungere e configurare un nuovo modulo

In questa sezione si aggiungerà un nuovo modulo alla stessa CustomVisionSolution e si specificherà il codice per creare la videocamera simulata. 

1. Nella stessa finestra di Visual Studio Code usare il riquadro comandi per eseguire **Azure IoT Edge: Aggiungi modulo IoT Edge**. Nel riquadro comandi immettere le informazioni seguenti per il nuovo modulo: 

   | Prompt | valore | 
   | ------ | ----- |
   | Select deployment template file (Selezionare il file del modello di distribuzione) | Selezionare il file **deployment.template.json** nella cartella CustomVisionSolution. |
   | Select module template (Selezionare un modello di modulo) | Selezionare **Modulo Python** |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **cameraCapture** |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Sostituire **localhost:5000** con il valore di **Server di accesso** del Registro Azure Container.<br><br>La stringa finale sarà simile a **\<registryname\>.azurecr.io/cameracapture**. |

   La finestra di Visual Studio Code carica il nuovo modulo nell'area di lavoro della soluzione e aggiorna il file deployment.template.json. Ora si dovrebbero vedere due cartelle di moduli: classifier e cameraCapture. 

2. Aprire il file **main.py** nella cartella **modules** / **cameraCapture**. 

3. Sostituire l'intero file con il codice seguente. Questo codice di esempio invia richieste POST al servizio di elaborazione delle immagini in esecuzione nel modulo classifier. Questo contenitore di modulo viene fornito con un'immagine di esempio da usare nelle richieste. Il codice crea quindi un pacchetto della risposta come messaggio dell'hub IoT e lo invia a una coda di output.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Salvare il file **main.py**. 

5. Aprire il file **requrements.txt**. 

6. Aggiungere una nuova riga per una libreria da includere nel contenitore.

   ```Text
   requests
   ```

7. Salvare il file **requirements.txt** .


#### <a name="add-a-test-image-to-the-container"></a>Aggiungere un'immagine di test al contenitore

Invece di usare una videocamera reale per fornire un feed di immagini per questo scenario, si userà una singola immagine di test. Un'immagine di test è inclusa nel repository GitHub precedentemente scaricato per le immagini di training in questa esercitazione. 

1. Passare all'immagine di test nel percorso **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Copiare **test_image.jpg** 

3. Passare alla directory della soluzione IoT Edge e incollare l'immagine di test nella cartella **modules** / **cameraCapture**. L'immagine dovrebbe essere nella stessa cartella del file main.py modificato nella sezione precedente. 

4. In Visual Studio Code aprire il file **Dockerfile.amd64** per il modulo cameraCapture.

5. Dopo la riga che stabilisce la directory di lavoro, `WORKDIR /app`, aggiungere la riga di codice seguente:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Salvare il Dockerfile.

### <a name="prepare-a-deployment-manifest"></a>Preparare un manifesto di distribuzione

Fino a questo punto dell'esercitazione è stato eseguito il training di un modello di Visione personalizzata per classificare immagini di alberi ed è stato creato un pacchetto di tale modello come modulo IoT Edge. Si è poi creato un secondo modulo in grado di eseguire query sul server di classificazione delle immagini e riportarne i risultati all'hub IoT. A questo punto si è pronti per creare il manifesto di distribuzione che indicherà a un dispositivo IoT Edge come avviare ed eseguire questi due moduli insieme. 

L'estensione IoT Edge per Visual Studio Code fornisce un modello in ogni soluzione IoT Edge che semplifica la creazione di un manifesto di distribuzione. 

1. Aprire il file **deployment.template.json** nella cartella della soluzione. 

2. Trovare la sezione **modules**, che dovrebbe contenere tre moduli: i due creati nell'esercitazione, classifier e cameraCapture, e un terzo incluso per impostazione predefinita, SimulatedTemperatureSensor. 

3. Eliminare il modulo **SimulatedTemperatureSensor** con tutti i relativi parametri. Lo scopo di questo modulo è fornire dati di esempio per gli scenari di test, che in questa distribuzione non sono necessari. 

4. Se al modulo di classificazione delle immagini è stato assegnato un nome diverso da **classifier**, verificare che contenga solo lettere minuscole. Il modulo cameraCapture chiama il modulo Classifier mediante una libreria di richieste che formatta tutte le richieste in minuscolo, e IoT Edge rileva la differenza tra maiuscole e minuscole. 

5. Aggiornare il parametro **createOptions** per il modulo cameraCapture con il codice JSON seguente. Queste informazioni creano nel contenitore di moduli le variabili di ambiente recuperate nel processo main.py. Includendo queste informazioni nel manifesto di distribuzione, è possibile modificare l'immagine o l'endpoint senza dover ricompilare l'immagine del modulo. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Se al modulo di Visione personalizzata è stato assegnato un nome diverso da *classifier*, aggiornare di conseguenza il valore dall'endpoint di elaborazione delle immagini. 

6. In fondo al file, aggiornare il parametro **routes** per il modulo $edgeHub. In questo modo i risultati della stima verranno instradati da cameraCapture all'hub IoT.

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Se al secondo modulo è stato assegnato un nome diverso da *cameraCapture*, aggiornare di conseguenza il valore di routes. 

7. Salvare il file **deployment.template.json**.

## <a name="build-and-push-your-iot-edge-solution"></a>Eseguire la compilazione e il push della soluzione IoT Edge

Dopo aver creato i due moduli e aver configurato il modello di manifesto della distribuzione, si è pronti per compilare le immagini del contenitore ed eseguirne il push nel registro contenitori.

Una volta inserite le immagini nel registro, è possibile distribuire la soluzione in un dispositivo IoT Edge. È possibile impostare i moduli in un dispositivo tramite l'hub IoT, ma è anche possibile accedere all'hub IoT e ai dispositivi tramite Visual Studio Code. In questa sezione si configura l'accesso all'hub IoT e quindi si usa VS Code per distribuire la soluzione nel dispositivo IoT Edge.

Prima di tutto, compilare la soluzione ed eseguirne il push nel registro contenitori.

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**.

2. Accedere a Docker immettendo il comando seguente nel terminale. Accedere con il nome utente, la password e il server di accesso del Registro Azure Container. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

3. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and Push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente.

## <a name="deploy-modules-to-device"></a>Distribuire i moduli nel dispositivo

Usare Esplora risorse di Visual Studio Code e l'estensione Azure IoT Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.amd64.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. Nello strumento di esplorazione di Visual Studio Code espandere **Dispositivi** nella sezione **Azure IoT Hub** (Hub IoT di Azure) per visualizzare l'elenco dei dispositivi IoT.

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Selezionare il file **deployment.amd64.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

4. Nel dispositivo espandere **Moduli** per visualizzare un elenco dei moduli distribuiti e in esecuzione. Fare clic sul pulsante Aggiorna. Verranno visualizzati i nuovi moduli **classifier** e **cameraCapture** in esecuzione insieme a **$edgeAgent** e **$edgeHub**.  

È anche possibile verificare che tutti i moduli siano operativi nel dispositivo. Nel dispositivo IoT Edge eseguire questo comando per visualizzare lo stato dei moduli.

   ```bash
   iotedge list
   ```

L'avvio dei moduli potrebbe richiedere alcuni minuti. Il runtime IoT Edge deve ricevere il nuovo manifesto della distribuzione, eseguire il pull delle immagini dei moduli dal runtime del contenitore e quindi avviare ogni nuovo modulo.

## <a name="view-classification-results"></a>Visualizzare i risultati della classificazione

È possibile visualizzare i risultati dei moduli in due modi: nel dispositivo stesso quando i messaggi vengono generati e inviati oppure da Visual Studio Code man mano che i messaggi arrivano all'hub IoT. 

Dal dispositivo, visualizzare i log del modulo cameraCapture per vedere i messaggi inviati e la conferma che siano stati ricevuti dall'hub IoT. 

   ```bash
   iotedge logs cameraCapture
   ```

In Visual Studio Code fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito). 

> [!NOTE]
> Nell'output del modulo cameraCapture potrebbero essere inizialmente visualizzati alcuni errori di connessione. Ciò è dovuto al ritardo tra la distribuzione e l'avvio dei moduli.
>
> Il modulo cameraCapture effettua di nuovo automaticamente un tentativo di connessione fino a quando non avrà esito positivo. Una volta stabilita la connessione, verranno visualizzati i messaggi di classificazione delle immagini previsti, descritti di seguito.

I risultati del modulo di Visione personalizzata, inviati come messaggi dal modulo cameraCapture, includono la probabilità che l'immagine sia un abete canadese o un ciliegio. Poiché l'immagine corrisponde a un abete canadese, la probabilità dovrebbe essere indicata come 1.0.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test. 

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito il training di un modello di Visione personalizzata, che è stato poi distribuito come modulo in un dispositivo IoT Edge. Si è poi compilato un modulo in grado di eseguire query sul server di classificazione delle immagini e riportarne i risultati all'hub IoT. 

Se si vuole provare una versione più avanzata di questo scenario con un feed di videocamera dal vivo, vedere il progetto GitHub [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) (Visione personalizzata e Azure IoT Edge in un Raspberry Pi 3). 

Continuare con le esercitazioni successive per ottenere informazioni sugli altri modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni dettagliate aziendali nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Archiviare dati sul perimetro con database di SQL Server](tutorial-store-data-sql-server.md)