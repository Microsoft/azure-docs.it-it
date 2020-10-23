---
title: Informazioni sui formati di dati Apache Spark per Azure Data Lake Analytics sviluppatori U-SQL.
description: Questo articolo descrive Apache Spark concetti per aiutare gli sviluppatori U_SQL a comprendere le differenze tra i formati di dati U-SQL e Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221112"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Comprendere le differenze tra i formati di dati U-SQL e Spark

Se si vuole usare [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) o [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), è consigliabile eseguire la migrazione dei dati da [Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-overview.md) a [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Oltre a trasferire i file, sarà anche possibile rendere i dati, archiviati nelle tabelle U-SQL, accessibili a Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Spostare i dati archiviati nei file di Azure Data Lake Storage Gen1

I dati archiviati nei file possono essere spostati in diversi modi:

- Scrivere una pipeline [Azure Data Factory](../data-factory/introduction.md) per copiare i dati dall'account [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) all'account [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md) .
- Scrivere un processo Spark che legge i dati dall'account [Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-overview.md) e li scrive nell'account [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . In base al caso d'uso, è consigliabile scriverlo in un formato diverso, ad esempio parquet, se non è necessario mantenere il formato di file originale.

Si consiglia di leggere l'articolo [aggiornare le soluzioni di analisi di Big data da Azure Data Lake storage Gen1 a Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Spostare i dati archiviati nelle tabelle U-SQL

Le tabelle U-SQL non vengono riconosciute da Spark. Se sono presenti dati archiviati nelle tabelle U-SQL, verrà eseguito un processo U-SQL che estrae i dati della tabella e li salva in un formato comprensibile per Spark. Il formato più appropriato consiste nel creare un set di file parquet dopo il layout della cartella del metastore Hive.

L'output può essere realizzato in U-SQL con l'outputter parquet incorporato e usando il partizionamento di output dinamico con i set di file per creare le cartelle di partizione. [Elaborare più file che mai e usare parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) fornisce un esempio di come creare questi dati utilizzabili per Spark.

Dopo questa trasformazione, i dati vengono copiati come descritto nel capitolo spostare i [dati archiviati nei file di Azure Data Lake storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Precisazioni

- Semantica dei dati durante la copia dei file, la copia viene eseguita a livello di byte. Quindi gli stessi dati devono essere visualizzati nell'account [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . Si noti tuttavia che Spark può interpretare alcuni caratteri in modo diverso. Ad esempio, può usare un valore predefinito diverso per un delimitatore di riga in un file CSV.
    Inoltre, se si copiano dati tipizzati (dalle tabelle), parquet e Spark possono avere precisione e scala diversi per alcuni dei valori tipizzati (ad esempio, un valore float) e possono trattare i valori null in modo diverso. Ad esempio, U-SQL ha la semantica C# per i valori null, mentre Spark ha una logica a tre valori per i valori null.

- Le tabelle U-SQL di organizzazione dei dati (partizionamento) forniscono il partizionamento a due livelli. Il livello esterno ( `PARTITIONED BY` ) è per valore e viene mappato principalmente nello schema di partizionamento hive/Spark usando le gerarchie di cartelle. È necessario assicurarsi che i valori null siano mappati alla cartella corretta. Il livello interno ( `DISTRIBUTED BY` ) in U-SQL offre 4 schemi di distribuzione: Round Robin, intervallo, hash e hash diretto.
    Le tabelle hive/Spark supportano solo il partizionamento dei valori o il partizionamento hash, usando una funzione hash diversa da U-SQL. Quando si generano i dati della tabella U-SQL, probabilmente sarà possibile eseguire il mapping solo al partizionamento del valore per Spark e potrebbe essere necessario eseguire un'ulteriore ottimizzazione del layout dei dati in base alle query Spark finali.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti di codice Spark per gli sviluppatori U-SQL](understand-spark-code-concepts.md)
- [Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET per Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Trasformare dati tramite l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Trasformare i dati usando l'attività Hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Informazioni su Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)