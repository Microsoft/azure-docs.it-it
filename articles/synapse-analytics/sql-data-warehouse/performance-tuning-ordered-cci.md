---
title: Ottimizzazione delle prestazioni con indice columnstore cluster ordinato
description: Raccomandazioni e considerazioni che è necessario tenere presente quando si usa l'indice columnstore cluster ordinato per migliorare le prestazioni delle query in pool SQL dedicati.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: afb6efcee2ad4f5cf25a411eed353ff2fc27d75c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460796"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ottimizzazione delle prestazioni con indice columnstore cluster ordinato  

Quando gli utenti eseguono una query su una tabella columnstore in un pool SQL dedicato, l'utilità di ottimizzazione controlla i valori minimo e massimo archiviati in ogni segmento.  I segmenti che non rientrano nei limiti del predicato della query non vengono letti dal disco alla memoria.  Una query può ottenere prestazioni più veloci se il numero di segmenti da leggere e le dimensioni totali sono ridotte.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Confronto tra indice columnstore cluster ordinato e non ordinato

Per impostazione predefinita, per ogni tabella creata senza un'opzione di indice, un componente interno (Generatore di indici) crea un indice columnstore cluster non ordinato (CCI).  I dati in ogni colonna vengono compressi in un segmento CCI rowgroup separato.  Sono presenti metadati nell'intervallo di valori di ogni segmento, quindi i segmenti che non rientrano nei limiti del predicato della query non vengono letti dal disco durante l'esecuzione della query.  CCI offre il massimo livello di compressione dei dati e riduce le dimensioni dei segmenti da leggere, in modo che le query possano essere eseguite più velocemente. Tuttavia, poiché il generatore di indici non Ordina i dati prima di comprimerli in segmenti, è possibile che si verifichino segmenti con intervalli di valori sovrapposti, facendo in modo che le query leggano più segmenti dal disco e imprendano più tempo.  

Quando si crea una CCI ordinata, il motore del pool SQL dedicato Ordina i dati esistenti in memoria in base alle chiavi degli ordini prima che il generatore di indici li comprime in segmenti di indice.  Con i dati ordinati, la sovrapposizione dei segmenti è ridotta, consentendo alle query di avere un'eliminazione più efficiente del segmento e quindi prestazioni più veloci perché il numero di segmenti da leggere dal disco è inferiore.  Se tutti i dati possono essere ordinati in memoria contemporaneamente, è possibile evitare la sovrapposizione del segmento.  A causa di tabelle di grandi dimensioni nei data warehouse, questo scenario non si verifica spesso.  

Per verificare gli intervalli di segmenti per una colonna, eseguire il comando seguente con il nome della tabella e il nome della colonna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> In una tabella CCI ordinata, i nuovi dati risultanti dallo stesso batch di operazioni DML o di caricamento dei dati vengono ordinati all'interno del batch, non esiste alcun ordinamento globale in tutti i dati della tabella.  Gli utenti possono ricompilare la CCI ordinata per ordinare tutti i dati nella tabella.  Nel pool SQL dedicato, la ricompilazione dell'indice columnstore è un'operazione offline.  Per una tabella partizionata, la ricompilazione viene eseguita una partizione alla volta.  I dati della partizione che viene ricompilata sono "offline" e non sono disponibili fino al completamento della ricompilazione per la partizione. 

## <a name="query-performance"></a>Prestazioni delle query

Il miglioramento delle prestazioni di una query da una CCI ordinata dipende dai modelli di query, dalle dimensioni dei dati, dall'ordinamento dei dati, dalla struttura fisica dei segmenti e dalla classe di risorse e DWU scelta per l'esecuzione della query.  Gli utenti devono esaminare tutti questi fattori prima di scegliere le colonne di ordinamento durante la progettazione di una tabella CCI ordinata.

Le query con tutti questi modelli vengono in genere eseguite più velocemente con le CCI ordinate.  
1. Le query hanno predicati di uguaglianza, disuguaglianza o di intervallo
1. Le colonne del predicato e le colonne CCI ordinate sono le stesse.  
1. Le colonne del predicato vengono utilizzate nello stesso ordine dell'ordinale di colonna delle colonne CCI ordinate.  
 
In questo esempio, la tabella T1 include un indice columnstore cluster ordinato nella sequenza di Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Le prestazioni di query 1 possono trarre vantaggio dalla CCI ordinata rispetto alle altre tre query. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestazioni di caricamento dei dati

