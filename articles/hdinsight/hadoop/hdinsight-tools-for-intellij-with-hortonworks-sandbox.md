---
title: Usare Azure Toolkit for IntelliJ con Hortonworks Sandbox
description: Informazioni su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ con Hortonworks Sandbox.
keywords: strumenti hadoop,query hive,intellij,hortonworks sandbox,azure toolkit for intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647833"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox

Informazioni su come usare gli strumenti HDInsight per IntelliJ per sviluppare applicazioni Apache Scala e quindi testare le applicazioni su [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) in esecuzione nel computer. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) è un ambiente di sviluppo integrato (IDE) Java per lo sviluppo di software per computer. Dopo aver sviluppato e testato le applicazioni su Hortonworks Sandbox, è possibile trasferirle in [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

- Hortonworks Data Platform (HDP) 2.4 su Hortonworks Sandbox in esecuzione nel computer locale. Per configurare HDP, vedere [Introduzione all'ecosistema Apache Hadoop con un ambiente sandbox Hadoop su una macchina virtuale](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Gli strumenti HDInsight per IntelliJ sono stati testati solo con HDP 2.4. Per ottenere HDP 2.4, espandere [Archivio Hortonworks Sandbox](https://hortonworks.com/downloads/#sandbox) nel **sito di download di Hortonworks Sandbox**.

- [Java Developer Kit (JDK) versione 1.8 o successiva](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ richiede il kit JDK.

- [Edizione della community di IntelliJ IDEA](https://www.jetbrains.com/idea/download) con il plug-in [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) e il plug-in [Azure Toolkit for IntelliJ.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) Gli strumenti HDInsight per IntelliJ sono disponibili come parte di Azure Toolkit for IntelliJ. 

Per installare i plug-in:

  1. Aprire IntelliJ IDEA.
  2. Nella **pagina iniziale** selezionare **Configure** (Configura) e quindi **Plugins** (Plug-in).
  3. Nell'angolo inferiore sinistro selezionare **Install JetBrains plugin** (Installa plug-in JetBrains).
  4. Usare la funzione di ricerca per cercare **Scala** e quindi selezionare **Install** (Installa).
  5. Per completare l'installazione, selezionare **Restart IntelliJ IDEA** (Riavvia IntelliJ IDEA).
  6. Ripetere i passaggi 4 e 5 per installare **Azure Toolkit for IntelliJ**. Per altre informazioni, vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Creare un'applicazione Apache Spark Scala

In questa sezione verrà creato un progetto Scala di esempio usando IntelliJ IDEA. Nella sezione successiva, IntelliJ IDEA verrà collegato a Hortonworks Sandbox (emulatore) prima di inviare il progetto.

1. Aprire IntelliJ IDEA nel computer in uso. Nella finestra di dialogo **New Project** (Nuovo progetto) completare questi passaggi:

   1. Selezionare **HDInsight** > **Spark in HDInsight (Scala)**.
   2. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, a seconda dello scenario:

      * **Maven**: Per il supporto della creazione guidata progetto Scala.Maven : For Scala project-creation wizard support.
      * **SBT**: per la gestione delle dipendenze e la compilazione per il progetto Scala.

   ![Intellij crea un nuovo progetto scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Fare clic su **Avanti**.
3. Nella finestra di dialogo **New Project**(Nuovo progetto) successiva completare questi passaggi:

   1. Nella casella **Project name** (Nome progetto) immettere un nome per il progetto.
   2. Nella casella **Project location** (Percorso progetto) immettere un percorso per il progetto.
   3. Accanto all'elenco a discesa **Project SDK** (SDK progetto) selezionare **New** (Nuovo), **JDK** e quindi specificare la cartella per Java JDK 1.7 o versione successiva. Selezionare **Java 1.8** per il cluster Spark 2.x. Selezionare **Java 1.7** per il cluster Spark 1.x. Il percorso predefinito è C:\Programmi\Java\jdk1.8.x_xxx.
   4. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark2.x**. In questo esempio viene usata la versione Spark 1.6.2 (Scala 2.10.5). Assicurarsi di usare il repository contrassegnato come **Scala 2.10.x**. Non usare il repository contrassegnato come Scala 2.11.x.
    
      ![Proprietà della creazione del progetto IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Fare clic su **Fine**.
5. Se la visualizzazione **Project** (Progetto) non è già aperta, premere **ALT+1** per aprirla.
6. In **Project Explorer** (Esplora progetti) espandere il progetto e quindi selezionare **src**.
7. Fare clic con il pulsante destro del mouse su **src**, scegliere **New** (Nuovo) e quindi fare clic su **Scala class** (Classe Scala).
8. Nella casella **Nome** immettere un nome. Nella casella **Kind** (Tipo) selezionare **Object** (Oggetto). Selezionare quindi **OK**.

    ![Finestra di dialogo Create New Scala Class (Crea nuova classe Scala)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Incollare il codice seguente nel file con estensione scala:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Dal menu **Build** (Compila) scegliere **Build project** (Compila progetto). Verificare che la compilazione venga eseguita correttamente.


## <a name="link-to-the-hortonworks-sandbox"></a>Eseguire il collegamento a Hortonworks Sandbox

Per poter eseguire il collegamento a Hortonworks Sandbox (emulatore), deve essere presente un'applicazione IntelliJ esistente.

Per eseguire il collegamento a un emulatore:

1. Aprire il progetto in IntelliJ.
2. Dal menu **View** (Visualizza) scegliere **Tool Windows** (Finestre degli strumenti) e quindi selezionare **Azure Explorer**.
3. Espandere **Azure**, fare clic con il pulsante destro del mouse su **HDInsight** e quindi scegliere **Link an Emulator** (Collega un emulatore).
4. Nella finestra di dialogo **Link A New Emulator** (Collega nuovo emulatore) immettere la password impostata per l'account radice di Hortonworks Sandbox. Immettere quindi valori simili a quelli usati nello screenshot seguente. Selezionare quindi **OK**. 

   ![Finestra Link a New Emulator (Collega nuovo emulatore)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Per configurare l'emulatore, selezionare **Yes** (Sì).

Quando è connesso correttamente, l'emulatore (Hortonworks Sandbox) è elencato nel nodo HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Inviare l'applicazione Spark Scala a Hortonworks Sandbox

Dopo aver collegato IntelliJ IDEA all'emulatore è possibile inviare il progetto.

Per inviare un progetto a un emulatore:

1. In **Project Explorer** (Esplora progetti) fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).
2. Completare i passaggi seguenti:

    1. Nell'elenco a discesa **Spark cluster (Linux only)** (Cluster Spark - solo Linux) selezionare l'ambiente Hortonworks Sandbox locale.
    2. Nella casella **Main class name** (Nome classe principale) scegliere o immettere il nome della classe principale. Per questo articolo, il nome è **GroupByTest**.

3. Selezionare **Submit**. I log di invio dei processi vengono visualizzati nella finestra degli strumenti di invio Spark.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Per un video sugli strumenti HDInsight per IntelliJ, vedere [Introducing HDInsight Tools for IntelliJ for Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Introduzione agli strumenti HDInsight per IntelliJ per lo sviluppo Apache Spark).

- Leggere le informazioni su come [eseguire il debug remoto di applicazioni Apache Spark in un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Leggere le informazioni su come [usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Apache Spark in un cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Leggere le informazioni su come [usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

