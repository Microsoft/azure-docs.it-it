---
title: Indicazioni per la progettazione di tabelle distribuite
description: Suggerimenti per la progettazione di tabelle distribuite con distribuzione hash e Round Robin usando un pool SQL dedicato in Azure sinapsi Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5aefe869041d9fff8112b6aa380961ca6568ae0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673570"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Linee guida per la progettazione di tabelle distribuite usando un pool SQL dedicato in Azure sinapsi Analytics

Suggerimenti per la progettazione di tabelle distribuite con distribuzione hash e Round Robin in pool SQL dedicati.

Questo articolo presuppone che l'utente abbia familiarità con i concetti relativi alla distribuzione dei dati e allo spostamento dei dati nel pool SQL dedicato.  Per altre informazioni, vedere [architettura di analisi delle sinapsi di Azure](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Che cos'è una tabella distribuita?

Una tabella distribuita viene visualizzata come una singola tabella, ma le righe al suo interno, in realtà, sono archiviate in 60 distribuzioni. Le righe, inoltre, vengono distribuite con un algoritmo hash o round robin.  

Le **tabella con distribuzione hash** migliorano le prestazioni delle query nelle tabelle dei fatti di grandi dimensioni e rappresentano l'argomento principale di questo articolo. Le **tabelle round robin** consentono invece di aumentare la velocità di caricamento. Queste scelte di progettazione, quindi, possono contribuire in maniera significativa al miglioramento delle prestazioni delle query e di caricamento.

Un'altra opzione di archiviazione delle tabelle prevede la replica di una tabella di piccole dimensioni in tutti i nodi di calcolo. Per altre informazioni, vedere [Linee guida di progettazione per l'uso di tabelle replicate in Azure SQL Data Warehouse](design-guidance-for-replicated-tables.md). Per scegliere rapidamente tra queste tre opzioni, vedere Tabelle distribuite nell'articolo di [panoramica sulle tabelle](sql-data-warehouse-tables-overview.md).

Come parte della progettazione di tabelle, è necessario comprendere quanto più possibile i propri dati e il modo in cui vengono eseguite query sui dati.    Ad esempio, considerare queste domande:

- Quali sono le dimensioni della tabella?
- Quanto spesso viene aggiornata la tabella?
- Sono presenti tabelle dei fatti e delle dimensioni in un pool SQL dedicato?

### <a name="hash-distributed"></a>Tabelle con distribuzione hash

