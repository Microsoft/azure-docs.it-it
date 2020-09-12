---
title: Integrare Data Lake Storage Gen1 con altri servizi di Azure
description: Informazioni su come integrare Azure Data Lake Storage Gen1 con altri servizi di Azure.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9318ce7d73f2d05dde6815a0ded4269510760251
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441987"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrazione di Azure Data Lake Storage Gen1 con altri servizi di Azure
Azure Data Lake Storage Gen1 può essere usato in combinazione con altri servizi di Azure per abilitare il supporto di una gamma più ampia di scenari. L'articolo seguente elenca i servizi con cui Data Lake Storage Gen1 può essere integrato.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Usare Data Lake Storage Gen1 con Azure HDInsight
È possibile eseguire il provisioning di un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa Data Lake Storage Gen1 come risorsa di archiviazione conforme a HDFS. Per questa versione, per i cluster Hadoop e Storm in Windows e Linux è possibile usare Data Lake Storage Gen1 solo come risorsa di archiviazione aggiuntiva. Tali cluster continuano a usare l'archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Tuttavia, per i cluster HBase in Windows e Linux è possibile usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita o come risorsa di archiviazione aggiuntiva oppure in entrambe le modalità.

Per istruzioni su come eseguire il provisioning di un cluster HDInsight con Data Lake Storage Gen1, vedere:

* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 usando portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione predefinita usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Effettuare il provisioning di un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva usando Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Usare Data Lake Storage Gen1 con Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) consente di usare i Big Data a livello di cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte e addirittura exabyte di dati che possono essere archiviati nelle numerose origini dati supportate, ad esempio Data Lake Storage Gen1. Data Lake Analytics è ottimizzato in modo specifico per usare Data Lake Storage Gen1 in modo da offrire il più elevato livello di prestazioni, velocità effettiva e parallelizzazione per i carichi di lavoro dei Big Data.

Per istruzioni su come usare Data Lake Analytics con Data Lake Storage Gen1, vedere [Introduzione a Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Usare Data Lake Storage Gen1 con Azure Data Factory
È possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per inserire dati da tabelle di Azure, database SQL di Azure, data warehouse SQL di Azure, BLOB di archiviazione di Azure e database locali. Essendo un elemento di primo piano nell'ecosistema Azure, Azure Data Factory può essere usato per gestire l'inserimento di dati da queste origini a Data Lake Storage Gen1.

Per istruzioni su come usare Azure Data Factory con Data Lake Storage Gen1, vedere [Spostare dati da e verso Data Lake Storage Gen1 usando Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1
Azure Data Lake Storage Gen1 offre uno strumento da riga di comando, AdlCopy, che consente di copiare dati da Archiviazione BLOB di Azure a un account di Data Lake Storage Gen1. Per altre informazioni, vedere [Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiare i dati tra un database SQL di Azure e Data Lake Storage Gen1
È possibile usare Apache Sqoop per importare ed esportare dati tra un database SQL di Azure e Data Lake Storage Gen1. Per altre informazioni, vedere [copiare dati tra data Lake storage Gen1 e il database SQL di Azure con Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Usare Data Lake Storage Gen1 con l'analisi di flusso
È possibile usare Data Lake Storage Gen1 come uno degli output per archiviare dati di flusso usando l'analisi di flusso di Azure. Per altre informazioni, vedere [Trasmettere i dati dal BLOB di archiviazione di Azure a Data Lake Storage Gen1 usando l'analisi di flusso di Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Usare Data Lake Storage Gen1 con Power BI
È possibile usare Power BI per importare dati da un account Data Lake Storage Gen1 per analizzare e visualizzare i dati. Per altre informazioni, vedere [Analisi dei dati in Data Lake Storage Gen1 con Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Usare Data Lake Storage Gen1 con Data Catalog
È possibile registrare dati di Data Lake Storage Gen1 in Azure Data Catalog per rendere i dati individuabili nell'intera organizzazione. Per altre informazioni, vedere [Registrare i dati di Data Lake Storage Gen1 in Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Usare Data Lake Storage Gen1 con SQL Server Integration Services (SSIS)
È possibile usare la gestione connessione di Data Lake Storage Gen1 in SSIS per connettere un pacchetto SSIS a Data Lake Storage Gen1. Per altre informazioni, vedere [Usare Data Lake Storage Gen1 con SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Usare Data Lake Storage Gen1 con Azure sinapsi Analytics
È possibile usare la polibase per caricare i dati da Data Lake Storage Gen1 in Azure sinapsi Analytics (in precedenza SQL Data Warehouse). Per altre informazioni, vedere [usare Data Lake storage Gen1 con analisi delle sinapsi di Azure](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Usare Data Lake Storage Gen1 con Hub eventi di Azure
È possibile usare Azure Data Lake Storage Gen1 per archiviare e acquisire i dati ricevuti da Hub eventi di Azure. Per altre informazioni, vedere [Usare Data Lake Storage Gen1 con Hub eventi di Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Vedi anche
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introduzione a Data Lake Storage Gen1 con il portale](data-lake-store-get-started-portal.md)
* [Introduzione all'uso di Data Lake Storage Gen1 con PowerShell](data-lake-store-get-started-powershell.md)  

