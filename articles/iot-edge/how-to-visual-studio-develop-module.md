---
title: Sviluppare ed eseguire il debug di moduli in Visual Studio-Azure IoT Edge
description: Usare Visual Studio con gli strumenti di Azure per lo sviluppo di un modulo di IoT Edge C o C# ed eseguirne il push dall'hub delle cose a un dispositivo Internet, come configurato da un manifesto della distribuzione.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 1883ffdff20bbbef8efec1440854f01a21a281dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994520"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Utilizzare Visual Studio 2019 per sviluppare ed eseguire il debug di moduli per Azure IoT Edge

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra come usare Visual Studio 2019 come strumento principale per sviluppare ed eseguire il debug di moduli.

Azure IoT Edge Tools for Visual Studio offre i vantaggi seguenti:

- Possibilità di creare, modificare, compilare, eseguire ed effettuare il debug di soluzioni e moduli per Azure IoT Edge nel computer di sviluppo locale.
- Capacità per la distribuzione della soluzione Azure IoT Edge a un dispositivo Azure IoT Edge tramite l'hub IoT di Azure.
- Consente di codificare i moduli di Azure Internet in C o C# con tutti i vantaggi dello sviluppo di Visual Studio.
- Funzionalità di gestione di dispositivi e moduli per Azure IoT Edge con l'interfaccia utente.

Questo articolo illustra come usare gli strumenti Azure IoT Edge per Visual Studio 2019 per sviluppare i moduli di IoT Edge. Si apprenderà anche come distribuire il progetto a un dispositivo Azure IoT Edge. Attualmente, Visual Studio 2019 fornisce supporto per i moduli scritti in C e C#. Le architetture di dispositivi supportate sono Windows x64 e Linux x64 o ARM32. Per ulteriori informazioni sui sistemi operativi, i linguaggi e le architetture supportati, vedere [supporto per lingue e architetture](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si usi un computer o una macchina virtuale Windows come computer di sviluppo. Nei computer Windows è possibile sviluppare moduli Windows o Linux. Per sviluppare moduli Windows, usare un computer Windows che esegue la versione 1809/Build 17763 o successiva. Per sviluppare moduli Linux, usare un computer Windows che soddisfi i [requisiti per il desktop Docker](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Poiché in questo articolo viene usato Visual Studio 2019 come strumento di sviluppo principale, installare Visual Studio. Assicurarsi di includere i carichi di lavoro sviluppo di **Azure** e **sviluppo di applicazioni desktop con C++** nell'installazione di Visual Studio 2019. È possibile [modificare Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019) per aggiungere i carichi di lavoro necessari.

Quando Visual Studio 2019 è pronto, sono necessari anche gli strumenti e i componenti seguenti:

- Scaricare e installare [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) da Visual Studio Marketplace per creare un progetto di IOT Edge in visual studio 2019.

> [!TIP]
> Se si usa Visual Studio 2017, scaricare e installare [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) per vs 2017 da Visual Studio Marketplace

