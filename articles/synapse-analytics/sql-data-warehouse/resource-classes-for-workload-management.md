---
title: Classi di risorse per la gestione del carico di lavoro
description: Indicazioni per l'uso delle classi di risorse per gestire la concorrenza e le risorse di calcolo per le query in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7714ce748eb172565357723924ab2212e9559e1f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685328"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Gestione del carico di lavoro con le classi di risorse in Azure Synapse Analytics

Indicazioni per l'uso delle classi di risorse per gestire la memoria e la concorrenza per le query del pool Synapse SQL in Azure Synapse.  

## <a name="what-are-resource-classes"></a>Che cosa sono le classi di risorse

La capacità di prestazioni di una query è determinata dalla classe di risorse dell'utente.  Le classi di risorse sono limiti delle risorse predeterminati nel pool Synapse SQL che regolano le risorse di calcolo e la concorrenza per l'esecuzione delle query. Le classi di risorse possono agevolare la configurazione delle risorse per le query, consentendo di impostare limiti per il numero di query eseguite contemporaneamente e per le risorse di calcolo assegnate a ogni query.  È necessario trovare il giusto compromesso tra memoria e concorrenza.

- Le classi di risorse di piccole dimensioni riducono la memoria massima per ogni query, ma aumentano la concorrenza.
- Le classi di risorse di grandi dimensioni aumentano la memoria massima per ogni query, ma riducono la concorrenza.

Esistono due tipi di classi di risorse:

- Le classi di risorse statiche che sono più adatte per assicurare maggiore concorrenza in un set di dati di dimensioni fisse.
- Le classi di risorse dinamiche, che sono più adatte per i set di dati con dimensioni in aumento che necessitano di prestazioni ottimizzate quando il livello di servizio passa a un piano superiore.

