---
title: Linee guida per l'ottimizzazione delle prestazioni per applicazioni e database
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni sull'ottimizzazione di database e applicazioni di database per le prestazioni nel database SQL di Azure e in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 54a6293a29a407a7014aafb66587dcb01fc13337
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645795"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ottimizzare le applicazioni e i database per le prestazioni nel database SQL di Azure e in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dopo aver identificato un problema di prestazioni che si sta ritrovando nel database SQL di Azure e in Azure SQL Istanza gestita, questo articolo è stato progettato per semplificare le operazioni seguenti:

- Ottimizzare l'applicazione e applicare alcune procedure consigliate che consentono di migliorare le prestazioni.
- Ottimizzare il database modificando gli indici e le query per usare i dati in modo più efficiente.

Questo articolo presuppone che siano già state utilizzate le raccomandazioni di [Advisor](database-advisor-implement-performance-recommendations.md) per database SQL di Azure e le raccomandazioni di [ottimizzazione automatica](automatic-tuning-overview.md)del database SQL di Azure, se applicabile. Si presuppone inoltre che sia già stato letto [Monitoraggio e ottimizzazione delle prestazioni](monitor-tune-overview.md) e gli articoli correlati alla risoluzione dei problemi relativi alle prestazioni. In questo articolo si presuppone inoltre che il problema di prestazioni non sia correlato a risorse della CPU per la cui soluzione basta incrementare le dimensioni di calcolo o il livello di servizio per fornire maggiori risorse al database.

## <a name="tune-your-application"></a>Ottimizzare l'applicazione

Nel sistema SQL Server tradizionale in locale il processo di pianificazione della capacità iniziale è spesso separato dal processo di esecuzione di un'applicazione in produzione. Vengono acquistate prima di tutto le licenze per hardware e prodotti e l'ottimizzazione delle prestazioni viene eseguita in un secondo momento. Quando si usa SQL di Azure, è consigliabile intrecciare il processo di esecuzione di un'applicazione e di ottimizzazione. Il modello di pagamento della capacità su richiesta consente di ottimizzare l'applicazione in modo da usare la quantità minima di risorse necessaria in un momento specifico, invece di effettuare l'overprovisioning dell'hardware in base a ipotesi di piani di crescita futura per un'applicazione, che spesso si rivelano errati. Alcuni clienti potrebbero decidere di non ottimizzare un'applicazione e scegliere in alternativa di effettuare l'overprovisioning delle risorse hardware. Questo approccio potrebbe risultare valido se non si vuole modificare un'applicazione chiave in un periodo di attività elevata. Tuttavia, l'ottimizzazione di un'applicazione può ridurre al minimo i requisiti di risorse e le fatture mensili quando si usano i livelli di servizio nel database SQL di Azure e in Azure SQL Istanza gestita.

### <a name="application-characteristics"></a>Caratteristiche dell'applicazione

Sebbene i livelli di servizio database SQL di Azure e Azure SQL Istanza gestita siano progettati per migliorare la stabilità e la prevedibilità delle prestazioni di un'applicazione, alcune procedure consigliate consentono di ottimizzare l'applicazione in modo da sfruttare al meglio le risorse a dimensione di calcolo. Anche se in molte applicazioni è possibile ottenere miglioramenti significativi delle prestazioni semplicemente passando a un livello di servizio o dimensioni di calcolo superiori, questo vantaggio non è assicurato per tutte le applicazioni senza un'ottimizzazione aggiuntiva. Per ottenere un aumento delle prestazioni, prendere in considerazione operazioni di ottimizzazione aggiuntive per le applicazioni con queste caratteristiche:

