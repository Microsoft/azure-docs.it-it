---
title: Tabelle temporanee
description: Indicazioni essenziali per l'uso di tabelle temporanee in Azure SQL Data Warehouse, evidenziando i principi delle tabelle temporanee a livello di sessione.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3a8772550e67c250b1a84dbae17d1d3fe6c5c90e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351162"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelle temporanee in SQL Data Warehouse
Questo articolo contiene le linee guida fondamentali per l'uso delle tabelle temporanee ed evidenzia i principi delle tabelle temporanee a livello di sessione. Usando le informazioni in questo articolo è possibile modularizzare il codice, aumentando le possibilità di riutilizzo e la facilità di manutenzione del codice.

## <a name="what-are-temporary-tables"></a>Introduzione delle tabelle temporanee
Le tabelle temporanee sono utili durante l'elaborazione dati, soprattutto durante la trasformazione in cui i risultati intermedi sono temporanei. In SQL Data Warehouse le tabelle temporanee esistono a livello di sessione.  Sono visibili solo per la sessione in cui sono stati creati e vengono eliminati automaticamente quando si disconnette tale sessione.  Le tabelle temporanee offrono un miglioramento delle prestazioni, perché i loro risultati vengono scritti in locale anziché nell'archiviazione remota.

## <a name="create-a-temporary-table"></a>Creazione di una tabella temporanea
Le tabelle temporanee vengono create aggiungendo un prefisso al nome di una tabella con `#`.  Ad esempio:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Le tabelle temporanee possono essere create anche con `CTAS` adottando esattamente lo stesso approccio:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS` è un comando efficace e offre l'ulteriore vantaggio di essere efficiente nell'uso dello spazio dei log delle transazioni. 
> 
> 

## <a name="dropping-temporary-tables"></a>Eliminazione delle tabelle temporanee
Quando viene creata una nuova sessione, non deve esistere alcuna tabella temporanea.  Tuttavia, se si richiama la stessa stored procedure che crea una variabile temporanea con lo stesso nome, per garantire che le istruzioni `CREATE TABLE` abbiano esito positivo è possibile eseguire un controllo di pre-esistenza con `DROP`, come nell'esempio seguente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Una procedura consigliata per la coerenza della codifica è usare questo modello per le tabelle e le tabelle temporanee.  È inoltre consigliabile usare `DROP TABLE` per rimuovere le tabelle temporanee quando non sono più necessarie.  Nello sviluppo delle stored procedure è comune visualizzare i comandi di eliminazione raggruppati in bundle alla fine di una procedura per garantire che questi oggetti vengano puliti.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizzazione del codice
Dal momento che le tabelle temporanee sono visibili in qualsiasi punto di una sessione utente, questo può essere sfruttato per modularizzare il codice dell'applicazione.  Ad esempio, la stored procedure seguente genera l'errore DDL per aggiornare tutte le statistiche nel database in base al nome della statistica.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

In questa fase, l'unica azione che si è verificata è la creazione di una stored procedure che genera una tabella temporanea, #stats_ddl, con istruzioni DDL.  Questa stored procedure elimina #stats_ddl se esiste già per garantire che non avrà esito negativo se eseguita più volte all'interno di una sessione.  Tuttavia, poiché non esiste `DROP TABLE` alla fine della stored procedure, al termine della stored procedure, la tabella creata viene conservata in modo che possa essere letta all'esterno della stored procedure.  A differenza che negli altri server di database SQL, in SQL Data Warehouse è possibile usare la tabella temporanea all'esterno della procedura che l'ha creata.  Le tabelle temporanee di SQL Data Warehouse possono essere usate **ovunque** all'interno della sessione. In questo modo è possibile ottenere codice più modulare e gestibile come nell'esempio seguente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitazioni della tabella temporanea
SQL Data Warehouse impone un paio di limitazioni quando si implementano tabelle temporanee.  Attualmente sono supportate solo le tabelle temporanee nell'ambito della sessione.  Le tabelle temporanee globali non sono supportate.  Inoltre, non è possibile creare visualizzazioni nelle tabelle temporanee.  Le tabelle temporanee possono essere create solo con la distribuzione hash o round robin.  La distribuzione di tabelle temporanee replicate non è supportata. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sullo sviluppo di tabelle, vedere [Panoramica delle tabelle](sql-data-warehouse-tables-overview.md).

