---
title: 'Esercitazione: Sviluppare moduli C# per Linux con Azure IoT Edge'
description: Questa esercitazione illustra come creare un modulo per IoT Edge con codice C# e distribuirlo in un dispositivo IoT Edge di Linux.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 01b30fed23b33719f08e93907075eee757343b1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461743"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Esercitazione: sviluppare un modulo di IoT Edge C# con i contenitori Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Usare Visual Studio Code per sviluppare codice C# e distribuirlo in un dispositivo che esegue Azure IoT Edge.

È possibile usare i moduli di Azure IoT Edge per distribuire codice che implementa la logica di business direttamente nei dispositivi di IoT Edge. Questa esercitazione illustra la creazione e distribuzione di un modulo IoT Edge che filtra i dati del sensore. Verrà usato il dispositivo IoT Edge simulato creato nelle guide introduttive. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Usare Visual Studio Code per creare un modulo di IoT Edge basato su .NET Core 2.1 SDK.
> * Usare Visual Studio Code e Docker per creare un'immagine Docker e pubblicarla nel registro.
> * Distribuire il modulo nel dispositivo IoT Edge.
> * Visualizzare i dati generati.

Il modulo di IoT Edge creato in questa esercitazione filtra i dati relativi alla temperatura generati dal dispositivo. Invia messaggi upstream solo quando la temperatura è superiore a una soglia specificata. Questo tipo di analisi alla rete perimetrale è utile per ridurre la quantità di dati comunicati e archiviati nel cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione illustra come sviluppare un modulo in **C#** usando **Visual Studio Code** e distribuirlo in un dispositivo IOT Edge. Se si stanno sviluppando moduli con i contenitori di Windows, passare a [sviluppare un modulo IOT Edge C# usando i contenitori di Windows](tutorial-csharp-module-windows.md) .