Le classi di risorse usano gli slot di concorrenza per misurare il consumo di risorse.  Gli [slot di concorrenza](#concurrency-slots) verranno illustrati più avanti nell'articolo.

- Per visualizzare l'uso delle risorse per le classi di risorse, vedere [Memory and concurrency limits](memory-concurrency-limits.md) (Limiti di memoria e concorrenza).
- Per modificare la classe di risorse, è possibile usare un altro utente per eseguire la query o [modificare l'appartenenza della classe di risorse dell'utente corrente](#change-a-users-resource-class).

### <a name="static-resource-classes"></a>Classi di risorse statiche

Le classi di risorse statiche allocano la stessa quantità di memoria indipendentemente dal livello di prestazioni corrente, misurato in [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Poiché le query ottengono la stessa allocazione di memoria indipendentemente dal livello di prestazioni, la [scalabilità orizzontale del data warehouse](quickstart-scale-compute-portal.md) consente di eseguire più query in una classe di risorse.  Le classi di risorse statiche sono ideali se il volume di dati è noto e costante.

Le classi di risorse statiche vengono implementate con i ruoli predefiniti del database seguenti:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Classi di risorse dinamiche

Le classi di risorse dinamiche allocano una quantità variabile di memoria in base al livello di servizio corrente. Anche se le classi di risorse statiche sono utili per assicurare maggiore concorrenza e per i volumi di dati statici, le classi di risorse dinamiche sono più adatte per una quantità di dati in aumento o variabile.  Quando si passa a un livello di servizio superiore, le query ottengono automaticamente più memoria.  

Le classi di risorse dinamiche vengono implementate con i ruoli predefiniti del database seguenti:

- smallrc
- mediumrc
- largerc
- xlargerc

L'allocazione di memoria per ogni classe di risorse è la seguente.

| Livello di servizio  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12,5%             | 12,5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6,25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 5%                | 10%                    | 22%                    | 70%                    |
| Da DW1000c a<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |

### <a name="default-resource-class"></a>Classe di risorse predefinita

Per impostazione predefinita, ogni utente è membro della classe di risorse dinamica **smallrc**.

La classe di risorse dell'amministratore del servizio è fissa su smallrc e non può essere modificata.  L'amministratore del servizio è l'utente creato durante il processo di provisioning.  L'amministratore del servizio in questo contesto è l'account di accesso specificato per l'"accesso amministratore server" durante la creazione di un nuovo pool Synapse SQL con un nuovo server.

> [!NOTE]
> Gli utenti o gruppi definiti come amministratori di Active Directory sono anche amministratori del servizio.
>
>

## <a name="resource-class-operations"></a>Operazioni sulle classi di risorse

Le classi di risorse sono progettate per migliorare le prestazioni per le attività di gestione e modifica dei dati. Anche le query complesse possono trarre vantaggio dall'esecuzione in una classe di risorse di grandi dimensioni. Ad esempio, le prestazioni di query per i join e gli ordinamenti di grandi dimensioni possono migliorare quando la classe di risorse è sufficientemente grande da consentire l'esecuzione della query in memoria.

### <a name="operations-governed-by-resource-classes"></a>Operazioni regolate dalle classi di risorse

Le operazioni seguenti sono regolate dalle classi di risorse:

- INSERT-SELECT, UPDATE, DELETE
- SELEZIONARE (quando si esegue una query sulle tabelle utente)
- ALTER INDEX - REBUILD o REORGANIZE
- MODIFICA TABELLA RICOMPILAZIONE
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Caricamento dei dati
- Operazioni di spostamento dati condotte dal Servizio di spostamento dati (DMS)

> [!NOTE]  
> Le istruzioni SELECT per le viste a gestione dinamica (DMV) o altre viste del sistema non sono regolate da nessuno dei limiti di concorrenza. È possibile monitorare il sistema indipendentemente dal numero di query in esecuzione nel sistema.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operazioni non regolate dalle classi di risorse

Alcune query vengono sempre eseguite nella classe di risorse smallrc anche se l'utente è membro di una classe di risorse più grande. Le query esenti non vengono conteggiate ai fini del limite di concorrenza. Ad esempio, se il limite di concorrenza è 16, molti utenti possono selezionare le viste di sistema senza conseguenze per gli slot di concorrenza disponibili.

Le istruzioni seguenti sono esenti dalle classi di risorse e vengono sempre eseguite in smallrc:

- CREATE o DROP TABLE
- L'istruzione ALTER TABLE ... SWITCH, SPLIT o MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE o DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER o DROP USER
- CREATE, ALTER o DROP PROCEDURE
- CREATE o DROP VIEW
- INSERT VALUES
- SELECT da viste di sistema e DMV
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Slot di concorrenza

Gli slot di concorrenza sono un modo pratico per verificare le risorse disponibili per l'esecuzione di query. Hanno la stessa utilità dei biglietti per i concerti, che riservano i posti a sedere perché limitati. Il numero totale di slot concorrenza per ogni data warehouse è determinato dal livello di servizio. Per poter procedere con l'avvio dell'esecuzione, una query deve essere in grado di riservare slot di concorrenza sufficienti. Al completamento dell'esecuzione, la query rilascia i relativi slot di concorrenza.  

- Una query in esecuzione con 10 slot di concorrenza può accedere a un numero di risorse di calcolo 5 volte maggiore di una query in esecuzione con 2 slot di concorrenza.
- Se ogni query richiede 10 slot di concorrenza e sono disponibili 40 slot di concorrenza, solo 4 query possono essere eseguite contemporaneamente.

Solo le query che dipendono da una risorsa usano tutti gli slot di concorrenza. Le query di sistema e alcune query semplici non usano slot. Il numero esatto di slot di concorrenza usato è determinato dalla classe di risorse della query.

## <a name="view-the-resource-classes"></a>Visualizzare le classi di risorse

Le classi di risorse vengono implementate come ruoli predefiniti del database. Esistono due tipi di classi di risorse: statiche e dinamiche. Per visualizzare un elenco delle classi di risorse, usare la query seguente:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Modificare la classe di risorse di un utente

Le classi di risorse vengono implementate assegnando gli utenti ai ruoli del database. Quando un utente esegue una query, la query viene eseguita con la classe di risorse dell'utente. Ad esempio, se un utente è membro del ruolo del database staticrc10, le relative query vengono eseguite con piccole quantità di memoria. Se un utente del database è membro del ruolo xlargerc o staticrc80, le relative query vengono eseguite con grandi quantità di memoria.

Per aumentare la classe di risorse di un utente, usare [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per aggiungere l'utente a un ruolo del database di una classe di risorse di grandi dimensioni.  Il codice seguente aggiunge un utente al ruolo del database largerc.  Ogni richiesta ottiene il 22% della memoria di sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Per diminuire la classe di risorse, usare [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Se "loaduser" non è un membro di un'altra classe di risorse, rientra nella classe di risorse smallrc predefinita con una concessione di memoria del 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Precedenza delle classi di risorse

Gli utenti possono essere membri di più classi di risorse. Quando un utente appartiene a più di una classe di risorse:

- Le classi di risorse dinamiche hanno la precedenza sulle classi di risorse statiche. Ad esempio, se un utente è membro sia di mediumrc (dinamica) sia di staticrc80 (statica), le query vengono eseguite con mediumrc.
- Le classi di risorse più grandi hanno la precedenza sulle classi di risorse più piccole. Ad esempio, se un utente è membro di mediumrc e largerc, le query vengono eseguite con largerc. Analogamente, se un utente è membro sia di staticrc20, sia di statirc80, le query vengono eseguite con allocazioni di risorse staticrc80.

## <a name="recommendations"></a>Consigli

>[!NOTE]
>Si consiglia di sfruttare le funzionalità di gestione del carico di lavoro ([isolamento del carico di lavoro](sql-data-warehouse-workload-isolation.md), [classificazione](sql-data-warehouse-workload-classification.md) e [importanza](sql-data-warehouse-workload-importance.md)) per un maggior controllo sul carico di lavoro e prestazioni prevedibili.  
>
>

Si consiglia di creare un utente dedicato all'esecuzione di un tipo specifico di query o di operazione di caricamento. Assegnare a tale utente una classe di risorse permanente anziché modificare regolarmente la classe di risorse. Le classi di risorse statiche consentono un maggior controllo generale sul carico di lavoro, pertanto è consigliabile usare le classi di risorse statiche prima di considerare le classi di risorse dinamiche.

### <a name="resource-classes-for-load-users"></a>Classi di risorse per gli utenti del caricamento

`CREATE TABLE` usa indici columnstore cluster per impostazione predefinita. La compressione dei dati in un indice columnstore è un'operazione a uso intensivo di memoria e questo può ridurre la qualità dell'indice. L'utilizzo elevato della memoria può comportare la necessità di una classe di risorse superiore per il caricamento dei dati. Per garantire che sia disponibile una quantità di memoria sufficiente per il caricamento, è possibile creare un utente designato per l'esecuzione del caricamento e assegnare tale utente a una classe di risorse superiore.

La memoria necessaria per elaborare in modo efficiente i caricamenti dipende dalla natura della tabella caricata e dalle dimensioni dei dati. Per altre informazioni sui requisiti di memoria, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Dopo aver determinato i requisiti di memoria, scegliere se assegnare l'utente del caricamento a una classe di risorse statica o dinamica.

- Usare una classe di risorse statica quando i requisiti di memoria della tabella rientrano in un intervallo specifico. I caricamenti vengono eseguiti con la memoria appropriata. Quando si ridimensiona il data warehouse, i caricamenti non richiedono altra memoria. Se si usa una classe di risorse statica, le allocazioni di memoria rimangono costanti. Questa coerenza consente di conservare la memoria e di eseguire contemporaneamente più query. Si consiglia di fare in modo che le nuove soluzioni usino prima le classi di risorse statiche poiché offrono un maggiore controllo.
- Usare una classe di risorse dinamica quando i requisiti di memoria della tabella variano notevolmente. I caricamenti potrebbero richiedere più memoria rispetto alla quantità messa a disposizione dal livello attuale di DWU o DWU a elevato utilizzo di calcolo. Il ridimensionamento del data warehouse aggiunge altra memoria alle operazioni di caricamento, consentendo un'esecuzione più rapida di tali operazioni.

### <a name="resource-classes-for-queries"></a>Classi di risorse per le query

Alcune query sono a elevato utilizzo di calcolo e altre no.  

- Scegliere una classe di risorse dinamica quando le query sono complesse ma non richiedono una concorrenza elevata.  Ad esempio, la generazione di report giornalieri o settimanali richiede risorse in modo occasionale. Se i report elaborano grandi quantità di dati, il ridimensionamento del data warehouse rende disponibile una maggiore quantità di memoria per la classe di risorse esistente dell'utente.
- Scegliere una classe di risorse statica quando le aspettative di risorse variano nel corso della giornata. Ad esempio, una classe di risorse statica funziona bene quando molti utenti eseguono query nel data warehouse. Quando si ridimensiona il data warehouse, la quantità di memoria allocata all'utente non cambia. Di conseguenza, è possibile eseguire più query in parallelo nel sistema.

Una concessione di memoria appropriata dipende da molti fattori, come la quantità di dati sottoposti a query, la natura degli schemi di tabella e i vari predicati di gruppo, selezione e join. In generale, l'allocazione di più memoria consente di completare più rapidamente le query, ma riduce la concorrenza complessiva. Se la concorrenza non è un problema, un'allocazione eccessiva di memoria non influisce sulla velocità effettiva.

Per ottimizzare le prestazioni, utilizzare diverse classi di risorse. La sezione successiva presenta una stored procedure che consente di individuare la classe di risorse migliore.

## <a name="example-code-for-finding-the-best-resource-class"></a>Esempio di codice per la ricerca della classe di risorse migliore

È possibile usare la stored procedure specificata seguente per ottenere informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO e sulla classe di risorse migliore per operazioni CCI a elevato utilizzo di memoria su una tabella CCI non partizionata con una classe di risorse specifica:

Ecco lo scopo della stored procedure:

1. Visualizzare informazioni sulla concorrenza e sulla concessione di memoria per ogni classe di risorse in un determinato SLO. L'utente deve specificare NULL sia per lo schema che per il nome di tabella, come indicato nell'esempio.  
2. Visualizzare informazioni sulla classe di risorse migliore per operazioni CCI a elevato utilizzo di memoria (caricamento, copia di tabelle, ricompilazione dell'indice e così via) su una tabella CCI non partizionata con una classe di risorse specifica. La stored procedure usa lo schema di tabella per individuare la concessione di memoria necessaria.

### <a name="dependencies--restrictions"></a>Dipendenze e restrizioni

- Questa stored procedure non è progettata per calcolare i requisiti di memoria per una tabella CCI partizionata.
- Questa stored procedure non prende in considerazione i requisiti di memoria per la parte SELECT di un'istruzione CTAS/INSERT-SELECT e presuppone che si tratti di un'istruzione SELECT.
- Questa stored procedure usa una tabella temporanea che è disponibile nella sessione in cui è stata creata la stored procedure.
- Questa stored procedure dipende dalle risorse correnti (ad esempio, configurazione hardware e configurazione DMS) e in caso di modifiche non funzionerà più correttamente.  
- Questa stored procedure dipende dalle offerte di limiti di concorrenza esistenti e in caso di modifiche non funzionerà più correttamente.  
- Questa stored procedure dipende dalle offerte di classi di risorse esistenti e in caso di modifiche non funzionerà più correttamente.  

>[!NOTE]  
>Se non si ottiene alcun output dopo l'esecuzione della stored procedure con i parametri specificati, i motivi potrebbero essere due.
>
>1. Uno dei parametri di Data Warehouse contiene un valore SLO non valido
>2. Oppure, non esiste alcuna classe di risorse corrispondente per l'operazione CCI nella tabella.
>
>Ad esempio, con DW100c la concessione di memoria massima disponibile è 1 GB e lo schema di tabella è sufficientemente ampio per soddisfare il requisito di 1 GB.

### <a name="usage-example"></a>Esempio di utilizzo

Sintassi:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Fornire un parametro NULL per estrarre la DWU corrente dal database di data warehouse oppure fornire una DWU supportata nel formato "DW100c"
2. @SCHEMA_NAME: Fornire un nome di schema della tabella
3. @TABLE_NAME: Fornire un nome di tabella

Esempi di esecuzione di questa stored procedure:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

L'istruzione seguente crea l'elemento Table1 usato negli esempi precedenti.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione degli utenti e della sicurezza del database, vedere [Proteggere un database in Synapse SQL](sql-data-warehouse-overview-manage-security.md). Per altre informazioni su come le classi di risorse più grandi possono migliorare la qualità degli indici columnstore cluster, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
