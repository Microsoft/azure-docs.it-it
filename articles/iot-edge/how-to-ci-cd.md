---
title: Integrazione continua & distribuzione continua - Azure IoT Edge
description: "Configurare l'integrazione e la distribuzione continue: Azure IoT Edge con DevOps di Azure, Azure Pipelines"
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510975"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integrazione e distribuzione continue in Azure IoT Edge

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo descrive come usare le funzionalità di integrazione continua e di distribuzione continua di Azure Pipelines per compilare, testare e distribuire applicazioni in Azure IoT Edge in modo rapido ed efficiente.

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-ci-cd/cd.png)

Questo articolo descrive come usare le attività integrate Azure IoT Edge per Azure Pipelines per creare due pipeline per la soluzione IoT Edge. Esistono quattro azioni che possono essere usate nelle attività di Azure IoT Edge.There are four actions can be used in the Azure IoT Edge tasks.

* **Azure IoT Edge: le immagini del modulo** di compilazione accetta il codice della soluzione IoT Edge e compila le immagini del contenitore.
* **Azure IoT Edge: le immagini** del modulo Push inviano le immagini del modulo nel Registro di sistema del contenitore specificato.
* **Azure IoT Edge: genera manifesto di distribuzione** accetta un file deployment.template.json e le variabili, quindi genera il file del manifesto di distribuzione finale di IoT Edge.Azure IoT Edge Edge - Generate Deployment Manifest takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
* **Azure IoT Edge: la distribuzione nei dispositivi perimetrali IoT** consente di creare distribuzioni di IoT Edge in più/più dispositivi IoT Edge.Azure IoT Edge - Deploy to IoT Edge devices helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>Prerequisiti

* Un repository Azure Repos. Se non se ne ha uno, è possibile [creare un nuovo repository Git nel progetto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Una soluzione IoT Edge di cui sia stato eseguito il commit e il push nel repository. Se si vuole creare una nuova soluzione di esempio per testare le procedure di questo articolo, seguire i passaggi descritti in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md) oppure [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge](how-to-visual-studio-develop-csharp-module.md).

   Per questo articolo, tutto quello che serve è la cartella della soluzione creata dai modelli IoT Edge in Visual Studio Code o Visual Studio. Non è necessario compilare, eseguire il push, distribuire o eseguire il debug di questo codice prima di procedere. Questi processi saranno configurati in Azure Pipelines.

   Se si sta creando una nuova soluzione, prima di tutto clonare il repository in locale. Quindi, quando si crea la soluzione, sarà possibile crearla direttamente nella cartella del repository. Da questa posizione è possibile eseguire facilmente il commit e il push dei nuovi file.

* Un registro contenitori in cui eseguire il push delle immagini dei moduli. È possibile usare il [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) o un registro di terze parti.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) attivo con almeno i dispositivi IoT Edge per provare le fasi separate di test e distribuzione in produzione. È possibile seguire gli articoli di avvio rapido per creare un dispositivo IoT Edge in [Linux](quickstart-linux.md) o [Windows](quickstart.md)

Per altre informazioni sull'uso di Azure Repos, vedere [Condividere il codice con Visual Studio e Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configurare l'integrazione continua

In questa sezione viene creata una nuova pipeline di compilazione. Configurare la pipeline per l'esecuzione automatica quando si archiviano modifiche alla soluzione IoT Edge di esempio e pubblicare i log di compilazione.

