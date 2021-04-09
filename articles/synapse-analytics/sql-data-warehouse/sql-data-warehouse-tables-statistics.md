---
title: Creazione e aggiornamento delle statistiche nelle tabelle
description: Suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query nelle tabelle nel pool SQL dedicato.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673367"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Statistiche della tabella per il pool SQL dedicato in Azure sinapsi Analytics

In questo articolo sono disponibili indicazioni ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query nelle tabelle nel pool SQL dedicato.

## <a name="why-use-statistics"></a>Perché usare le statistiche?

Il pool SQL più dedicato conosce i dati, più rapidamente può eseguire query su di esso. Dopo il caricamento dei dati in un pool SQL dedicato, la raccolta delle statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per ottimizzare le query.

Il Query Optimizer del pool SQL dedicato è un ottimizzatore basato sui costi. Esegue un confronto fra i costi dei vari piani di query e poi sceglie quello che costa meno, che in molti casi è il piano eseguito più velocemente.

Se, ad esempio, l'ottimizzatore stima che la data in base alla quale si sta filtrando la query restituirà una riga, verrà scelto un piano. Se invece stima che la data selezionata restituirà un milione righe, verrà restituito un piano diverso.

## <a name="automatic-creation-of-statistic"></a>Creazione automatica di statistiche

Quando l'opzione database AUTO_CREATE_STATISTICS è on, il pool SQL dedicato analizza le query utente in ingresso per le statistiche mancanti.

Se non sono presenti, Query Optimizer creerà statistiche su singole colonne nel predicato della query o nella condizione di join per migliorare le stime di cardinalità del piano di query.

> [!NOTE]
> Per impostazione predefinita, la creazione automatica di statistiche è attiva.

È possibile verificare se il pool SQL dedicato è AUTO_CREATE_STATISTICS configurato eseguendo il comando seguente:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se il pool SQL dedicato non ha AUTO_CREATE_STATISTICS configurato, è consigliabile abilitare questa proprietà eseguendo il comando seguente:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Queste istruzioni attiveranno la creazione automatica delle statistiche:

- SELECT
- INSERT SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN quando contengono un join o viene rilevata la presenza di un predicato

> [!NOTE]
> L'opzione di creazione automatica di statistiche non crea statistiche in tabelle temporanee o esterne.

La creazione automatica di statistiche viene generata in modo sincrono; se nelle colonne non sono presenti tutte le statistiche, quindi, è possibile che si verifichi un leggero peggioramento delle prestazioni delle query. Il tempo necessario per creare le statistiche relative a una colonna dipende dalle dimensioni della tabella.

Per evitare una riduzione delle prestazioni, verificare che le statistiche siano già state create eseguendo il carico di lavoro di benchmark prima della profilatura del sistema.

