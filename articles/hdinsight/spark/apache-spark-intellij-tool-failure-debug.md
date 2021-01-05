---
title: Eseguire il debug del processo Spark con IntelliJ Azure Toolkit (anteprima)-HDInsight
description: Indicazioni sull'uso degli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug delle applicazioni
keywords: eseguire debug remoto di intellij, debug remoto di intellij, ssh, intellij, hdinsight, debug di intellij, debug
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: c8ce1a66d6dbf215c3d49740f2aec47ab01f7591
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822319"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Errore durante il debug del processo Spark con Azure Toolkit for IntelliJ (anteprima)

Questo articolo fornisce istruzioni dettagliate su come usare gli strumenti HDInsight in [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) per eseguire le applicazioni di **debug degli errori di Spark** .

## <a name="prerequisites"></a>Prerequisiti

* [Kit di sviluppo di Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Questa esercitazione usa Java versione 8.0.202.
  
* IntelliJ IDEA. Questo articolo usa [INTELLIJ idea community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Vedere [Installazione di Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Connettersi al cluster HDInsight. Vedere [connettersi al cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Vedere [scaricare Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Creare un progetto con il modello di debug

Creare un progetto Spark 2.3.2 per continuare il debug degli errori, eseguire il debug dell'attività di esempio file di esempio in questo documento.

1. Aprire IntelliJ IDEA. Aprire la finestra **nuovo progetto** .

   a. Selezionare **Azure Spark/HDInsight** nel riquadro sinistro.

   b. Selezionare il **progetto Spark con l'esempio di debug dell'attività non riuscita (anteprima) (scala)** dalla finestra principale.

     ![IntelliJ creare un progetto di debug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selezionare **Avanti**.

2. Nella finestra di dialogo **New Project** (Nuovo progetto) eseguire questa procedura:

   ![IntelliJ nuovo progetto selezionare versione Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Specificare un nome per il progetto e il relativo percorso.

   b. Nell'elenco a discesa **SDK progetto** selezionare **Java 1,8** per il cluster **Spark 2.3.2** .

   c. Nell'elenco a discesa **Spark Version (versione Spark** **) selezionare Spark 2.3.2 (scala 2.11.8)**.

   d. Selezionare **Fine**.

3. Selezionare **src**  >  **Main**  >  **scala** per aprire il codice nel progetto. In questo esempio viene usato lo script **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Eseguire un'applicazione Spark scala/Java in un cluster HDInsight

Creare un'applicazione Spark scala/Java, quindi eseguire l'applicazione in un cluster Spark attenendosi alla procedura seguente:

1. Fare clic su **Aggiungi configurazione** per aprire la finestra **configurazioni di esecuzione/debug** .

   ![Aggiunta configurazione di HDI IntelliJ](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Apache Spark on HDInsight** .

   ![IntelliJ aggiungere una nuova configurazione](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Passare alla scheda **Esegui in remoto in cluster** . Immettere le informazioni per il **nome**, il **cluster Spark** e il **nome della classe principale**. Questi strumenti supportano il debug con **executor**. **NumExectors**, il valore predefinito è 5 e non è consigliabile impostare un valore maggiore di 3. Per ridurre il tempo di esecuzione, è possibile aggiungere **Spark. Yarn. maxAppAttempts** nelle **configurazioni dei processi** e impostare il valore su 1. Fare clic sul pulsante **OK** per salvare la configurazione.

   ![IntelliJ eseguire configurazioni di debug nuove](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**.

5. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto sul cluster remoto.

   ![Pulsante di esecuzione remota del processo Spark remoto di IntelliJ](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. È possibile controllare l'ID applicazione dalla finestra output.

   ![Risultato dell'esecuzione remota del processo Spark remoto di IntelliJ debug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Download profilo processo non riuscito

Se l'invio del processo ha esito negativo, è possibile scaricare il profilo del processo non riuscito nel computer locale per eseguire ulteriori operazioni di debug.

1. Aprire **Microsoft Azure Storage Explorer**, individuare l'account HDInsight del cluster per il processo non riuscito, scaricare le risorse del processo non riuscite dal percorso corrispondente: **\hdp\spark2-Events \\ . Spark \\ \<application ID> -errori** in una cartella locale. La finestra **attività** indicherà lo stato del download.

   ![Errore di download Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Download Azure Storage Explorer riuscito](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configurare l'ambiente di debug locale ed eseguire il debug in caso di errore

1. Aprire il progetto originale o creare un nuovo progetto e associarlo al codice sorgente originale. Attualmente è supportata solo la versione di Spark 2.3.2 per il debug degli errori.

1. In IntelliJ IDEA creare un file di configurazione di **debug di errore Spark** , selezionare il file FTD dal campo risorse processo non riuscito scaricato in precedenza per il campo **percorso del contesto di errore del processo Spark** .

   ![configurazione degli errori di Crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Fare clic sul pulsante Esegui locale sulla barra degli strumenti. l'errore verrà visualizzato nella finestra Esegui.

   ![esecuzione-errore-Configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![esecuzione-errore-Configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Impostare punto di rottura come indicato nel log, quindi fare clic sul pulsante debug locale per eseguire il debug locale esattamente come i normali progetti scala/Java in IntelliJ.

1. Dopo il debug, se il progetto viene completato correttamente, è possibile inviare nuovamente il processo non riuscito al cluster Spark in HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Passaggi successivi

* [Panoramica: eseguire il debug di applicazioni Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari

* [Apache Spark con BI: eseguire un'analisi interattiva dei dati usando Spark in HDInsight con gli strumenti di Business Intelligence](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](./apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per Jupyter Notebook nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con notebook di Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)