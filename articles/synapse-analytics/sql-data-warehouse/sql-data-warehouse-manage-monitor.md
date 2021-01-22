---
title: Monitorare il carico di lavoro del pool SQL dedicato usando DMV
description: Informazioni su come monitorare il carico di lavoro del pool SQL dedicato di Azure sinapsi Analytics e l'esecuzione di query con DMV.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685209"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>Monitorare il carico di lavoro del pool SQL dedicato di Azure sinapsi Analytics con DMV

Questo articolo descrive come usare le viste a gestione dinamica (DMV) per monitorare il carico di lavoro, inclusa l'analisi dell'esecuzione di query nel pool SQL.

## <a name="permissions"></a>Autorizzazioni

Per eseguire una query su DMV in questo articolo, è necessario disporre dell'autorizzazione **View database state** o **Control** . In genere, la concessione di **View database state** è l'autorizzazione preferita perché è molto più restrittiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorare le connessioni

Tutti gli account di accesso per il data warehouse vengono registrati [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Questa DMV contiene gli ultimi 10.000 accessi.  L'elemento session_id è la chiave primaria e viene assegnato in sequenza per ogni nuovo accesso.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorare l'esecuzione di query

Tutte le query eseguite nel pool SQL vengono registrate in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Questa DMV contiene le ultime 10.000 query eseguite.  L'elemento request_id identifica in modo univoco ogni query ed è la chiave primaria per questa DMV.  L'elemento request_id viene assegnato in sequenza per ogni nuova query ed è preceduto da un prefisso con QID, che indica l'ID query.  Se si esegue una query nella DMV per un dato session_id, vengono visualizzate tutte le query per un determinato accesso.

> [!NOTE]
> Le stored procedure usano più ID richiesta.  Gli ID richiesta vengono assegnati in ordine sequenziale.

Ecco i passaggi da seguire per analizzare i piani e i tempi di esecuzione delle query per una query specifica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSAGGIO 1: individuare la query da analizzare

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

**Prendere nota dell'ID richiesta** della query che si desidera analizzare dai risultati della query precedente.

Le query nello stato **suspended** possono essere accodate a causa di un elevato numero di query in esecuzione attive. Queste query vengono visualizzate anche nella query [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) attese con un tipo di UserConcurrencyResourceType. Per informazioni sui limiti di concorrenza, vedere la pagina relativa ai [limiti di memoria e concorrenza](memory-concurrency-limits.md) o [alle classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md). L'attesa delle query può dipendere anche da altre motivazioni, come i blocchi degli oggetti.  Se la query è in attesa di una risorsa, vedere [Analisi delle query in attesa di risorse](#monitor-waiting-queries) più avanti in questo articolo.

Per semplificare la ricerca di una query nella tabella [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , usare [Label](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per assegnare un commento alla query, che può essere cercato nella visualizzazione sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>PASSAGGIO 2: Esaminare il piano di query

Usare l'ID richiesta per recuperare il piano SQL distribuito (DSQL) della query da [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando un piano di DSQL impiega più tempo del previsto, la causa può essere un la complessità del piano, dovuta a molti passaggi di DSQL o a un solo passaggio che richiede molto tempo.  Se il piano prevede molti passaggi con numerose operazioni di spostamento, prendere in considerazione di ottimizzare le distribuzioni di tabelle per ridurre lo spostamento dei dati. L'articolo [distribuzione di tabelle](sql-data-warehouse-tables-distribute.md) spiega perché è necessario spostare i dati per risolvere una query. Questo articolo illustra anche alcune strategie di distribuzione per ridurre al minimo lo spostamento dei dati.

Per altre informazioni su un singolo passaggio, fare riferimento alla colonna *operation_type* del passaggio della query con esecuzione prolungata e all'**indice dei passaggi**:

* Procedere con il passaggio 3 per **le operazioni SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Procedere con il passaggio 4 per **le operazioni di spostamento dei dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>PASSAGGIO 3: analizzare SQL nei database distribuiti

Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni da [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), che contiene informazioni sull'esecuzione del passaggio della query in tutti i database distribuiti.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando è in esecuzione il passaggio della query, è possibile usare [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per recuperare il piano stimato di SQL Server dalla cache dei piani di SQL Server per il passaggio di esecuzione in una distribuzione specifica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>PASSAGGIO 4: esaminare lo spostamento dei dati nei database distribuiti

Usare l'ID richiesta e l'indice dei passaggi per recuperare informazioni sul passaggio di spostamento dei dati in esecuzione in ogni distribuzione da [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Controllare la colonna *total_elapsed_time* per verificare se una distribuzione particolare richiede più tempo per lo spostamento dei dati rispetto alle altre.
* Per la distribuzione con esecuzione prolungata, esaminare la colonna *rows_processed* e controllare se il numero di righe spostato da tale distribuzione è significativamente più grande rispetto alle altre. In caso affermativo, questo potrebbe indicare asimmetria dei dati sottostanti. Una delle cause per l'asimmetria dei dati è la distribuzione in una colonna con molti valori NULL, le cui righe verranno inserite nella stessa distribuzione. Evitare le query lente evitando la distribuzione su questi tipi di colonne o filtrando la query per eliminare i valori NULL, quando possibile. 

Se la query è in esecuzione, è possibile utilizzare [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per recuperare il piano stimato SQL Server dalla cache dei piani di SQL Server per il passaggio SQL in esecuzione all'interno di una particolare distribuzione.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorare le query in attesa

Se si rileva il mancato avanzamento di una query perché in attesa di una risorsa, ecco una query che mostra tutte le risorse attese da una query.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se la query è attivamente in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**.  Se la query dispone di tutte le risorse necessarie, lo stato sarà **Granted**.

## <a name="monitor-tempdb"></a>Monitorare tempdb

Tempdb viene utilizzato per mantenere i risultati intermedi durante l'esecuzione della query. Un utilizzo elevato del database tempdb può causare un rallentamento delle prestazioni delle query. Per ogni DW100c configurato, 399 GB di spazio tempdb allocato (compreso dw1000c avrebbe 3,99 TB di spazio totale in tempdb).  Di seguito sono riportati alcuni suggerimenti per il monitoraggio dell'utilizzo di tempdb e per ridurre l'utilizzo di tempdb nelle query.

### <a name="monitoring-tempdb-with-views"></a>Monitoraggio di tempdb con visualizzazioni

Per monitorare l'utilizzo di tempdb, installare prima la visualizzazione [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) dal [pool di Microsoft Toolkit per SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). È quindi possibile eseguire la query seguente per visualizzare l'utilizzo di tempdb per nodo per tutte le query eseguite:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Se si dispone di una query che utilizza una quantità elevata di memoria o se è stato ricevuto un messaggio di errore relativo all'allocazione di tempdb, il problema potrebbe essere dovuto a un CREATE TABLE molto grande [come Select (CTAs)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) o a un'istruzione [INSERT SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in esecuzione che non riesce nell'operazione di spostamento finale dei dati. Questo può essere in genere identificato come un'operazione ShuffleMove nel piano di query distribuite immediatamente prima dell'inserimento finale SELECT.  Usare [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per monitorare le operazioni di ShuffleMove.

La mitigazione più comune consiste nel suddividere l'istruzione CTAS o INSERT SELECT in più istruzioni Load, in modo che il volume di dati non superi il limite del tempdb di 1 TB per nodo. È anche possibile ridimensionare il cluster a dimensioni maggiori, in modo da suddividere le dimensioni di tempdb tra più nodi riducendo il tempdb in ogni singolo nodo.

Oltre alle istruzioni CTAS e INSERT SELECT, le query di grandi dimensioni e complesse eseguite con memoria insufficiente possono essere distribuite in tempdb causando un errore di query.  Provare a eseguire con una [classe di risorse](resource-classes-for-workload-management.md) più grande per evitare la distribuzione in tempdb.

## <a name="monitor-memory"></a>Monitorare la memoria

La memoria può essere la causa principale del rallentamento delle prestazioni e dei problemi di memoria insufficiente. Se durante l'esecuzione di query l'uso di memoria di SQL Server raggiunge i limiti previsti, prendere in considerazione il ridimensionamento del data warehouse.

La query seguente restituisce l'uso di memoria di SQL Server e l'eventuale uso elevato in ogni nodo:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Monitorare le dimensioni del log delle transazioni

La query seguente restituisce le dimensioni del log delle transazioni per ogni distribuzione. Se uno dei file di log sta per raggiungere i 160 GB, considerare l'espansione dell'istanza del programma o la limitazione delle dimensioni delle transazioni.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Monitorare il ripristino dello stato precedente del log delle transazioni

Se le query non vanno a buon fine o richiedono molto tempo, verificare se è in corso il ripristino dello stato precedente delle transazioni e monitorare questa operazione.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Monitorare il carico di base

La query seguente fornisce una stima approssimativa dello stato di avanzamento del caricamento. La query Mostra solo i file attualmente in fase di elaborazione.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle DMV, vedere [Viste di sistema](../sql/reference-tsql-system-views.md).