> [!NOTE]
> La creazione di statistiche verrà registrata [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in un contesto utente diverso.

Le statistiche automatiche create sono nel formato: _WA_Sys_<id colonna a 8 cifre in hex>_<id tabella a 8 cifre in hex>. È possibile visualizzare le statistiche che sono già state create eseguendo il comando [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Il table_name è il nome della tabella che contiene le statistiche da visualizzare. Questa tabella non può essere una tabella esterna. La destinazione è il nome dell'indice di destinazione, delle statistiche o della colonna per cui visualizzare le informazioni statistiche.

## <a name="update-statistics"></a>Aggiornare le statistiche

Una procedura consigliata consiste nell'aggiornare le statistiche sulle colonne data ogni giorno quando vengono aggiunte nuove date. Ogni volta che vengono caricate nuove righe nel pool SQL dedicato, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

È possibile che non sia mai necessario aggiornare le statistiche relative a una colonna Country/Region in una tabella Customer perché la distribuzione dei valori non cambia in genere. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Tuttavia, se il pool SQL dedicato contiene solo un paese e si importano i dati da un nuovo paese/area geografica, causando l'archiviazione di dati da più paesi o aree geografiche, è necessario aggiornare le statistiche nella colonna paese/area geografica.

Di seguito sono forniti alcuni elementi consigliati per l'aggiornamento delle statistiche:

|||
|-|-|
| **Frequenza degli aggiornamenti delle statistiche**  | Conservativa: Giornaliera </br> Dopo il caricamento o la trasformazione dei dati |
| **Campionamento** |  Se inferiore a 1 miliardo di righe, usare il campionamento predefinito (20%). </br> Se superiore a 1 miliardo righe, usare il campionamento del 2%. |

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Questa verifica non può essere basata sulla data di creazione dei dati. Un oggetto statistiche aggiornato può essere vecchio se non sono state apportate modifiche sostanziali ai dati sottostanti. È necessario aggiornare le statistiche *quando* vengono apportate modifiche importanti al numero di righe o modifiche sostanziali alla distribuzione dei valori per una colonna specifica. 

Non esiste alcuna vista a gestione dinamica per determinare se i dati all'interno della tabella sono cambiati dall'ultimo aggiornamento delle statistiche.  Le due query seguenti consentono di determinare se le statistiche non sono aggiornate.

**Query 1:**  Individuare la differenza tra il conteggio delle righe dalle statistiche (**stats_row_count**) e il conteggio delle righe effettivo (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Query 2:** Verificare l'età delle statistiche controllando l'ultima volta in cui le statistiche sono state aggiornate in ogni tabella. 

> [!NOTE]
> Se è stata apportata una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche a prescindere dalla data dell'ultimo aggiornamento.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Le **colonne della data** in un pool SQL dedicato, ad esempio, richiedono in genere aggiornamenti frequenti delle statistiche. Ogni volta che vengono caricate nuove righe nel pool SQL dedicato, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

Al contrario, è possibile che non sia mai necessario aggiornare le statistiche relative alla colonna del sesso in una tabella clienti. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Se il pool SQL dedicato contiene un solo sesso e un nuovo requisito produce più sessi, è necessario aggiornare le statistiche sulla colonna Gender.

Per ulteriori informazioni, vedere indicazioni su [Statistiche](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche

Spesso è consigliabile estendere il processo di caricamento dei dati per assicurare che le statistiche vengano aggiornate al termine del caricamento per evitare o ridurre al minimo il blocco o la contesa di risorse tra le query simultanee.  

Il caricamento dei dati è la fase in cui si verifica con maggiore frequenza una modifica delle dimensioni e/o della distribuzione dei valori delle tabelle. Il caricamento dei dati è una posizione logica per implementare alcuni processi di gestione.

Di seguito vengono illustrati i principi guida per l'aggiornamento delle statistiche:

- Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Ciò permette di aggiornare le informazioni sulle dimensioni delle tabelle (numero di righe e pagine) come parte dell'aggiornamento delle statistiche.
- Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
- Prendere in considerazione una maggiore frequenza per l'aggiornamento delle colonne "chiave crescente", ad esempio le date delle transazioni, poiché questi valori non verranno inclusi nell'istogramma delle statistiche.
- Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
- Occorre ricordare che ogni oggetto statistiche viene aggiornato in sequenza. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` non è sempre ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistiche.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Esempi: Creare le statistiche

Questi esempi illustrano come usare diverse opzioni per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

### <a name="create-single-column-statistics-with-default-options"></a>Creare statistiche a colonna singola con opzioni predefinite

Per creare statistiche su una colonna, fornire un nome per l'oggetto statistiche e il nome della colonna.

Questa sintassi usa tutte le opzioni predefinite. Per impostazione predefinita, durante la creazione delle statistiche viene campionato il **20%** della tabella.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

La frequenza di campionamento del 20% è sufficiente per la maggior parte delle situazioni. È tuttavia possibile modificare la frequenza di campionamento.

Per eseguire il campionamento dell'intera tabella, usare la sintassi seguente:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

In alternativa, è possibile specificare le dimensioni del campione sotto forma di percentuale:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Creare statistiche a colonna singola solo su alcune righe

È anche possibile creare statistiche su una parte delle righe della tabella. Questa opzione è definita statistica filtrata.

Ad esempio, è possibile usare le statistiche filtrate quando si pianifica di eseguire una query in una partizione specifica di una tabella partizionata di grandi dimensioni. Se si creano statistiche solo sui valori della partizione, la precisione delle statistiche migliorerà e miglioreranno quindi le prestazioni delle query.

Questo esempio crea statistiche su un intervallo di valori. È possibile definire con facilità i valori in modo che corrispondano all'intervallo di valori in una partizione.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Per fare in modo che Query Optimizer prenda in considerazione l'uso delle statistiche filtrate quando sceglie il piano di query distribuite, è necessario che la query rientri nella definizione dell'oggetto statistiche. Usando l'esempio precedente, la clausola WHERE della query deve specificare i valori col1 compresi tra 2000101 e 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Creare statistiche a colonna singola con tutte le opzioni

È anche possibile combinare le varie opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione del campione personalizzata:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREAZIONE DELLE  STATISTICHE](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Creare statistiche a più colonne

Per creare un oggetto statistiche a più colonne, usare gli esempi precedenti, specificando però più colonne.

> [!NOTE]
> L'istogramma, che viene usato per stimare il numero di righe nei risultato della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche sulle colonne vengono calcolate su *product\_category* e *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché esiste una correlazione tra *product\_category* e *product\_sub\_category*, un oggetto statistiche a più colonne può essere utile se si accede contemporaneamente a queste colonne.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Creare statistiche su tutte le colonne in una tabella

Un modo per creare le statistiche consiste nell'emettere comandi CREATE STATISTICS dopo la creazione della tabella:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Usare un stored procedure per creare statistiche su tutte le colonne in un pool SQL

Il pool SQL dedicato non dispone di un stored procedure di sistema equivalente a sp_create_stats in SQL Server. Questo stored procedure crea un oggetto statistiche a colonna singola su ogni colonna di un pool SQL che non dispone già di statistiche.

L'esempio seguente consente di iniziare a usare la progettazione del pool SQL. È possibile adattare l'operazione alle proprie esigenze.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Per creare statistiche su tutte le colonne della tabella usando le impostazioni predefinite, chiamare la stored procedure.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Per creare statistiche su tutte le colonne della tabella usando un FULLSCAN, chiamare questa procedura.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Per creare statistiche campionate su tutte le colonne della tabella, immettere 3 e la percentuale di campionamento. Questa procedura usa una frequenza di campionamento del 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Esempi: Aggiornare le statistiche

Per aggiornare le statistiche, è possibile eseguire le operazioni seguenti:

- Aggiornare un oggetto statistiche. Specificare il nome dell'oggetto statistiche che si desidera aggiornare.
- Aggiornare tutti gli oggetti statistiche in una tabella. Specificare il nome della tabella invece di un oggetto statistiche specifico.

### <a name="update-one-specific-statistics-object"></a>Aggiornare un oggetto statistiche specifico

Usare la sintassi seguente per aggiornare un oggetto statistiche specifico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ad esempio:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

L'aggiornamento di oggetti statistiche specifici permette di ridurre al minimo il tempo e le risorse necessari per gestire le statistiche. In questo modo è necessario scegliere gli oggetti statistiche migliori da aggiornare.

### <a name="update-all-statistics-on-a-table"></a>Aggiornamento di tutte le statistiche di una tabella

Di seguito è illustrato un semplice metodo di aggiornamento di tutti gli oggetti statistiche in una tabella.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio:

```sql
UPDATE STATISTICS dbo.table1;
```

L'istruzione UPDATE STATISTICS è facile da usare. Occorre ricordare che aggiorna *tutte* le statistiche nella tabella e che quindi potrebbe eseguire più lavoro del necessario. Se le prestazioni non sono un problema, questo è il modo più semplice e più completo per garantire che le statistiche siano aggiornate.

> [!NOTE]
> Quando si aggiornano tutte le statistiche in una tabella, il pool SQL dedicato esegue un'analisi per campionare la tabella per ogni oggetto statistiche. Se si tratta di una tabella di grandi dimensioni, che include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alle necessità.

Per un'implementazione di una `UPDATE STATISTICS` procedura, vedere [tabelle temporanee](sql-data-warehouse-tables-temporary.md). Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato è lo stesso.

Per la sintassi completa, vedere [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="statistics-metadata"></a>Metadati delle statistiche

Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione stats-date per vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche

Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | Descrizione |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni colonna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni oggetto del database. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni schema del database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni oggetto statistiche. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni tipo di dati. |

### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche

Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | Descrizione |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Riepilogo e informazioni dettagliate sulla distribuzione di valori riconosciuti dall'oggetto statistiche. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinare le colonne delle statistiche e le funzioni in un'unica visualizzazione

Questa visualizzazione riunisce le colonne relative alle statistiche e ai risultati della funzione STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra i dati inclusi in un oggetto statistiche. Questi dati sono costituiti da tre parti:

- Intestazione
- Vettore di densità
- Istogramma

Metadati di intestazione sulle statistiche. L'istogramma mostra la distribuzione dei valori nella prima colonna chiave dell'oggetto statistiche. Il vettore di densità misura la correlazione tra le colonne.

> [!NOTE]
> Il pool SQL dedicato calcola le stime della cardinalità con tutti i dati nell'oggetto statistiche.

### <a name="show-header-density-and-histogram"></a>Mostrare l'intestazione, la densità e l'istogramma

Questo semplice esempio mostra tutte e tre le parti di un oggetto statistiche:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrare una o più parti di DBCC SHOW_STATISTICS()

Se si è interessati a visualizzare solo parti specifiche, usare la clausola `WITH` e specificare le parti da visualizzare:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Differenze di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS () viene implementato in modo più rigoroso nel pool SQL dedicato rispetto a SQL Server:

- Le funzionalità non documentate non sono supportate.
- Non è possibile usare Stats_stream.
- Non è possibile unire i risultati per sottoinsiemi specifici di dati statistici. ad esempio: STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS non può essere impostato per l'eliminazione del messaggio.
- Non è possibile usare le parentesi quadre per i nomi delle statistiche.
- Non è possibile usare i nomi di colonna per identificare gli oggetti statistiche.
- L'errore personalizzato 2767 non è supportato.

## <a name="next-steps"></a>Passaggi successivi

Per ottimizzare ulteriormente le prestazioni delle query, vedere [Monitorare il carico di lavoro](sql-data-warehouse-manage-monitor.md)
