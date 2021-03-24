---
title: Usare Apache Spark per leggere e scrivere dati nel database SQL di Azure
description: Informazioni su come configurare una connessione tra il cluster HDInsight Spark e il database SQL di Azure. Per leggere dati, scrivere dati e trasmettere dati in un database SQL
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864619"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usare il cluster HDInsight Spark per leggere e scrivere dati nel database SQL di Azure

Informazioni su come connettere un cluster Apache Spark in Azure HDInsight con il database SQL di Azure. Quindi leggere, scrivere e trasmettere i dati nel database SQL. Le istruzioni in questo articolo usano un Jupyter Notebook per eseguire i frammenti di codice scala. Tuttavia, è possibile creare un'applicazione autonoma in scala o Python ed eseguire le stesse attività.

## <a name="prerequisites"></a>Prerequisiti

* Cluster Azure HDInsight Spark.  Seguire le istruzioni riportate in [Creare un cluster Apache Spark in HDInsight](apache-spark-jupyter-spark-sql.md).

* Database SQL di Azure. Seguire le istruzioni in [creare un database nel database SQL di Azure](../../azure-sql/database/single-database-create-quickstart.md). Assicurarsi di creare un database con lo schema e i dati dell'esempio **AdventureWorksLT**. Assicurarsi inoltre di creare una regola del firewall a livello di server per consentire all'indirizzo IP del client di accedere al database SQL. Le istruzioni per aggiungere la regola del firewall sono disponibili nello stesso articolo. Dopo aver creato il database SQL, assicurarsi di avere a portata di mano i valori seguenti. che saranno necessari per connettersi al database da un cluster Spark.

    * Nome del server.
    * nome del database.
    * Nome utente/password amministratore del database SQL di Azure.

