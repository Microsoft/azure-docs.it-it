---
title: Procedure consigliate per i pool SQL dedicati
description: Raccomandazioni e procedure consigliate da tenere presente durante l'utilizzo di pool SQL dedicati.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 03/17/2021
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b11a76be94fc52285482e13dadbc8c7c92af1374
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609789"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Procedure consigliate per i pool SQL dedicati in Azure sinapsi Analytics

Questo articolo fornisce una raccolta di procedure consigliate che consentono di ottenere prestazioni ottimali per i pool SQL dedicati in Azure sinapsi Analytics. Di seguito sono descritte alcune indicazioni di base e aree importanti su cui concentrarsi mentre si realizza la soluzione. Ogni sezione presenta un concetto e rimanda ad articoli più dettagliati che analizzano il concetto in modo più approfondito.

## <a name="dedicated-sql-pools-loading"></a>Caricamento pool SQL dedicati

Per informazioni aggiuntive sul caricamento di pool SQL dedicati, vedere [linee guida per il caricamento dei dati](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire il calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Gestire le statistiche

Il pool SQL dedicato può essere configurato per rilevare e creare automaticamente statistiche sulle colonne.  La qualità dei piani di query creati dall'utilità di ottimizzazione dipende dalla qualità delle statistiche disponibili.  

È consigliabile abilitare AUTO_CREATE_STATISTICS per i database e mantenere aggiornate le statistiche ogni giorno o dopo ogni caricamento per assicurarsi che le statistiche sulle colonne utilizzate nelle query siano sempre aggiornate.

Per accorciare il tempo di gestione delle statistiche, è necessario selezionare in base alle colonne con statistiche o che hanno bisogno di essere aggiornate con più frequenza. Ad esempio potrebbe essere consigliabile aggiornare le colonne di data, in cui potrebbero venire aggiunti nuovi valori ogni giorno. Concentrarsi su statistiche per le colonne usate nei join, nella clausola WHERE e presenti in GROUP BY.

Altre informazioni sulle statistiche sono disponibili negli articoli [Gestire le statistiche delle tabelle](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ottimizzare le prestazioni delle query con nuovi miglioramenti del prodotto

- [Ottimizzazione delle prestazioni con viste materializzate](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati](../sql-data-warehouse/performance-tuning-result-set-caching.md)


## <a name="group-insert-statements-into-batches"></a>Raggruppare le istruzioni INSERT in batch

Un caricamento in una sola volta in una tabella di piccole dimensioni con un'istruzione INSERT, ad esempio `INSERT INTO MyLookup VALUES (1, 'Type 1')`, può essere l'approccio migliore a seconda delle esigenze. Se tuttavia è necessario caricare migliaia o milioni di righe nel corso della giornata, è probabile che singole operazioni INSERT non siano ottimali.

Un modo per risolvere il problema consiste nello sviluppare un processo che scriva in un file e un altro processo che carichi periodicamente tale file. Per altre informazioni, fare riferimento all'articolo [INSERT](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utilizzare PolyBase per caricare ed esportare rapidamente i dati

Il pool SQL dedicato supporta il caricamento e l'esportazione di dati tramite diversi strumenti, tra cui Azure Data Factory, polibase e BCP.  Per piccole quantità di dati in cui le prestazioni non sono fondamentali, qualsiasi strumento può essere adeguato per soddisfare le esigenze.  

> [!NOTE]
> PolyBase è la scelta migliore quando si caricano o si esportano grandi volumi di dati oppure servono prestazioni più veloci.

Le operazioni di caricamento di PolyBase possono essere eseguite usando CTAS o INSERT INTO. CTAS riduce al minimo la registrazione delle transazioni ed è il modo più rapido per caricare i dati. Azure Data Factory supporta anche i caricamenti PolyBase e può raggiungere prestazioni simili a quelle di CTAS. PolyBase supporta vari formati di file, inclusi i file con estensione gzip.

Per aumentare al massimo la velocità effettiva quando si usano file di testo con estensione gzip, suddividere i file in 60 o più file per ottimizzare il parallelismo del caricamento. Per una velocità effettiva totale maggiore, prendere in considerazione il caricamento simultaneo dei dati. Altre informazioni relative agli argomenti di cui si parla in questa sezione sono incluse negli articoli seguenti:

- [Caricare i dati](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Guida per l'uso di PolyBase](data-loading-best-practices.md)
- [Modelli e strategie di caricamento del pool SQL dedicati](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Caricare i dati con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Spostare dati con Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create Table As Select (CTAS) in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Caricare ed eseguire query su tabelle esterne

PolyBase non è ottimale per le query. Le tabelle di base per i pool SQL dedicati attualmente supportano solo file BLOB di Azure e archiviazione Azure Data Lake. Questi file non sono supportati da alcuna risorsa di calcolo. Di conseguenza, i pool SQL dedicati non possono eseguire l'offload di questo lavoro ed è necessario che leggano l'intero file caricando il file in tempdb in modo che sia in grado di leggere i dati.

In presenza di numerose query relative a questi dati, è consigliabile caricare i dati una volta ed eseguire le query tramite la tabella locale. Altre informazioni su PolyBase sono incluse nell'articolo [Guida all'uso di PolyBase](data-loading-best-practices.md).

## <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.   Con questa impostazione predefinita, per gli utenti è più facile iniziare a creare tabelle senza dover decidere come devono essere distribuite. Le tabelle round robin possono garantire prestazioni sufficienti per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi una colonna di distribuzione fornisce prestazioni migliori.  

L'esempio più comune di tabella distribuita da una colonna che offre prestazioni superiori rispetto a una tabella round robin è quando viene creato un join di due grandi tabelle dei fatti.  

Ad esempio, se sono presenti una tabella degli ordini distribuita in base a order_id e una tabella delle transazioni, anch'essa distribuita in base a order_id, quando si crea un join tra le due tabelle in order_id, la query diventa una query pass-through. Vengono quindi eliminate le operazioni di spostamento dati. Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query. Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!TIP]
> Quando si carica una tabella distribuita, i dati in ingresso non devono essere ordinati in base alla chiave di distribuzione. Questa operazione rallenterà i caricamenti.

I collegamenti agli articoli riportati di seguito forniranno altri dettagli sul miglioramento delle prestazioni tramite la selezione di una colonna di distribuzione. Sono inoltre disponibili informazioni su come definire una tabella distribuita nella clausola WITH dell'istruzione CREATE TABLE:

- [Panoramica delle tabelle](develop-tables-overview.md)
- [Distribuzione di tabelle in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [celta della modalità di distribuzione delle tabelle](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni

Anche se il partizionamento dei dati può essere efficace per la gestione dei dati tramite il cambio di partizione o l'ottimizzazione delle analisi con l'eliminazione delle partizioni, un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL dedicato.  

Un numero eccessivo di partizioni può ridurre l'efficacia degli indici columnstore in cluster se ogni partizione contiene meno di 1 milione di righe. i pool SQL dedicati partizionano automaticamente i dati in database 60. Quindi, se si crea una tabella con 100 partizioni, il risultato sarà 6000 partizioni. Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

Una delle opzioni da considerare è l'uso di una granularità inferiore a quella implementata usando SQL Server. Ad esempio, prendere in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Altre informazioni sul partizionamento sono illustrate in dettaglio nell'articolo [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

In una transazione vengono eseguite le istruzioni INSERT, UPDATE e DELETE. Quando hanno esito negativo, occorre eseguire il rollback. Per ridurre il rischio di un rollback prolungato, ridurre al minimo le dimensioni delle transazioni, quando possibile.  Tale riduzione può essere eseguita suddividendo in parti le istruzioni INSERT, UPDATE e DELETE. Se, ad esempio, si dispone di un'istruzione INSERT che si prevede richieda 1 ora, è possibile suddividerla in quattro parti. Ogni esecuzione verrà accorciata a 15 minuti.  

> [!TIP]
> Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  Ad esempio, anziché eseguire un'istruzione DELETE per eliminare tutte le righe di una tabella in cui order_date era nel mese di ottobre 2001, è possibile partizionare i dati mensilmente. È quindi possibile scambiare la partizione con dati con una partizione vuota di un'altra tabella (vedere gli esempi ALTER TABLE).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire, perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Altre informazioni sui contenuti descritti in questa sezione sono incluse negli articoli seguenti:

- [Create Table As Select (CTAS) in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Informazioni sulle transazioni](develop-transactions.md)
- [Ottimizzazione delle transazioni per SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Ridurre le dimensioni dei risultati delle query

La riduzione delle dimensioni dei risultati delle query contribuisce a evitare problemi lato client causati da numerosi risultati delle query.  È possibile modificare la query in modo da ridurre il numero di righe restituite. Alcuni strumenti per la generazione di query consentono di aggiungere la sintassi "top N" a ogni query.  È anche possibile usare la sintassi CETAS sui risultati delle query per creare una tabella temporanea e poi usare l'esportazione PolyBase per l'elaborazione di livello inferiore.

## <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo consiglio è particolarmente importante per le colonne CHAR e VARCHAR.  Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente invece di usare NVARCHAR.

Per una revisione più dettagliata dei concetti fondamentali relativi alle informazioni precedenti, vedere gli articoli [Panoramica delle tabelle](develop-tables-overview.md), [Tipi di dati delle tabelle](develop-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utilizzare tabelle heap temporanee per i dati temporanei

Quando si inseriscono temporaneamente i dati nei pool SQL dedicati, le tabelle heap in genere rendono più veloce il processo complessivo.  Se si caricano dati solo per la gestione temporanea prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore in cluster.  

Il caricamento dei dati in una tabella temporanea sarà molto più veloce del caricamento di una tabella in un archivio permanente.  Le tabelle temporanee iniziano con "#" e può accedervi solo la sessione che le ha create. Di conseguenza, potrebbero funzionare solo in scenari limitati. Le tabelle heap sono definite nella clausola WITH di CREATE TABLE.  Se si usa una tabella temporanea, ricordarsi di creare le statistiche anche su tale tabella temporanea.

Per altre istruzioni, vedere gli articoli [Tabelle temporanee](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati in un pool SQL dedicato.  Per impostazione predefinita, le tabelle nel pool SQL dedicato vengono create come cluster columnstore.  Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità dei segmenti si può misurare in base al numero di righe in un gruppo di righe compresso. Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore in cluster, vedere la sezione [Possibili cause di una qualità scadente dell'indice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) nell'articolo [Indici delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Generalmente le tabelle columnstore non effettuano il push dei dati in un segmento columnstore compresso fino a quando non è presente più di 1 milione di righe per tabella. Ogni tabella del pool SQL dedicata è partizionata in 60 tabelle. Di conseguenza, le tabelle columnstore non trarranno vantaggio da una query, a meno che la tabella non contenga più di 60 milioni di righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni di righe, la presenza di un indice columnstore potrebbe non essere la soluzione ottimale.  

Se i dati vengono partizionati, ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore in cluster.  Una tabella con 100 partizioni deve contenere almeno 6 miliardi di righe per poter trarre vantaggio da un archivio di colonne in cluster (60 distribuzioni *100 partizioni* 1 milione di righe).  

Se la tabella non contiene 6 miliardi di righe, esistono due opzioni principali: ridurre il numero di partizioni o prendere il considerazione l'uso di una tabella heap.  Potrebbe anche essere utile fare delle prove per verificare se è possibile ottenere prestazioni migliori usando una tabella heap con indici secondari al posto di una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  Altre informazioni sugli indici columnstore e tabella sono disponibili negli articoli seguenti:
- [Indicizzazione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [ricompilazione degli indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) 
- [Ottimizzazione delle prestazioni con indice columnstore cluster ordinato](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utilizzare una classe di risorse più grande per migliorare le prestazioni delle query

I pool SQL usano i gruppi di risorse come modo per allocare memoria alle query. Inizialmente, tutti gli utenti vengono assegnati alla classe di risorse piccola, che garantisce 100 MB di memoria per distribuzione.  Sono sempre presenti 60 distribuzioni. A ogni distribuzione sono assegnati almeno 100 MB. L'allocazione di memoria totale a livello di sistema è di 6000 MB o appena inferiore a 6 GB.  

Alcune query, ad esempio i join di grandi dimensioni oppure le operazioni di caricamento in tabelle columnstore cluster, risultano avvantaggiate da allocazioni di una quantità maggiore di memoria.  Alcune query, come le operazioni di sola analisi, non traggono alcun vantaggio. L'uso di classi di risorse di dimensioni maggiori incide sulla concorrenza. Quindi, prima di spostare tutti gli utenti in una classe di risorse di grandi dimensioni, è necessario tenere a mente tutti questi fattori.

Per altre informazioni sulle classi di risorse, fare riferimento all'articolo [Classi di risorse per la gestione del carico di lavoro](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usare una classe di risorse più piccola per aumentare la concorrenza

Se si nota un ritardo prolungato nelle query, è possibile che gli utenti stiano usando classi di risorse più grandi. Questo scenario promuove il consumo di slot della concorrenza, che può causare l'accodamento di altre query.  Per determinare se le query degli utenti sono in coda, eseguire `SELECT * FROM sys.dm_pdw_waits` per verificare se vengono restituite righe.

Altre informazioni sono disponibili negli articoli [Classi di risorse per la gestione del carico di lavoro](../sql-data-warehouse/resource-classes-for-workload-management.md) e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utilizzare DMV per monitorare e ottimizzare le query

I pool SQL dedicati hanno diverse DMV che possono essere usate per monitorare l'esecuzione delle query.  L'articolo relativo al monitoraggio indicato di seguito fornisce istruzioni dettagliate su come visualizzare i dati di una query in esecuzione.  Per trovare rapidamente le query in queste DMV, può essere utile usare l'opzione LABEL con le query. Per altre informazioni dettagliate, consultare gli articoli elencati di seguito:

- [Monitoraggio del carico di lavoro mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Passaggi successivi

Consultare anche l'articolo [Risoluzione dei problemi](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) con i problemi comuni e le relative soluzioni.

Se sono necessarie informazioni non fornite in questo articolo, eseguire una ricerca nella pagina Domande e risposte di [Microsoft&una domanda relativa a sinapsi di Azure](/answers/topics/azure-synapse-analytics.html) è una posizione in cui è possibile porre domande ad altri utenti e al gruppo di prodotto analisi di sinapsi di Azure.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è anche presente un forum di [analisi delle sinapsi di Azure stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse).

Per le richieste di funzionalità, usare la pagina [feedback di Azure sinapsi Analytics](https://feedback.azure.com/forums/307516-sql-data-warehouse) .  L'aggiunta di richieste o il voto di altre richieste consente di concentrarsi sulle funzionalità più richieste.