- Scaricare e installare [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo per compilare ed eseguire le immagini del modulo. È necessario configurare Docker CE per l'esecuzione in modalità contenitore Linux o in modalità contenitore Windows.

- Impostare l'ambiente di sviluppo locale per eseguire e testare la soluzione IoT Edge ed effettuarne il debug installando [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Installare [Python (2.7/3.6 +) e PIP](https://www.python.org/) , quindi installare il pacchetto **iotedgehubdev** eseguendo il comando seguente nel terminale. Assicurarsi che la versione di Azure IoT EdgeHub Dev Tool sia superiore a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clonare il repository e installare Gestione librerie vcpkg, quindi installare il **pacchetto Azure--SDK-c** per Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro Azure Container](../container-registry/index.yml) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud.

- Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come dispositivo IoT Edge, seguire i passaggi nella guida introduttiva per [Linux](quickstart-linux.md) o [Windows](quickstart.md). Se si esegue il daemon di IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di iniziare lo sviluppo in Visual Studio.

### <a name="check-your-tools-version"></a>Controllare la versione degli strumenti

1. Dal menu **estensioni** selezionare **Gestisci estensioni**. Espandere **strumenti di > installati** ed è possibile trovare **Azure IOT Edge Tools per visual studio** e **Cloud Explorer per Visual Studio**.

1. Prendere nota della versione installata. È possibile confrontare questa versione con quella più recente in Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Se la versione è precedente a quella disponibile in Visual Studio Marketplace, aggiornare gli strumenti in Visual Studio come illustrato nella sezione seguente.

### <a name="update-your-tools"></a>Aggiornare gli strumenti

1. Nella finestra **Gestisci estensioni** espandere **aggiornamenti > Visual Studio Marketplace**, selezionare **strumenti Azure IOT Edge** o **Cloud Explorer per Visual Studio** e selezionare **Aggiorna**.

1. Dopo aver scaricato l'aggiornamento degli strumenti, chiudere Visual Studio per attivare l'aggiornamento degli strumenti con il programma di installazione di VSIX.

1. Nel programma di installazione selezionare **OK** per avviare il processo e quindi **Modifica** per aggiornare gli strumenti.

1. Al termine dell'aggiornamento, selezionare **Chiudi** e riavviare Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Creare un progetto Azure IoT Edge

Il modello di progetto Azure IoT Edge in Visual Studio consente di creare un progetto che può essere distribuito in dispositivi Azure IoT Edge nell'hub IoT di Azure. Innanzitutto si crea una soluzione di Azure IoT Edge e quindi si genera il primo modulo nella soluzione. Ogni soluzione IoT Edge può contenere più di un modulo.

> [!TIP]
> La struttura del progetto IoT Edge creato da Visual Studio è diversa rispetto a quella in Visual Studio Code.

1. Nella finestra di dialogo nuovo progetto di Visual Studio cercare e selezionare **Azure IOT Edge** progetto e fare clic su **Avanti**. Nella finestra configurazione progetto immettere un nome per il progetto e specificare il percorso, quindi selezionare **Crea**. Il nome del progetto predefinito è **AzureIoTEdgeApp1**.

   ![Creare un nuovo progetto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Nella finestra **aggiungi IOT Edge applicazione e modulo** selezionare modulo **C#** o modulo **C** e quindi specificare il nome del modulo e il repository di immagini del modulo. Visual Studio popola automaticamente il nome del modulo con il valore **localhost:5000/<nome del modulo\>**. Sostituire tale valore con le proprie informazioni di registro. Se si usa un registro Docker locale per il test, **localhost** è corretto. Se si usa Registro Azure Container, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un aspetto simile a **_\<registry name\>_ . azurecr.io**. Sostituire solo la parte **localhost: 5000** della stringa, in modo che il risultato finale appaia come **\<*registry name*\> . _\<your module name\>_ azurecr.io/**. Il nome del modulo predefinito è **IotEdgeModule1**

   ![Aggiungi applicazione e modulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selezionare **OK** per creare la soluzione Azure IOT Edge con un modulo che usa C# o C.

A questo punto si dispone di un progetto **AzureIoTEdgeApp1. Linux. amd64** o di un progetto **AzureIoTEdgeApp1. Windows. amd64** e di un progetto **IotEdgeModule1** nella soluzione. Ogni progetto **AzureIoTEdgeApp1** include un `deployment.template.json` file che definisce i moduli da compilare e distribuire per la soluzione IOT Edge e definisce anche le route tra i moduli. La soluzione predefinita include un modulo **SimulatedTemperatureSensor** e un modulo **IotEdgeModule1** . Il modulo **SimulatedTemperatureSensor** genera dati simulati nel modulo **IotEdgeModule1** , mentre il codice predefinito nel modulo **IotEdgeModule1** Invia direttamente i messaggi ricevuti all'hub Azure.

Per vedere come funziona il sensore di temperatura simulato, visualizzare il [codice sorgente di SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Il progetto **IotEdgeModule1** è un'applicazione console .net core 2,1 se è un modulo C#. Contiene i file Docker richiesti per l'esecuzione del dispositivo IoT Edge con un contenitore Windows o un contenitore Linux. Il `module.json` file descrive i metadati di un modulo. Il codice effettivo del modulo, che accetta l'SDK di dispositivi Azure per dispositivi come dipendenza, si trova nel `Program.cs` `main.c` file o.

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo predefinito incluso nella soluzione si trova in **IotEdgeModule1**  >  **Program.cs** (per C#) o **Main. c** (c). Il modulo e il `deployment.template.json` file sono configurati in modo da poter compilare la soluzione, eseguirne il push nel registro contenitori e distribuirla in un dispositivo per avviare i test senza toccare alcun codice. Il modulo viene compilato per l'input da un'origine (in questo caso, il modulo **SimulatedTemperatureSensor** che simula i dati) e lo invia tramite pipe all'hub Azure.

Quando si è pronti per personalizzare il modello di modulo con il proprio codice, usare gli [SDK dell'hub Azure](../iot-hub/iot-hub-devguide-sdks.md) per creare moduli che risolvono le esigenze principali per le soluzioni Internet, come la sicurezza, la gestione dei dispositivi e l'affidabilità.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inizializzare iotedgehubdev con la stringa di connessione del dispositivo IoT Edge

1. Copiare la stringa di connessione di un dispositivo IoT Edge qualsiasi da **Stringa di connessione primaria** in Visual Studio Cloud Explorer. Assicurarsi di non copiare la stringa di connessione di un dispositivo non Edge, dal momento che l'icona di un dispositivo IoT Edge è diversa da quella di un dispositivo non Edge.

   ![Copiare la stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Dal menu **strumenti** selezionare **Azure IOT Edge strumenti**  >  **installazione IOT Edge simulatore**, incollare la stringa di connessione e fare clic su **OK**.

   ![Apertura della finestra di impostazione della stringa di connessione del dispositivo Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Immettere la stringa di connessione del primo passaggio e quindi selezionare **OK**.

> [!NOTE]
> È necessario seguire questa procedura solo una volta nel computer di sviluppo, poiché i risultati vengono applicati automaticamente a tutte le soluzioni Azure IoT Edge successive. Questa procedura può essere eseguita nuovamente se è necessario usare una stringa di connessione diversa.

## <a name="build-and-debug-single-module"></a>Compila ed Esegui il debug del modulo singolo

In genere, è consigliabile testare ed eseguire il debug di ogni modulo prima di eseguirlo all'interno di un'intera soluzione con più moduli.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **IotEdgeModule1** e scegliere **Imposta come progetto di avvio** dal menu di scelta rapida.

   ![Impostare il progetto di avvio](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Premere **F5** o fare clic sul pulsante riportato di seguito per eseguire il modulo; potrebbero occorrere circa 10&ndash;20 secondi alla prima esecuzione.

   ![Eseguire il modulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si noterà che viene avviata un'app console .NET Core se il modulo è stato inizializzato correttamente.

   ![Modulo in esecuzione](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se si sviluppa in C#, impostare un punto di interruzione nella `PipeMessage()` funzione in **Program.cs**; se si usa C, impostare un punto di interruzione nella `InputQueue1Callback()` funzione in **Main. C**. È quindi possibile testarlo inviando un messaggio eseguendo il comando seguente in una shell bash **git** o **WSL bash** . (non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Eseguire il debug del modulo singolo](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Il punto di interruzione deve essere attivato. È possibile controllare le variabili nella finestra **Variabili locali** di Visual Studio.

   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

1. Premere **CTRL+F5** oppure fare clic sul pulsante Arresta per arrestare il debug.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilare ed eseguire il debug di una soluzione IoT Edge con più moduli

Dopo aver completato lo sviluppo di un modulo singolo, è possibile eseguire ed effettuare il debug di un'intera soluzione con più moduli.

1. In **Esplora soluzioni** aggiungere un secondo modulo alla soluzione facendo clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e scegliendo **Aggiungi**  >  **nuovo modulo di IOT Edge**. Il nome predefinito del secondo modulo è **IotEdgeModule2** e fungerà da altro modulo pipe.

1. Aprire il file `deployment.template.json` . si noterà che **IotEdgeModule2** è stato aggiunto nella sezione **modules** . Sostituire la sezione **routes** con il contenuto seguente. Se i nomi dei moduli sono stati personalizzati, assicurarsi di aggiornare questi nomi in modo che corrispondano.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e selezionare **Imposta come progetto di avvio** dal menu di scelta rapida.

1. Creare i punti di interruzione e quindi premere **F5** per eseguire ed effettuare il debug di più moduli contemporaneamente. Verranno visualizzate più finestre dell'app console .NET Core, ciascuna delle quali rappresenta un modulo diverso.

   ![Eseguire il debug di più moduli](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Premere **CTRL+F5** oppure selezionare il pulsante Arresta per arrestare il debug.

## <a name="build-and-push-images"></a>Compilare le immagini ed eseguirne il push

1. Assicurarsi che **AzureIoTEdgeApp1** sia il progetto di avvio. Selezionare **Debug** o **Versione** come configurazione per la compilazione delle immagini del modulo.

    > [!NOTE]
    > Quando si sceglie **Debug**, Visual Studio usa `Dockerfile.(amd64|windows-amd64).debug` per compilare le immagini Docker. Ciò include VSDBG, ovvero il debugger della riga di comando di .NET Core, nell'immagine del contenitore durante la compilazione. Per i moduli IoT Edge per l'ambiente di produzione, è consigliabile usare la configurazione **Versione**, che usa `Dockerfile.(amd64|windows-amd64)` senza VSDBG.

1. Se si usa un registro privato come Azure Container Registry (ACR), usare il comando Docker seguente per accedere a tale registro.  È possibile ottenere il nome utente e la password dalla pagina **chiavi di accesso** del registro di sistema nella portale di Azure. Se si usa un registro locale, è possibile [eseguire un registro locale](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se si usa un registro privato come Registro Azure Container, è necessario aggiungere le informazioni di accesso del registro alle impostazioni di runtime presenti nel file `deployment.template.json`. Sostituire i segnaposto con il nome utente amministratore, la password e il nome del registro di Registro Azure Container effettivi.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **AzureIoTEdgeApp1** e scegliere **Compila ed Esegui push IOT Edge moduli** per compilare ed eseguire il push dell'immagine Docker per ogni modulo.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Nell'articolo della guida introduttiva usato per configurare il dispositivo IoT Edge è stato distribuito un modulo usando il portale di Azure. È anche possibile distribuire i moduli con Cloud Explorer per Visual Studio. Il manifesto della distribuzione, il file `deployment.json`, è già disponibile per questo scenario; è sufficiente selezionare un dispositivo che riceverà la distribuzione.

1. Aprire **Cloud Explorer** facendo clic su **Visualizza** > **Cloud Explorer**. Assicurarsi di aver effettuato l'accesso a Visual Studio 2019.

1. In **Cloud Explorer** espandere la sottoscrizione, trovare l'hub IoT di Azure e il dispositivo Azure IoT Edge da distribuire.

1. Fare clic con il pulsante destro del mouse sul dispositivo IoT Edge per creare una distribuzione. Passare al manifesto di distribuzione configurato per la piattaforma che si trova nella cartella **config** della soluzione di Visual Studio, ad esempio `deployment.arm32v7.json` .

1. Fare clic sul pulsante Aggiorna per visualizzare i nuovi moduli in esecuzione insieme al modulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Visualizzare i dati generati

1. Per monitorare il messaggio D2C per uno specifico dispositivo di IoT-Edge, selezionarlo nell'hub di Internet delle cose in **Cloud Explorer** e quindi fare clic su **Avvia monitoraggio endpoint evento predefinito** nella finestra **azione** .

1. Per arrestare il monitoraggio dei dati, selezionare **Interrompi monitoraggio endpoint evento predefinito** nella finestra **azione** .

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare moduli personalizzati per i dispositivi IoT Edge, [comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).