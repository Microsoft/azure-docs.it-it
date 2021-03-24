---
title: Analizzare Azure Data Lake Storage Gen1 con HDInsight Apache Spark
description: Eseguire processi Apache Spark per analizzare i dati archiviati in Azure Data Lake Storage Gen1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: e79c2f361108f1daa3c4a125491d1b399e050648
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863701"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Usare il cluster HDInsight Spark per analizzare i dati in Azure Data Lake Storage Gen1

In questo articolo si usa [Jupyter notebook](https://jupyter.org/) disponibile con i cluster HDInsight Spark per eseguire un processo che legge i dati da un account di data Lake storage.

## <a name="prerequisites"></a>Prerequisiti

* Account Azure Data Lake Storage Gen1. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Azure HDInsight Spark con Azure Data Lake Storage Gen1 come risorsa di archiviazione. Seguire le istruzioni in [Guida introduttiva: impostare i cluster in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prepare-the-data"></a>Preparare i dati

> [!NOTE]  
> Non è necessario eseguire questo passaggio se è stato creato il cluster HDInsight con Data Lake Storage come spazio di archiviazione predefinito. Il processo di creazione del cluster aggiunge alcuni dati di esempio nell'account di Data Lake Storage specificato durante la creazione del cluster. Passare alla sezione Usare il cluster HDInsight Spark con Data Lake Storage.

Se è stato creato un cluster HDInsight con Data Lake Storage come risorsa di archiviazione aggiuntiva e un BLOB del servizio di archiviazione di Azure come risorsa di archiviazione predefinita, è necessario copiare prima di tutto alcuni dati di esempio nell'account Data Lake Storage. È possibile usare i dati di esempio dal BLOB di Archiviazione di Azure associati al cluster HDInsight. Per farlo, è possibile usare lo [strumento ADLCopy](https://www.microsoft.com/download/details.aspx?id=50358) . Scaricare e installare lo strumento dal collegamento.

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.

2. Eseguire il comando seguente per copiare un BLOB specifico dal contenitore di origine a Data Lake Storage:

    ```scala
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Copiare il file di dati di esempio **HVAC.csv** disponibile nel percorso **/HdiSamples/HdiSamples/SensorSampleData/hvac/** nell'account Azure Data Lake Storage. Il frammento di codice dovrebbe essere simile al seguente:

    ```scala
    AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

   > [!WARNING]  
   > Assicurarsi che nel nome del file e nel percorso le maiuscole siano corrette.

3. Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Data Lake Storage. L'output visualizzato dovrebbe essere simile al frammento di codice seguente:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Copy Completed. 1 file copied.
    ```

    Il file di dati (**HVAC.csv**) sarà copiato in una cartella denominata **/hvac** nell'account Data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Usare un cluster HDInsight Spark con Azure Data Lake Storage Gen1

1. Dal [portale di Azure](https://portal.azure.com/), dal schermata iniziale, fare clic sul riquadro per il cluster di Apache Spark (se è stato aggiunto a schermata iniziale). È anche possibile passare al cluster in **Esplora tutti i**  >  **cluster HDInsight**.

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

   > [!NOTE]  
   > È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire **clustername** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

    :::image type="content" source="./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png " alt-text="Crea una nuova Jupyter Notebook" border="true":::

4. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. A tale scopo incollare il frammento di codice seguente in una cella e premere **MAIUSC+INVIO**.

    ```scala
    from pyspark.sql.types import *
    ```

    Ogni volta che viene eseguito un processo in Jupyter, il titolo della finestra del Web browser visualizzerà lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Dopo il completamento del processo, viene visualizzato un cerchio vuoto.

     :::image type="content" source="./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png " alt-text="Stato di un processo di Jupyter Notebook" border="true":::

5. Caricare i dati di esempio in una tabella temporanea usando il file **HVAC.csv** che è stato copiato nell'account Azure Data Lake Storage Gen1. È possibile accedere ai dati dell'account di Data Lake Storage usando il modello di URL seguente.

   * Se è presente Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita, il file HVAC.csv sarà disponibile in un percorso analogo all'URL seguente:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

       In alternativa, è possibile anche usare un formato abbreviato, ad esempio il seguente:

        ```scala
        adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

   * Se è presente Data Lake Storage come risorsa di archiviazione aggiuntiva, il file HVAC.csv sarà disponibile nel percorso in cui è stato copiato, ad esempio:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>
        ```

     In una cella vuota incollare l'esempio di codice seguente, sostituire **MYDATALAKESTORE** con il nome dell'account Data Lake Storage e premere **MAIUSC + INVIO**. Questo esempio di codice registra i dati in una tabella temporanea denominata **hvac**.

      ```scala
      # Load the data. The path below assumes Data Lake Storage is   default storage for the Spark cluster
      hvacText = sc.textFile("adl://MYDATALAKESTORazuredatalakestore.  net/cluster/mysparkclusteHdiSamples/HdiSamples/  SensorSampleData/hvac/HVAC.csv")

      # Create the schema
      hvacSchema = StructType([StructField("date", StringTy(), False)  ,StructField("time", StringType(), FalseStructField  ("targettemp", IntegerType(), FalseStructField("actualtemp",   IntegerType(), FalseStructField("buildingID", StringType(),   False)])

      # Parse the data in hvacText
      hvac = hvacText.map(lambda s: s.split(",")).filt(lambda s: s  [0] != "Date").map(lambda s:(str(s[0]), s(s[1]), int(s[2]), int  (s[3]), str(s[6]) ))

      # Create a data frame
      hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

      # Register the data fram as a table to run queries against
      hvacdf.registerTempTable("hvac")
      ```

6. Dato che si usa un kernel PySpark, è ora possibile eseguire direttamente una query SQL sulla tabella temporanea **hvac** appena creata usando il comando Magic `%%sql`. Per altre informazioni sul `%%sql` Magic e sugli altri Magic disponibili con il kernel PySpark, vedere [kernel disponibili nei notebook di Jupyter con Apache Spark cluster HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
7. Una volta che il processo viene completato correttamente, per impostazione predefinita viene visualizzato l'output tabulare seguente.

      :::image type="content" source="./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png " alt-text="Output tabulare dei risultati della query" border="true":::

     È anche possibile visualizzare i risultati in altri formati. Ad esempio, un grafico ad area per lo stesso output apparirebbe come segue.

     :::image type="content" source="./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png " alt-text="Grafico ad area dei risultati della query" border="true":::

8. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Questa operazione consente di arrestare e chiudere il notebook.


## <a name="next-steps"></a>Passaggi successivi

* [Creare un'applicazione Scala autonoma da eseguire nel cluster Apache Spark in HDInsight Linux](apache-spark-create-standalone-application.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per il cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark per il cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)