- **Applicazioni con prestazioni ridotte a causa di un comportamento "eccessivamente comunicativo"**

  Le applicazioni con un livello di comunicazioni elevato eseguono un numero eccessivo di operazioni di accesso ai dati, sensibili alla latenza di rete. Potrebbe essere necessario modificare questi tipi di applicazioni per ridurre il numero di operazioni di accesso ai dati al database. Ad esempio, è possibile migliorare le prestazioni dell'applicazione utilizzando tecniche come l'invio in batch di query ad hoc o lo spostando le query nelle stored procedure. Per altre informazioni, vedere [Invio di query in batch](#batch-queries).

- **Database con carichi di lavoro intensivi che non possono essere supportati da un intero computer singolo**

   I database che superano le risorse delle dimensioni di calcolo Premium più elevate potrebbero trarre vantaggio dall'aumento del numero di istanze del carico di lavoro. Per altre informazioni, vedere [Partizionamento orizzontale tra database](#cross-database-sharding) e [Partizionamento funzionale](#functional-partitioning).

- **Applicazioni con query non ottimali**

  Le applicazioni, in particolare quelle incluse nel livello di accesso ai dati, con query non ottimizzate correttamente potrebbero non ottenere vantaggi da dimensioni di calcolo superiori. Tra queste sono incluse query prive della clausola WHERE, con indici mancanti o con statistiche obsolete. Queste applicazioni possono trarre vantaggio dalle tecniche standard di ottimizzazione delle prestazioni delle query. Per altre informazioni, vedere [Indici mancanti](#identifying-and-adding-missing-indexes) e [Hint/Ottimizzazione di query](#query-tuning-and-hinting).

- **Applicazioni con struttura di accesso ai dati non ottimale**

   Le applicazioni con problemi intrinseci di concorrenza per l'accesso ai dati, ad esempio il deadlock, potrebbero non trarre vantaggio da dimensioni di calcolo superiori. Valutare la possibilità di ridurre i round trip sul database memorizzando nella cache i dati sul lato client con il servizio di memorizzazione nella cache di Azure o un'altra tecnologia di Caching. Vedere [Memorizzazione nella cache a livello di applicazione](#application-tier-caching).

## <a name="tune-your-database"></a>Ottimizzare il database

In questa sezione vengono esaminate alcune tecniche che è possibile usare per ottimizzare il database per ottenere prestazioni ottimali per l'applicazione ed eseguirlo con la dimensione di calcolo più bassa possibile. Alcune di queste tecniche corrispondono alle procedure consigliate per l'ottimizzazione dei SQL Server tradizionali, ma altre sono specifiche del database SQL di Azure e di Azure SQL Istanza gestita. In alcuni casi, è possibile esaminare le risorse utilizzate per un database per trovare le aree per ottimizzare ulteriormente ed estendere le tecniche di SQL Server tradizionali per lavorare nel database SQL di Azure e in Azure SQL Istanza gestita.

### <a name="identifying-and-adding-missing-indexes"></a>Identificazione e aggiunta di indici mancanti

Un problema comune nelle prestazioni del database OLTP è correlato alla progettazione fisica del database. Spesso gli schemi di database vengono progettati e forniti senza verificare la scala (nel caricamento o nel volume di dati). Le prestazioni di un piano di query possono essere accettabili su scala ridotta, ma potrebbero purtroppo peggiorare notevolmente in caso di volumi elevati di dati a livello di produzione. L'origine più comune di questo problema è legata alla mancanza di indici adatti per soddisfare i filtri o altre restrizioni in una query. La mancanza di indici si manifesta spesso con una scansione di tabella quando potrebbe essere sufficiente una ricerca dell'indice.

In questo esempio il piano di query selezionato usa un'analisi quando invece sarebbe sufficiente una ricerca:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Piano di query con indici mancanti](./media/performance-guidance/query_plan_missing_indexes.png)

Il database SQL di Azure e Istanza gestita SQL di Azure consentono di trovare e correggere le condizioni comuni degli indici mancanti. DMV integrati nel database SQL di Azure e in SQL di Azure Istanza gestita esaminano le compilazioni di query in cui un indice ridurrebbe significativamente il costo stimato per l'esecuzione di una query. Durante l'esecuzione della query, il motore di database tiene traccia della frequenza con cui viene eseguito ogni piano di query e tiene traccia del gap stimato tra il piano di query in esecuzione e quello immaginato in cui è esistito tale indice. È possibile usare queste viste a gestione dinamica per ipotizzare rapidamente quali modifiche alla progettazione fisica del database potrebbero migliorare il costo complessivo del carico di lavoro per un database e il rispettivo carico di lavoro reale.

È possibile usare questa query per valutare gli indici potenzialmente mancanti:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

In questo esempio la query ha restituito questo suggerimento:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Dopo la creazione, la stessa istruzione SELECT seleziona un piano diverso, che usa una ricerca invece di un'analisi, e quindi esegue il piano in modo più efficiente:

![Piano di query con indici corretti](./media/performance-guidance/query_plan_corrected_indexes.png)

L'aspetto chiave è che la capacità di I/O di un sistema apposito condiviso è più limitata di un computer server dedicato. È disponibile un vantaggio per ridurre al minimo le operazioni di i/o non necessarie per sfruttare al massimo il sistema nelle risorse di ogni dimensione di calcolo dei livelli di servizio. Le opzioni appropriate di progettazione fisica del database possono migliorare notevolmente la latenza delle singole query, la velocità effettiva di richieste simultanee gestibili per unità di scala e ridurre i costi necessari per soddisfare la query. Per altre informazioni sulle DMV di indici mancanti, vedere [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Hint/Ottimizzazione di query

Il Query Optimizer nel database SQL di Azure e in Azure SQL Istanza gestita è simile a quello tradizionale Query Optimizer di SQL Server. La maggior parte delle procedure consigliate per l'ottimizzazione delle query e per comprendere le limitazioni del modello di ragionamento per il Query Optimizer si applica anche al database SQL di Azure e al Istanza gestita SQL di Azure. Se si ottimizzano le query nel database SQL di Azure e in Istanza gestita SQL di Azure, è possibile ottenere l'ulteriore vantaggio di ridurre le richieste di risorse aggregate. L'applicazione potrebbe essere eseguita a un costo inferiore rispetto a un'applicazione equivalente non ottimizzata, perché può usare dimensioni di calcolo inferiori.

Un esempio comune in SQL Server e che si applica anche al database SQL di Azure e al Istanza gestita di Azure SQL è il modo in cui il Query Optimizer i parametri "Sniffs". Durante la compilazione, Query Optimizer valuta il valore corrente di un parametro per determinare se può generare un piano di query più idoneo. Sebbene questa strategia possa spesso comportare un piano di query molto più veloce rispetto a un piano compilato senza valori di parametri noti, attualmente funziona in modo non perfetto sia in SQL Server, nel database SQL di Azure che in Azure SQL Istanza gestita. In alcuni casi il parametro non viene analizzato e in altri casi viene analizzato, ma il piano generato non è ottimale per l'intero set di valori di parametri in un carico di lavoro. Microsoft include gli hint per la query (direttive) per consentire di specificare la finalità più deliberatamente ed eseguire l'override del comportamento predefinito per l'analisi dei parametri. Spesso, se si usano gli hint, è possibile correggere i casi in cui il SQL Server predefinito, il database SQL di Azure e il comportamento di Istanza gestita SQL di Azure sono imperfetti per un carico di lavoro specifico del cliente.

L'esempio successivo illustra il modo in cui Query Processor può generare un piano non ottimale per i requisiti di prestazioni e risorse. Questo esempio mostra inoltre che se si usa un hint per la query, è possibile ridurre il tempo di esecuzione della query e i requisiti delle risorse per il database:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Tramite il codice di installazione viene creata una tabella indicante la differenza nella distribuzione dei dati. Il piano di query ottimale varia in base al parametro selezionato. Il comportamento di memorizzazione nella cache del piano non esegue sempre la ricompilazione della query in base al valore di parametro più comune, purtroppo. È quindi possibile che un piano non ottimale venga memorizzato nella cache e usato per molti valori, anche se un piano diverso potrebbe costituire mediamente una scelta migliore. Il piano di query crea quindi due stored procedure identiche, ad eccezione del fatto che una include un hint di query speciale.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

È consigliabile attendere almeno 10 minuti prima di iniziare la parte 2 dell'esempio, in modo che i risultati siano distinti nei dati di telemetria risultanti.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Ogni parte di questo esempio prova a eseguire 1.000 volte un'istruzione INSERT con parametri, per generare un carico sufficiente utilizzabile in un set di dati di test. Durante l'esecuzione di stored procedure, Query Processor esamina il valore del parametro passato alla procedura durante la prima compilazione ("analisi" dei parametri). Query Processor memorizza nella cache il piano risultante e lo usa per le chiamate successive, anche se il valore del parametro è diverso. È possibile che non venga usato il piano ottimale in tutti i casi. È a volte necessario consentire a Query Optimizer la selezione di un piano che sia migliore per la metà dei casi anziché per il caso specifico quando la query viene compilata per la prima volta. In questo esempio, il piano iniziale genera un piano di "analisi" che legge tutte le righe per cercare tutti i valori corrispondenti al parametro:

![Ottimizzazione delle query mediante un piano di analisi](./media/performance-guidance/query_tuning_1.png)

Poiché la procedura è stata eseguita con il valore 1, il piano risultante è ottimale per il valore 1, ma non per tutti gli altri valori nella tabella. È probabile che il risultato non sia quello che si sceglierebbe se si potesse selezionare casualmente ogni piano, perché presenta prestazioni inferiori e usa una quantità maggiore di risorse.

Se si esegue il test con `SET STATISTICS IO` impostato su `ON`, le operazioni di analisi logica in questo esempio vengono eseguite in background. Come si può notare, 1.148 operazioni di lettura vengono eseguite dal piano e ciò è poco efficiente, se il caso medio consiste nel restituire solo una riga:

![Ottimizzazione delle query mediante un'analisi logica](./media/performance-guidance/query_tuning_2.png)

La seconda parte dell'esempio usa un hint per la query per indicare a Query Optimizer di usare un valore specifico durante il processo di compilazione. In questo caso impone a Query Processor di ignorare il valore passato come parametro e di presupporre invece `UNKNOWN`. Ciò fa riferimento a un valore con frequenza media nella tabella, ignorando eventuali asimmetrie. Il piano risultante è un piano basato su ricerca, più veloce e con un minore impiego medio di risorse rispetto al piano della parte 1 dell'esempio:

![Ottimizzazione delle query mediante un hint di query](./media/performance-guidance/query_tuning_3.png)

È possibile verificarne l'effetto nella tabella **sys.resource_stats**. Si verifica un ritardo dal momento in cui il test viene eseguito a quando i dati popolano la tabella. Per questo esempio, la parte 1 è stata eseguita durante l'intervallo di tempo 22:25:00 e la parte 2 nell'intervallo di tempo 22:35:00. Nell'intervallo di tempo precedente sono state usate più risorse rispetto a quello successivo, in seguito ai miglioramenti di efficienza di piano.

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Risultati di esempio di ottimizzazione delle query](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Anche se il volume in questo esempio è intenzionalmente ridotto, l'effetto dei parametri non ottimali può essere significativo, in particolare nei database di dimensioni più grandi. La differenza, nei casi estremi, può essere di alcuni secondi per i casi veloci e di alcune ore per i casi lenti.

Esaminando **sys.resource_stats** è possibile determinare se la risorsa usata per un test usa una quantità di risorse superiore o inferiore rispetto a un altro test. Quando si confrontano i dati, separare gli intervalli di test in modo che non rientrino nella stessa finestra di 5 minuti nella vista **sys.resource_stats**. L'obiettivo dell'esercizio consiste nel ridurre la quantità totale di risorse usate e non di ridurre le risorse di picco. In genere, anche con l'ottimizzazione di una parte di codice per la latenza viene ridotto il consumo di risorse. Assicurarsi che le modifiche apportate a un'applicazione siano necessarie e che non influiscano negativamente sull'esperienza dei clienti che potrebbero usare hint di query nell'applicazione.

Se un carico di lavoro include un set di query ripetute, è spesso consigliabile acquisire e confermare la validità delle scelte del piano perché determinerà l'unità di dimensioni minima delle risorse per ospitare il database. Dopo la convalida, esaminare di nuovo occasionalmente i piani per assicurarsi che siano ancora ottimali. Per altre informazioni, vedere [Hint per la query (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="very-large-database-architectures"></a>Architetture di database di dimensioni molto grandi

Prima del rilascio del livello di servizio di [iperscalabilità](service-tier-hyperscale.md) per database singoli nel database SQL di Azure, i clienti usavano per raggiungere i limiti di capacità per i singoli database. Questi limiti di capacità sono ancora disponibili per i database in pool nei pool elastici del database SQL di Azure e nei database di istanza in istanze gestite di SQL di Azure. Nelle due sezioni seguenti vengono illustrate due opzioni per la risoluzione di problemi con database di grandi dimensioni nel database SQL di Azure e in Azure SQL Istanza gestita quando non è possibile usare il livello di servizio con iperscalabilità.

### <a name="cross-database-sharding"></a>Partizionamento orizzontale tra database

Poiché il database SQL di Azure e il Istanza gestita SQL di Azure vengono eseguiti su hardware di base, i limiti di capacità per un singolo database sono inferiori rispetto a quelli di un'installazione SQL Server locale tradizionale. Alcuni clienti usano tecniche di partizionamento orizzontale per suddividere le operazioni di database su più database quando le operazioni non rientrano nei limiti di un singolo database nel database SQL di Azure e in Azure SQL Istanza gestita. La maggior parte dei clienti che usano le tecniche di partizionamento orizzontale nel database SQL di Azure e in Azure SQL Istanza gestita suddivide i dati in una singola dimensione tra più database. Per questo approccio è necessario comprendere che le applicazioni OLTP eseguono spesso transazioni applicabili a una riga o a un piccolo gruppo di righe nello schema.

> [!NOTE]
> Il database SQL di Azure offre ora una libreria di supporto per il partizionamento orizzontale. Per altre informazioni, vedere [Panoramica della libreria client dei database elastici](elastic-database-client-library.md).

Se, ad esempio, un database include il nome del cliente, l'ordine e i dettagli dell'ordine (come illustrato nel database di esempio tradizionale Northwind incluso in SQL Server), è possibile suddividere questi dati in più database raggruppando un cliente con l'ordine correlato e con le informazioni dettagliate sull'ordine. È possibile assicurare che i dati del cliente rimangano in un singolo database. L'applicazione suddividerebbe i diversi clienti tra database, estendendo di fatto il carico tra più database. Con il partizionamento orizzontale, i clienti non possono solo evitare il limite massimo delle dimensioni del database, ma il database SQL di Azure e il Istanza gestita SQL di Azure possono anche elaborare carichi di lavoro notevolmente maggiori rispetto ai limiti delle diverse dimensioni di calcolo, purché ogni singolo database si inserisca nei limiti del livello di servizio.

Anche se il partizionamento orizzontale del database non riduce la capacità aggregata delle risorse per una soluzione, è notevolmente efficace nel supportare soluzioni di dimensioni molto elevate distribuite in più database. Ogni database può essere eseguito a dimensioni di calcolo diverse per supportare database "efficaci" di dimensioni molto grandi con requisiti molto elevati a livello di risorse.

#### <a name="functional-partitioning"></a>Partizionamento funzionale

Spesso gli utenti combinano molte funzioni in un singolo database. Se un'applicazione include ad esempio la logica per gestire l'inventario di un negozio, è possibile che quel database includa la logica associata all'inventario, il rilevamento degli ordini di acquisto, le stored procedure e le viste indicizzate o materializzate mediante le quali sono stati gestiti i report di fine mese. Questa tecnica semplifica l'amministrazione del database per operazioni quali il backup, ma richiede anche il ridimensionamento dell'hardware per gestire il carico massimo in tutte le funzioni di un'applicazione.

Se si usa un'architettura con scalabilità orizzontale nel database SQL di Azure e in Azure SQL Istanza gestita, è consigliabile suddividere diverse funzioni di un'applicazione in database diversi. Se si usa questa tecnica, ogni applicazione viene ridimensionata in modo indipendente. Quando un'applicazione viene usata con maggiore frequenza e il carico nel relativo database aumenta, l'amministratore può scegliere dimensioni di calcolo indipendenti per ogni funzione in un'applicazione. Nei limiti, questa architettura fa sì che le dimensioni di un'applicazione diventino più grandi di quelle gestibili da una singola macchina apposita, perché il carico viene distribuito in più macchine.

### <a name="batch-queries"></a>Invio di query in batch

Per le applicazioni che accedono ai dati tramite query di volume elevato, frequenti e ad hoc, una notevole quantità di tempo di risposta viene utilizzata per la comunicazione di rete tra il livello applicazione e il livello database. Anche quando l'applicazione e il database si trovano nello stesso data center, la latenza di rete tra i due può essere ingrandita da un numero elevato di operazioni di accesso ai dati. Per ridurre i round trip di rete per le operazioni di accesso ai dati, prendere in considerazione l'uso dell'opzione per l'invio in batch delle query ad hoc o per la compilazione delle query come stored procedure. Se si esegue il batch delle query ad hoc, è possibile inviare più query come un unico batch di grandi dimensioni in un unico viaggio al database. La compilazione di query ad hoc in stored procedure può produrre lo stesso risultato dell'invio in batch. L'utilizzo di un stored procedure offre anche il vantaggio di aumentare le possibilità di memorizzare nella cache i piani di query nel database, in modo da poter utilizzare nuovamente il stored procedure.

Alcune applicazioni comportano un utilizzo elevato di scrittura. È a volte possibile ridurre il carico totale di I/O in un database considerando la modalità di invio in batch delle scritture. Questa operazione è spesso semplice come l'uso di transazioni esplicite anziché di transazioni autocommit in stored procedure e batch ad hoc. Per una valutazione delle diverse tecniche che è possibile usare, vedere la pagina relativa alle tecniche di invio [in batch per le applicazioni di database in Azure](../performance-improve-use-batching.md). Provare a usare il proprio carico di lavoro per individuare il modello ottimale per l'invio in batch. Assicurarsi di comprendere che un modello potrebbe offrire garanzie di coerenza transazionale leggermente diverse. Per trovare il carico di lavoro ottimale che consenta un uso delle risorse minimo è necessario individuare la corretta combinazione di compromessi tra prestazioni e coerenza.

### <a name="application-tier-caching"></a>Memorizzazione nella cache a livello di applicazione

Alcune applicazioni di database contengono carichi di lavoro con intensa attività di lettura. I livelli di memorizzazione nella cache possono ridurre il carico del database e potenzialmente ridurre le dimensioni di calcolo necessarie per supportare un database usando il database SQL di Azure e Azure SQL Istanza gestita. Con [cache di Azure per Redis](https://azure.microsoft.com/services/cache/), se si dispone di un carico di lavoro di lettura intenso, è possibile leggere i dati una sola volta (o forse una volta per ogni computer a livello di applicazione, a seconda della configurazione) e quindi archiviare i dati all'esterno del database. In questo modo è possibile ridurre il carico del database (CPU e I/O letti), ma vi sarà un impatto sulla coerenza transazionale poiché i dati letti dalla cache potrebbero non essere sincronizzati con i dati nel database. Anche se in molte applicazioni è accettabile un livello di incoerenza, ciò non rappresenta una soluzione valida per tutti i carichi di lavoro. È necessario conoscere bene tutti i requisiti delle applicazioni prima di implementare una strategia di caching a livello di applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui livelli di servizio basati su DTU, vedere [modello di acquisto basato su DTU](service-tiers-dtu.md).
- Per altre informazioni sui livelli di servizio basati su vCore, vedere il [modello di acquisto basato su vCore](service-tiers-vcore.md).
- Per altre informazioni sui pool elastici, vedere [Informazioni sui pool elastici di Azure](elastic-pool-overview.md)
- Per informazioni sulle prestazioni e sui pool elastici, vedere [Quando usare un pool elastico](elastic-pool-overview.md)
