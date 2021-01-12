---
title: Progettare una strategia di caricamento dei dati di base per il pool SQL dedicato
description: Invece di ETL, progettare un processo di estrazione, caricamento e trasformazione (ELT) per il caricamento di dati con SQL dedicato.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 58e14ab04084871dfd5de400cac0c38401855d0c
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120258"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Progettare una strategia di caricamento dei dati di base per il pool SQL dedicato in Azure sinapsi Analytics

I data warehouse SMP tradizionali usano un processo di estrazione, trasformazione e caricamento (ETL) per il caricamento dei dati. Il pool SQL di Azure è un'architettura MPP (Massive Parallel Processing) che sfrutta la scalabilità e la flessibilità delle risorse di calcolo e di archiviazione. L'uso di un processo di estrazione, caricamento e trasformazione (ELT) può sfruttare le funzionalità di elaborazione delle query distribuite predefinite ed eliminare le risorse necessarie per trasformare i dati prima del caricamento.

Sebbene il pool SQL supporti molti metodi di caricamento, tra cui opzioni non di base quali BCP e l'API BulkCopy SQL, il modo più rapido e scalabile per caricare la data è tramite la polibase.  una tecnologia che accede ai dati archiviati esterni in Archiviazione BLOB di Azure o Azure Data Lake Store tramite il linguaggio T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Estrazione, caricamento e trasformazione (ELT)

Estrazione, caricamento e trasformazione (ELT) è un processo mediante il quale i dati vengono estratti da un sistema di origine, caricati in un data warehouse e quindi trasformati.

I passaggi di base per l'implementazione di un ELT di base per il pool SQL dedicato sono:

1. Estrarre i dati di origine in file di testo.
2. Trasferire i dati nell'archivio BLOB di Azure o in Azure Data Lake Store.
3. Preparare i dati per il caricamento.
4. Caricare i dati in tabelle di staging del pool SQL dedicato usando la polibase.
5. Trasformare i dati.
6. Inserire i dati in tabelle di produzione.

