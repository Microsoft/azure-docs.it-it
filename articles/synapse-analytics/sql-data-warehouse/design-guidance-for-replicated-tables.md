---
title: Linee guida di progettazione per le tabelle replicate
description: Suggerimenti per la progettazione di tabelle replicate in Synapse SQLRecommendations for designing replicated tables in Synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0b240c45afcb2374f41eb26e86e46b106e314e76
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582232"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql"></a>Linee guida di progettazione per l'utilizzo di tabelle replicate in Synapse SQLDesign guidance for using replicated tables in Synapse SQL

In questo articolo vengono forniti suggerimenti per la progettazione di tabelle replicate nello schema SQL Synapse.This article gives recommendations for designing replicated tables in your Synapse SQL schema. Usare questi consigli per migliorare le prestazioni delle query riducendo lo spostamento dei dati e la complessità delle query stesse.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che l'utente abbia familiarità con i concetti di distribuzione e spostamento dei dati in Synapse SQL.Per altre informazioni, vedere l'articolo relativo all'[architettura](massively-parallel-processing-mpp-architecture.md). 

Come parte della progettazione di tabelle, è necessario comprendere quanto più possibile i propri dati e il modo in cui vengono eseguite query sui dati.Ad esempio, considerare queste domande:

- Quali sono le dimensioni della tabella?   
- Quanto spesso viene aggiornata la tabella?   
- Sono disponibili tabelle dei fatti e delle dimensioni in un database SQL Synapse?   

## <a name="what-is-a-replicated-table"></a>Che cos'è una tabella replicata?

Una tabella replicata include una copia completa della tabella accessibile in ogni nodo di calcolo. La replica di una tabella elimina la necessità di trasferire dati tra i nodi di calcolo prima di un join o un'aggregazione. Poiché la tabella ha più copie, le tabelle replicate funzionano meglio quando le dimensioni delle tabelle sono inferiori a 2 GB, già compresse.  2 GB non è un limite rigido.  Se i dati sono statici e non vengono modificati, è possibile replicare tabelle più grandi.

Il diagramma seguente mostra una tabella replicata accessibile in ogni nodo di calcolo. In Synapse SQL, la tabella replicata viene copiata completamente in un database di distribuzione in ogni nodo di calcolo. 