* SQL Server Management Studio (SSMS). Seguire le istruzioni riportate in [Usare SQL Server Management Studio per connettersi ed eseguire query sui dati](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Creare un notebook di Jupyter Notebook

Iniziare creando una Jupyter Notebook associata al cluster Spark. Usare quindi il notebook per eseguire i frammenti di codice illustrati in questo articolo.

1. Dal [Portale di Azure](https://portal.azure.com/), aprire il cluster.
1. Selezionare **Jupyter notebook** sotto i **Dashboard del cluster** sul lato destro.  Se non vengono visualizzati i **Dashboard del cluster**, scegliere **Panoramica** dal menu a sinistra. Se richiesto, immettere le credenziali per il cluster.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Jupyter Notebook Apache Spark" border="true":::

   > [!NOTE]  
   > È anche possibile accedere al Jupyter Notebook nel cluster Spark aprendo l'URL seguente nel browser. Sostituire **clustername** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Nell'angolo in alto a destra della Jupyter Notebook fare clic su **nuovo** e quindi su **Spark** per creare un notebook di scala. I notebook di Jupyter nel cluster HDInsight Spark forniscono anche il kernel **PySpark** per le applicazioni python2 e il kernel **PySpark3** per le applicazioni Python3. In questo articolo si creerà un notebook di Scala.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Kernel per Jupyter Notebook in Spark" border="true":::

    Per altre informazioni sui kernel, vedere [usare Jupyter notebook kernel con cluster di Apache Spark in HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > In questo articolo viene usato un kernel Spark (scala) perché i dati di streaming da Spark nel database SQL sono attualmente supportati solo in scala e Java. Anche se le operazioni di lettura e scrittura in SQL possono essere eseguite con Python, per coerenza a livello di articolo si userà Scala per tutte e tre le operazioni.

1. Verrà aperto un nuovo notebook con un nome predefinito, senza **titolo**. Fare clic sul nome del notebook e immettere un nome a scelta.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="Specificare un nome per il notebook" border="true":::

È ora possibile iniziare a creare l'applicazione.

## <a name="read-data-from-azure-sql-database"></a>Leggere dati dal database SQL di Azure

In questa sezione si leggeranno i dati di una tabella (ad esempio, **SalesLT.Address**) presente nel database AdventureWorks.

1. In una nuova Jupyter Notebook, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori del database.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. Usare il frammento di codice riportato di seguito per creare un URL JDBC che è possibile passare alle API di Spark dataframe. Il codice crea un `Properties` oggetto per conservare i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Usare il frammento di codice seguente per creare un frame di dati con i dati di una tabella nel database. In questo frammento viene utilizzata una `SalesLT.Address` tabella disponibile come parte del database **AdventureWorksLT** . Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. È ora possibile eseguire operazioni sul dataframe, ad esempio ottenere lo schema di dati:

    ```scala
    sqlTableDF.printSchema
    ```

    Viene visualizzato un output simile all'immagine seguente:

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="output dello schema" border="true":::

1. È anche possibile eseguire operazioni come, recuperare le prime 10 righe.

    ```scala
    sqlTableDF.show(10)
    ```

1. o recuperare colonne specifiche dal set di dati.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Scrivere dati nel database SQL di Azure

In questa sezione viene usato un file CSV di esempio disponibile nel cluster per creare una tabella nel database e popolarla con i dati. Il file CSV di esempio (**HVAC.csv**) è disponibile in tutti i cluster HDInsight al percorso `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. In una nuova Jupyter Notebook, in una cella di codice, incollare il frammento di codice seguente e sostituire i valori segnaposto con i valori del database.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Premere **MAIUSC+INVIO** per eseguire la cella di codice.  

1. Il frammento di codice seguente compila un URL JDBC che è possibile passare alle API del frame di frame Spark. Il codice crea un `Properties` oggetto per conservare i parametri. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Usare il frammento di codice seguente per estrarre lo schema dei dati in HVAC.csv e usarlo per caricare i dati dal file CSV in un dataframe, `readDf`. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Utilizzare il dataframe `readDf` per creare una tabella temporanea, `temphvactable`. Usare quindi la tabella temporanea per creare una tabella hive, `hvactable_hive`.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Usare infine la tabella hive per creare una tabella nel database. Il frammento di codice seguente crea un `hvactable` database SQL di Azure.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Connettersi al database SQL di Azure con SSMS e verificare che sia `dbo.hvactable` presente.

    a. Avviare SSMS e connettersi al database SQL di Azure fornendo i dettagli della connessione, come illustrato nella schermata seguente.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="Connettersi al database SQL tramite SSMS1" border="true":::

    b. Da **Esplora oggetti**, espandere il database e il nodo della tabella per visualizzare il **dbo. oggetto hvactable** creato.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="Connettersi al database SQL tramite SSMS2" border="true":::

1. Eseguire una query in SSMS per visualizzare le colonne nella tabella.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Trasmettere i dati nel database SQL di Azure

In questa sezione viene eseguito il flusso dei dati nell'oggetto `hvactable` creato nella sezione precedente.

1. Come primo passaggio, assicurarsi che non siano presenti record in `hvactable` . Eseguire la query seguente nella tabella tramite SQL Server Management Studio.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Creare una nuova Jupyter Notebook nel cluster HDInsight Spark. Incollare il frammento di codice seguente in una cella di codice e quindi premere **MAIUSC+INVIO**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. I dati vengono trasmessi dal **HVAC.csv** al `hvactable` . Il file di HVAC.csv è disponibile nel cluster all'indirizzo `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . Nel frammento di codice seguente, prima si ottiene lo schema dei dati da trasmettere e quindi si usa lo schema per creare un dataframe di streaming. Incollare il frammento di codice in una cella di codice e premere **MAIUSC+INVIO** per eseguirlo.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. L'output mostra lo schema di **HVAC.csv**, `hvactable`Ha anche lo stesso schema. Nell'output vengono elencate le colonne presenti nella tabella.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="' HDInsight Apache Spark schema table '" border="true":::

1. Infine, usare il frammento di codice seguente per leggere i dati dal HVAC.csv e trasmetterli nel `hvactable` nel database. Incollare il frammento di codice in una cella di codice, sostituire i valori segnaposto con i valori per il database e quindi premere **MAIUSC + INVIO** per eseguire.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Verificare che i dati vengano trasmessi nel `hvactable` eseguendo la query seguente in SQL Server Management Studio (SSMS). Ogni volta che si esegue la query, il numero di righe nella tabella aumenta.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il cluster HDInsight Spark per analizzare i dati in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](apache-spark-load-data-run-query.md)
* [usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
