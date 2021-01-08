---
title: Copiare dati tra Data Lake Storage Gen1 e Azure SQL-Sqoop | Microsoft Docs
description: Usare Sqoop per copiare i dati tra il database SQL di Azure e Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "92109188"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiare dati tra Data Lake Storage Gen1 e il database SQL di Azure con Sqoop

Informazioni su come usare Apache Sqoop per importare ed esportare dati tra un database SQL di Azure e Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Informazioni su Sqoop

Le applicazioni Big Data sono una scelta naturale per l'elaborazione di dati non strutturati e semi-strutturati, ad esempio log e file. Tuttavia, potrebbe essere necessario elaborare dati strutturati archiviati in database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) è uno strumento progettato per trasferire dati tra database relazionali e un repository Big Data, ad esempio Data Lake Storage Gen1. È possibile usarlo per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio un database SQL di Azure, in Data Lake Storage Gen1. È quindi possibile trasformare e analizzare i dati usando Big Data carichi di lavoro e quindi esportare di nuovo i dati in un RDBMS. In questo articolo si usa un database nel database SQL di Azure come database relazionale per importare/esportare da.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come creare l'account, vedere [Introduzione a Azure Data Lake storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Questo articolo presuppone un cluster HDInsight Linux con accesso a Data Lake Storage Gen1.
* **Database SQL di Azure**. Per istruzioni su come creare un database nel database SQL di Azure, vedere [creare un database nel database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Creare tabelle di esempio nel database

1. Per iniziare, creare due tabelle di esempio nel database. Utilizzare [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) o Visual Studio per connettersi al database e quindi eseguire le query seguenti.

    **Create Table1**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Create Table2**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. Eseguire il comando seguente per aggiungere alcuni dati di esempio a **Tabella1**. Lasciare vuota **Table2** . Successivamente, verranno importati dati da **Tabella1** in data Lake storage Gen1. Si esporteranno quindi i dati da Data Lake Storage Gen1 a **Table2**.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Usare Sqoop da un cluster HDInsight con accesso a Data Lake Storage Gen1

In un cluster HDInsight sono già disponibili i pacchetti di Sqoop. Se il cluster HDInsight è stato configurato per l'uso di Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, è possibile usare Sqoop (senza apportare modifiche alla configurazione) per importare/esportare dati tra un database relazionale come il database SQL di Azure e un account di Data Lake Storage Gen1.

1. Per questo articolo si presuppone che sia stato creato un cluster Linux, quindi è consigliabile usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage Gen1 dal cluster. Eseguire il comando seguente dal prompt SSH:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Questo comando fornisce un elenco di file/cartelle nell'account Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importare dati da un database SQL di Azure a Data Lake Storage Gen1

1. Passare alla directory in cui sono disponibili i pacchetti di Sqoop. Questo percorso è in genere `/usr/hdp/<version>/sqoop/bin` .

1. Importare i dati da **Table1** all'account di Data Lake Storage Gen1. Usare la sintassi seguente:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   Il segnaposto **SQL-database-server-name** rappresenta il nome del server in cui è in esecuzione il database. **sql-database-name** rappresenta il nome effettivo del database.

   Ad esempio,

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Verificare che i dati siano stati trasferiti all'account Data Lake Storage Gen1. Eseguire il comando seguente:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   Viene visualizzato l'output seguente.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Ogni file **part-m-** _ corrisponde a una riga nella tabella di origine _ *Tabella1 * *. È possibile visualizzare il contenuto del file part-m-* files da verificare.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Esportare dati da Data Lake Storage Gen1 a un database SQL di Azure

1. Esportare i dati dall'account di Data Lake Storage Gen1 alla tabella vuota, **Table2**, nel database SQL di Azure. Usare la sintassi seguente.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Ad esempio,

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Verificare che i dati siano stati caricati nella tabella del database SQL. Usare [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) o Visual Studio per connettersi al database SQL di Azure e quindi eseguire la query seguente.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Questo comando deve avere il seguente output.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Considerazioni sulle prestazioni per l'uso di Sqoop

Per informazioni sulle prestazioni che consentono di ottimizzare il processo del Sqoop per copiare i dati in Data Lake Storage Gen1, vedere il [post di Blog sulle prestazioni di Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

## <a name="next-steps"></a>Passaggi successivi

* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)