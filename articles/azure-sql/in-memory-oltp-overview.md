---
title: Tecnologie in memoria
description: Le tecnologie in memoria migliorano significativamente le prestazioni dei carichi di lavoro transazionali e analitici nel database SQL di Azure e in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 43527e8e5860e0bbfc50643210156be943d2f174
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985191"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ottimizzare le prestazioni usando le tecnologie in memoria nel database SQL di Azure e in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Le tecnologie in memoria consentono di migliorare le prestazioni dell'applicazione e di ridurre potenzialmente i costi del database.

## <a name="when-to-use-in-memory-technologies"></a>Quando utilizzare le tecnologie in memoria

Grazie alle tecnologie in memoria, è possibile ottenere miglioramenti delle prestazioni con vari carichi di lavoro:

- **Transazionale** (elaborazione transazionale online o OLTP), in cui la maggior parte delle richieste esegue la lettura o l'aggiornamento di set di dati più piccoli (ad esempio, le operazioni CRUD).
- **Analitico** (elaborazione analitica online o OLAP), in cui la maggior parte delle query contiene calcoli complessi per la creazione di report, con un determinato numero di query che caricano e aggiungono dati nelle tabelle esistenti (il cosiddetto caricamento bulk) oppure eliminano dati dalle tabelle.
- **Misto** (elaborazione ibrida transazione/analitica o HTAP), in cui vengono eseguite query OLTP e OLAP sullo stesso set di dati.

Le tecnologie in memoria possono migliorare le prestazioni di questi carichi di lavoro mantenendo in memoria i dati che devono essere elaborati, tramite la compilazione nativa delle query o un'elaborazione avanzata come l'elaborazione batch e le istruzioni SIMD disponibili sull'hardware sottostante.

## <a name="overview"></a>Panoramica

Il database SQL di Azure e Azure SQL Istanza gestita hanno le tecnologie in memoria seguenti:

