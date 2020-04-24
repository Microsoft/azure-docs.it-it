---
title: Sviluppare moduli ed eseguirne il debug per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare, compilare un modulo per Azure IoT Edge ed eseguirne il debug usando C#, Python, Node.js, Java o C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944308"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra il modo per usare Visual Studio Code come strumento principale per sviluppare moduli ed eseguirne il debug.

Esistono due modi per eseguire il debug di moduli scritti in C#, node. js o Java in Visual Studio Code: è possibile aggiungere un processo in un contenitore di moduli o avviare il codice del modulo in modalità di debug. Per eseguire il debug di moduli scritti in Python o C, è possibile connettersi solo a un processo in contenitori amd64 Linux.

Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging).

Questo articolo fornisce istruzioni per lo sviluppo e il debug di moduli in più linguaggi per più architetture. Attualmente, Visual Studio Code fornisce il supporto per i moduli scritti in C#, C, Python, node. js e Java. Le architetture di dispositivi supportate sono x64 e ARM32. Per ulteriori informazioni sui sistemi operativi, i linguaggi e le architetture supportati, vedere [supporto per lingue e architetture](module-development.md#language-and-architecture-support).

>[!NOTE]
>Il supporto per lo sviluppo e il debug per i dispositivi Linux ARM64 è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per altre informazioni, vedere [Sviluppare ed eseguire il debug di moduli IoT Edge ARM64 in Visual Studio Code (anteprima)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Prerequisiti

È possibile usare un computer o una macchina virtuale Windows, macOS o Linux come computer di sviluppo. Nei computer Windows è possibile sviluppare moduli Windows o Linux. Per sviluppare moduli Windows, usare un computer Windows che esegue la versione 1809/Build 17763 o successiva. Per sviluppare moduli Linux, usare un computer Windows che soddisfi i [requisiti per il desktop Docker](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Prima installare [Visual Studio Code](https://code.visualstudio.com/) e poi aggiungere le estensioni seguenti:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Estensioni Visual Studio specifiche per il linguaggio usato per lo sviluppo:
  - C#, incluse funzioni di Azure: [estensione c#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [pacchetto di estensione Java per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [estensione c/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Sarà inoltre necessario installare alcuni strumenti aggiuntivi specifici del linguaggio per sviluppare il modulo:

- C#, incluso Funzioni di Azure: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) e [PIP](https://pip.pypa.io/en/stable/installing/#installation) per l'installazione di pacchetti Python (in genere inclusi nell'installazione di Python).

- Node. js: [node. js](https://nodejs.org). È necessario installare anche [Yeoman](https://www.npmjs.com/package/yo) e il [generatore di moduli Node.js di Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java se Development Kit 10](https://aka.ms/azure-jdks) e [Maven](https://maven.apache.org/). È necessario [impostare la variabile di ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) in modo che punti all'installazione di JDK.

Per compilare e distribuire l'immagine del modulo, è necessario Docker per compilare l'immagine del modulo e un registro contenitori in cui inserire l'immagine del modulo:

- [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo.

- [Azure container Registry](https://docs.microsoft.com/azure/container-registry/) o [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud.

A meno che il modulo non venga sviluppato in C, è necessario anche lo [strumento di sviluppo Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) basato su Python per configurare l'ambiente di sviluppo locale in modo da eseguire e testare la soluzione IoT Edge ed eseguirne il debug. Se non è già stato fatto, installare [Python (2.7/3.6/3.7) e PIP](https://www.python.org/) , quindi installare **iotedgehubdev** eseguendo questo comando nel terminale.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Attualmente, iotedgehubdev usa una libreria Docker-py non compatibile con Python 3,8.
>
> Se si dispone di più Python, incluso Python 2,7 preinstallato (ad esempio, in Ubuntu o macOS), assicurarsi di usare il corretto `pip` o `pip3` per installare **iotedgehubdev**

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come dispositivo IoT Edge, seguire i passaggi nella guida introduttiva per [Linux](quickstart-linux.md) o [Windows](quickstart.md). Se si esegue il daemon IoT Edge nel computer di sviluppo, potrebbe essere necessario arrestare EdgeHub ed EdgeAgent prima di andare al passaggio successivo.

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

I passaggi seguenti mostrano come creare un modulo IoT Edge nel linguaggio di sviluppo preferito (incluso Funzioni di Azure, scritto in C#) usando Visual Studio Code e lo strumento Azure IoT Edge. Per iniziare, si crea una soluzione e si genera quindi il primo modulo all'interno della soluzione. Ogni soluzione può contenere più moduli.

1. Selezionare **Visualizza** > **Riquadro comandi**.

1. Nel riquadro comandi, immettere ed eseguire il comando **Azure IOT Edge: nuova soluzione IOT Edge**.

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Passare alla cartella in cui si vuole creare la nuova soluzione e poi selezionare **Seleziona cartella**.

1. Immettere un nome per la soluzione.

1. Selezionare un modello di modulo per fare in modo che il linguaggio di sviluppo preferito sia il primo modulo nella soluzione.

1. Immettere un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori.

1. Specificare il nome del repository di immagini del modulo. Visual Studio Code popola automaticamente il nome del modulo con il valore **localhost:5000/<nome del modulo\>**. Sostituire tale valore con le proprie informazioni di registro. Se si usa un registro Docker locale per il test, **localhost** è corretto. Se si usa Registro Azure Container, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un aspetto simile ** _ \<al nome\>del registro di sistema_. azurecr.io**. Sostituire solo la parte **localhost: 5000** della stringa, in modo che il risultato finale appaia come ** \< *nome*\>del registro di sistema. azurecr.io/_\<il\>nome del modulo_**.

   ![Specificare il repository di immagini Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra.

Nella soluzione sono presenti quattro elementi:

- Una cartella **.vscode** contenente le configurazioni di debug.

- Una cartella **modules** contenente le sottocartelle di ogni modulo.  All'interno della cartella per ogni modulo è presente un file, **Module. JSON**, che controlla il modo in cui i moduli vengono compilati e distribuiti.  Questo file deve essere modificato per modificare il registro contenitori distribuzione moduli da localhost a un registro remoto. A questo punto, è presente un solo modulo.  È possibile tuttavia aggiungere altri elemento nel riquadro comandi con il comando **Azure IoT Edge: Aggiungi modulo Edge IoT**.

- Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se Registro Azure Container è il registro, si avranno un nome utente e una password per Registro Azure Container.

  > [!NOTE]
  > Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente.

- Un file **Deployment. template. JSON** elenca il nuovo modulo insieme a un modulo **SimulatedTemperatureSensor** di esempio che simula i dati che è possibile usare per il test. Per altre informazioni su come funzionano i manifesti di distribuzione, vedere [Informazioni su come usare i manifesti della distribuzione per distribuire moduli e definire route](module-composition.md).

Per verificare il funzionamento del modulo temperature simulate, visualizzare il [codice sorgente SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Aggiungere altri moduli

Per aggiungere altri moduli alla soluzione, eseguire il comando **Azure IOT Edge: aggiungere IOT Edge modulo** dal riquadro comandi. È anche possibile fare clic con il pulsante destro del mouse sulla cartella **moduli** o sul file `deployment.template.json` nella visualizzazione Explorer di Visual Studio Code e poi selezionare **Aggiungi modulo di IoT Edge**.

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo predefinito fornito con la soluzione si trova al percorso seguente:

- Funzione di Azure (C#): **moduli > * &lt;il nome&gt;* > *&lt;&gt;*** del modulo nome del modulo. cs
- C#: **moduli > *&lt;nome del modulo&gt;* > Program.cs**
- Python: **moduli > *&lt;nome del modulo&gt;* > main.py**
- Node.js: **moduli > *&lt;nome del modulo&gt;* > app.js**
- Java: **moduli > *&lt;nome del modulo&gt;* > src > main > java > com > edgemodulemodules > App.java**
- C: **moduli > *&lt;nome del modulo&gt;* > main.c**

Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo è stato creato per l'input semplice da un'origine (in questo caso, il modulo SimulatedTemperatureSensor che simula i dati) e lo invia tramite pipe all'hub Internet.

Quando si è pronti per personalizzare il modello con il proprio codice, usare gli [SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md) per compilare moduli che rispondono alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Eseguire il debug di un modulo senza contenitore (C#, Node.js, Java)

Se si sta sviluppando in C#, Node. js o Java, il modulo richiede l'uso di un oggetto **ModuleClient** nel codice del modulo predefinito in modo che possa avviare, eseguire e instradare i messaggi. Si userà anche il canale di input predefinito **input1** per intervenire quando il modulo riceve i messaggi.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Impostare il simulatore di IoT Edge per la soluzione IoT Edge

Nel computer di sviluppo è possibile avviare un simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per poter eseguire la soluzione IoT Edge.

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, quindi selezionare **Setup IoT Edge Simulator** (Imposta simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.
1. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato leggendo i dettagli dell'avanzamento.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Impostare il simulatore di IoT Edge per l'app a modulo singolo

Per configurare e avviare il simulatore, eseguire il comando **Azure IOT Edge: avviare IOT Edge simulatore hub per un modulo singolo** dal riquadro comandi di Visual Studio Code. Quando richiesto, usare il valore **input1** dal codice del modulo predefinito (o il valore equivalente dal codice) come nome di input per l'applicazione. Il comando attiva l'interfaccia della riga di comando **iotedgehubdev** e poi avvia il simulatore di IoT Edge e un contenitore di moduli utilità di test. Se il simulatore è stato avviato correttamente in modalità modulo singolo, è possibile vedere l'output seguente nel terminale integrato. È anche possibile vedere un comando `curl` che aiuta a inviare messaggi e che sarà necessario più avanti.

   ![Impostare il simulatore di IoT Edge per l'app a modulo singolo](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   È possibile usare la visualizzazione Docker Explorer di Visual Studio Code per vedere lo stato di esecuzione del modulo.

   ![Stato del modulo del simulatore](media/how-to-develop-csharp-module/simulator-status.png)

   Il contenitore **edgeHubDev** costituisce il nucleo del simulatore di IoT Edge locale. Può essere eseguito nel computer di sviluppo senza il daemon di sicurezza di IoT Edge e fornisce le impostazioni di ambiente per l'app a modulo nativo o i contenitori di moduli. Il contenitore **input** espone le API REST per agevolare il bridging dei messaggi al canale di input di destinazione nel modulo.

### <a name="debug-module-in-launch-mode"></a>Eseguire il debug del modulo in modalità di avvio

1. Preparare l'ambiente per il debug in base ai requisiti del linguaggio di sviluppo, impostare un punto di interruzione nel modulo e selezionare la configurazione di debug da usare:
   - **C#**
     - Nel terminale Visual Studio Code integrato, modificare la directory nella cartella del *** &lt;nome&gt; del modulo*** , quindi eseguire il comando seguente per compilare l'applicazione .NET Core.

       ```cmd
       dotnet build
       ```

     - Aprire il file `Program.cs` e aggiungere un punto di interruzione.

     - Passare alla visualizzazione Debug di Visual Studio Code selezionando **Visualizza > Debug**. Selezionare la configurazione ** * &lt;&gt; * di debug nome del modulo debug locale (.NET Core)** nell'elenco a discesa.

        > [!NOTE]
        > Se .NET Core `TargetFramework` non è coerente con il percorso del programma in `launch.json`, è necessario aggiornare manualmente il percorso del programma in `launch.json` in modo che corrisponda `TargetFramework` a nel file con estensione csproj in modo che Visual Studio Code possibile avviare correttamente il programma.

   - **Node.js**
     - Nel terminale Visual Studio Code integrato, modificare la directory nella cartella del *** &lt;nome&gt; del modulo*** , quindi eseguire il comando seguente per installare i pacchetti del nodo

       ```cmd
       npm install
       ```

     - Aprire il file `app.js` e aggiungere un punto di interruzione.

     - Passare alla visualizzazione Debug di Visual Studio Code selezionando **Visualizza > Debug**. Selezionare la configurazione ** * &lt;&gt; * di debug nome del modulo debug locale (node. js)** nell'elenco a discesa.
   - **Java**
     - Aprire il file `App.java` e aggiungere un punto di interruzione.

     - Passare alla visualizzazione Debug di Visual Studio Code selezionando **Visualizza > Debug**. Selezionare la configurazione ** * &lt;&gt; * di debug nome del modulo local debug (Java)** nell'elenco a discesa.

1. Fare clic su **Avvia debug** o premere **F5** per avviare la sessione di debug.

1. Nel terminale integrato di Visual Studio Code eseguire il comando seguente per inviare un messaggio **Hello World** al modulo. Questo è il comando mostrato nei passaggi precedenti quando è stato impostato il simulatore di IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se si usa Windows, verificare che la shell del terminale integrato di Visual Studio Code sia **Git Bash** oppure **WSL Bash**. Non è possibile eseguire il comando `curl` in PowerShell o nel prompt dei comandi.
   > [!TIP]
   > È anche possibile usare [PostMan](https://www.getpostman.com/) o altri strumenti API per inviare messaggi invece di `curl`.

1. Nella visualizzazione Debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra.

1. Per arrestare la sessione di debug, selezionare il pulsante Interrompi o premere **MAIUSC + F5**, quindi eseguire **Azure IoT Edge: arrestare IOT Edge simulatore** nel riquadro comandi per arrestare il simulatore e pulire.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Eseguire il debug in modalità di collegamento con il simulatore di IoT Edge (C#, Node. js, Java, Funzioni di Azure)

La soluzione predefinita contiene due moduli: uno è un modulo di sensore temperatura simulato e l'altro è il modulo di pipe. Il sensore temperatura simulato invia messaggi al modulo di pipe; i messaggi vengono poi inviati tramite pipe all'hub IoT. Nella cartella del modulo creato sono presenti diversi file Docker per tipi di contenitore differenti. Usare uno dei file che terminano con l'estensione **debug** per compilare il modulo per il test.

Attualmente, il debug in modalità di collegamento è supportato solo come indicato di seguito:

- I moduli C#, inclusi quelli per Funzioni di Azure, supportano il debug nei contenitori amd64 di Linux
- I moduli Node.js supportano solo il debug nei contenitori amd64 e arm32v7 di Linux e nei contenitori amd64 di Windows
- I moduli Java supportano solo il debug nei contenitori amd64 e arm32v7 di Linux

> [!TIP]
> È possibile passare da un'opzione all'altra per la piattaforma predefinita per la soluzione IoT Edge, facendo clic sull'elemento nella barra di stato di Visual Studio Code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Impostare il simulatore di IoT Edge per la soluzione IoT Edge

Nel computer di sviluppo è possibile avviare un simulatore di IoT Edge invece di installare il daemon di sicurezza IoT Edge per poter eseguire la soluzione IoT Edge.

1. In Device Explorer sul lato sinistro fare clic con il pulsante destro del mouse sull'ID del dispositivo IoT Edge, quindi selezionare **Setup IoT Edge Simulator** (Imposta simulatore IoT Edge) per avviare il simulatore con la stringa di connessione del dispositivo.

1. Nel terminale integrato è possibile vedere che il simulatore di IoT Edge è stato correttamente configurato leggendo i dettagli dell'avanzamento.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Creare ed eseguire il contenitore per il debug ed eseguire il debug in modalità connessione

1. Aprire il file del modulo (`Program.cs`, `app.js`, `App.java` o `<your module name>.cs`) e aggiungere un punto di interruzione.

1. Nella visualizzazione Explorer di Visual Studio Code fare clic con il pulsante destro del mouse sul file `deployment.debug.template.json` per la soluzione e quindi selezionare **Build and Run IoT Edge solution in Simulator** (Compila ed esegui la soluzione IoT Edge nel simulatore). È possibile esaminare tutti i log del contenitore del modulo nella stessa finestra. È anche possibile passare alla visualizzazione Docker per controllare lo stato del contenitore.

   ![Variabili delle espressioni di controllo](media/how-to-vs-code-develop-module/view-log.png)

1. Passare alla visualizzazione Debug di Visual Studio Code e selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile al ** * &lt;nome&gt; del modulo* Remote Debug**

1. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.

1. Nella visualizzazione Debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra.

1. Per arrestare la sessione di debug, prima di tutto selezionare il pulsante Interrompi o premere **MAIUSC + F5**, quindi selezionare **Azure IoT Edge: arrestare IOT Edge simulatore** dal riquadro comandi.

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge nei contenitori. Ha aggiunto porte esposte alle impostazioni `createOptions` del contenitore del modulo. Al termine del debug dei moduli, si consiglia di rimuovere le porte esposte per predisporre i moduli IoT Edge per l'ambiente di produzione.
>
> Per i moduli scritti in C#, tra cui Funzioni di Azure, l'esempio si basa sulla versione di debug di `Dockerfile.amd64.debug`, che include VSDBG, ovvero il debugger della riga di comando di .NET Core, nell'immagine del contenitore durante la compilazione. Dopo aver eseguito il debug dei moduli C#, si consiglia di usare direttamente Dockerfile senza VSDBG per moduli IoT Edge per l'ambiente di produzione.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Eseguire il debug di un modulo con il runtime IoT Edge

In ogni cartella di modulo sono presenti diversi file Docker per tipi di contenitore differenti. Usare uno dei file che terminano con l'estensione **debug** per compilare il modulo per il test.

Durante il debug di moduli con questo metodo, i moduli sono in esecuzione in primo piano. Il dispositivo IoT Edge e il Visual Studio Code possono essere nello stesso computer o, più probabilmente, Visual Studio Code è nel computer di sviluppo mentre il runtime di IoT Edge e i moduli sono in esecuzione in altro computer fisico. Per eseguire il debug da Visual Studio Code, è necessario:

- Configurare il dispositivo IoT Edge, compilare i moduli di IoT Edge con il documento Dockerfile con estensione **debug** e quindi eseguire la distribuzione nel dispositivo IoT Edge.
- Esporre l'indirizzo IP e la porta del modulo in modo che il debugger possa essere collegato.
- Aggiornare il file `launch.json` in modo che Visual Studio Code possa collegarsi al processo nel contenitore del computer remoto. Questo file si trova nella cartella `.vscode` dell'area di lavoro e viene aggiornato ogni volta che si aggiunge un nuovo modulo che supporta il debug.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Compilare e distribuire il modulo nel dispositivo IoT Edge

1. In Visual Studio Code aprire il file `deployment.debug.template.json`, che contiene la versione di debug delle immagini del modulo con i valori `createOptions` corretti impostati.

1. Se si sta sviluppando il modulo in Python, seguire questi passaggi prima di procedere:
   - Aprire il file `main.py` e aggiungere il codice dopo la sezione di importazione:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Aggiungere la singola riga di codice seguente al callback da sottoporre a debug:

      ```python
      ptvsd.break_into_debugger()
      ```

     Se ad esempio si desidera eseguire il debug della `receive_message_listener` funzione, inserire la riga di codice come illustrato di seguito:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Nel riquadro comandi di Visual Studio Code:
   1. Eseguire il comando **Azure IOT Edge: compilazione e Push IOT Edge soluzione**.

   1. Selezionare il file `deployment.debug.template.json` per la soluzione.

1. Nella sezione **Azure IoT Hub Devices** (Dispositivi Azure IoT Hub) della visualizzazione Explorer di Visual Studio Code:
   1. Fare clic con il pulsante destro del mouse su un ID dispositivo IoT Edge e quindi scegliere **Crea distribuzione per singolo dispositivo**.

      > [!TIP]
      > Per controllare di aver scelto un dispositivo IoT Edge, selezionarlo per espandere l'elenco dei moduli e verificare la presenza di **$edgeHub** e **$edgeAgent**. Ogni dispositivo IoT Edge include questi due moduli.

   1. Passare alla cartella **config** della soluzione, selezionare il file `deployment.debug.amd64.json`, quindi selezionare **Select Edge Deployment Manifest** (Seleziona manifesto della distribuzione di Edge).

Si noterà che la distribuzione è stata creata correttamente con un ID distribuzione nel terminale integrato.

È possibile controllare lo stato del contenitore tramite il comando `docker ps` nel terminale. Se Visual Studio Code e il runtime IoT Edge risultano in esecuzione nello stesso computer, controllare lo stato nella visualizzazione Docker in Visual Studio Code.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Esporre l'indirizzo IP e la porta del modulo per il debugger

È possibile ignorare questa sezione se i moduli sono in esecuzione nello stesso computer di Visual Studio Code, perché si usa localhost per collegarsi al contenitore e si hanno già le impostazioni corrette della porta nel documento Dockerfile con estensione **debug**, le impostazioni `createOptions` del contenitore del modulo e il file `launch.json`. Se i moduli e Visual Studio Code sono in esecuzione in computer separati, seguire i passaggi per il linguaggio di sviluppo usato.

- **C#, incluse funzioni di Azure**

  [Configurare il canale SSH nel computer di sviluppo e nel dispositivo IoT Edge](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) e quindi modificare il file `launch.json` per il collegamento.

- **Node.js**

  - Assicurarsi che il modulo nel computer di cui eseguire il debug sia in esecuzione e pronto per il collegamento dei debugger e che la porta 9229 sia accessibile esternamente. È possibile verificarlo aprendo `http://<target-machine-IP>:9229/json` nel computer debugger. Questo URL deve visualizzare informazioni sul modulo Node.js da sottoporre a debug.
  
  - Nel computer di sviluppo aprire Visual Studio Code e quindi modificare `launch.json` in modo che il valore dell'indirizzo del profilo di **debug remoto (node. js) del * &lt;nome&gt; * ** del modulo (o ** * &lt;il&gt; nome del modulo* Remote Debug (node. js nel contenitore di Windows)** , se il modulo è in esecuzione come contenitore Windows, sia l'indirizzo IP del computer di cui è in corso il debug.

- **Java**

  - Compilare un tunnel SSH al computer da sottoporre a debug eseguendo `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`.
  
  - Nel computer di sviluppo aprire Visual Studio Code e modificare il profilo del ** * &lt;nome&gt; del modulo* Remote Debug (Java)** in `launch.json` in modo che sia possibile connettersi al computer di destinazione. Per altre informazioni sulla modifica di `launch.json` e sul debug di Java con Visual Studio Code, vedere la sezione sulla [configurazione del debugger](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Assicurarsi che la porta 5678 nel computer di cui eseguire il debug sia aperta e accessibile.

  - Nel codice `ptvsd.enable_attach(('0.0.0.0', 5678))` inserito precedentemente in `main.py`, sostituire **0.0.0.0** con l'indirizzo IP del computer di cui eseguire il debug. Compilare, eseguire il push e distribuire di nuovo il modulo di IoT Edge.

  - Nel computer di sviluppo aprire Visual Studio Code e quindi `launch.json` modificare in modo che il `host` valore del profilo di ** * &lt;&gt; * debug remoto (Python) del nome del modulo** usi l'indirizzo IP del computer di destinazione invece di. `localhost`

### <a name="debug-your-module"></a>Eseguire il debug del modulo

1. Nella visualizzazione Debug di Visual Studio Code selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile al ** * &lt;nome&gt; del modulo* Remote Debug**

1. Aprire il file del modulo per il linguaggio di sviluppo e aggiungere un punto di interruzione:

   - **Funzione di Azure (C#)**: aggiungere il punto di interruzione `<your module name>.cs`al file.
   - **C#**: aggiungere il punto di interruzione al `Program.cs`file.
   - **Node. js**: aggiungere il punto di interruzione al `app.js`file.
   - **Java**: aggiungere il punto di interruzione al `App.java`file.
   - **Python**: aggiungere il punto di interruzione al `main.py`file nel metodo di callback in cui è `ptvsd.break_into_debugger()` stata aggiunta la riga.
   - **C**: aggiungere il punto di interruzione al `main.c`file.

1. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.

1. Nella visualizzazione Debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra.

> [!NOTE]
> L'esempio precedente illustra come eseguire il debug di moduli IoT Edge nei contenitori. Ha aggiunto porte esposte alle impostazioni `createOptions` del contenitore del modulo. Al termine del debug dei moduli, si consiglia di rimuovere le porte esposte per predisporre i moduli IoT Edge per l'ambiente di produzione.

## <a name="build-and-debug-a-module-remotely"></a>Compilare ed eseguire il debug di un modulo in remoto

Con le modifiche recenti nei motori Docker e Moby per supportare le connessioni SSH e una nuova impostazione negli strumenti di Azure IoT che consente l'inserimento delle impostazioni di ambiente nel riquadro comandi di Visual Studio Code e nei terminali di Azure IoT Edge, è ora possibile compilare i moduli ed eseguirne il debug nei dispositivi remoti.

Vedere questo [post di blog per sviluppatori IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) per altre informazioni e istruzioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
