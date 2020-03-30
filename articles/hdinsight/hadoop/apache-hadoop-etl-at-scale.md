---
title: Estrarre, trasformare e caricare (ETL) su larga scala - Azure HDInsight
description: Informazioni su come estrarre, trasformare e caricare viene usato in HDInsight con Apache Hadoop.Learn how extract, transform, and load is used in HDInsight with Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529476"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Estrarre, trasformare e caricare (ETL) su larga scala

Estrazione, trasformazione e caricamento (ETL) è il processo tramite il quale i dati vengono acquisiti da varie origini, raccolti in una posizione standard, puliti ed elaborati e infine caricati in un archivio dati da cui è possibile eseguire query. I processi ETL legacy importano i dati, li puliscono sul posto e quindi li archiviano in un motore dati relazionale. Con HDInsight, un'ampia gamma di componenti dell'ecosistema Apache Hadoop supporta l'esecuzione di ETL su larga scala.

L'uso di HDInsight nel processo ETL può essere riassunto da questa pipeline:

![Panoramica di HDInsight ETL in scalaHDInsight ETL at scale overview](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Le sezioni seguenti illustrano ogni fase del processo ETL e i relativi componenti associati.

## <a name="orchestration"></a>Orchestrazione

L'orchestrazione comprende tutte le fasi della pipeline ETL. I processi ETL in HDInsight interessano spesso prodotti diversi che funzionano in combinazione tra loro.  È possibile usare Hive per pulire una parte dei dati, mentre Pig ne pulisce un'altra parte.  È possibile usare Azure Data Factory per caricare i dati nel database SQL di Azure da Azure Data Lake Store.

L'orchestrazione è necessaria per eseguire il processo appropriato al momento giusto.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Viene eseguito all'interno di un cluster HDInsight ed è integrato con lo stack di Hadoop. Oozie supporta i processi Hadoop per Apache Hadoop MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere usato per pianificare i processi specifici di un sistema, ad esempio i programmi Java o gli script della shell.

Per altre informazioni, vedere [Usare Apache Oozie con Apache Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](../hdinsight-use-oozie-linux-mac.md). Per un approfondimento su come usare Oozie per gestire una pipeline end-to-end, vedere [Rendere operativa una pipeline di analisi dei dati](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Data factory di Azure

Azure Data Factory offre funzionalità di orchestrazione sotto forma di piattaforma distribuita come servizio (PaaS). Si tratta di un servizio di data integration basato su cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento dei dati e la trasformazione dei dati.

Con Azure Data Factory è possibile:

1. Creare e pianificare flussi di lavoro basati sui dati (denominati pipeline) per inserire dati da archivi diversi.
2. Elaborare e trasformare i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch e Azure Machine Learning.
3. Pubblicare i dati di output in archivi dati, ad esempio Azure SQL Data Warehouse, per consentire alle applicazioni di business intelligence (BI) di utilizzarli.

Per altre informazioni su Azure Data Factory, vedere la [documentazione](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Inserire dati nell'archiviazione file e nell'archivio risultati

I file di dati di origine vengono in genere caricati in una posizione di Archiviazione di Azure o di Azure Data Lake Store. I file possono essere in qualsiasi formato, ma in genere sono file flat come CSV.

### <a name="azure-storage"></a>Archiviazione di Azure

[Archiviazione di Azure](https://azure.microsoft.com/services/storage/blobs/) ha obiettivi di scalabilità specifici. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../../storage/blobs/scalability-targets.md). Per la maggior parte dei nodi analitici, le prestazioni di Archiviazione di Azure risultano ottimali quando vengono gestiti molti file di piccole dimensioni.  Archiviazione di Azure garantisce prestazioni identiche, a prescindere dal numero di file o dalla relativa dimensione (a condizione che non siano superati i limiti).  Ciò significa che è possibile archiviare terabyte di dati e ottenere comunque prestazioni coerenti, indipendentemente dal fatto che si utilizzi un sottoinsieme dei dati o tutti i dati.

Archiviazione di Azure ha diversi tipi di BLOB.  Un *BLOB di aggiunta* è un'ottima scelta per archiviare blog o dati sensore.  

Diversi BLOB possono essere distribuiti in più server per aumentare il numero di istanze di accesso, ma ogni BLOB può essere gestito da un solo server. Sebbene i BLOB possano essere raggruppati logicamente in contenitori di BLOB, non vi sono implicazioni sul partizionamento derivanti da questo raggruppamento.

Archiviazione di Azure include anche un livello API WebHDFS per l'archiviazione dei BLOB.  Tutti i servizi di HDInsight possono accedere ai file in Archiviazione BLOB di Azure per la pulizia e l'elaborazione dei dati, in modo analogo a come questi servizi potrebbero usare Hadoop Distributed File System (HDFS).

I dati vengono in genere inseriti in Archiviazione di Azure tramite PowerShell, Azure Storage SDK o AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) è un repository con iperscalabilità gestito per dati di analisi compatibili con HDFS.  ADLS usa un paradigma di progettazione simile ad HDFS e offre scalabilità illimitata in termini di capacità totale e dimensione dei singoli file. ADLS è un'ottima scelta quando si lavora con file di grandi dimensioni, poiché un file di questo tipo può essere archiviato in più nodi.  Il partizionamento dei dati in ADLS viene eseguito in background.  Si ottiene una velocità effettiva molto elevata per l'esecuzione di processi di analisi con migliaia di esecutori simultanei che leggono e scrivono centinaia di terabyte di dati in modo efficiente.

I dati vengono in genere inseriti in ADLS tramite Azure Data Factory, gli SDK di ADLS, il servizio AdlCopy, Apache DistCp o Apache Sqoop.  La scelta del servizio da usare dipende in gran parte dalla posizione dei dati.  Se i dati si trovano attualmente in un cluster Hadoop esistente, si può usare Apache DistCp, il servizio AdlCopy o Azure Data Factory.  Se si trovano in Archiviazione BLOB di Azure, si può usare Azure Data Lake Storage .NET SDK, Azure PowerShell o Azure Data Factory.

ADLS è ottimizzato anche per l'inserimento di eventi tramite l'hub eventi di Azure o Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considerazioni per entrambe le opzioni di archiviazione

Per il caricamento dei set di dati nell'intervallo di terabyte, la latenza di rete può creare seri problemi, in particolare se i dati provengono da una posizione locale.  In questi casi, è possibile usare le opzioni seguenti:

* Azure ExpressRoute: Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Queste connessioni costituiscono un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su ExpressRoute](../../expressroute/expressroute-introduction.md).

* Caricamento "offline" dei dati: è possibile usare il [servizio Importazione/Esportazione di Azure](../../storage/common/storage-import-export-service.md) per inviare le unità disco rigido con i dati a un data center di Azure. I dati vengono caricati prima di tutto in BLOB di archiviazione di Azure. È quindi possibile usare [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) o lo strumento [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) per copiare i dati dai BLOB di Archiviazione di Azure in Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW è la scelta ottimale per archiviare risultati puliti e preparati per analisi future.  Azure HDInsight consente di eseguire i servizi per Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) è un archivio database relazionale ottimizzato per carichi di lavoro analitici.  Azure SQL DW viene ridimensionato in base a tabelle  che possono essere partizionate tra più nodi.  I nodi di Azure SQL DW vengono selezionati nel momento in cui si creano.  Successivamente possono essere ridimensionati, ma si tratta di un processo attivo che può richiedere lo spostamento dei dati. Per ulteriori informazioni, vedere [SQL Data Warehouse - Gestire il calcolo](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase è un archivio chiave-valore disponibile in Azure HDInsight.  Apache HBase è un database NoSQL open source basato su Hadoop e modellato su Google BigTable. Fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati in un database privo di schema organizzato per famiglie di colonne.

I dati sono archiviati nelle righe di una tabella e i dati di ogni riga sono raggruppati in base al tipo di colonna. HBase è un database privo di schema, poiché non è necessario definire le colonne o i tipi di dati archiviati nelle colonne prima dell'uso. Il codice open source offre scalabilità lineare, in modo da gestire petabyte di dati in migliaia di nodi. HBase può contare su ridondanza dei dati, elaborazione batch e altre funzionalità offerte dalle applicazioni distribuite nell'ecosistema Hadoop.

HBase è una destinazione eccellente per le analisi future di dati di log e dati sensore.

La scalabilità di HBase dipende dal numero di nodi del cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Database SQL di Azure e database di Azure

Azure offre tre diversi database relazionali come piattaforma distribuita come servizio (PaaS).

* Il [database SQL di Azure](../../sql-database/sql-database-technical-overview.md) è un'implementazione di Microsoft SQL Server. Per altre informazioni sulle prestazioni, vedere [Ottimizzazione delle prestazioni del database SQL di Azure](../../sql-database/sql-database-performance-guidance.md).
* [Database di Azure per MySQL](../../mysql/overview.md) è un'implementazione di Oracle MySQL.
* [Database di Azure per PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) è un'implementazione di PostgreSQL.

Questi prodotti scalano verso l'alto, il che significa che vengono scalati aggiungendo più CPU e memoria.  Con questi prodotti è anche possibile scegliere di usare dischi premium per migliorare le prestazioni di I/O.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) è un motore di dati analitici usato nel supporto decisionale e nelle analisi business, che offre dati analitici per report aziendali e applicazioni client, ad esempio Power BI, Excel, Reporting Services e altri strumenti di visualizzazione di dati.

I cubi di analisi possono essere ridimensionati modificando i livelli per ogni singolo cubo.  Per altre informazioni, vedere [Prezzi di Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Estrarre e caricare

Quando i dati esistono in Azure, è possibile estrarli e caricarli in altri prodotti usando numerosi servizi.  HDInsight supporta Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop è uno strumento progettato per trasferire dati in modo efficiente tra origini strutturate, semistrutturate e non strutturate.

Sqoop usa MapReduce per l'importazione e l'esportazione dei dati per assicurare operazioni parallele e tolleranza di errore.

### <a name="apache-flume"></a>Apache Flume

Apache Flume è un servizio distribuito, affidabile e disponibile per raccogliere, aggregare e spostare in modo efficiente grandi quantità di dati di log. Ha un'architettura semplice e flessibile basata su flussi di dati. Flume è un servizio solido e a tolleranza di errore dotato di meccanismi di affidabilità ottimizzabili e molti meccanismi di failover e ripristino. Usa un modello di dati estensibile semplice per consentire applicazioni analitiche online.

Apache Flume can't be used with Azure HDInsight.  Un'installazione locale di Hadoop può usare Flume per inviare dati ai BLOB del servizio di archiviazione di Azure o a Azure Data Lake Storage.  Per altre informazioni, vedere [Using Apache Flume with HDInsigh](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/) (Uso di Apache Flume con HDInsight).

## <a name="transform"></a>Trasformare

Quando i dati esistono nella posizione scelta, è necessario pulirli, combinarli o prepararli per un modello di utilizzo specifico.  Hive, Pig e Spark SQL sono soluzioni ottime per questo tipo di attività  Sono tutti supportati su HDInsight.

## <a name="next-steps"></a>Passaggi successivi

* [Usare Apache Hive come strumento per estrazione, trasformazione e caricamento (ETL, Extract, Transform, and Load)](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Spostare i dati dal database SQL di Azure alla tabella Apache HiveMove data from Azure SQL Database to Apache Hive table](./apache-hadoop-use-sqoop-mac-linux.md)
