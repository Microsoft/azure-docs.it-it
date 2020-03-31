---
title: Ottimizzare i processi Spark per ottenere prestazioni ottimali - Azure HDInsight
description: Mostra strategie comuni per le migliori prestazioni dei cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198872"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Ottimizzare i processi di Apache Spark in HDInsightOptimize Apache Spark jobs in HDInsight

Informazioni su come ottimizzare la configurazione del cluster [Apache Spark](https://spark.apache.org/) per carichi di lavoro specifici.  La sfida più comune è la pressione della memoria, a causa di configurazioni non corrette (in particolare esecutori di dimensioni errate), operazioni a esecuzione prolungata e attività che comportano operazioni cartesiane. È possibile velocizzare i processi con una memorizzazione nella cache appropriata e consentendo l'[asimmetria dei dati](#optimize-joins-and-shuffles). Per ottenere prestazioni ottimali, monitorare e analizzare i processi Spark che comportano un'esecuzione prolungata e un consumo eccessivo di risorse. Per informazioni su come iniziare a usare Apache Spark in HDInsight, vedere Creare un cluster Apache Spark tramite il portale di Azure.For information on getting started with Apache Spark on HDInsight, see [Create Apache Spark cluster using Azure portal.](apache-spark-jupyter-spark-sql-use-portal.md)

Le sezioni seguenti descrivono alcune raccomandazioni e procedure di ottimizzazione comuni per i processi Spark.

## <a name="choose-the-data-abstraction"></a>Scegliere l'astrazione dei dati

Le versioni precedenti di Spark utilizzano RDD per astrarre i dati, Spark 1.3 e 1.6 hanno introdotto rispettivamente i frame di dati e i Set di dati. Valutare i seguenti vantaggi:

* **Dataframe**
    * La soluzione ottimale nella maggior parte dei casi
    * Consentono l'ottimizzazione query tramite Catalyst
    * Generazione di codici whole-stage
    * Accesso diretto alla memoria
    * Sovraccarico ridotto di Garbage Collection (GC)
    * Per gli sviluppatori non risultano efficienti quanto i set di dati, perché non sono presenti controlli in fase di compilazione né alcuna programmazione degli oggetti di dominio
* **Dataset**
    * Ideali nelle pipeline ETL complesse in cui l'impatto sulle prestazioni è accettabile
    * Sconsigliati nelle aggregazioni in cui l'impatto sulle prestazioni può essere considerevole
    * Consentono l'ottimizzazione query tramite Catalyst
    * Apprezzati dagli sviluppatori, offrono controlli in fase di compilazione e programmazione di oggetti di dominio
    * Comportano un sovraccarico di serializzazione/deserializzazione
    * Sovraccarico GC elevato
    * Interrompono la generazione di codici whole-stage
* **RDD**
    * Non è necessario utilizzare RDD, a meno che non sia necessario creare un nuovo RDD personalizzato.
    * Nessuna ottimizzazione query tramite Catalyst
    * Nessuna generazione di codici whole-stage
    * Sovraccarico GC elevato
    * È necessario usare API legacy per Spark 1.x

## <a name="use-optimal-data-format"></a>Uso del formato dati ottimale

Spark supporta molti formati, come ad esempio csv, json, xml, parquet, orc e avro. Spark può essere esteso per supportare numerosi formati con origini dati esterne; per altre informazioni, vedere [Pacchetti Apache Spark](https://spark-packages.org).

Il formato migliore per le prestazioni è Parquet con *compressione Snappy*, ovvero l'impostazione predefinita in Spark 2.x. Parquet archivia i dati in formato a colonne ed è altamente ottimizzato in Spark.

## <a name="select-default-storage"></a>Selezionare la risorsa di archiviazione predefinita

Quando si crea un nuovo cluster Spark, è possibile selezionare Archiviazione BLOB di Azure o Archiviazione dati di Azure come archivio predefinito del cluster. Entrambe le opzioni offrono i vantaggi dell'archiviazione a lungo termine per i cluster temporanei, in modo che i dati non vengano eliminati automaticamente quando si elimina il cluster. È possibile ricreare un cluster temporaneo e accedere comunque ai dati.

| Tipo di store | File system | speed | Temporaneo | Modalità di utilizzo comuni |
| --- | --- | --- | --- | --- |
| Archiviazione BLOB di Azure | **wasb:**//url/ | **Standard** | Sì | Cluster temporaneo |
| Archiviazione BLOB di Azure (sicura)Azure Blob Storage (secure) | **wasbs:**//url/ | **Standard** | Sì | Cluster temporaneo |
| Azure Data Lake Storage Gen 2| **abfs:**//url/ | **Più rapido** | Sì | Cluster temporaneo |
| Azure Data Lake Storage Gen 1| **adl:**//url/ | **Più rapido** | Sì | Cluster temporaneo |
| Hadoop Distributed File System locale | **hdfs:**//url/ | **Il più rapido** | No | Cluster interattivo 24/7 |

Per una descrizione completa delle opzioni di archiviazione disponibili per i cluster HDInsight, vedere Confrontare le opzioni di [archiviazione per l'uso con i cluster di Azure HDInsight.For](../hdinsight-hadoop-compare-storage-options.md)a full description of the storage options available for HDInsight clusters, see Compare storage options for use with Azure HDInsight clusters.

## <a name="use-the-cache"></a>Usare la cache

Spark offre meccanismi di memorizzazione nella cache nativi che possono essere usati con metodi diversi, ad esempio `.persist()`, `.cache()` e `CACHE TABLE`. La memorizzazione nella cache nativa è efficace con set di dati di piccole dimensioni, così come con pipeline ETL, in cui è necessario memorizzare nella cache i risultati intermedi. Tuttavia, la memorizzazione nella cache nativa di Spark attualmente non funziona bene con il partizionamento, poiché una tabella memorizzata nella cache non mantiene i dati di partizionamento. Una tecnica di memorizzazione nella cache più generica e affidabile è la *memorizzazione nella cache a livello di archiviazione*.

* Memorizzazione nella cache nativa di Spark (non consigliata)
    * Adatta per set di dati di piccole dimensioni.
    * Non funziona con il partizionamento, che potrebbe cambiare nelle versioni future di Spark.

* Memorizzazione nella cache a livello di archiviazione (consigliata)
    * Può essere implementato in HDInsight usando la funzionalità [Cache I/O.Can be](apache-spark-improve-performance-iocache.md) implemented on HDInsight using the II Cache feature.
    * Usa una memorizzazione nella cache di unità SSD e interna alla memoria.

* Hadoop Distributed File System locale (opzione consigliata)
    * Percorso `hdfs://mycluster`.
    * Usa la memorizzazione nella cache di unità SSD.
    * I dati memorizzati nella cache andranno persi quando si elimina il cluster, richiedendo così una ricompilazione della cache.

## <a name="use-memory-efficiently"></a>Uso efficiente della memoria

Spark funziona inserendo i dati in memoria; pertanto, la gestione delle risorse di memoria è un aspetto essenziale per ottimizzare l'esecuzione dei processi Spark.  Esistono diverse tecniche che è possibile applicare per usare la memoria del cluster in modo efficiente.

* Prediligere partizioni di dati più piccole e tenere conto delle dimensioni, del tipo e della distribuzione dei dati nella strategia di partizionamento.
* Valutare la versione più recente e più efficiente di [serializzazione dei dati Kryo](https://github.com/EsotericSoftware/kryo), anziché la serializzazione Java predefinita.
* Prediligere l'uso di YARN, che consente la separazione `spark-submit` per batch.
* Monitorare e ottimizzare le impostazioni di configurazione di Spark.

La struttura della memoria Spark e alcuni parametri di memoria dell'executor chiave vengono visualizzati nella figura seguente per riferimento.

### <a name="spark-memory-considerations"></a>Considerazioni sulla memoria Spark

Se si utilizza [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), YARN controlla la somma massima di memoria utilizzata da tutti i contenitori in ogni nodo Spark.  Il diagramma seguente mostra gli oggetti chiave e le relative relazioni.

![Gestione della memoria Spark in YARN](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Per indirizzare messaggi di "memoria insufficiente", provare a:

* Esaminare le riproduzioni casuali con gestione DAG. Ridurre mediante riduzione lato mappa, pre-partizione (o assegnazione di bucket) dell'origine dati, ottimizzazione delle riproduzioni con sequenza casuale singole e riduzione della quantità di dati inviati.
* Prediligere `ReduceByKey` per il limite di memoria fissa rispetto a `GroupByKey`, che fornisce aggregazioni, windowing e altre funzioni, ma non ha limite di memoria.
* Prediligere `TreeReduce`, che esegue più operazioni su executor e partizioni rispetto a `Reduce`, che esegue tutto il lavoro sul driver.
* Sfruttare i frame di dati, anziché gli oggetti RDD di livello inferiore.
* Creare tipi complessi in grado di incapsulare azioni, come ad esempio "Top N", diverse aggregazioni od operazioni di windowing.

Per altre procedure di risoluzione dei problemi, vedere [Eccezioni OutOfMemoryError per Apache Spark in Azure HDInsight.For additional troubleshooting steps, see OutOfMemoryError exceptions for Apache Spark in Azure HDInsight.](apache-spark-troubleshoot-outofmemory.md)

## <a name="optimize-data-serialization"></a>Ottimizzare la serializzazione dei dati

I processi Spark vengono distribuiti, per cui una serializzazione dei dati appropriata è importante per ottenere prestazioni ottimali.  Sono disponibili due opzioni di serializzazione per Spark:

* La serializzazione predefinita è quella in Java.
* La serializzazione Kryo è un formato più recente e può rivelarsi più veloce e più compatta rispetto a Java.  Kryo richiede di registrare le classi nel programma e non supporta ancora tutti i tipi Serializable.

## <a name="use-bucketing"></a>Uso di bucket

L'uso di bucket è simile al partizionamento dei dati, ma ogni bucket può contenere un set di valori in colonna anziché uno solo. I bucket funzionano anche per il partizionamento di valori più numerosi (nell'ordine di milioni o più), ad esempio per gli identificatori prodotto. Un bucket è determinato dall'hash affiancato alla chiave di bucket della riga. Le tabelle inserite in bucket offrono ottimizzazioni univoche perché archiviano i metadati relativi a come sono state inserite in bucket e ordinate.

Ecco alcune funzionalità bucket avanzate:

* Ottimizzazione query in base alla ripartizione in bucket di metainformazioni
* Aggregazioni ottimizzate
* Join ottimizzati

È possibile usare partizionamento e bucket contemporaneamente.

## <a name="optimize-joins-and-shuffles"></a>Ottimizzare join e riproduzioni con sequenza casuale

Se sono presenti processi lenti in un'operazione Join o di riproduzione casuale, la causa è probabilmente dovuta a un'*asimmetria dei dati*, ovvero nei dati del processo. Ad esempio, un processo di mapping può richiedere 20 secondi, mentre l'esecuzione di un processo in cui i dati vengono uniti in Join o aggiunti o selezionati in ordine casuale richiede ore. Per correggere l'asimmetria dei dati è consigliabile archiviare in valori salt l'intera chiave o usare un *salt isolato* soltanto per alcuni subset di chiavi. Se si usa un salt isolato, è consigliabile filtrare ulteriormente per isolare il subset di chiavi con salt nei join della mappa. È anche possibile introdurre una colonna di bucket ed eseguire una prima aggregazione in bucket.

Un altro fattore che può causare join lenti è il tipo di join. Per impostazione predefinita, Spark usa il tipo di join `SortMerge`. Questo tipo di join è ideale per grandi set di dati, mentre in caso contrario risulta oneroso, perché è necessario innanzitutto ordinare i lati sinistro e destro dei dati prima di unirli.

Un join `Broadcast` è particolarmente appropriato per i set di dati più piccoli o laddove un lato del join sia nettamente inferiore all'altro. Questo tipo di join trasmette un solo lato a tutti gli executor e pertanto richiede più memoria per le trasmissioni in generale.

È possibile modificare il tipo di join nella configurazione impostando `spark.sql.autoBroadcastJoinThreshold`; in alternativa, è possibile impostare un join hint usando le API del frame di dati (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Se si utilizzano tabelle con bucket, si dispone `Merge` di un terzo tipo di join, il join. Un set di dati correttamente pre-partizionato e preordinato ignorerà la fase costosa di ordinamento da un join `SortMerge`.

L'ordine dei join è importante, in particolare nelle query più complesse. Iniziare con i join più selettivi. Laddove possibile, spostare anche i join che aumentano il numero di righe dopo le aggregazioni.

Per gestire il parallelismo per i join cartesiani, è possibile aggiungere strutture annidate, finestre ed eseguire forse uno o più passaggi nel processo Spark.

## <a name="customize-cluster-configuration"></a>Personalizzare la configurazione del cluster

A seconda del carico di lavoro del cluster Spark, è possibile stabilire se una configurazione non predefinita per Spark darà origine a un'esecuzione del processo Spark maggiormente ottimizzata.  Eseguire test di benchmark con carichi di lavoro di esempio per convalidare tutte le configurazioni cluster non predefinite.

Ecco alcuni parametri comuni che è possibile modificare:

* `--num-executors` imposta il numero appropriato di executor.
* `--executor-cores` imposta il numero di core per ogni executor. Di norma si useranno degli executor di dimensioni medie, giacché parte della memoria disponibile è già usata da altri processi.
* `--executor-memory` imposta le dimensioni della memoria per ogni executor, che controlla le dimensioni dell'heap in YARN. È consigliabile lasciare una certa quantità di memoria per l'overhead di esecuzione.

### <a name="select-the-correct-executor-size"></a>Selezionare le dimensioni corrette dell'executor

Quando si sceglie la configurazione dell'executor, prendere in considerazione l'overhead di Java Garbage Collection (GC).

* Fattori che riducono le dimensioni dell'executor:
    1. Ridurre le dimensioni heap al di sotto di 32 GB per mantenere un overhead di GC < 10%.
    2. Ridurre il numero di core per mantenere un overhead di GC < 10%.

* Fattori che aumentano le dimensioni dell'executor:
    1. Ridurre l'overhead di comunicazione tra executor.
    2. Ridurre il numero di connessioni aperte tra executor (N2) nei cluster di grandi dimensioni (> 100 executor).
    3. Aumentare le dimensioni dell'heap per consentire un uso intensivo della memoria.
    4. Facoltativo: ridurre l'overhead della memoria per ogni executor.
    5. Facoltativo: aumentare la concorrenza e l'uso mediante sovrascrittura della CPU.

Come regola empirica generale quando si selezionano le dimensioni dell'executor:

1. Iniziare con 30 GB per executor e distribuire i core disponibili sul computer.
2. Aumentare il numero di core per executor per i cluster di grandi dimensioni (> 100 executor).
3. Modificare le dimensioni in base alle esecuzioni di prova e ai fattori precedenti, ad esempio l'overhead GC.

Durante l'esecuzione di query simultanee, considerare quanto segue:

1. Iniziare con 30 GB per ogni executor e per tutti i core del computer.
2. Creare più applicazioni Spark parallele sovrascrivendo la CPU (miglioramento della latenza di circa il 30%).
3. Distribuire le query tra applicazioni parallele.
4. Modificare le dimensioni in base alle esecuzioni di prova e ai fattori precedenti, ad esempio l'overhead GC.

Per ulteriori informazioni sull'utilizzo di Ambari per configurare gli esecutori, vedere Impostazioni di [Apache Spark - Esecutori Spark](apache-spark-settings.md#configuring-spark-executors).

Monitorare le prestazioni della query per gli outlier o altri problemi di prestazioni osservando la visualizzazione della sequenza temporale, il grafico SQL, le statistiche dei processi e così via. Per informazioni sul debug dei processi Spark tramite YARN e il server Spark History, vedere Debug dei [processi Apache Spark in esecuzione in Azure HDInsight.](apache-spark-job-debugging.md) Per suggerimenti sull'utilizzo del server YARN Timeline, vedere Accesso ai registri delle [applicazioni Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

In alcuni casi, uno o più executor possono rivelarsi più lenti rispetto ad altri e richiedere molto più tempo per eseguire le attività. Ciò si verifica spesso nei cluster di dimensioni maggiori (> 30 nodi). In questo caso, suddividere il lavoro in un numero maggiore di attività, in modo che l'utilità di pianificazione riesca a compensare l'effetto di rallentamento delle attività. Ad esempio, prevedere almeno il doppio di attività rispetto al numero di core degli executor nell'applicazione. È anche possibile abilitare l'esecuzione speculativa delle attività con `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Ottimizzare l'esecuzione del processo

* Memorizzare nella cache secondo necessità; ad esempio, se si usano i dati due volte, memorizzarli nella cache.
* Trasmissione di variabili a tutti gli executor. Le variabili vengono serializzate solo una volta, con conseguente snellimento delle ricerche.
* Usare il pool di thread sul driver, determinando operazioni più veloci per molte attività.

Monitorare regolarmente i processi in esecuzione per rilevare eventuali problemi di prestazioni. Se è necessario approfondire alcuni problemi, è possibile usare uno degli strumenti di profilatura delle prestazioni seguenti:

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) monitora l'uso della CPU, delle risorse di archiviazione, della rete e della larghezza di banda.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profila il codice dell'executor e di Spark.

La chiave per le prestazioni delle query di Spark 2.x è il motore al tungsteno, che dipende dalla generazione di codici whole-stage. In alcuni casi, la generazione di codici whole-stage potrebbe essere disabilitata. Ad esempio, se si usa un tipo non modificabile (`string`) nell'espressione di aggregazione, viene visualizzato `SortAggregate` al posto di `HashAggregate`. Ad esempio, per ottenere prestazioni migliori, eseguire le operazioni seguenti, quindi abilitare nuovamente la generazione di codici:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight](apache-spark-job-debugging.md)
* [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
* [Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster Apache Spark](apache-spark-livy-rest-interface.md)
* [Ottimizzazione di Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Come ottimizzare efficacemente i processi Apache Spark in modo che funzionino](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serializzazione Kryo](https://github.com/EsotericSoftware/kryo)