Una tabella con distribuzione hash distribuisce le righe della tabella nei vari nodi di calcolo usando una funzione hash deterministica per assegnare ogni riga a una [distribuzione](massively-parallel-processing-mpp-architecture.md#distributions).

![Tabella distribuita](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Tabella distribuita")  

Poiché i valori identici vengono sempre sottoposti a hashing nella stessa distribuzione, SQL Analytics include informazioni predefinite sui percorsi delle righe. Nel pool SQL dedicato queste informazioni vengono usate per ridurre al minimo lo spostamento dei dati durante le query, migliorando così le prestazioni delle query.

Le tabelle con distribuzione hash sono particolarmente indicate per le tabelle dei fatti di grandi dimensioni in uno schema star. Possono contenere un numero molto elevato di righe e conseguire comunque prestazioni elevate. È necessario, ovviamente, considerare anche alcuni aspetti di progettazione per ottenere le prestazioni che il sistema distribuito è in grado di offrire. Uno di questi riguarda la scelta di una colonna di distribuzione appropriata, illustrata in questo articolo.

Valutare l'opportunità di usare una tabella con distribuzione hash se:

- La dimensione della tabella su disco è superiore a 2 GB.
- La tabella prevede frequenti operazioni di inserimento, aggiornamento ed eliminazione.

### <a name="round-robin-distributed"></a>Distribuzione round robin

Una tabella con distribuzione round robin distribuisce le righe della tabella in modo uniforme tra tutte le distribuzioni. L'assegnazione delle righe alle distribuzioni è casuale. A differenza delle tabelle con distribuzione hash, inoltre, non è garantito che valori identici vengano assegnati alla stessa distribuzione.

Di conseguenza, a volte il sistema deve richiamare un'operazione di spostamento dei dati per organizzare meglio i dati e poter risolvere una query.  Questo passaggio aggiuntivo può rallentare le query. L'aggiunta di una tabella con distribuzione round robin, ad esempio, richiede una ridistribuzione dei dati, con una conseguente riduzione delle prestazioni.

Valutare l'opportunità di usare la distribuzione round robin per una tabella negli scenari seguenti:

- Quando si inizia come punto di partenza semplice (impostazione predefinita)
- Se non è presente una chiave di join ovvia.
- Se non è presente una colonna candidata ottimale per la distribuzione hash della tabella.
- Se la tabella non condivide una chiave di join comune con altre tabelle.
- Se il join è meno significativo di altri join nella query.
- Quando si tratta di una tabella di staging temporaneo.

L'esercitazione relativa al [caricamento dei dati relativi ai taxi di New York](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) illustra un esempio di caricamento dei dati in una tabella di staging con distribuzione round robin.

## <a name="choosing-a-distribution-column"></a>Scelta di una colonna di distribuzione

Nelle tabelle con distribuzione hash è presente una colonna di distribuzione che rappresenta la chiave hash. Il codice seguente, ad esempio, crea una tabella con distribuzione hash con ProductKey come colonna di distribuzione.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

È possibile aggiornare i dati archiviati nella colonna di distribuzione. Gli aggiornamenti apportati ai dati nella colonna di distribuzione possono tuttavia comportare un'operazione di riproduzione casuale dei dati.

La scelta della colonna di distribuzione è una decisione di progettazione importante, poiché i valori presenti in questa colonna determinano il modo in cui vengono distribuite le righe. La scelta ottimale dipende da vari fattori e, in genere, comporta alcuni compromessi. Dopo aver scelto una colonna di distribuzione, non è più possibile modificarla.  

Se non si riesce a scegliere subito la tabella ottimale, è possibile usare [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per ricreare la tabella con una colonna di distribuzione diversa.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Scegliere una colonna di distribuzione in modo che i dati vengano distribuiti in modo uniforme

Per ottenere prestazioni ottimali, tutte le distribuzioni devono avere approssimativamente lo stesso numero di righe. Se una o più distribuzioni hanno un numero sproporzionato di righe, alcune distribuzioni completano la propria porzione di query parallela prima delle altre. La query, quindi, viene completata solo nel momento in cui tutte le distribuzioni hanno terminato l'elaborazione e la velocità di ogni query corrisponde di fatto alla distribuzione più lenta.

- L'asimmetria dei dati significa che i dati non vengono distribuiti in modo uniforme tra le distribuzioni
- L'asimmetria di elaborazione significa che alcune distribuzioni richiedono più tempo di altre per eseguire query parallele. Questa situazione può verificarsi in caso di asimmetria dei dati.
  
Per bilanciare l'elaborazione parallela, selezionare una colonna di distribuzione che:

- **Contenga molti valori univoci.** La colonna può includere alcuni valori duplicati, ma tutte le righe con lo stesso valore vengono assegnate alla stessa distribuzione. Poiché sono presenti 60 distribuzioni, la colonna deve avere almeno 60 valori univoci.  In genere, tuttavia, il numero di valori univoci è molto più elevato.
- **Non contenga valori null o ne contenga un numero limitato.** Come esempio estremo, se tutti i valori della colonna sono NULL, tutte le righe vengono assegnate alla stessa distribuzione. L'elaborazione della query, quindi, è assegnata a un'unica distribuzione e non può usufruire dei vantaggi dell'elaborazione in parallelo.
- **Non è una colonna data**. Tutti i dati relativi alla stessa data vengono inseriti nella stessa distribuzione. In questo modo, se più utenti filtrano in base alla stessa data, l'intero lavoro di elaborazione viene eseguito solo da una delle 60 distribuzioni.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Scegliere una colonna di distribuzione che riduca al minimo lo spostamento dei dati

Per ottenere il risultato corretto, è possibile che le query spostino i dati da un nodo di calcolo a un altro. Lo spostamento dei dati si verifica in genere quando le query hanno join e aggregazioni in tabelle distribuite. La scelta di una colonna di distribuzione che consente di ridurre al minimo lo spostamento dei dati è una delle strategie più importanti per ottimizzare le prestazioni del pool SQL dedicato.

Per ridurre al minimo lo spostamento dei dati, selezionare una colonna di distribuzione che:

- Viene usata in clausole `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING`. Se due tabelle dei fatti di grandi dimensioni hanno join frequenti, le prestazioni delle query migliorano se si distribuiscono entrambe le tabelle in una delle colonne di join.  Se una tabella non viene usata in operazioni di join, valutare l'opportunità di distribuire la tabella in una colonna che si trova spesso nella clausola `GROUP BY`.
- *Non* viene usata in clausole `WHERE`. Questo potrebbe limitare la query in modo che non venga eseguita in tutte le distribuzioni.
- *Non* è una colonna dati. Le clausole WHERE filtrano spesso per data.  Quando si verifica questa situazione, l'intera elaborazione può essere eseguita solo su alcune distribuzioni.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Cosa fare quando nessuna delle colonne è una colonna di distribuzione appropriata

Se nessuna delle colonne ha valori distinti sufficienti per una colonna di distribuzione, è possibile creare una nuova colonna come composizione di uno o più valori. Per evitare spostamenti di dati durante l'esecuzione di query, usare la colonna di distribuzione composita come colonna di join nelle query.

Dopo aver progettato una tabella con distribuzione round robin, è necessario caricare i dati nella tabella.  Per informazioni sul caricamento, vedere l'articolo di [panoramica sul caricamento](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Come stabilire se una colonna di distribuzione è appropriata

Dopo aver caricato i dati in una tabella con distribuzione hash, verificare che le righe siano state distribuite in modo uniforme tra le 60 distribuzioni. Una variazione fino al 10% del numero di righe assegnate a ogni distribuzione non influisce in modo significativo sulle prestazioni.

### <a name="determine-if-the-table-has-data-skew"></a>Determinare se la tabella presenta un'asimmetria dei dati

Per controllare se è presente un'asimmetria dei dati, è possibile usare [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Il codice SQL seguente restituisce il numero di righe di tabella archiviate in ognuna delle 60 distribuzioni. Per ottenere prestazioni bilanciate, le righe nella tabella distribuita devono essere suddivise in modo uniforme tra tutte le distribuzioni.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Per identificare quali tabelle presentano un'asimmetria dei dati superiore al 10%:

1. Creare la visualizzazione dbo.vTableSizes illustrata nell'articolo di [panoramica delle tabelle](sql-data-warehouse-tables-overview.md#table-size-queries).  
2. Eseguire la query riportata di seguito:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Verificare lo spostamento dei dati nei piani di query

Se la colonna di distribuzione è appropriata, i join e le aggregazioni presentano uno spostamento minimo dei dati. Questo aspetto influisce sul modo in cui devono essere scritti i join. Per ottenere uno spostamento minimo dei dati per un join in due tabelle con distribuzione hash, una delle colonne di join deve essere una colonna di distribuzione.  Se due tabelle con distribuzione hash creano un join in una colonna di distribuzione dello stesso tipo di dati, il join non richiede lo spostamento dei dati. I join possono usare colonne aggiuntive senza richiedere uno spostamento dei dati.

Per evitare lo spostamento dei dati durante un join:

- È necessario eseguire la distribuzione hash delle tabelle coinvolte nel join in **una** delle colonne che fanno parte del join.
- I tipi di dati delle colonne di join nelle due tabelle devono corrispondere.
- Le colonne devono essere unite con un operatore Uguale.
- Il tipo di join può non essere `CROSS JOIN`.

Per vedere se nelle query si verifica uno spostamento dei dati, è possibile controllare il piano di query.  

## <a name="resolve-a-distribution-column-problem"></a>Risolvere un problema relativo a una colonna di distribuzione

Non è necessario risolvere tutti i casi di asimmetria dei dati. La distribuzione è essenzialmente l'individuazione del giusto equilibrio fra la minimizzazione dell'asimmetria dei dati e la minimizzazione dello spostamento dei dati. Non è sempre possibile ridurre al minimo entrambi i valori. In alcuni casi, il vantaggio di uno spostamento dei dati minimo può essere quello di compensare l'impatto negativo dell'asimmetria dei dati.

Per decidere se sia necessario risolvere la differenza dati di una tabella, è necessario conoscere nel modo più completo possibile i volumi di dati e le query del carico di lavoro. È possibile seguire la procedura descritta nell'articolo [Monitoraggio delle query](sql-data-warehouse-manage-monitor.md) per monitorare l'impatto dell'asimmetria sulle prestazioni delle query. In particolare, è possibile scoprire quanto tempo richiede il completamento di query di grandi dimensioni in singole distribuzioni.

Non essendo possibile modificare la colonna di distribuzione in una tabella esistente, il modo più comune per risolvere l'asimmetria dei dati consiste nel ricreare la tabella con una colonna di distribuzione diversa.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Ricreare la tabella con una nuova colonna di distribuzione

Questo esempio usa [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per ricreare una tabella con una colonna di distribuzione hash diversa.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Passaggi successivi

Per creare una tabella distribuita, usare una di queste istruzioni:

- [CREATE TABLE (pool SQL dedicato)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE come SELECT (pool SQL dedicato)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)