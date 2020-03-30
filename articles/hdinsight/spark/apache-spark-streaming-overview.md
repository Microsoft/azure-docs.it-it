---
title: Spark Streaming in Azure HDInsight
description: Come usare le applicazioni Apache Spark Streaming nei cluster HDInsight Spark.How to use Apache Spark Streaming applications on HDInsight Spark clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406289"
---
# <a name="overview-of-apache-spark-streaming"></a>Panoramica di Apache Spark Streaming

[Apache Spark](https://spark.apache.org/) Streaming fornisce l'elaborazione di flussi di dati in cluster HDInsight Spark, con la certezza che qualsiasi evento di input venga elaborato esattamente una sola volta, anche in caso di errore del nodo. Un flusso Spark è un processo a esecuzione prolungata che riceve i dati di input da un'ampia gamma di origini, tra cui Hub eventi di Azure, un hub IoT di Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), socket TCP non elaborati o dal monitoraggio dei file system [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). A differenza di un processo unicamente guidato dagli eventi, un flusso Spark carica in batch i dati di input in intervalli di tempo, ad esempio una sezione di 2 secondi, e quindi trasforma ogni batch di dati tramite operazioni di mapping, riduzione, join ed estrazione. Il flusso Spark scrive quindi i dati trasformati in file system, database, dashboard e nella console.

![Elaborazione di flussi con HDInsight e Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Le applicazioni Spark Streaming devono attendere una frazione di secondo per raccogliere ogni *micro batch* di eventi prima di inviare ogni batch per l'elaborazione. Al contrario, un'applicazione guidata dagli eventi elabora ogni evento immediatamente. La latenza di Spark Streaming è in genere inferiore a pochi secondi. I vantaggi dell'approccio basato su micro batch consistono in un'elaborazione dati più efficiente e in calcoli di aggregazione più semplici.

## <a name="introducing-the-dstream"></a>Introduzione a DStream

Spark Streaming rappresenta un flusso continuo di dati in ingresso con un *flusso discretizzato* chiamato DStream. È possibile creare un flusso DStream da origini di input come Hub eventi o Kafka oppure applicando trasformazioni a un altro flusso DStream.

Un flusso DStream fornisce un livello di astrazione in aggiunta ai dati di evento non elaborati.

Iniziare con un singolo evento, ad esempio una lettura di temperatura da un termostato connesso. Quando questo evento arriva all'applicazione Spark Streaming, l'evento viene archiviato in modo affidabile, dove viene replicato su più nodi. Questa tolleranza di errore garantisce che l'errore di qualsiasi singolo nodo non comporterà la perdita dell'evento. Spark Core usa una struttura di dati che distribuisce i dati tra più nodi nel cluster, in cui ogni nodo mantiene in genere i propri dati in memoria per garantire prestazioni ottimali. Questa struttura di dati viene chiamata *RDD* (Resilient Distributed Dataset, set di dati distribuito resiliente).

Ogni RDD rappresenta gli eventi raccolti in un intervallo di tempo definito dall'utente denominato *intervallo batch*. Allo scadere di ogni intervallo di invio in batch, viene prodotto un nuovo RDD che contiene tutti i dati dell'intervallo. Il set continuo di RDD viene raccolto in un flusso DStream. Ad esempio, se l'intervallo di invio in batch è lungo un secondo, il flusso DStream genera ogni secondo un batch che contiene un RDD, che a sua volta contiene tutti i dati inseriti durante il secondo. Durante l'elaborazione del flusso DStream, l'evento di temperatura appare in uno di questi batch. Un'applicazione Spark Streaming elabora i batch che contengono gli eventi e infine agisce sui dati archiviati in ogni RDD.

![Flusso DStream di esempio con eventi di temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struttura di un'applicazione Spark Streaming

Un'applicazione Spark Streaming è un'applicazione a esecuzione prolungata che riceve dati da origini di inserimento, applica trasformazioni per elaborare i dati e quindi esegue il push dei dati in una o più destinazioni. La struttura di un'applicazione Spark Streaming è costituita da una parte statica e da una parte dinamica. La parte statica definisce l'origine da cui provengono i dati, il tipo di elaborazione da eseguire sui dati e dove devono essere generati i risultati. La parte dinamica consiste nell'esecuzione dell'applicazione a tempo indefinito, in attesa di un segnale di arresto.

Ad esempio, la semplice applicazione seguente riceve una riga di testo tramite un socket TCP e conteggia il numero di occorrenze di ogni parola.

### <a name="define-the-application"></a>Definire l'applicazione

La definizione della logica dell'applicazione è costituita da quattro passaggi:

1. Creare un oggetto StreamingContext.
2. Creare un flusso DStream da StreamingContext.
3. Applicare trasformazioni al flusso DStream.
4. Generare i risultati.

Questa definizione è statica e i dati non vengono elaborati fino a quando non si esegue l'applicazione.

#### <a name="create-a-streamingcontext"></a>Creare un oggetto StreamingContext

Creare un oggetto StreamingContext dall'oggetto SparkContext che punta al cluster. Quando si crea un oggetto StreamingContext, specificare la dimensione del batch in secondi, ad esempio:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Creare un flusso DStream

Con l'istanza StreamingContext creare un flusso DStream di input per l'origine di input. In questo caso, l'applicazione sta controllando l'arrivo di nuovi file nella risorsa di archiviazione predefinita collegata al cluster HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Applicare trasformazioni

Per implementare l'elaborazione, applicare trasformazioni nel flusso DStream. L'applicazione riceve una riga di testo in un certo momento dal file, suddivide ogni riga in parole e quindi usa un modello MapReduce per conteggiare il numero di occorrenze di ogni parola.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Generare i risultati

Eseguire il push dei risultati delle trasformazioni nei sistemi di destinazione tramite l'applicazione di operazioni di output. In questo caso, il risultato di ogni esecuzione tramite il calcolo viene visualizzato nell'output della console.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Avviare l'applicazione di streaming e proseguire l'esecuzione fino a quando non si riceve un segnale di arresto.

```
ssc.start()
ssc.awaitTermination()
```

Per informazioni dettagliate sull'API di Spark Streaming, nonché su origini degli eventi, trasformazioni e operazioni di output supportate, vedere [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html) (Guida ad Apache Spark Streaming per programmatori).

L'applicazione di esempio seguente è indipendente e di conseguenza può essere eseguita all'interno di [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). Questo esempio crea un'origine dati fittizia nella classe DummySource che restituisce il valore di un contatore e l'intervallo di tempo corrente in millisecondi ogni cinque secondi. Un nuovo oggetto StreamingContext è associato a un intervallo di invio in batch di 30 secondi. Ogni volta che viene creato un batch, l'applicazione di streaming esamina l'RDD generato, lo converte in un frame di dati Spark e quindi crea una tabella temporanea per il frame di dati.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Attendere circa 30 secondi dopo l'avvio dell'applicazione precedente.  Quindi, è possibile eseguire una query periodicamente sul frame di dati per visualizzare il set corrente di valori presenti nel batch, ad esempio utilizzando questa query SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

L'output risultante è simile al seguente:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Sono presenti sei valori, perché DummySource crea un valore ogni 5 secondi e l'applicazione genera un batch ogni 30 secondi.

## <a name="sliding-windows"></a>Finestre temporali scorrevoli

Per eseguire calcoli di aggregazione sul flusso DStream per un certo periodo di tempo, ad esempio per ottenere la temperatura media degli ultimi due secondi, è possibile usare le operazioni con *finestra temporale scorrevole* incluse in Spark Streaming. Una finestra temporale scorrevole ha una durata (lunghezza della finestra) e un intervallo durante il quale viene valutato il contenuto della finestra (intervallo di scorrimento).

Le finestre temporali scorrevoli possono sovrapporsi, ad esempio è possibile definire una finestra con una lunghezza di due secondi, con scorrimento ogni secondo. Questo significa che ogni volta che si esegue un calcolo di aggregazione, la finestra includerà i dati dall'ultimo secondo della finestra precedente e tutti i nuovi dati nel secondo successivo.

![Finestra iniziale di esempio con eventi di temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Finestra di esempio con eventi di temperatura dopo lo scorrimento](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

L'esempio seguente aggiorna il codice che usa DummySource per raccogliere i batch in una finestra con una durata di un minuto e uno scorrimento di un minuto.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Dopo il primo minuto sono presenti 12 voci, sei da ognuno dei due batch raccolti nella finestra.

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Le funzioni di finestra temporale scorrevole disponibili nell'API di Spark Streaming includono window, countByWindow, reduceByWindow e countByValueAndWindow. Per informazioni dettagliate su queste funzioni, vedere [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams) (Trasformazioni nei flussi DStream).

## <a name="checkpointing"></a>Checkpoint

Per offrire resilienza e tolleranza di errore, Spark Streaming usa checkpoint per garantire che l'elaborazione di flussi possa continuare senza interruzioni, anche in caso di errori dei nodi. In HDInsight Spark crea checkpoint in un archivio durevole (Archiviazione di Azure o Data Lake Storage). Questi checkpoint archiviano i metadati sull'applicazione di streaming, ad esempio la configurazione, le operazioni definite dall'applicazione e qualsiasi batch accodato ma non ancora elaborato. In alcuni casi, i checkpoint includono anche il salvataggio dei dati negli RDD per ricostruire più rapidamente lo stato dei dati rispetto a quelli presenti negli RDD gestiti da Spark.

## <a name="deploying-spark-streaming-applications"></a>Distribuzione di applicazioni Spark Streaming

In genere, un'applicazione Spark Streaming viene compilata in locale in un file JAR e quindi viene distribuita in Spark su HDInsight copiando il file JAR nella risorsa di archiviazione predefinita collegata al cluster HDInsight. È possibile avviare l'applicazione tramite le API REST LIVY disponibili dal cluster usando un'operazione POST. Il corpo dell'operazione POST include un documento JSON che fornisce il percorso del file JAR, il nome della classe il cui metodo principale definisce ed esegue l'applicazione di streaming e, facoltativamente, i requisiti relativi alle risorse del processo, ad esempio il numero di executor, la memoria e i core, e tutte le impostazioni di configurazione necessarie per il codice dell'applicazione.

![Distribuzione di un'applicazione Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Lo stato di tutte le applicazioni può anche essere verificato con una richiesta GET su un endpoint LIVY. Infine, è possibile terminare un'applicazione in esecuzione eseguendo una richiesta DELETE sull'endpoint LIVY. Per informazioni dettagliate sull'API LIVY, vedere [Processi remoti con Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cluster Apache Spark in HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html) (Guida ad Apache Spark Streaming per programmatori)
* [Avviare processi Apache Spark in remoto con Apache LIVY](apache-spark-livy-rest-interface.md)