![Tabella replicata](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabella replicata")  

Le tabelle replicate funzionano bene per le tabelle delle dimensioni in uno schema a stella. Le tabelle delle dimensioni vengono in genere unite in join alle tabelle dei fatti distribuite in modo diverso rispetto alla tabella delle dimensioni.  Le dimensioni sono in genere di dimensioni che rendono possibile l'archiviazione e la manutenzione di più copie. Nelle dimensioni sono archiviati dati descrittivi che cambiano raramente, come il nome e l'indirizzo di un cliente e i dettagli del prodotto. La natura a lenta evoluzione dei dati porta a una minore manutenzione della tabella replicata. 

Provare a usare una tabella replicata nei casi seguenti:

- La dimensione della tabella su disco è inferiore a 2 GB, indipendentemente dal numero di righe. Per individuare la dimensione di una tabella, usare il comando [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest): `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- La tabella viene usata in join che richiederebbero altrimenti lo spostamento dei dati. Quando si crea un join di tabelle non distribuite nella stessa colonna, ad esempio una tabella con distribuzione hash in una tabella round robin, è necessario lo spostamento dei dati per completare la query.  Se una delle tabelle ha dimensioni ridotte, provare una tabella replicata. È consigliabile usare tabelle replicate invece di tabelle round robin nella maggior parte dei casi. Per visualizzare le operazioni di spostamento dei dati nei piani di query, usare [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  L'operazione tipica di spostamento di dati che può essere eliminata usando una tabella replicata è BroadcastMoveOperation.  
 
Le tabelle replicate possono essere causa di prestazioni delle query non ottimali nei casi seguenti:

- La tabella prevede frequenti operazioni di inserimento, aggiornamento ed eliminazione.Le operazioni DML (Data Manipulation Language) richiedono una ricostruzione della tabella replicata.La ricompilazione causa spesso un rallentamento delle prestazioni.
- Il database SQL Synapse viene ridimensionato frequentemente. La scalabilità di un database comporta la modifica del numero di nodi di calcolo, in cui viene eseguita la ricompilazione della tabella replicata.
- La tabella include un numero elevato di colonne, ma le operazioni sui dati accedono in genere solo a una quantità ridotta di colonne. In questo scenario, invece di replicare l'intera tabella, può essere più efficace eseguire una distribuzione della tabella e quindi creare un indice per le colonne cui si accede di frequente. Quando una query richiede lo spostamento dei dati, vengono spostati solo i dati per le colonne richieste.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usare tabelle replicate con predicati di query semplici

Prima di scegliere di distribuire o replicare una tabella, considerare i tipi di query che si prevede di eseguire sulla tabella. Se possibile:

- Usare tabelle replicate per query con predicati di query semplici, come le query di uguaglianza o disuguaglianza.
- Usare tabelle distribuite per query con predicati di query complessi, come LIKE o NOT LIKE.

Le query che richiedono un uso intensivo della CPU hanno prestazioni migliori quando il lavoro viene distribuito tra tutti i nodi di calcolo. Ad esempio, le query che eseguono calcoli in ogni riga di una tabella hanno prestazioni migliori nelle tabelle distribuite che non nelle tabelle replicate. Poiché una tabella replicata viene completamente archiviata in ogni nodo di calcolo, una query che richiede un uso intensivo di CPU su una tabella replicata viene eseguita sull'intera tabella in ogni nodo di calcolo. Il calcolo aggiuntivo può rallentare le prestazioni delle query.

Questa query, ad esempio, ha un predicato complesso.  Viene eseguito più velocemente quando i dati si trova in una tabella distribuita anziché in una tabella replicata. In questo esempio, i dati possono essere distribuiti round robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'
       
```       
           
## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Convertire le tabelle round robin esistenti in tabelle replicate
Se si dispone già di tabelle round robin, è consigliabile convertirle in tabelle replicate se soddisfano i criteri descritti in questo articolo. Le tabelle replicate migliorano le prestazioni rispetto alle tabelle round robin, perché eliminano la necessità di spostare i dati.  Una tabella round robin richiede lo spostamento dei dati per i join. 

Questo esempio usa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per modificare la tabella DimSalesTerritory in una tabella replicata. Questo esempio funziona indipendentemente dal fatto che per DimSalesTerritory sia stata eseguita una distribuzione hash o round robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
``` 
    
### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Esempio di prestazioni delle query delle tabelle round robin rispetto alle tabelle replicate 
    
Una tabella replicata non richiede lo spostamento dei dati per i join, perché l'intera tabella è già presente in ogni nodo di calcolo. Se viene eseguita una distribuzione round robin delle tabelle delle dimensioni, un join copia interamente la tabella delle dimensioni in ogni nodo di calcolo. Per spostare i dati, il piano di query contiene un'operazione chiamata BroadcastMoveOperation. Questo tipo di operazione di spostamento dei dati rallenta le prestazioni delle query e viene eliminato usando tabelle replicate. Per visualizzare i passaggi del piano di query, usare la vista del catalogo di sistema [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Nella query seguente sullo schema AdventureWorks, ad esempio, la tabella `FactInternetSales` è con distribuzione hash. Le tabelle `DimDate` e `DimSalesTerritory` sono tabelle delle dimensioni più piccole. Questa query restituisce le vendite totali in America del Nord per l'anno fiscale 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
`DimDate` e `DimSalesTerritory` sono state ricreate come tabelle round robin. Di conseguenza, la query ha mostrato il piano di query seguente, che prevede più operazioni di spostamento di trasmissione: 
 
![Piano di query round robin](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

`DimDate` e `DimSalesTerritory` sono state ricreate come tabelle replicate e su di esse è stata eseguita una query. Il piano di query risultante è molto più breve e non include spostamenti di trasmissione.

![Piano di query di tabelle replicate](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerazioni sulle prestazioni per la modifica di tabelle replicate

Una tabella replicata viene implementata mantenendo una versione master della tabella. Il servizio copia la versione master in un database di distribuzione in ogni nodo di calcolo. Quando viene apportata una modifica, la tabella master viene aggiornata per prima. Quindi la tabella in ogni nodo di calcolo viene ricompilata. La ricompilazione di una tabella replicata include la copia della tabella in ogni nodo di calcolo e quindi la compilazione degli indici.  Ad esempio una tabella replicata su DW400 ha 5 copie di dati.  Una copia master e una copia completa in ogni nodo di calcolo.  Tutti i dati vengono archiviati nei database di distribuzione per supportare istruzioni di modifica dei dati più veloci e operazioni di ridimensionamento flessibili. 

Le compilazioni sono necessarie dopo le operazioni seguenti:
- Vengono caricati o modificati dati
- L'istanza SQL Synapse viene ridimensionata a un livello diverso
- Viene aggiornata la definizione di tabella

Le ricompilazioni non sono necessarie dopo le operazioni seguenti:
- Operazione di sospensione
- Operazione di ripresa

La ricompilazione non viene eseguita immediatamente dopo la modifica dei dati. Al contrario, la ricompilazione viene attivata la prima volta che una query esegue una selezione dalla tabella.  La query che ha attivato la ricompilazione esegue la lettura immediatamente dalla versione master della tabella, mentre i dati vengono copiati in modo asincrono in ogni nodo di calcolo. Fino al completamento della copia dei dati, le query successive continueranno a usare la versione master della tabella.  Se viene eseguita un'attività in base alla tabella replicata che forza un'altra ricompilazione, la copia dei dati viene invalidata e l'istruzione SELECT successiva attiverà di nuovo la copia dei dati. 

### <a name="use-indexes-conservatively"></a>Usare gli indici con moderazione

Alle tabelle replicate si applicano le procedure di indicizzazione standard. Ogni indice di tabella replicato viene ricompilato come parte di una ricostruzione dell'indice. Usare gli indici solo quando le prestazioni sono più importanti del costo della ricompilazione degli indici.  
 
### <a name="batch-data-loads"></a>Caricare in batch i dati
Quando si caricano dati in tabelle replicate, provare a ridurre al minimo le ricompilazioni eseguendo i caricamenti in batch. Eseguire tutti i caricamenti in batch prima di eseguire istruzioni di selezione.

Ad esempio, questo modello di carico carica dati da quattro origini e richiama quattro ricompilazioni. 

        Load from source 1.
- L'istruzione di selezione attiva la ricompilazione 1.
        Caricamento dall'origine 2.
- L'istruzione di selezione attiva la ricompilazione 2.
- Caricamento dall'origine 3.
- L'istruzione di selezione attiva la ricompilazione 3.
- Caricamento dall'origine 4.
- L'istruzione di selezione attiva la ricompilazione 4.

Ad esempio, questo modello di carico carica dati da quattro origini, ma richiama una sola ricompilazione.

- Caricamento dall'origine 1.
- Caricamento dall'origine 2.
- Caricamento dall'origine 3.
- Caricamento dall'origine 4.
- L'istruzione di selezione attiva la ricompilazione.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Ricompilare una tabella replicata dopo un caricamento in batch

Per garantire tempi di esecuzione di query coerenti, prendere in considerazione di forzare la compilazione delle tabelle replicate dopo un caricamento in batch. In caso contrario, la prima query continuerà a usare lo spostamento dei dati per completare la query. 

Questa query usa la DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per elencare le tabelle replicate che sono state modificate, ma non ricompilate.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Per attivare una ricompilazione, eseguire l'istruzione seguente in ogni tabella nell'output precedente. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Passaggi successivi

Per creare una tabella replicata, usare una di queste istruzioni:

- [CREA TABELLA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREA TABELLA COME SELEZIONARE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Per una panoramica delle tabelle distribuite, vedere [Tabelle distribuite](sql-data-warehouse-tables-distribute.md).