Usare la tabella seguente per comprendere le opzioni per lo sviluppo e la distribuzione di moduli C# con i contenitori Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Moduli C# per LinuxAMD64 in Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Moduli C# per LinuxAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Moduli C# per LinuxARM32 in Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Moduli C# per LinuxARM64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Il supporto per i dispositivi Linux ARM64 è disponibile in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per altre informazioni, vedere [Sviluppare ed eseguire il debug di moduli IoT Edge ARM64 in Visual Studio Code (anteprima)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Prima di iniziare questa esercitazione, è necessario eseguire l'esercitazione precedente per configurare l'ambiente di sviluppo, [sviluppare un modulo IOT Edge con i contenitori Linux](tutorial-develop-for-linux.md). Dopo aver completato questa esercitazione, saranno già stati soddisfatti i prerequisiti seguenti:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Un dispositivo che esegue Azure IoT Edge. È possibile usare le guide introduttive per configurare un [dispositivo Linux](quickstart-linux.md) o un [dispositivo Windows](quickstart.md).
* Un registro contenitori, ad esempio [Registro Azure Container](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurato per eseguire i contenitori Linux.

Per completare queste esercitazioni, preparare i prerequisiti aggiuntivi seguenti nel computer di sviluppo:

* [Estensione C# per Visual Studio Code con tecnologia OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Creare un progetto di modulo

La procedura seguente consente di creare un progetto di modulo IoT Edge per C# tramite Visual Studio Code e l'estensione Azure IoT Tools. Dopo aver creato il modello di progetto, aggiungere nuovo codice in modo che il modulo filtri i messaggi in base alle relative proprietà segnalate.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare un modello di soluzione C# che è possibile personalizzare con il proprio codice.

1. In Visual Studio Code selezionare **Visualizza** > **Riquadro comandi** per aprire il riquadro comandi di VS Code.

2. Nel riquadro comandi immettere ed eseguire il comando **Azure: Accedere** e seguire le istruzioni per accedere all'account Azure. Se è stato già effettuato l'accesso, è possibile ignorare questo passaggio.

3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **C# Module** (Modulo C#). |
   | Provide a module name (Specificare un nome per il modulo) | Assegnare al modulo il nome **CSharpModule**. |
   | Provide Docker image repository for the module (Specificare il repository di immagini Docker per il modulo) | Un repository di immagini include il nome del registro contenitori e il nome dell'immagine del contenitore. L'immagine del contenitore viene preinserita in base al nome specificato nell'ultimo passaggio. Sostituire **localhost:5000** con il valore di **Server di accesso** del registro contenitori di Azure. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure. <br><br>Il repository di immagini finale sarà simile a \<registry name\>.azurecr.io/csharpmodule. |

   ![Specificare il repository di immagini Docker](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Aggiungere le credenziali del registro

Il file dell'ambiente archivia le credenziali per il registro contenitori e le condivide con il runtime IoT Edge. Queste credenziali sono necessarie al runtime per eseguire il pull delle immagini private nel dispositivo IoT Edge. Usare le credenziali della sezione **Chiavi di accesso** del Registro Azure Container.

L'estensione IoT Edge cerca di eseguire il pull delle credenziali del Registro Container da Azure, per inserirle nel file di ambiente. Verificare se le credenziali sono già incluse. In caso contrario, aggiungerle:

1. Nello strumento di esplorazione di VS Code aprire il file con estensione **env**.
2. Aggiornare i campi con i valori di **nome utente** e **password** del Registro Azure Container.
3. Salvare questo file.

### <a name="select-your-target-architecture"></a>Selezionare l'architettura di destinazione

Attualmente, Visual Studio Code può sviluppare moduli C# per dispositivi Linux AMD64 e Linux ARM32v7. È necessario selezionare l'architettura di destinazione per ogni soluzione, perché il contenitore viene creato ed eseguito in modo diverso in base al tipo di architettura. L'impostazione predefinita è Linux AMD64.

1. Aprire il riquadro comandi e cercare **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: Imposta la piattaforma di destinazione predefinita per la soluzione Edge) oppure selezionare l'icona del collegamento sulla barra laterale nella parte inferiore della finestra.

2. Nel riquadro comandi selezionare l'architettura di destinazione nell'elenco di opzioni. Per questa esercitazione si usa una macchina virtuale Ubuntu come dispositivo IoT Edge, quindi si manterrà il valore predefinito **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aggiornare il modulo con il codice personalizzato

1. Nello strumento di esplorazione di VS Code aprire **modules** > **CSharpModule** > **Program.cs**.

2. Nella parte superiore dello spazio dei nomi **CSharpModule** aggiungere tre istruzioni **using** per i tipi che verranno usati in un secondo momento:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Aggiungere la variabile **temperatureThreshold** alla classe **Program**. Questa variabile imposta il valore che la temperatura misurata deve superare per inviare i dati all'hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Aggiungere le classi **MessageBody**, **Machine** e **Ambient** alla classe **Program**. Queste classi definiscono lo schema previsto per il corpo dei messaggi in arrivo.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Trovare la funzione **Init**. Questa funzione crea e configura l'oggetto **ModuleClient**, che consente al modulo di connettersi al runtime locale di Azure IoT Edge per inviare e ricevere messaggi. Dopo la creazione di **ModuleClient**, il codice legge il valore **temperatureThreshold** dalle proprietà desiderate del modulo gemello. Il codice registra un callback per la ricezione di messaggi da un hub IoT Edge tramite l'endpoint **input1**. Sostituire il metodo **SetInputMessageHandlerAsync** con un nuovo metodo e aggiungere un metodo **SetDesiredPropertyUpdateCallbackAsync** per gli aggiornamenti alle proprietà desiderate. Per apportare questa modifica, sostituire l'ultima riga del metodo **Init** con il codice seguente:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Aggiungere il metodo **onDesiredPropertiesUpdate** alla classe **Program**. Questo metodo riceve gli aggiornamenti della proprieta desiderata dal modulo gemello e aggiorna la variabile **temperatureThreshold**. Tutti i moduli hanno un modulo gemello che consente di configurare il codice in esecuzione all'interno di un modulo direttamente dal cloud.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Sostituire il metodo **PipeMessage** con il metodo **FilterMessages**. Questo metodo viene chiamato ogni volta che l'hub di IoT Edge invia un messaggio al modulo. Filtra i messaggi con un valore della temperatura inferiore alla soglia relativa alla temperatura configurata tramite il modulo gemello. Aggiunge inoltre la proprietà **MessageType** al messaggio con il valore impostato su **Avviso**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Salvare il file Program.cs.

9. Nello strumento di esplorazione di VS Code aprire il file **deployment.template.json** nell'area di lavoro della soluzione IoT Edge.

10. Aggiungere il modulo gemello **CSharpModule** al manifesto della distribuzione. Inserire il contenuto JSON seguente alla fine della sezione **modulesContent** dopo il modulo gemello **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Aggiungere il modulo gemello al modello di distribuzione](./media/tutorial-csharp-module/module-twin.png)

11. Salvare il file deployment.template.json.

## <a name="build-and-push-your-module"></a>Creare il modulo ed eseguirne il push

Nella sezione precedente è stata creata una soluzione IoT Edge ed è stato aggiunto codice a CSharpModule. Il nuovo codice filtra i messaggi in cui la temperatura del computer segnalata rientra nei limiti accettabili. È ora necessario compilare la soluzione come immagine del contenitore ed eseguirne il push nel registro contenitori.

1. Aprire il terminale integrato di VS Code selezionando **Visualizza** > **Terminale**.

1. Accedere a Docker immettendo il comando seguente nel terminale. Accedere con il nome utente, la password e il server di accesso del Registro Azure Container. È possibile recuperare questi valori dalla sezione **Chiavi di accesso** del registro nel portale di Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Potrebbe venire visualizzato un avviso di sicurezza in cui si consiglia l'uso di `--password-stdin`. Sebbene si tratti di una procedura consigliata per gli scenari di produzione, esula dell'ambito di questa esercitazione. Per altri dettagli, vedere le informazioni di riferimento sull'[accesso a docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

1. Nello strumento di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul file **deployment.template.json** e scegliere **Build and push IoT Edge solution** (Compila ed esegui il push della soluzione IoT Edge).

   Il comando di creazione e push avvia tre operazioni. Prima di tutto, crea una nuova cartella nella soluzione denominata **config** che contiene il manifesto completo della distribuzione, basato sulle informazioni del modello di distribuzione e di altri file della soluzione. In secondo luogo, esegue `docker build` per creare l'immagine del contenitore in base al documento dockerfile appropriato per l'architettura di destinazione. Infine, esegue `docker push` per eseguire il push del repository di immagini nel registro contenitori.

   Questo processo può richiedere alcuni minuti quando viene eseguito per la prima volta, ma alla successiva esecuzione dei comandi avviene più rapidamente.

## <a name="deploy-and-run-the-solution"></a>Distribuire ed eseguire la soluzione

Usare Esplora risorse di Visual Studio Code e l'estensione Azure IoT Tools per distribuire il progetto di modulo nel dispositivo IoT Edge. Il manifesto della distribuzione, il file **deployment.amd64.json**, è già disponibile per questo scenario nella cartella config. Ora è sufficiente selezionare un dispositivo che riceverà la distribuzione.

Assicurarsi che il dispositivo IoT Edge sia in esecuzione.

1. Nello strumento di esplorazione di Visual Studio Code espandere **Dispositivi** nella sezione **Azure IoT Hub** (Hub IoT di Azure) per visualizzare l'elenco dei dispositivi IoT.

2. Fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge, quindi selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

3. Selezionare il file **deployment.amd64.json** nella cartella **config** e quindi fare clic su **Select Edge Deployment Manifest** (Seleziona il manifesto della distribuzione di Edge). Non usare il file deployment.template.json.

4. Nel dispositivo espandere **Moduli** per visualizzare un elenco dei moduli distribuiti e in esecuzione. Fare clic sul pulsante Aggiorna. Dovrebbe essere visualizzato il nuovo **CSharpModule** in esecuzione insieme al modulo **SimulatedTemperatureSensor** e a **$edgeAgent** e **$edgeHub**.

    L'avvio dei moduli potrebbe richiedere alcuni minuti. Il runtime IoT Edge deve ricevere il nuovo manifesto della distribuzione, eseguire il pull delle immagini dei moduli dal runtime del contenitore e quindi avviare ogni nuovo modulo.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Dopo aver applicato il manifesto della distribuzione al dispositivo IoT Edge, il runtime di IoT Edge nel dispositivo raccoglie le informazioni della nuova distribuzione e si avvia all'interno di questa. Tutti i moduli in esecuzione nel dispositivo che non sono inclusi nel manifesto della distribuzione vengono arrestati. Tutti i moduli mancanti dal dispositivo vengono avviati.

1. Nella finestra di esplorazione di Visual Studio Code fare clic con il pulsante destro del mouse sul nome del dispositivo IoT Edge e scegliere **Start Monitoring Built-in Event Endpoint** (Avvia monitoraggio endpoint eventi predefinito).

2. Visualizzare i messaggi in arrivo nell'hub IoT. L'arrivo dei messaggi potrebbe richiedere del tempo, perché il dispositivo IoT Edge deve ricevere la nuova distribuzione e avviare tutti i moduli. Quindi, a causa delle modifiche apportate, il codice CModule aspetta che la temperatura del computer raggiunga i 25 gradi prima di inviare messaggi. Ai messaggi che indicano il raggiungimento della soglia di temperatura viene inoltre aggiunto il tipo di messaggio **Alert**.

   ![Visualizzare i messaggi in arrivo nell'hub IoT](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Modificare il modulo gemello

Si è usato il modulo gemello CSharpModule nel manifesto della distribuzione per impostare la soglia di temperatura su 25 gradi. È possibile usare il modulo gemello per cambiare la funzionalità senza dover aggiornare il codice del modulo.

1. In Visual Studio Code espandere i dettagli relativi al dispositivo IoT Edge per vedere i moduli in esecuzione.

2. Fare clic con il pulsante destro del mouse su **CSharpModule** e selezionare **Modifica modulo gemello**.

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