Per un'esercitazione sul caricamento, vedere [usare la polibase per caricare dati dall'archivio BLOB di Azure ad Azure sinapsi Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Per altre informazioni, vedere il [blog sui modelli di caricamento](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Estrarre i dati di origine in file di testo

La modalità di recupero dei dati dal sistema di origine dipende dalla posizione di archiviazione.  L'obiettivo è spostare i dati in file di testo delimitati supportati da PolyBase.

### <a name="polybase-external-file-formats"></a>Formati di file esterni PolyBase

PolyBase carica i dati da file di testo delimitati con codifica UTF-8 e UTF-16. Oltre ai file di testo delimitati, supporta il caricamento da formati di file Hadoop, ovvero RC, ORC e Parquet. PolyBase può anche caricare dati da file compressi Gzip e Snappy. PolyBase non supporta attualmente i formati ASCII esteso, a larghezza fissa e annidati, come WinZip, JSON e XML.

Se si esegue l'esportazione da SQL Server, è possibile usare lo [strumento da riga di comando bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per esportare i dati in file di testo delimitati. Il mapping del tipo di dati parquet ad Azure sinapsi Analytics è il seguente:

| **Tipo di dati parquet** |                      **Tipo di dati SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        TINYINT        |                           TINYINT                            |
|       SMALLINT        |                           SMALLINT                           |
|          INT          |                             INT                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          SMALLMONEY                          |
|        string         |                            NCHAR                             |
|        string         |                           NVARCHAR                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        BINARY         |                            BINARY                            |
|        BINARY         |                          varbinary                           |
|       timestamp       |                             Data                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           Datetime                           |
|       timestamp       |                             time                             |
|       Data            |                             Data                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Trasferire i dati in Archiviazione BLOB di Azure o in Azure Data Lake Store

Per trasferire i dati in Archiviazione di Azure, è possibile spostarli nell'[archivio BLOB di Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) o in [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). In entrambe le posizioni, i dati devono essere archiviati in file di testo. PolyBase può eseguire il caricamento da entrambe le posizioni.

Strumenti e servizi che è possibile usare per spostare i dati in Archiviazione di Azure:

- Il servizio [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) migliora la velocità effettiva della rete, le prestazioni e la prevedibilità. ExpressRoute è un servizio che instrada i dati tramite una connessione privata dedicata ad Azure. Le connessioni ExpressRoute non instradano i dati attraverso la rete Internet pubblica. Queste connessioni offrono maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle tipiche connessioni tramite la rete Internet pubblica.
- L'[utilità AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) sposta i dati in Archiviazione di Azure tramite la rete Internet pubblica. Si tratta di un'opzione appropriata se le dimensioni dei dati sono inferiori a 10 TB. Per eseguire regolarmente caricamenti con AZCopy, assicurasi che la velocità di rete sia accettabile.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) include un gateway che è possibile installare nel server locale. È quindi possibile creare una pipeline per spostare i dati dal server locale ad Archiviazione di Azure. Per usare Data Factory con un pool SQL dedicato, vedere [caricare i dati in un pool SQL dedicato](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparare i dati per il caricamento

Potrebbe essere necessario preparare e pulire i dati nell'account di archiviazione prima di caricarli in un pool SQL dedicato. La preparazione dei dati può essere eseguita nella posizione di origine dei dati, mentre si esportano i dati in file di testo o quando i dati raggiungono Archiviazione di Azure.  È più facile lavorare con i dati il prima possibile nel processo.  

### <a name="define-external-tables"></a>Definire tabelle esterne

Prima di caricare i dati, è necessario definire le tabelle esterne nel data warehouse. PolyBase usa le tabelle esterne per definire i dati e accedervi in Archiviazione di Azure. Una tabella esterna è simile a una vista di database. La tabella esterna contiene lo schema di tabella e punta a dati archiviati all'esterno del data warehouse.

La definizione di tabelle esterne include la specifica dell'origine dati, del formato dei file di testo e delle definizioni delle tabelle. Di seguito sono riportati gli argomenti della sintassi T-SQL necessari:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="format-text-files"></a>Formattare i file di testo

Dopo aver definito gli oggetti esterni, è necessario allineare le righe dei file di testo alla definizione della tabella esterna e del formato del file. I dati in ogni riga del file di testo devono essere allineati alla definizione della tabella.
Per formattare i file di testo:

- Se i dati provengono da un'origine non relazionale, è necessario trasformarli in righe e colonne. Sia che i dati provengano da un'origine relazionale o non relazionale, devono essere trasformati per allinearli alle definizioni di colonna per la tabella in cui si prevede di caricare i dati.
- Formattare i dati nel file di testo per allinearli con le colonne e i tipi di dati nella tabella di destinazione del pool SQL. In caso di non allineamento dei tipi di dati nei file di testo esterni e nella tabella del data warehouse, le righe verranno rifiutate durante il caricamento.
- Separare i campi nel file di testo con un carattere di terminazione.  Assicurarsi di usare un carattere o una sequenza di caratteri non inclusi nei dati di origine. Usare il carattere di terminazione specificato con [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. caricare i dati in tabelle di staging del pool SQL dedicato usando la polibase

È consigliabile caricare i dati in una tabella di staging. Le tabelle di staging consentono di gestire gli errori senza interferire con le tabelle di produzione. Una tabella di staging offre inoltre la possibilità di usare le funzionalità di elaborazione delle query distribuite predefinite del pool SQL per le trasformazioni dei dati prima di inserire i dati nelle tabelle di produzione.

### <a name="options-for-loading-with-polybase"></a>Opzioni per il caricamento con PolyBase

Per caricare i dati con PolyBase, è possibile usare una di queste opzioni di caricamento:

- [PolyBase con T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json): ideale quando i dati sono nell'archivio BLOB di Azure o in Azure Data Lake Store. Questa opzione offre il massimo controllo sul processo di caricamento, ma richiede anche di definire oggetti dati esterni. Gli altri metodi definiscono questi oggetti dietro le quinte, man mano che si esegue il mapping di tabelle di origine e tabelle di destinazione.  Per orchestrare i caricamenti con T-SQL, è possibile usare Azure Data Factory, SSIS o funzioni di Azure.
- La funzione [polibase con SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funziona bene quando i dati di origine sono in SQL Server. SSIS definisce i mapping delle tabelle di origine e di destinazione, oltre a orchestrare il caricamento. Se sono già disponibili pacchetti SSIS, è possibile modificarli per utilizzare la nuova destinazione di data warehouse.
- [PolyBase con Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) è un altro strumento di orchestrazione,  che definisce una pipeline e pianifica i processi.
- La [polibase con Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) trasferisce i dati da una tabella di analisi di sinapsi di Azure a un frame di dati databricks e/o scrive i dati da un dataframe di databricks a una tabella di analisi di sinapsi di Azure usando la polibase.

### <a name="non-polybase-loading-options"></a>Opzioni di caricamento non PolyBase

Se i dati non sono compatibili con PolyBase, è possibile usare [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) o l'[API SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). BCP viene caricato direttamente nel pool SQL dedicato senza passare attraverso l'archiviazione BLOB di Azure ed è destinato solo a piccoli carichi. Si noti che le prestazioni di caricamento di queste opzioni sono notevolmente inferiori rispetto a PolyBase.

## <a name="5-transform-the-data"></a>5. Trasformare i dati

Mentre i dati sono nella tabella di staging, eseguire le trasformazioni richieste dal carico di lavoro, quindi spostare i dati in una tabella di produzione.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserire i dati in tabelle di produzione

L'istruzione INSERT INTO... SELECT sposta i dati dalla tabella di staging alla tabella permanente.

Quando si progetta un processo ETL, provare a eseguire il processo su un campione di test di piccole dimensioni. Provare a estrarre 1000 righe dalla tabella in un file, spostarlo in Azure e quindi provare a caricarlo in una tabella di staging.

## <a name="partner-loading-solutions"></a>Soluzioni di caricamento dei partner

Molti partner Microsoft dispongono di soluzioni di caricamento. Per altre informazioni, vedere l'elenco dei [partner che offrono soluzioni](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Per linee guida relative al caricamento, vedere [Linee guida per il caricamento di dati](data-loading-best-practices.md).