---
title: Scenari di dati con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Conoscere i diversi scenari e gli strumenti disponibili per inserire, elaborare, scaricare e visualizzare i dati in Data Lake Storage Gen2, noto in precedenza come Azure Data Lake Store
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4ed2458b09d200542ce8789d90250027a07a970d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735472"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data

L'elaborazione di Big Data prevede quattro fasi principali:

> [!div class="checklist"]
> * Inserimento di grandi quantità di dati in un archivio dati, in tempo reale o in batch
> * Elaborazione dei dati
> * Download dei dati
> * Visualizzazione dei dati

In questo articolo vengono evidenziate le opzioni e gli strumenti per ogni fase di elaborazione.

Per un elenco completo dei servizi di Azure che è possibile usare con Azure Data Lake Storage Gen2, vedere [integrare Azure Data Lake storage con i servizi di Azure](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Inserire i dati in Data Lake Storage Gen2

Questa sezione descrive le diverse origini dei dati e i diversi modi in cui i dati possono essere inseriti in un account Data Lake Storage Gen2.

![Inserire i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Inserire i dati in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dati ad hoc

Si tratta di set di dati di piccole dimensioni usati per la creazione del prototipo di un'applicazione Big Data. L'inserimento di dati ad hoc può essere eseguito in modi diversi, a seconda dell'origine dei dati. 

Ecco un elenco di strumenti che è possibile usare per inserire dati ad hoc.

| origine dati | Inserire usando |
| --- | --- |
| Computer locale |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Interfaccia della riga di comando di Azure](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)|
| BLOB di Archiviazione di Azure |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp in esecuzione nel cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dati di streaming

Rappresenta i dati che possono essere generati da varie origini, ad esempio applicazioni, dispositivi, sensori e così via. Questi dati possono essere inseriti in Data Lake Storage Gen2 da un'ampia gamma di strumenti. Questi strumenti in genere acquisiscono ed elaborano i dati dei singoli eventi in tempo reale e quindi scrivono gli eventi in batch in Data Lake Storage Gen2 perché possano essere elaborati ulteriormente.

Ecco un elenco di strumenti che è possibile usare per inserire dati trasmessi.

|Strumento | Indicazioni |
|---|--|
|Analisi di flusso di Azure|[Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Uscita per Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Scrivere in Apache Hadoop HDFS da Apache Storm in HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Dati relazionali

È inoltre possibile recuperare i dati dai database relazionali. I database relazionali raccolgono nel tempo elevate quantità di dati che possono fornire informazioni significative se elaborate tramite una pipeline Big Data. Per spostare tali dati in Data Lake Storage Gen2, è possibile usare gli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire dati relazionali.

|Strumento | Indicazioni |
|---|--|
|Azure Data Factory | [Attività Copy in Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati di log del server Web (caricamento tramite applicazioni personalizzate)

Questo tipo di set di dati è indicato in modo specifico perché l'analisi dei dati di log dei server Web è un caso d'uso comune per le applicazioni di Big Data e richiede il caricamento di grandi volumi di file di log in Data Lake Storage Gen2. Per scrivere script o applicazioni per il caricamento dei dati è possibile usare uno degli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire dati log del server Web.

|Strumento | Indicazioni |
|---|--|
|Azure Data Factory | [Attività Copy in Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Interfaccia della riga di comando di Azure|[Interfaccia della riga di comando di Azure](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Per il caricamento di dati di log del server Web e per il caricamento di altri tipi di dati, ad esempio i dati delle valutazioni dei social network, è consigliabile scrivere script o applicazioni personalizzate che offrono la flessibilità di includere il componente di caricamento dei dati come parte dell'applicazione Big Data più grande. In alcuni casi, questo codice può essere rappresentato da uno script o un'utilità della riga di comando. In altri casi, il codice può essere usato per integrare l'elaborazione di Big Data in un'applicazione o una soluzione aziendale.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati ai cluster Azure HDInsight

La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, Storm) supporta Data Lake Storage Gen2 come repository di archiviazione dei dati. I cluster HDInsight accedono ai dati dai BLOB di archiviazione di Azure (WASB). Per prestazioni ottimali, è possibile copiare i dati da WASB a un account Data Lake Storage Gen2 associato al cluster. Per copiare i dati è possibile usare gli strumenti seguenti.

Ecco un elenco di strumenti che è possibile usare per inserire i dati associati ai cluster HDInsight.

|Strumento | Indicazioni |
|---|--|
|Apache DistCp | [Usare DistCp per copiare dati tra i BLOB del servizio di Archiviazione di Azure e Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Strumento AzCopy | [Trasferire dati con AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Copiare dati da e verso Azure Data Lake Storage Gen2 tramite Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dati archiviati in locale o in cluster IaaS Hadoop

Grandi quantità di dati possono essere archiviati in cluster Hadoop esistenti, localmente, nei computer che usano HDFS. I cluster Hadoop potrebbero essere in una distribuzione locale o all'interno di un cluster IaaS in Azure. Potrebbe essere necessario copiare tali dati in Azure Data Lake Storage Gen2 una sola volta o regolarmente. Sono disponibili diverse opzioni che è possibile usare per ottenere questo. Di seguito è riportato un elenco di alternative con i relativi compromessi.

| Approccio | Dettagli | Vantaggi | Considerazioni |
| --- | --- | --- | --- |
| Usare Azure Data Factory (ADF) per copiare i dati direttamente dai cluster Hadoop in Azure Data Lake Storage Gen2 |[ADF supporta HDFS come origine dati](../../data-factory/connector-hdfs.md) |ADF fornisce il supporto nativo per HDFS e il monitoraggio e gestione end-to-end di prima classe |Richiede che il gateway di gestione dati sia distribuito localmente nel cluster IaaS |
| Usare Distcp per copiare dati da Hadoop ad Archiviazione di Azure. Copiare quindi i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando un meccanismo appropriato. |È possibile copiare i dati da Archiviazione di Azure a Data Lake Storage Gen2 usando: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Strumento AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp Apache in esecuzione nei cluster HDInsight](data-lake-storage-use-distcp.md)</li></ul> |È possibile usare strumenti open source. |Processo in più passaggi che prevede più tecnologie |

### <a name="really-large-datasets"></a>Set di dati di grandi dimensioni

L'uso dei metodi descritti in precedenza per il caricamento di set di dati di più terabyte può talvolta risultare lento e costoso. In questi casi, è possibile utilizzare Azure ExpressRoute.  

Azure ExpressRoute consente di creare connessioni private tra i data center di Azure e l'infrastruttura locale. Ciò offre un'opzione affidabile per il trasferimento di grandi quantità di dati. Per altre informazioni, vedere la [Documentazione su Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Elaborare i dati

Quando i dati sono disponibili in Data Lake Storage Gen2, è possibile eseguire l'analisi sui dati tramite le applicazioni di Big Data supportate. 

![Analizzare i dati in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizzare i dati in Data Lake Storage Gen2")

Ecco un elenco di strumenti che è possibile usare per eseguire processi di analisi dei dati sui dati archiviati in Data Lake Storage Gen2.

|Strumento | Indicazioni |
|---|--|
|Azure HDInsight | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[Guida introduttiva: analizzare i dati in Azure Data Lake Storage Gen2 tramite Azure Databricks](./data-lake-storage-use-databricks-spark.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Esercitazione: Estrarre, trasformare e caricare dati tramite Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualizzare i dati

Usare il connettore Power BI per creare rappresentazioni visive dei dati archiviati nel Data Lake Storage Gen2. Vedere [analizzare i dati in Azure Data Lake storage Gen2 usando Power bi](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Scaricare i dati

È anche possibile che si voglia scaricare o spostare i dati da Azure Data Lake Storage Gen2 per scenari simili ai seguenti:

* Spostare i dati in altri repository per l'interfaccia con le pipeline di elaborazione dati esistenti. Ad esempio, potrebbe essere necessario spostare i dati da Data Lake Storage Gen2 al database SQL di Azure o a un'istanza di SQL Server.

* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Dati in uscita da Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dati in uscita da Data Lake Storage Gen2")

Ecco un elenco di strumenti che è possibile usare per scaricare dati da Data Lake Storage Gen2.

|Strumento | Indicazioni |
|---|--|
|Azure Data Factory | [Attività Copy in Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Usare DistCp per copiare dati tra i BLOB del servizio di Archiviazione di Azure e Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Azure Storage Explorer|[Usare Azure Storage Explorer per gestire directory, file ed elenchi di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Strumento AzCopy|[Trasferire i dati con AzCopy e l'archiviazione BLOB](../common/storage-use-azcopy-v10.md#transfer-data)|