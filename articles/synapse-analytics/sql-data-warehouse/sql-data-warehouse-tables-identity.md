---
title: Uso di IDENTITY per la creazione di chiavi surrogate
description: Suggerimenti ed esempi per l'uso della proprietà IDENTITY per creare chiavi surrogate nelle tabelle nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 60f2e3f949a4f627839a07137ebaf77518db87a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213976"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Uso di IDENTITY per la creazione di chiavi surrogate nel pool SQL sinapsi

In questo articolo sono disponibili indicazioni ed esempi per l'uso della proprietà IDENTITY per la creazione di chiavi surrogate nelle tabelle nel pool SQL sinapsi.

## <a name="what-is-a-surrogate-key"></a>Che cos'è una chiave surrogata

Una chiave surrogata in una tabella è una colonna con un identificatore univoco per ogni riga. La chiave non viene generata dai dati della tabella. I progettisti di modelli di dati preferiscono creare chiavi surrogate nelle tabelle durante la progettazione dei modelli per i data warehouse. È possibile usare la proprietà IDENTITY per raggiungere questo obiettivo in modo semplice ed efficace senza effetti sulle prestazioni di caricamento.  

## <a name="creating-a-table-with-an-identity-column"></a>Creazione di una tabella con una colonna IDENTITY

La proprietà IDENTITY è progettata per scalare orizzontalmente tutte le distribuzioni nel pool SQL sinapsi senza influire sulle prestazioni di caricamento. Pertanto, l'implementazione di IDENTITY è orientata al raggiungimento di questi obiettivi.

È possibile definire una tabella con la proprietà IDENTITY al momento della creazione, usando una sintassi simile all'istruzione seguente:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

È quindi possibile usare `INSERT..SELECT` per popolare la tabella.

La parte rimanente di questa sezione illustra le varie sfumature dell'implementazione per comprenderle appieno.  

### <a name="allocation-of-values"></a>Allocazione dei valori

La proprietà IDENTITY non garantisce l'ordine di allocazione dei valori surrogati, in modo conforme al comportamento di SQL Server e del database SQL di Azure. Tuttavia, nel pool di SQL sinapsi, l'assenza di una garanzia è più evidente.

L'esempio seguente è una dimostrazione:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Nell'esempio precedente due righe raggiungono la distribuzione 1. La prima riga ha il valore surrogato 1 nella colonna `C1` e la seconda riga ha il valore surrogato 61. Entrambi questi valori sono stati generati dalla proprietà IDENTITY. L'allocazione dei valori non è tuttavia contigua. Questo comportamento dipende dalla progettazione.

### <a name="skewed-data"></a>Dati asimmetrici

L'intervallo di valori per il tipo di dati è distribuito uniformemente tra le distribuzioni. Nel caso di una tabella distribuita con dati asimmetrici, l'intervallo di valori disponibili per il tipo di dati può esaurirsi in modo anomalo. Ad esempio, se tutti i dati vengono destinati a una singola distribuzione, la tabella ha effettivamente accesso solo a un sessantesimo dei valori del tipo di dati. Per questo motivo, la proprietà IDENTITY è limitata solo ai tipi di dati `INT` e `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO

Quando una colonna IDENTITY esistente viene selezionata in una nuova tabella, la nuova colonna eredita la proprietà IDENTITY, a meno che non sia vera una delle condizioni seguenti:

- L'istruzione SELECT contiene un join.
- Più istruzioni SELECT sono unite in join tramite l'operatore UNION.
- La colonna IDENTITY è elencata più di una volta nell'elenco SELECT.
- La colonna IDENTITY fa parte di un'espressione.

Se una di queste condizioni risulta vera, la colonna viene creata come colonna NOT NULL, anziché ereditare la proprietà IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) ha lo stesso comportamento di SQL Server documentato per SELECT... INTO. Tuttavia, non è possibile specificare una proprietà IDENTITY nella definizione di colonna della parte `CREATE TABLE` dell'istruzione. Non è possibile nemmeno usare la funzione IDENTITY nella parte `SELECT` dell'istruzione CTAS. Per popolare una tabella, è necessario usare l'istruzione `CREATE TABLE` per definire la tabella, seguita da `INSERT..SELECT` per popolarla.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Inserimento in modo esplicito di valori in una colonna IDENTITY

Il pool SQL sinapsi supporta la `SET IDENTITY_INSERT <your table> ON|OFF` sintassi. È possibile usare questa sintassi per inserire in modo esplicito i valori nella colonna IDENTITY.

Molti progettisti di modelli di dati preferiscono usare valori negativi predefiniti per alcune righe nelle dimensioni. Un esempio è la riga -1 o "membro sconosciuto".

Il prossimo script mostra come aggiungere in modo esplicito questa riga tramite SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Caricamento dei dati

La presenza della proprietà IDENTITY ha alcune implicazioni per il codice di caricamento dei dati. Questa sezione evidenzia alcuni modelli di base per il caricamento di dati nelle tabelle usando IDENTITY.

Per caricare dati in una tabella e generare una chiave surrogata con IDENTITY, creare la tabella e quindi usare INSERT... SELECT o INSERT... VALUES per eseguire il caricamento.

L'esempio seguente illustra il modello di base:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Non è attualmente possibile usare `CREATE TABLE AS SELECT` per il caricamento di dati in una tabella con una colonna IDENTITY.
>

Per altre informazioni sul caricamento dei dati, vedere [progettazione di Extract, Load e Transform (ELT) per il pool SQL sinapsi](design-elt-data-loading.md) e [caricamento delle procedure consigliate](guidance-for-loading-data.md).

## <a name="system-views"></a>Viste di sistema

È possibile usare la vista del catalogo [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per identificare una colonna che dispone della proprietà IDENTITY.

Per comprendere meglio lo schema del database, questo esempio mostra come integrare sys.identity_columns con altre viste del catalogo di sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitazioni

Non è possibile usare la proprietà IDENTITY:

- Quando il tipo di dati della colonna non è INT o BIGINT
- Quando la colonna è anche la chiave di distribuzione
- Quando la tabella è una tabella esterna

Le funzioni correlate seguenti non sono supportate nel pool SQL sinapsi:

- [IDENTITY ()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>Attività comuni

Questa sezione include esempi di codice utilizzabili per eseguire attività comuni quando si usano colonne IDENTITY.

La colonna C1 è la colonna IDENTITY in tutte le attività seguenti.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Individuare il valore massimo allocato per una tabella

Usare la funzione `MAX()` per determinare il valore massimo allocato per una tabella distribuita:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Trovare il valore di seeding e incremento per la proprietà IDENTITY

È possibile usare le viste del catalogo per individuare i valori di configurazione di incremento e seeding di IDENTITY per una tabella usando la query seguente:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle tabelle](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) IDENTITY (proprietà)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
