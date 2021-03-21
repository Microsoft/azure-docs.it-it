---
title: 'Esercitazione: Sviluppare moduli C per Windows con Azure IoT Edge'
description: Questa esercitazione illustra come creare moduli IoT Edge con codice C e distribuirli in dispositivi Windows che eseguono IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f019c8f3c560fdfdc0c8e5992389c253c9b0d74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463375"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Esercitazione: sviluppare moduli C IoT Edge usando i contenitori di Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Questo articolo illustra come usare Visual Studio per sviluppare codice C e distribuirlo in un dispositivo Windows che esegue Azure IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS è il canale dell'ultimo rilascio che supporterà i contenitori di Windows. A partire dalla versione 1,2, i contenitori di Windows non sono supportati. Provare a usare o a passare a [IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md) per eseguire IOT Edge nei dispositivi Windows.

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Usare Visual Studio per creare un modulo IoT Edge basato sull'SDK per C.
> * Usare Visual Studio e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo se la temperatura supera una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come sviluppare un modulo in C usando Visual Studio 2019 e quindi distribuirlo in un dispositivo Windows. Se si stanno sviluppando moduli con i contenitori Linux, passare a [sviluppare moduli C IOT Edge usando i contenitori Linux](tutorial-csharp-module.md) .

Per comprendere le opzioni per lo sviluppo e la distribuzione di moduli C con i contenitori di Windows, vedere la tabella seguente:

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;e&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Sviluppare moduli C per WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Prima di iniziare questa esercitazione, configurare l'ambiente di sviluppo seguendo le istruzioni riportate nell'esercitazione [sviluppare IOT Edge moduli con i contenitori di Windows](tutorial-develop-for-windows.md) . Al termine, l'ambiente conterrà i prerequisiti seguenti:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un [dispositivo Windows che esegue Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Un registro contenitori, ad esempio [Registro Azure Container](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurato con l'estensione [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurato per eseguire i contenitori Windows.

Installare Azure IoT C SDK per Windows x64 tramite vcpkg eseguendo i comandi seguenti:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Se si usa Visual Studio 2017 (versione 15.7 o successiva), scaricare e installare Azure IoT Edge Tools per Visual Studio 2017 da [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Creare un progetto di modulo

In questa sezione si crea un progetto di modulo IoT Edge basato sull'SDK per C usando Visual Studio e l'estensione Azure IoT Edge Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare un modello di soluzione C che è possibile personalizzare con il proprio codice.

1. Aprire Visual Studio 2019 e selezionare **Crea nuovo progetto**.

1. Nel riquadro **Crea nuovo progetto** cercare **IoT Edge** e quindi, nell'elenco dei risultati, selezionare il progetto **Azure IOT Edge (Windows amd64)** .

   ![Screenshot del riquadro "Crea nuovo progetto" IoT Edge.](./media/tutorial-c-module-windows/new-project.png)

1. Selezionare **Avanti**.

    Viene visualizzato il riquadro **Configura il nuovo progetto**.

   ![Screenshot del riquadro "Configura il nuovo progetto".](./media/tutorial-c-module-windows/configure-project.png)

1. Nel riquadro **Configura il nuovo progetto** assegnare al progetto e alla soluzione un nuovo nome descrittivo, ad esempio **CTutorialApp**. 

1. Selezionare **Crea** per creare il progetto.

   Viene visualizzato il riquadro **Aggiungi modulo**.

   ![Screenshot del riquadro "Aggiungi modulo" per la configurazione del progetto.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Nella pagina **Configura il nuovo progetto** procedere come segue:

   a. Nel riquadro sinistro selezionare il modello **C Module**.  
   b. Nella casella **Nome modulo** immettere **CModule**.  
   c. Nella casella **URL del repository** sostituire **localhost:5000** con il valore di **Server di accesso** del registro contenitori di Azure, nel formato seguente: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore è prepopolata in base al valore del nome del progetto di modulo.  È possibile recuperare il server di accesso dalla pagina di panoramica del registro contenitori nel portale di Azure.

1. Selezionare **Aggiungi** per creare il progetto.

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il manifesto della distribuzione condivide le credenziali per il registro contenitori con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. Usare le credenziali della sezione **Chiavi di accesso** del Registro Azure Container.

1. In Esplora soluzioni di Visual Studio aprire il file *deployment.template.json*.

1. Cercare la proprietà **registryCredentials** nelle proprietà desiderate di $edgeAgent. L'indirizzo del registro della proprietà dovrebbe essere compilato automaticamente con le informazioni fornite al momento della creazione del progetto. I campi nome utente e password dovrebbero contenere nomi di variabili. Ad esempio:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Aprire il file dell'ambiente (ENV) nella soluzione del modulo. Per impostazione predefinita, il file in Esplora soluzioni è nascosto, quindi per visualizzarlo può essere necessario selezionare il pulsante **Mostra tutti i file**. Il file ENV dovrebbe contenere le stesse variabili per nome utente e password riportate nel file *deployment.template.json*.

1. Aggiungere i valori di **Nome utente** e **Password** dal Registro Azure Container.

1. Salvare le modifiche al file ENV.

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

Il codice del modulo predefinito riceve i messaggi in una coda di input e li passa attraverso una coda di output. Aggiungere ora altro codice in modo che il modulo elabori i messaggi al perimetro prima di inoltrarli all'hub IoT. Aggiornare il modulo in modo che analizzi i dati della temperatura in ogni messaggio e invii il messaggio all'hub IoT solo se la temperatura supera una determinata soglia.

1. I dati del sensore in questo scenario sono disponibili in formato JSON. Per filtrare i messaggi in formato JSON, importare una libreria JSON per C. Questa esercitazione usa Parson.

   a. Scaricare il [repository Parson di GitHub](https://github.com/kgabis/parson).  
   b. Copiare i file *parson.c* e *parson.h* nel progetto CModule.  
   c. In Visual Studio aprire il file *CMakeLists.txt* dalla cartella del progetto CModule.  
   d. All'inizio del file importare i file Parson come libreria denominata **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Aggiungere `my_parson` all'elenco di librerie nella sezione "target_link_libraries" del file *CMakeLists.txt*.  
   f. Salvare il file *CMakeLists.txt*.  
   g. Selezionare **CModule** > **main.c**. Alla fine dell'elenco di istruzioni include aggiungerne una nuova per includere `parson.h` per il supporto JSON:

      ```c
      #include "parson.h"
      ```

1. Nel file *main.c* aggiungere una variabile globale denominata `temperatureThreshold` accanto alla variabile `messagesReceivedByInput1Queue`. Questa variabile imposta il valore che la temperatura misurata deve superare perché i dati vengano inviati all'hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Trovare la funzione `CreateMessageInstance` in *main.c*. Sostituire l'istruzione *if-else* interna con il codice seguente, che aggiunge alcune righe di funzionalità:

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   Le nuove righe di codice nell'istruzione *else* aggiungono una nuova proprietà al messaggio, che lo etichetta come avviso. Questo codice etichetta tutti i messaggi come avvisi, perché verrà aggiunta una funzionalità che invia i messaggi all'hub IoT solo se segnalano temperature elevate.

1. Trovare la funzione `InputQueue1Callback` e sostituirla interamente con il codice seguente. Questa funzione implementa i filtri effettivi per i messaggi. Quando viene ricevuto un messaggio, verifica se la temperatura segnalata supera la soglia. Se la temperatura supera la soglia, la funzione inoltra il messaggio attraverso la relativa coda di output. Se non supera la soglia, la funzione ignora il messaggio.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Aggiungere una funzione `moduleTwinCallback`. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno del modulo direttamente dal cloud.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Cercare la funzione `SetupCallbacksForModule`. Sostituire la funzione con il codice seguente, che aggiunge un'istruzione *else-if* per verificare se il modulo gemello è stato aggiornato.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Salvare il file *main.c*.

1. Aprire il file *deployment.template.json*.

1. Aggiungere il modulo gemello **CModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione `moduleContent`, dopo il modulo gemello `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Screenshot che mostra il modulo gemello aggiunto al modello di distribuzione.](./media/tutorial-c-module-windows/module-twin.png)

1. Salvare il file *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a **CModule** per filtrare i messaggi in cui la temperatura segnalata del computer è inferiore alla soglia accettabile. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

### <a name="sign-in-to-docker"></a>Accedere a Docker

Specificare le credenziali del registro contenitori in Docker nel computer di sviluppo per consentire il push dell'immagine del contenitore da archiviare nel registro.

1. Aprire PowerShell o una finestra del prompt dei comandi.

1. Accedere a Docker con le credenziali del registro contenitori di Azure salvate dopo la creazione del registro.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza che consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le [informazioni di riferimento sull'accesso a Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilazione ed esecuzione del push

Il computer di sviluppo può ora accedere al Registro Container, come pure possono fare i dispositivi IoT Edge. A questo punto, è possibile trasformare il codice del progetto in un'immagine del contenitore.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto da creare. Il nome predefinito è **AzureIotEdgeApp1**. Per questa esercitazione è stato scelto il nome **CTutorialApp** e, poiché si sta creando un modulo Windows, l'estensione deve essere **Windows. amd64**.

1. Selezionare **Build and push IoT Edge modules** (Compila moduli IoT Edge ed esegui il push).

   Il comando di creazione e push avvia tre operazioni:
   * Crea prima di tutto una nuova cartella denominata *config* nella soluzione, che include il manifesto completo della distribuzione. Si basa sulle informazioni contenute nel modello di distribuzione e in altri file della soluzione. 
   * In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al Dockerfile appropriato per l'architettura di destinazione. 
   * Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

   Questo processo può richiedere diversi minuti alla prima esecuzione, ma diventa più veloce la volta successiva che si eseguono i comandi.

## <a name="deploy-modules-to-the-device"></a>Distribuire i moduli nel dispositivo

Usare Cloud Explorer di Visual Studio e l'estensione Azure IoT Edge Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Per questo scenario è già disponibile un manifesto della distribuzione, il file *deployment.windows-amd64.json* nella cartella *config*. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. In Cloud Explorer di Visual Studio espandere le risorse per visualizzare l'elenco di dispositivi IoT.

1. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge che dovrà ricevere la distribuzione.

1. Scegliere **Crea distribuzione**.

1. In Esplora file di Visual Studio selezionare il file *deployment.windows-amd64.json* nella cartella *config* della soluzione.

1. Aggiornare Cloud Explorer per visualizzare i moduli distribuiti elencati sotto il dispositivo.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Gli eventuali moduli in esecuzione nel dispositivo ma non inclusi nel manifesto della distribuzione vengono arrestati. I moduli mancanti dal dispositivo vengono avviati.

È possibile usare l'estensione IoT Edge Tools per visualizzare i messaggi in arrivo nell'hub IoT.

1. In Cloud Explorer di Visual Studio selezionare il nome del dispositivo IoT Edge.

1. Nell'elenco **Azioni** selezionare **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

1. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. A causa delle modifiche apportate, il codice CModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**.

   ![Screenshot della finestra di output che visualizza i messaggi in arrivo nell'hub IoT.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Il modulo gemello CModule è stato usato per impostare la soglia della temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio aprire il file *deployment.windows-amd64.json*. 

   *Non* aprire il file *deployment.template*. Se il manifesto della distribuzione non viene visualizzato nel file *config* di Esplora soluzioni, selezionare l'icona **Mostra tutti i file** sulla barra degli strumenti.

1. Trovare il modulo gemello CModule e impostare il valore del parametro **temperatureThreshold** su una nuova temperatura da 5 a 10 gradi più alta rispetto all'ultima segnalata.

1. Salvare il file *deployment.windows-amd64.json*.

1. Seguire di nuovo le istruzioni per applicare il manifesto della distribuzione aggiornato al dispositivo.

1. Monitorare i messaggi da dispositivo a cloud in ingresso. L'arrivo dei messaggi dovrebbe interrompersi finché non viene raggiunta la nuova soglia di temperatura.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare con il prossimo articolo consigliato, è possibile conservare e riutilizzare le risorse e le configurazioni create in questa esercitazione. È anche possibile continuare a usare lo stesso dispositivo IoT Edge come dispositivo di test.

In caso contrario, è possibile eliminare le configurazioni locali e le risorse di Azure usate in questo articolo per evitare addebiti.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un modulo di IoT Edge con codice per filtrare i dati non elaborati generati dal dispositivo di IoT Edge.

Continuare con le esercitazioni successive per informazioni sul modo in cui Azure IoT Edge semplifica la distribuzione di servizi cloud di Azure per elaborare e analizzare i dati al perimetro.

> [!div class="nextstepaction"]
> [Funzioni di Azure](tutorial-deploy-function.md)
> [Analisi di flusso di Azure](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Servizio Visione personalizzata](tutorial-deploy-custom-vision.md)