Le prestazioni del caricamento dei dati in una tabella CCI ordinata sono simili a quelle di una tabella partizionata.  Il caricamento dei dati in una tabella CCI ordinata può richiedere più tempo rispetto a una tabella CCI non ordinata a causa dell'operazione di ordinamento dei dati, ma le query possono essere eseguite più velocemente in seguito con l'istruzione CCI ordinata.  

Di seguito è riportato un esempio di confronto delle prestazioni di caricamento dei dati nelle tabelle con schemi diversi.

![Grafico a barre che mostra il confronto delle prestazioni del caricamento dei dati nelle tabelle con schemi diversi.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Di seguito è riportato un esempio di confronto delle prestazioni delle query tra CCI e CCI ordinato.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Riduzione della sovrapposizione del segmento

Il numero di segmenti sovrapposti dipende dalle dimensioni dei dati da ordinare, dalla memoria disponibile e dall'impostazione del grado massimo di parallelismo (MAXDOP) durante la creazione di CCI ordinata. Di seguito sono riportate le opzioni per ridurre la sovrapposizione del segmento durante la creazione di CCI ordinati.

- Usare la classe di risorse xlargerc in un DWU superiore per consentire una maggiore quantità di memoria per l'ordinamento dei dati prima che il generatore di indici comprime i dati in segmenti.  Una volta in un segmento di indice, la posizione fisica dei dati non può essere modificata.  Nessun ordinamento dei dati all'interno di un segmento o tra più segmenti.  

- Creare una CCI ordinata con MAXDOP = 1.  Ogni thread usato per la creazione di CCI ordinati funziona su un subset di dati e lo ordina localmente.  Non esiste alcun ordinamento globale tra i dati ordinati in base a thread diversi.  L'uso di thread paralleli può ridurre il tempo necessario per creare un'CCI ordinata, ma genererà più segmenti sovrapposti rispetto all'uso di un singolo thread.  Attualmente, l'opzione MAXDOP è supportata solo per la creazione di una tabella CCI ordinata usando CREATE TABLE come comando SELECT.  La creazione di una CCI ordinata tramite i comandi CREATE INDEX o CREATE TABLE non supporta l'opzione MAXDOP. Ad esempio,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Pre-ordinare i dati in base alle chiavi di ordinamento prima di caricarle nelle tabelle.

Di seguito è riportato un esempio di una distribuzione della tabella CCI ordinata con un segmento zero che si sovrappone alle raccomandazioni precedenti. La tabella CCI ordinata viene creata in un database DWU1000c tramite CTAS da una tabella heap da 20 GB con MAXDOP 1 e xlargerc.  Il valore CCI viene ordinato in una colonna BIGINT senza duplicati.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Creare CCI ordinati in tabelle di grandi dimensioni

La creazione di una CCI ordinata è un'operazione offline.  Per le tabelle senza partizioni, i dati non saranno accessibili agli utenti fino al completamento del processo di creazione di CCI ordinato.   Per le tabelle partizionate, poiché il motore crea la partizione CCI ordinata per partizione, gli utenti possono comunque accedere ai dati nelle partizioni in cui la creazione di CCI ordinata non è in corso.   È possibile utilizzare questa opzione per ridurre al minimo il tempo di inattività durante la creazione di CCI ordinata su tabelle di grandi dimensioni: 

1.    Creare partizioni nella tabella di grandi dimensioni di destinazione (denominata Table_A).
2.    Creare una tabella CCI ordinata vuota (denominata Table_B) con la stessa tabella e lo stesso schema di partizione della tabella A.
3.    Passare una partizione dalla tabella A alla tabella B.
4.    Eseguire ALTER INDEX <Ordered_CCI_Index> in <Table_B> RICOMPILA partizione = <Partition_ID> nella tabella B per ricompilare la partizione commutata.  
5.    Ripetere i passaggi 3 e 4 per ogni partizione in Table_A.
6.    Una volta passate tutte le partizioni da Table_A a Table_B e ricompilate, eliminare Table_A e rinominare Table_B Table_A. 

## <a name="examples"></a>Esempi

**Un. Per verificare la presenza di colonne ordinate e ordinale:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Per modificare il numero ordinale di colonna, aggiungere o rimuovere colonne dall'elenco degli ordini oppure per passare da CCI a ordered CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
