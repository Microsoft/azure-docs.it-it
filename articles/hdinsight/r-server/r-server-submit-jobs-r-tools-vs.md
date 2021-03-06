---
title: Inviare processi da R Tools per Visual Studio - Azure HDInsight
description: Inviare processi R dal computer Visual Studio locale a un cluster HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: ee984de22052076618728fbacfc31b73c18c073a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864687"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Inviare processi da R Tools per Visual Studio

[R Tools per Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) è un'estensione gratuita e open source per le edizioni Community (gratuita), Professional ed Enterprise sia di [Visual Studio 2017](https://www.visualstudio.com/downloads/) che di [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) o versioni successive. RTVS non è disponibile per [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

RTVS migliora il flusso di lavoro R offrendo strumenti come la [finestra R interattivo](/visualstudio/rtvs/interactive-repl) (REPL), intellisense (completamento del codice), [visualizzazione tracciato](/visualstudio/rtvs/visualizing-data) tramite librerie R come ggplot2 e ggviz, [debug del codice R](/visualstudio/rtvs/debugging)e altro ancora.

## <a name="set-up-your-environment"></a>Configura il tuo ambiente

1. Installare [R Tools per Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png" alt-text="Installazione di RTVS in Visual Studio 2017" border="true":::

2. Selezionare il carico di lavoro *Analisi scientifica dei dati e applicazioni analitiche*, quindi selezionare le opzioni **Supporto del linguaggio R**, **Supporto runtime per lo sviluppo R** e **Microsoft R Client**.

3. È necessario disporre di chiavi pubbliche e private per l'autenticazione SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installare [ML Server](/previous-versions/machine-learning-server/install/r-server-install-windows) nel computer. ML Server fornisce le [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) `RxSpark` funzioni e.

5. Installare [PuTTY](https://www.putty.org/) per fornire un contesto di calcolo per eseguire le funzioni `RevoScaleR` dal client locale al cluster HDInsight.

6. È possibile applicare le Impostazioni di Data Science all'ambiente di Visual Studio, ottenendo un nuovo layout per l'area di lavoro per gli strumenti R.
   1. Per salvare le impostazioni correnti di Visual Studio, usare il comando **Strumenti > Importa/Esporta impostazioni**, quindi selezionare **Esporta le impostazioni di ambiente selezionate** e specificare un nome file. Per ripristinare tali impostazioni, usare lo stesso comando e selezionare **Importa le impostazioni di ambiente selezionate**.

   2. Passare alla voce di menu **Strumenti R**, quindi selezionare **Impostazioni di Data Science**.

       :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png" alt-text="Impostazioni di Data Science di Visual Studio" border="true":::

      > [!NOTE]  
      > Usando l'approccio del passaggio 1 è possibile anche salvare e ripristinare il layout personalizzato, piuttosto che ripetere il comando **Impostazioni di Data Science**.

## <a name="execute-local-r-methods"></a>Eseguire i metodi R locali

1. Creare il cluster HDInsight ML Services.
2. Installare l'[estensione RTVS](/visualstudio/rtvs/installation).
3. Scaricare il [file zip di esempi](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Aprire `examples/Examples.sln` per avviare la soluzione in Visual Studio.
5. Aprire il file `1-Getting Started with R.R` nella cartella `A first look at R`.
6. Partendo dall'inizio del file, premere Ctrl + Invio per inviare ogni riga, una alla volta, nella finestra R interattivo. Alcune righe potrebbero richiedere un po' di tempo a causa dell'installazione di pacchetti.
    * In alternativa, è possibile selezionare tutte le righe nel file R (Ctrl + A), quindi eseguirle tutte (Ctrl + Invio) oppure selezionare l'icona Esegui in finestra interattiva sulla barra degli strumenti.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png" alt-text="Esecuzione interattiva di Visual Studio" border="true":::

7. Dopo aver eseguito tutte le righe dello script, verrà visualizzato un output simile al seguente:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png" alt-text="Strumenti R dell'area di lavoro di Visual Studio" border="true":::

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Inviare processi a un cluster HDInsight ML Services

Usando Microsoft ML Server/Microsoft R Client da un computer Windows dotato di PuTTY, è possibile creare un contesto di calcolo che eseguirà funzioni `RevoScaleR` distribuite dal client locale al cluster HDInsight. Usare `RxSpark` per creare il contesto di calcolo, specificare il nome utente, il nodo perimetrale del cluster Apache Hadoop, gli switch SSH e così via.

1. L'indirizzo del nodo perimetrale di ML Services in HDInsight è `CLUSTERNAME-ed-ssh.azurehdinsight.net` dove `CLUSTERNAME` è il nome del cluster di servizi ml.

1. Incollare il codice seguente nella finestra R interattivo in Visual Studio, modificando i valori delle variabili di installazione in base all'ambiente.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png" alt-text="impostazione del contesto per Apache Spark" border="true":::

1. Nella finestra R interattivo, eseguire i comandi seguenti:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    L'output dovrebbe essere simile al seguente:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png" alt-text="Esecuzione del comando rx avvenuta con esito positivo" border="true":::
a
1. Verificare che `rxHadoopCopy` abbia copiato correttamente il file `people.json` dalla cartella di dati di esempio nella cartella `/user/RevoShare/newUser` appena creata:

    1. Nel riquadro del cluster HDInsight ML Services in Azure selezionare **Account di archiviazione** dal menu a sinistra.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png" alt-text="Account di Azure Archiviazione HDInsight" border="true":::

    2. Selezionare l'account di archiviazione predefinito per il cluster, prendendo nota del nome del contenitore o della directory.

    3. Selezionare **Contenitori** dal menu a sinistra nel riquadro dell'account di archiviazione.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png" alt-text="Contenitori di Azure Archiviazione HDInsight" border="true":::

    4. Selezionare il nome del contenitore del cluster, scegliere la cartella **utente** (potrebbe essere necessario fare clic su *Carica altri* nella parte inferiore dell'elenco), quindi selezionare *RevoShare*, quindi **newUser**. Il file `people.json` dovrebbe comparire nella cartella `newUser`.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png" alt-text="HDInsight percorso cartella file copiato" border="true":::

1. Dopo aver terminato di usare il contesto Apache Spark corrente, è necessario arrestarlo. Non è possibile eseguire contemporaneamente più contesti.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Opzioni del contesto di calcolo per ML Services in HDInsight](r-server-compute-contexts.md)
* [La combinazione di ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornisce un esempio di stime di ritardo dei voli di una compagnia aerea.