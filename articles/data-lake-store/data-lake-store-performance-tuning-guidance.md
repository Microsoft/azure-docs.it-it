---
title: Azure Data Lake Storage Gen1 - Ottimizzazione delle prestazioniAzure Data Lake Storage Gen1 - performance tuning
description: Viene descritto come ottimizzare Azure Data Lake Storage Gen1 per le prestazioni.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904615"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Ottimizzazione di Archiviazione di Azure Data Lake Gen1 per le prestazioni

Data Lake Storage Gen1 supporta una velocità effettiva elevata per l'analisi intensiva di I/O e lo spostamento dei dati. In Data Lake Storage Gen1 è importante poter usare tutta la velocità effettiva disponibile, ovvero la quantità di dati che possono essere letti o scritti al secondo, per ottenere prestazioni ottimali. Questo risultato viene ottenuto eseguendo il numero massimo possibile di operazioni di lettura e scrittura in parallelo.

![Prestazioni di Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 può essere ridimensionato per offrire la velocità effettiva necessaria per qualsiasi scenario di analisi. Per impostazione predefinita, un account Data Lake Storage Gen1 offre automaticamente la velocità effettiva sufficiente per soddisfare le esigenze di un'ampia categoria di casi d'uso. Per i casi in cui i clienti raggiungono il limite predefinito, è possibile contattare il supporto tecnico Microsoft per configurare l'account Data Lake Storage Gen1 in modo da ottenere maggiore velocità effettiva.

## <a name="data-ingestion"></a>Inserimento di dati

Quando si ingeriscono dati da un sistema di origine a Data Lake Storage Gen1, è importante considerare che l'hardware di origine, l'hardware di rete di origine e la connettività di rete a Data Lake Storage Gen1 possono essere il collo di bottiglia.

![Prestazioni di Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

È importante assicurarsi che lo spostamento dei dati non sia influenzato da questi fattori.

### <a name="source-hardware"></a>Hardware di origine

Sia che si utilizzino macchine locali o macchine virtuali in Azure, è necessario selezionare con attenzione l'hardware appropriato. Per l'hardware del disco di origine, preferire le unità SSD alle HDD e scegliere hardware con spindle più veloci. Per l'hardware di rete di origine, usare le schede di interfaccia di rete più veloci possibili. In Azure è consigliabile utilizzare le macchine virtuali di Azure D14 con il disco e l'hardware di rete opportunamente potenti.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Connettività di rete a Data Lake Storage Gen1

La connettività di rete tra i dati di origine e Data Lake Storage Gen1 può talvolta costituire il collo di bottiglia. Quando i dati di origine sono in locale, valutare l'opportunità di usare un collegamento dedicato con [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se i dati di origine sono in Azure, si ottengono prestazioni ottimali quando i dati si trovano nella stessa area di Azure dell'account Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurare gli strumenti di inserimento dei dati per la massima parallelizzazione

Dopo aver risolto i colli di bottiglia dell'hardware di origine e della connettività di rete, è possibile configurare gli strumenti di inserimento. La tabella seguente presenta un riepilogo delle impostazioni delle chiavi per diversi strumenti di inserimento comuni e include collegamenti ad articoli di approfondimento sull'ottimizzazione delle prestazioni. Per altre informazioni sullo strumento da usare per uno scenario specifico, vedere questo [articolo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Strumento          | Impostazioni | Altre informazioni                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Unità Azure Data Lake Analytics | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Data factory di Azure| parallelCopies | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strutturare il set di dati

Quando i dati vengono archiviati in Data Lake Storage Gen1, le dimensioni, il numero di file e la struttura di cartelle influiscono sulle prestazioni. La sezione seguente descrive le procedure consigliate in queste aree.

### <a name="file-size"></a>Dimensione del file

I motori di analisi come HDInsight e Azure Data Lake Analytics in genere gestiscono il sovraccarico a livello di singolo file. Se quindi si archiviano i dati in molti file di piccole dimensioni, questo può influire negativamente sulle prestazioni.

Per ottenere prestazioni migliori, è in genere opportuno organizzare i dati in file di dimensioni più grandi. Come regola generale, organizzare i set di dati in file di dimensioni pari o superiori a 256 MB. In alcuni casi, ad esempio per le immagini e i dati binari, non è possibile eseguire l'elaborazione in parallelo In questi casi, si consiglia di mantenere i singoli file sotto i 2 GB.

In alcuni stati, le pipeline di dati hanno un controllo limitato sui dati non elaborati che dispongono di molti file di piccole dimensioni. È pertanto consigliabile prevedere l'esecuzione di un processo che genera file di maggiori dimensioni destinati all'uso da parte delle applicazioni downstream.

### <a name="organize-time-series-data-in-folders"></a>Organizzare i dati di serie temporali in cartelle

Per i carichi di lavoro Hive e ADLA, l'eliminazione delle partizioni dei dati di serie temporali può aiutare alcune query a leggere solo un sottoinsieme dei dati, che migliora le prestazioni.

Le pipeline che inseriscono dati di serie temporali, spesso inseriscono i propri file con una denominazione strutturata per file e cartelle. Di seguito è riportato un esempio comune che vediamo per i dati strutturati in base alla data:The following is a common example we see for data that is structured by date:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Si noti che le informazioni di data/ora vengono visualizzate sia come cartelle sia nel nome del file.

Per la data e l'ora, di seguito è riportato un modello comune

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Anche in questo caso, la scelta relativa all'organizzazione di file e cartelle deve prevedere una gestione ottimizzata dei file di maggiori dimensioni e l'inclusione di un numero ragionevole di file in ogni cartella.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Ottimizzare i processi con utilizzo intensivo di I/O nei carichi di lavoro Hadoop e Spark in HDInsightOptimize I/O intensive jobs on Hadoop and Spark workloads on HDInsight

I processi sono classificabili in una delle tre categorie seguenti:

* **Uso intensivo della CPU.** Questi processi hanno tempi di elaborazione lunghi e tempi di I/O minimi, come i processi di machine learning e quelli di elaborazione del linguaggio naturale.
* **Memoria intensiva.** Questi processi usano grandi quantità di memoria, come i processi di PageRank e quelli di analisi in tempo reale.
* **Uso intensivo dell'I/O.** Questi processi impiegano la maggior parte del tempo a eseguire operazioni di I/O. Un esempio comune è un processo di copia che esegue solo operazioni di lettura e scrittura. Altri esempi includono processi di preparazione dei dati che leggono numerosi dati, eseguono alcune trasformazioni dei dati e quindi scrivono nuovamente i dati nell'archivio.

Le linee guida seguenti sono applicabili solo ai processi con uso intensivo dell'I/O.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerazioni generali per un cluster HDInsight

* **Versioni di HDInsight.** Per prestazioni ottimali, usare la versione più recente di HDInsight.
* **Regioni.** Inserire l'account Data Lake Storage Gen1 nella stessa area del cluster HDInsight.

Un cluster HDInsight è composto da due nodi head e da alcuni nodi di ruolo di lavoro. Ogni nodo di ruolo di lavoro fornisce un numero specifico di core e memoria, in base al tipo di macchina virtuale. Quando si esegue un processo, YARN è il negoziatore di risorse che alloca la memoria e i core disponibili per creare contenitori. Ogni contenitore esegue le attività necessarie per completare il processo. I contenitori vengono eseguiti in parallelo per l'elaborazione rapida delle attività. È quindi possibile ottenere un miglioramento delle prestazioni eseguendo la quantità massima possibile di contenitori paralleli.

All'interno di un cluster HDInsight sono presenti tre livelli che possono essere ottimizzati per aumentare il numero di contenitori e sfruttare tutta la velocità effettiva disponibile.

* **Strato fisico**
* **Livello YARN**
* **Livello del carico di lavoroWorkload layer**

### <a name="physical-layer"></a>Strato fisico

**Eseguire il cluster con più nodi e/o macchine virtuali di dimensioni maggiori.** Un cluster di dimensioni maggiori consentirà di eseguire più contenitori YARN, come illustrato nell'immagine seguente.

![Prestazioni di Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Usare macchine virtuali con maggiore larghezza di banda di rete.** La larghezza di banda di rete può costituire un collo di bottiglia se la larghezza di banda di rete disponibile è inferiore alla velocità effettiva di Data Lake Storage Gen1. Macchine virtuali differenti avranno dimensioni variabili della larghezza di banda di rete. Scegliere un tipo di macchina virtuale con la massima larghezza di banda di rete possibile.

### <a name="yarn-layer"></a>Livello YARN

**Usare contenitori YARN di dimensioni inferiori.** Ridurre le dimensioni di ogni contenitore YARN per creare altri contenitori con la stessa quantità di risorse.

![Prestazioni di Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

A seconda del carico di lavoro, sarà sempre necessaria una dimensione minima per i contenitori YARN. Se si sceglie un contenitore troppo piccolo, si verificheranno problemi di memoria insufficiente per i processi. In genere i contenitori YARN non devono essere inferiori a 1 GB. È comune vedere contenitori YARN da 3 GB. Per alcuni carichi di lavoro, possono essere necessari contenitori YARN più grandi.

**Aumentare il numero di core per contenitore YARN.** Aumentare il numero di core allocati a ogni contenitore per aumentare il numero di attività parallele eseguite in ogni contenitore. Questo funziona per le applicazioni come Spark, che eseguono più attività per contenitore. Per le applicazioni come Hive che eseguono un singolo thread in ogni contenitore, è preferibile avere più contenitori anziché più core per contenitore.

### <a name="workload-layer"></a>Livello del carico di lavoroWorkload layer

**Usare tutti i contenitori disponibili.** Impostare il numero di attività in modo che sia uguale o maggiore del numero di contenitori disponibili in modo che vengano utilizzate tutte le risorse.

![Prestazioni di Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Le attività che non vengono eseguite correttamente sono dispendiose.** Se ogni attività deve elaborare una grande quantità di dati, l'esito negativo di un'attività ha come risultato l'esecuzione di un nuovo tentativo impegnativo in termini di risorse. Pertanto, è preferibile creare più attività, ognuna delle quali elabora una piccola quantità di dati.

Oltre alle linee guida generali sopra illustrate, ogni applicazione dispone di diversi parametri che è possibile ottimizzare. La tabella seguente elenca alcuni parametri e collegamenti per ottimizzare con facilità le prestazioni di ogni applicazione.

| Carico di lavoro               | Parametro per impostare le attività                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark in HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive in HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce in HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm in HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Numero di processi del ruolo di lavoro</li><li>Numero di istanze di spout executor</li><li>Numero di istanze di bolt executor </li><li>Numero di attività spout</li><li>Numero di attività bolt</li></ul>|

## <a name="see-also"></a>Vedere anche

* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introduzione all’analisi dei dati di Data Lake di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