>[!NOTE]
>Questo articolo usa la finestra di progettazione visiva di Azure DevOps. Prima di seguire i passaggi descritti in questa sezione, disattivare la funzionalità di anteprima per la nuova esperienza di creazione della pipeline YAML.
>
>1. In Azure DevOps selezionare l'icona del profilo, quindi selezionare **Funzionalità in anteprima**.
>2. Disattivare **Nuova esperienza di creazione pipeline YAML**.
>
>Per altre informazioni, vedere [Creare una pipeline di compilazione](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Accedere all'organizzazione DevOps di Azure (**\/https: /dev.azure.com/'your organization//**) e aprire il progetto che contiene il repository della soluzione IoT Edge.

   Per questo articolo è stato creato un repository denominato **IoTEdgeRepo**. Questo repository contiene **IoTEdgeSolution**, in cui c'è il codice per un modulo denominato **filtermodule**.

   ![Aprire il progetto DevOps](./media/how-to-ci-cd/init-project.png)

2. Passare ad Azure Pipelines nel progetto. Aprire la scheda **Compilazioni** e selezionare **Nuova pipeline**. In alternativa, se si hanno già pipeline di compilazione, scegliere il pulsante **Nuova**. Selezionare quindi **Nuova pipeline di compilazione**.

    ![Creazione di una nuova pipeline di compilazione](./media/how-to-ci-cd/add-new-build.png)

3. Seguire le istruzioni visualizzate per creare la pipeline.

   1. Specificare le informazioni sull'origine per la nuova pipeline di compilazione. Selezionare l'origine **GIT Azure Repos**, quindi selezionare progetto, repository e ramo in cui si trova il codice della soluzione IoT Edge. Selezionare quindi **Continua**.

      ![Selezionare l'origine della pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selezionare l'opzione per iniziare con una **fase vuota** anziché un modello.

      ![Iniziare con un progetto vuoto](./media/how-to-ci-cd/start-with-empty.png)

4. Dopo la creazione della pipeline, viene visualizzato l'editor pipeline. Nella descrizione della pipeline scegliere il pool di agenti corretto in base alla piattaforma di destinazione:

   * Se si desidera compilare i propri moduli nella piattaforma amd64 per i contenitori Linux, scegliere **Hosted Ubuntu 1604**

   * Se si desidera compilare i moduli nella piattaforma amd64 per i contenitori Windows 1809, è necessario [configurare un agente auto-ospitato in Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se si desidera costruire i moduli in piattaforma arm32v7 o arm64 per i contenitori Linux, è necessario [configurare l'agente self-hosted su Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Configurare il pool di agenti di compilazione](./media/how-to-ci-cd/configure-env.png)

5. La pipeline è preconfigurata con un processo denominato **Processo agente 1**. Selezionare il**+** segno più ( ) per aggiungere al processo tre attività: **Azure IoT Edge** due volte, **Copia file** una volta e Pubblica elementi di **compilazione** una volta. Passare il mouse sul nome di ogni attività per vedere il pulsante **Aggiungi**.

   ![Aggiungere l'attività Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando vengono aggiunte tutte e quattro le attività, il processo dell'agente è simile all'esempio seguente:When all four tasks are added, your Agent job looks like the following example:

   ![Tre attività nella pipeline di compilazione](./media/how-to-ci-cd/add-tasks.png)

6. Selezionare la prima attività **Azure IoT Edge** per modificarla. Questa attività compila tutti i moduli nella soluzione con la piattaforma di destinazione specificata.

   * **Nome visualizzato**: accettare il **bordo IoT di Azure**predefinito - Immagini del modulo di compilazione .
   * **Azione**: Accettare le immagini predefinite del **modulo di compilazione**.
   * **File .template.json**: selezionare i ellissi (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge.
   * **Piattaforma predefinita**: Selezionare la piattaforma appropriata per i moduli in base al dispositivo IoT Edge di destinazione.
   * **Variabili di output**: Le variabili di output includono un nome di riferimento che è possibile utilizzare per configurare il percorso del file in cui verrà generato il file deployment.json. Impostare il nome di riferimento su un valore facile da ricordare, ad esempio **edge**.

7. Selezionare la seconda attività **Azure IoT Edge** per modificarla. Questa attività esegue il push di tutte le immagini dei moduli nel registro contenitori selezionato.

   * **Nome visualizzato**: Il nome visualizzato viene aggiornato automaticamente quando il campo dell'azione viene modificato.
   * **Azione**: Utilizzare l'elenco a discesa per selezionare **Push module images**.
   * **Tipo di registro contenitore:** selezionare il tipo di registro contenitore utilizzato per archiviare le immagini del modulo. A seconda del tipo di registro scelto, il modulo cambia. Se si sceglie **Registro Azure Container**, usare gli elenchi a discesa per selezionare la sottoscrizione di Azure e il nome del registro contenitori. Se si sceglie **Generic Container Registry** (Registro contenitori generico), selezionare **Nuovo** per creare una connessione al servizio di registro.
   * **File .template.json**: selezionare i ellissi (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge.
   * **Piattaforma predefinita**: Selezionare la stessa piattaforma delle immagini del modulo create.

   Se si dispone di più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare **Ignora moduli** nelle impostazioni avanzate per ignorare le immagini che non sono per questo specifico registro.

8. Selezionare l'attività **Copia file** per modificarla. Utilizzare questa attività per copiare i file nella directory di gestione temporanea degli elementi.

   * **Nome visualizzato**: Copia file in: Cartella di ricezione.
   * **Contenuto**: Inserire due righe `deployment.template.json` `**/module.json`in questa sezione e . Questi due tipi di file sono gli input per generare il manifesto di distribuzione di IoT Edge.These two types of files are the inputs to generate IoT Edge deployment manifest. È necessario copiare nella cartella di gestione temporanea dell'elemento e pubblicare per la pipeline di rilascio.
   * **Cartella di destinazione** `$(Build.ArtifactStagingDirectory)`: Inserire la variabile . Vedere [Creare variabili](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) per informazioni sulla descrizione.

9. Selezionare l'attività **Pubblica artefatti di compilazione** per modificarla. Fornire il percorso della directory di gestione temporanea dell'elemento per l'attività in modo che il percorso possa essere pubblicato nella pipeline di rilascio.

   * **Nome visualizzato**: Pubblica artefatto: drop.
   * **Percorso da pubblicare** `$(Build.ArtifactStagingDirectory)`: Inserire la variabile . Vedere [Creare variabili](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) per informazioni sulla descrizione.
   * **Nome artefatto**: drop.
   * Percorso di **pubblicazione dell'elemento:** Pipeline di Azure.Afact publish location : Azure Pipelines.

10. Aprire la scheda **Trigger** e selezionare la casella **Abilita l'integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-ci-cd/configure-trigger.png)

11. Salvare la nuova pipeline di compilazione con il pulsante **Salva**.

Questa pipeline è ora configurata per l'esecuzione automatica quando si esegue il push di nuovo codice al repository. L'ultima attività, quella di pubblicazione di artefatti della pipeline, attiva una pipeline di versione. Passare alla sezione successiva per creare la pipeline di versione.

## <a name="configure-continuous-deployment"></a>Configurare la distribuzione continua

In questa sezione si crea una pipeline di versione configurata per essere eseguita automaticamente quando la pipeline di compilazione rilascerà gli artefatti e mostrerà i log di distribuzione in Azure Pipelines.

Creare una nuova pipeline e aggiungere una nuova faseCreate a new pipeline, and add a new stage

1. Nella scheda **Versioni**, scegliere **+ Nuova pipeline**. In alternativa, se sono già disponibili pipeline di versione, scegliere il pulsante **+ Nuova** e fare clic su **+ Nuova pipeline di versione**.  

    ![Aggiungere pipeline di versione](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando viene richiesto di selezionare un modello, scegliere di iniziare con una **fase vuota**.

    ![Iniziare con un processo vuoto](./media/how-to-ci-cd/start-with-empty-job.png)

3. La nuova pipeline di versione viene inizializzata con un'unica fase, denominata **Fase 1**. Rinominare la fase 1 per **dedicarla** e considerarlo come un ambiente di test. In genere, le pipeline di distribuzione continua hanno più fasi, tra cui **dev**, **staging** e **prod**. È possibile crearne altre in base alla procedura DevOps. Dopo aver modificato il nome, chiudere la finestra dei dettagli della fase.

4. Collegare la versione agli artefatti di compilazione pubblicati dalla pipeline di compilazione. Fare clic su **Aggiungi** nell'area degli elementi.

   ![Aggiungere artefatti](./media/how-to-ci-cd/add-artifacts.png)  

5. Nella pagina **Aggiungi un artefatto** selezionare il tipo di origine **Compilazione**. Selezionare quindi il progetto e la pipeline di compilazione che è stata creata. Selezionare quindi **Aggiungi**.

   ![Aggiungere un artefatto di compilazione](./media/how-to-ci-cd/add-an-artifact.png)

6. Aprire i trigger degli artefatti e selezionare l'interruttore per abilitare il trigger di distribuzione continua. A questo punto, ogni volta che è disponibile una nuova build, verrà creata una nuova versione.

   ![Configurare il trigger di distribuzione continua](./media/how-to-ci-cd/add-a-trigger.png)

7. La fase di **sviluppo** è preconfigurata con un processo e zero attività. Scegliere **Attività** dal menu Pipeline, quindi scegliere la fase di **sviluppo.**  Selezionare il numero di processi e attività per configurare le attività in questa fase.

    ![Configurare le attività di sviluppoConfigure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. Nella fase di **sviluppo** verrà visualizzato un **processo Agent**predefinito. È possibile configurare dettagli sul processo agente, ma l'attività di distribuzione è indipendente dalla piattaforma, il che significa che è possibile scegliere **Hosted VS2017** o **Hosted Ubuntu 1604** nel **Pool di agenti** (o qualsiasi altro agente gestito dall'utente stesso).

9. Selezionare il**+** segno più ( ) per aggiungere due attività. Cercare e aggiungere **Azure IoT Edge** due volte.

    ![Aggiungere attività per lo sviluppoAdd tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Selezionare la prima attività **di Azure IoT Edge** e configurarla con i valori seguenti:Select the first Azure IoT Edge task and configure it with the following values:

    * **Nome visualizzato**: Il nome visualizzato viene aggiornato automaticamente quando il campo dell'azione viene modificato.
    * **Azione**: Utilizzare l'elenco a discesa per selezionare **Genera manifesto di distribuzione**. Modificando il valore dell'azione viene aggiornato anche il nome visualizzato dell'attività, in modo che corrispondano.
    * **.template.json file**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Inserire il percorso . Il percorso viene pubblicato dalla pipeline di compilazione.
    * **Piattaforma predefinita**: Scegliere lo stesso valore durante la compilazione delle immagini del modulo.
    * **Percorso di output** `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`: Inserire il percorso . Questo percorso è il file manifesto di distribuzione finale di IoT Edge.This path is the final IoT Edge deployment manifest file.

    Queste configurazioni consentono di sostituire gli `deployment.template.json` URL delle immagini del modulo nel file. Il **manifesto di distribuzione Genera** consente inoltre di sostituire `deployment.template.json` le variabili con il valore esatto definito nel file. In VS/VS Code, si specifica il `.env` valore effettivo in un file. In Pipeline di Azure è possibile impostare il valore nella scheda Variabili pipeline di rilascio.

    * **ACR_ADDRESS:** indirizzo del Registro di sistema del contenitore di Azure.ACR_ADDRESS: Your Azure Container Registry address.
    * **ACR_PASSWORD:** password del Registro di sistema del contenitore di Azure.ACR_PASSWORD : Your Azure Container Registry password.
    * **ACR_USER**: nome utente del Registro di sistema del contenitore di Azure.The Azure Container Registry username.

    Se nel progetto sono presenti altre variabili, è possibile specificare il nome e il valore in questa scheda. Il **manifesto di distribuzione Genera** può `${VARIABLE}` riconoscere solo le variabili sono `*.template.json` in sapore, assicurarsi che si sta utilizzando questo nei file.

    ![Configurare le variabili per la pipeline di rilascioConfigure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

11. Selezionare la seconda attività **di Azure IoT Edge** e configurarla con i valori seguenti:Select the second Azure IoT Edge task and configure it with the following values:

    * **Nome visualizzato**: Il nome visualizzato viene aggiornato automaticamente quando il campo dell'azione viene modificato.
    * **Azione**: Utilizzare l'elenco a discesa per selezionare **Distribuisci in dispositivi perimetrali IoT**. Modificando il valore dell'azione viene aggiornato anche il nome visualizzato dell'attività, in modo che corrispondano.
    * **Sottoscrizione di Azure:** selezionare la sottoscrizione che contiene l'hub IoT.Azure subscription : Select the subscription that contains your IoT Hub.
    * **Nome hub IoT:** selezionare l'hub IoT.IoT hub name : Select your IoT hub.
    * **Scegliere un dispositivo singolo/più:** scegliere se si desidera distribuire la pipeline di rilascio in uno o più dispositivi.
      * Se si distribuisce a un singolo dispositivo, immettere l'**ID del dispositivo IoT Edge**.
      * Se si distribuisce a più dispositivi, specificare la **condizione di destinazione** del dispositivo. La condizione di destinazione è un filtro che corrisponde a un set di dispositivi IoT Edge nell'hub IoT.The target condition is a filter to match a set of IoT Edge devices in IoT Hub. Se si vogliono usare i Tag del dispositivo come condizione, è necessario aggiornare i tag di dispositivo corrispondenti con il dispositivo gemello hub IoT. Aggiornare l'**ID distribuzione di IoT Edge** e la **priorità della distribuzione IoT Edge** nelle impostazioni avanzate. Per altre informazioni sulla creazione di una distribuzione per più dispositivi, vedere [Informazioni sulle distribuzioni automatiche IoT Edge](module-deployment-monitoring.md).
    * Espandere Impostazioni avanzate , selezionare ID distribuzione `$(System.TeamProject)-$(Release.EnvironmentName)`Edge **IoT**, inserire la variabile . Verrà eseguito il mapping del progetto e del nome della versione con l'ID di distribuzione di IoT Edge.This maps the project and release name with your IoT Edge deployment ID.

12. Selezionare **Salva** per salvare le modifiche nella nuova pipeline di versione. Tornare alla visualizzazione pipeline selezionando **Pipeline** dal menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. In questa sezione si attiva manualmente la pipeline CI/CD per verificarne il funzionamento. Si verifica quindi che la distribuzione abbia esito positivo.

1. Passare alla pipeline di compilazione creata all'inizio di questo articolo.

2. È possibile attivare un processo di compilazione nella propria pipeline di compilazione selezionando il pulsante **Coda** come nello screenshot seguente.

    ![Trigger manuale](./media/how-to-ci-cd/manual-trigger.png)

3. Selezionare il processo di compilazione per controllare lo stato di avanzamento. Se la pipeline di compilazione viene completata correttamente, viene attivata una fase di rilascio per la fase di **sviluppo.**

    ![Log di compilazione](./media/how-to-ci-cd/build-logs.png)

4. La versione **di sviluppo** riuscita crea la distribuzione IoT Edge per i dispositivi IoT Edge di destinazione.

    ![Rilasciare per gli sviluppatoriRelease to dev](./media/how-to-ci-cd/pending-approval.png)

5. Fare clic sulla fase di **sviluppo** per visualizzare i log delle versioni.

    ![Log di versione](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Passaggi successivi

* Esempio di procedure consigliate IoT Edge in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