- *[OLTP in memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aumenta il numero di transazioni al secondo e riduce la latenza per l'elaborazione delle transazioni. Gli scenari che beneficiano dell'OLTP in memoria sono: elaborazione transazionale ad alta velocità di elaborazione, come l'inserimento di dati commerciali e da videogiochi, da eventi o dispositivi IoT, il caching, il caricamento di dati, le tabelle temporanee e gli scenari con variabili di tabella.
- Gli *indici columnstore cluster* riducono fino a 10 volte il footprint della memoria e migliorano le prestazioni delle query di reporting e analisi. È possibile usare gli indici con tabelle dei fatti nei data mart per inserire più dati nel database e migliorare le prestazioni. Gli indici possono anche essere usati con i dati cronologici nel database operativo per archiviare ed essere in grado di eseguire una query su una quantità di dati 10 volte superiore.
- Gli *indici columnstore non cluster* per HTAP consentono di ottenere in tempo reale informazioni approfondite sull'azienda eseguendo una query direttamente sul database operativo, senza la necessità di eseguire un processo ETL dispendioso e attendere che il data warehouse venga popolato. Gli indici columnstore non cluster consentono l'esecuzione rapida delle query di analisi nei database OLTP, riducendo l'impatto sul carico di lavoro operativo.
- Gli *indici columnstore cluster ottimizzati per la memoria* per HTAP consentono di elaborare le transazioni e *al contempo* di eseguire le query di analisi sugli stessi dati in tempi estremamente rapidi.

Gli indici columnstore e OLTP in memoria fanno parte di SQL Server rispettivamente dal 2012 e dal 2014. Il database SQL di Azure, Azure SQL Istanza gestita e SQL Server condividono la stessa implementazione delle tecnologie in memoria.

## <a name="benefits-of-in-memory-technology"></a>Vantaggi della tecnologia in memoria

Grazie alla maggiore efficienza di elaborazione delle query e delle transazioni, le tecnologie in memoria consentono anche di ridurre i costi. In genere non è necessario aggiornare il piano tariffario del database per migliorare le prestazioni. In alcuni casi, potrebbe anche essere possibile ridurre il piano tariffario, visualizzando al tempo stesso miglioramenti delle prestazioni con le tecnologie in memoria.

Di seguito sono riportati due esempi che mostrano come la tecnologia OLTP in memoria abbia contribuito a migliorare significativamente le prestazioni.

- Sfruttando la tecnologia OLTP in memoria [Quorum Business Solutions è riuscita a raddoppiare il carico di lavoro migliorando i valori DTU del 70%](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- Il video seguente illustra un miglioramento significativo nell'utilizzo delle risorse con un carico di lavoro di esempio: [video di OLTP in memoria](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Per altre informazioni, vedere il post di Blog relativo [a OLTP in memoria](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) .

> [!NOTE]  
> Le tecnologie in memoria sono disponibili nei livelli Premium e business critical.

Il video seguente illustra il potenziale miglioramento delle prestazioni con le tecnologie in memoria. Tenere presente che il miglioramento delle prestazioni dipende sempre da numerosi fattori, tra cui la natura del carico di lavoro e dei dati, il modello di accesso del database e così via.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Questo articolo descrive gli aspetti di OLTP in memoria e di indici columnstore specifici del database SQL di Azure e di Azure SQL Istanza gestita e include anche esempi:

- Viene analizzato l'impatto di queste tecnologie sulla memoria e i limiti sulle dimensioni dei dati.
- Verrà illustrato come gestire lo spostamento dei database che sfruttano queste tecnologie tra i diversi piani tariffari.
- Verranno visualizzati due esempi che illustrano l'uso di OLTP in memoria, nonché gli indici columnstore.

Per ulteriori informazioni sulla SQL Server in memoria, vedere:

- [Panoramica e scenari di utilizzo](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios), inclusi riferimenti a casi di studio sui clienti e informazioni introduttive
- [OLTP in memoria](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guida agli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview)
- L'elaborazione analitica e transazionale ibrida (HTAP), anche nota come [analisi operativa in tempo reale](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>OLTP in memoria

La tecnologia OLTP in memoria offre operazioni di accesso ai dati estremamente veloci mantenendo tutti i dati in memoria. Usa inoltre indici specializzati, la compilazione nativa delle query e un accesso ai dati privo di latch per migliorare le prestazioni del carico di lavoro OLTP. Esistono due modi per organizzare i dati OLTP in memoria:

- Il formato **rowstore ottimizzato per la memoria**, in cui ogni riga è un oggetto di memoria distinto. Questo è un classico formato OLTP in memoria ottimizzato per carichi di lavoro OLTP ad alte prestazioni. Esistono due tipi di tabelle ottimizzate per la memoria che possono essere usate nel formato rowstore ottimizzato per la memoria:

  - Le *tabelle durevoli* (SCHEMA_AND_DATA), in cui le righe inserite nella memoria vengono mantenute dopo il riavvio del server. Questo tipo di tabelle si comporta come una tabella rowstore tradizionale con i vantaggi aggiuntivi delle ottimizzazioni in memoria.
  - Le *tabelle non durevoli* (SCHEMA_ONLY) in cui le righe non vengono mantenute dopo il riavvio. Questo tipo di tabella è progettato per i dati temporanei (ad esempio, la sostituzione di tabelle temporanee) o per le tabelle in cui è necessario caricare rapidamente i dati prima di spostarli in una tabella persistente (le cosiddette tabelle di staging).

- Il formato **columnstore ottimizzato per la memoria**, in cui dati sono organizzati in un formato a colonne. Questa struttura è progettata per gli scenari HTAP in cui è necessario eseguire query di analisi sulla stessa struttura dei dati in cui è in esecuzione il carico di lavoro OLTP.

> [!Note]
> La tecnologia OLTP in memoria è progettata per le strutture dei dati che possono risiedere completamente in memoria. Poiché non è possibile eseguire l'offload su disco dei dati in memoria, assicurarsi di usare un database che disponga di memoria sufficiente. Per altre informazioni, vedere [Limite su dimensioni dei dati e archiviazione per OLTP in memoria](#data-size-and-storage-cap-for-in-memory-oltp).

Guida introduttiva di OLTP in memoria: avvio rapido [1: tecnologie OLTP in memoria per migliorare le prestazioni di T-SQL](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (un altro articolo per iniziare)

Video di approfondimento sulle tecnologie:

- [OLTP in memoria](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (che contiene una demo dei vantaggi e delle procedure per la riproduzione di questi risultati)
- [Video OLTP in memoria: Che cos'è e come/quando usarlo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Esiste un modo a livello di codice per capire se un determinato database supporta OLTP in memoria. È possibile eseguire la query Transact-SQL seguente:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se la query restituisce **1**, OLTP in memoria è supportato nel database. Le query seguenti identificano tutti gli oggetti che devono essere rimossi prima di poter eseguire il downgrade di un database a per utilizzo generico, standard o Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limite su dimensioni dei dati e archiviazione per OLTP in memoria

OLTP in memoria include tabelle ottimizzate per la memoria che vengono usate per archiviare i dati utente. Queste tabelle devono rientrare nella memoria. Poiché la memoria viene gestita direttamente nel database SQL, si ha il concetto di quota per i dati utente. Questo concetto è definito *archiviazione di OLTP in memoria*.

Ogni piano tariffario relativo a database singoli e pool elastici supportati include una certa quantità di spazio di archiviazione OLTP in memoria.

- [Limiti delle risorse basate su DTU-database singolo](database/resource-limits-dtu-single-databases.md)
- [Limiti delle risorse basate su DTU-pool elastici](database/resource-limits-dtu-elastic-pools.md)
- [Limiti delle risorse basate su vCore - database singoli](database/resource-limits-vcore-single-databases.md)
- [Limiti delle risorse basate su vCore - pool elastici](database/resource-limits-vcore-elastic-pools.md)
- [limiti delle risorse basate su vCore-istanza gestita](managed-instance/resource-limits.md)

Gli elementi seguenti rientrano nel limite di archiviazione di OLTP in memoria:

- Righe di dati utente attive nelle tabelle ottimizzate per la memoria e variabili di tabella. Si noti che le versioni precedenti della riga non vengono conteggiate nel limite.
- Indici nelle tabelle ottimizzate per la memoria.
- Costi operativi delle operazioni ALTER TABLE.

Se si raggiunge il limite, si riceve un errore di superamento della quota e non sarà più possibile inserire o aggiornare dati. Per risolvere il problema, eliminare i dati o aumentare il piano tariffario del database o del pool.

Per informazioni dettagliate sul monitoraggio dell'utilizzo dello spazio di archiviazione OLTP in memoria e sulla configurazione degli avvisi quando si raggiunge quasi il limite, vedere [monitorare l'archiviazione in memoria](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Informazioni sui pool elastici

Con i pool elastici, lo spazio di archiviazione OLTP in memoria è condiviso tra tutti i database nel pool. Ne consegue che l'utilizzo in un database può potenzialmente influire sugli altri database. Esistono due metodi per la risoluzione di questo problema:

- Configurare per i database un valore `Max-eDTU` o `MaxvCore` inferiore al numero di eDTU o vCore configurati per l'intero pool. Ciò limita l'uso dello spazio di archiviazione OLTP in memoria in qualsiasi database del pool alla dimensione corrispondente al numero di eDTU.
- Configurare `Min-eDTU` o `MinvCore` su un valore maggiore di 0. In questo modo si garantisce che ogni database nel pool abbia a disposizione la quantità di spazio di archiviazione OLTP in memoria corrispondente al valore `Min-eDTU` o `vCore` configurato.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Modifica dei livelli di servizio dei database che usano le tecnologie OLTP in memoria

È sempre possibile aggiornare il database o l'istanza a un piano superiore, ad esempio da Utilizzo generico a Business Critical (o da Standard a Premium). Il passaggio implica semplicemente un aumento di funzionalità e risorse.

Tuttavia, eseguire il downgrade del piano può avere un impatto negativo sul database. Questo impatto è particolarmente evidente quando si effettua il downgrade da Business Critical a Utilizzo generico (o da Premium a Standard o Basic) nei casi in cui il database contenga oggetti OLTP in memoria. Le tabelle ottimizzate per la memoria non sono disponibili dopo il downgrade, anche se dovessero rimanere visibili. Le stesse considerazioni si applicano quando si abbassa il piano tariffario di un pool elastico o si trasferisce un database con tecnologie in memoria in un pool elastico per utilizzo generico, standard o Basic.

> [!Important]
> OLTP in memoria non è supportato nel piano Utilizzo generico, Standard o Basic. Non è pertanto possibile spostare un database con oggetti OLTP in memoria in uno di questi livelli.

Prima di eseguire il downgrade del database a per utilizzo generico, standard o Basic, rimuovere tutte le tabelle ottimizzate per la memoria e i tipi di tabella, nonché tutti i moduli T-SQL compilati in modo nativo.

*Ridimensionamento delle risorse nel livello business critical*: i dati nelle tabelle ottimizzate per la memoria devono rientrare nell'archiviazione OLTP in memoria associata al livello del database o dell'istanza gestita oppure sono disponibili nel pool elastico. Se si tenta di ridurre il piano tariffario o di spostare il database in un pool che non dispone di sufficiente spazio di archiviazione OLTP in memoria, l'operazione avrà esito negativo.

## <a name="in-memory-columnstore"></a>Columnstore in memoria

La tecnologia columnstore in memoria consente di archiviare ed eseguire query su una grande quantità di dati nelle tabelle. La tecnologia columnstore usa un formato di archiviazione dei dati basato su colonne e l'elaborazione batch delle query allo scopo di ottenere prestazioni delle query fino a 10 volte superiori nei carichi di lavoro OLAP rispetto all'archiviazione tradizionale orientata alle righe. È anche possibile migliorare fino a 10 volte la compressione dei dati rispetto alla dimensione dei dati non compressi.
Esistono due tipi di modelli di columnstore che è possibile usare per organizzare i dati:

- **Columnstore cluster**, in cui tutti i dati nella tabella sono organizzati in un formato a colonne. In questo modello, tutte le righe nella tabella vengono inserite in formato a colonne, che esegue la compressione dei dati e consente di eseguire rapidamente query analitiche e report sulla tabella. A seconda della natura dei dati, è possibile ottenere una riduzione delle dimensioni da 10 a 100 volte. Il modello con columnstore cluster consente inoltre l'inserimento rapido di grandi quantità di dati (caricamento bulk), perché i batch di dati di grandi dimensioni con più di 100.000 righe vengono compressi prima di essere archiviati su disco. Questo modello è una scelta appropriata per i classici scenari di data warehouse.
- **Columnstore non cluster**, in cui i dati vengono archiviati in una tabella rowstore tradizionale ed è presente un indice in formato columnstore usato per le query di analisi. Questo modello consente l'elaborazione analitica e transazionale ibrida (HTAP), che offre la possibilità di eseguire analisi in tempo reale ad alte prestazioni su carichi di lavoro transazionali. Le query OLTP vengono eseguite sulla tabella rowstore ottimizzata per l'accesso a un set di righe limitato, mentre le query OLAP vengono eseguite sull'indice columnstore, che rappresenta la scelta migliore per le analisi. Il Query Optimizer sceglie dinamicamente il formato rowstore o columnstore in base alla query. Gli indici columnstore non cluster non riducono le dimensioni dei dati, poiché il set di dati originale viene mantenuto nella tabella rowstore originale senza apportare modifiche. Tuttavia, le dimensioni dell'indice columnstore aggiuntivo dovrebbero essere significativamente inferiori rispetto all'indice ad albero B equivalente.

> [!Note]
> La tecnologia columnstore in memoria mantiene in memoria solo i dati necessari per l'elaborazione, mentre i dati che non possono essere contenuti nella memoria sono archiviati su disco. Pertanto, la quantità di dati nelle strutture columnstore in memoria può superare la quantità di memoria disponibile.

Video di approfondimento sulla tecnologia:

- [Indice columnstore: video sull'analisi in memoria da Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Dimensioni dei dati e archiviazione per gli indici columnstore

Gli indici columnstore non devono essere contenuti nella memoria. L'unico limite alla dimensione degli indici è quindi la dimensione complessiva massima del database, descritta negli articoli [Modello di acquisto basato su DTU](database/service-tiers-dtu.md) e [Modello di acquisto basato su vCore](database/service-tiers-vcore.md).

Quando si usano gli indici columnstore cluster, viene impiegata la compressione a colonne per l'archiviazione delle tabelle di base. Ciò può ridurre notevolmente il footprint di archiviazione dei dati utente, ovvero è possibile inserire più dati nel database. Usando la [compressione a colonne dell'archivio](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression), è possibile inserire una quantità ancora maggiore di dati. La quantità di compressione che è possibile ottenere dipende dalla natura dei dati, ma generalmente si aggira intorno a 10 volte (10X) la compressione tradizionale.

Ad esempio, se si dispone di un database con dimensioni massime di 1 terabyte (TB) e si raggiunge una compressione 10X tramite columnstore, nel database è possibile inserire un totale di 10 TB di dati utente.

Quando si usano indici columnstore non cluster, la tabella di base è ancora archiviata nel formato rowstore tradizionale. Pertanto, il risparmio di spazio di archiviazione non è significativo come con gli indici columnstore cluster. Tuttavia, se si sostituisce un numero di indici non in cluster tradizionali con un indice columnstore singolo, è sempre possibile riscontrare un risparmio complessivo nel footprint della memoria per la tabella.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Modifica dei livelli di servizio dei database che contengono indici columnstore

*Il downgrade di un database singolo a Basic o Standard* potrebbe non essere possibile se il database di destinazione è inferiore a S3. Gli indici columnstore sono supportati solo nel piano tariffario Business Critical/Premium e non nel piano Standard, S3 e superiore, né nel piano Basic. Quando si effettua il downgrade del database a un piano o un livello non supportato, l'indice columnstore non è più disponibile. Il sistema conserva l'indice columnstore, ma non lo usa mai. Se in seguito si torna a un piano o un livello supportato, l'indice columnstore torna subito disponibile all'uso.

Se dispone di un indice columnstore **cluster**, l'intera tabella non sarà più disponibile dopo il downgrade. Pertanto è consigliabile eliminare tutti gli indici columnstore *cluster* prima di effettuare il downgrade del database a un piano o un livello non supportato.

> [!Note]
> SQL Istanza gestita supporta gli indici columnstore in tutti i livelli.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva 1: tecnologie OLTP in memoria per migliorare le prestazioni di T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Usare OLTP in memoria in un'applicazione Azure SQL esistente](in-memory-oltp-configure.md)
- [Monitorare l'archiviazione OLTP in memoria](in-memory-oltp-monitor-space.md) per OLTP in memoria
- [Provare le funzionalità in memoria](in-memory-sample.md)

## <a name="additional-resources"></a>Risorse aggiuntive

### <a name="deeper-information"></a>Approfondimenti

- [Informazioni in Learn how Quorum doubles key database's workload while lowering DTU by 70% with In-Memory OLTP in SQL Databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database) (Il quorum raddoppia il carico di lavoro del database principale riducendo il DTU del 70% con OLTP in memoria nel database SQL)
- [Post di Blog di OLTP in memoria](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Informazioni su OLTP in memoria](https://msdn.microsoft.com/library/dn133186.aspx)
- [Informazioni sugli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- [Informazioni sulle analisi operative in tempo reale](https://msdn.microsoft.com/library/dn817827.aspx)
- Vedere l'articolo sui [modelli comuni dei carichi di lavoro e considerazioni relative alla migrazione](https://msdn.microsoft.com/library/dn673538.aspx), che descrive modelli di carico di lavoro in cui OLTP in memoria fornisce in genere miglioramenti significativi delle prestazioni

### <a name="application-design"></a>Progettazione di applicazioni

- [OLTP in memoria (ottimizzazione in memoria)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Usare OLTP in memoria in un'applicazione Azure SQL esistente](in-memory-oltp-configure.md)

### <a name="tools"></a>Strumenti

- [Azure portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
