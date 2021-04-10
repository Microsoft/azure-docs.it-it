---
title: Flussi scalabili in Azure HDInsight
description: Come usare il flusso di dati con cluster Apache scalabili in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 3bf036ff413f720f54deef1a62b502668d1d8ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871997"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streaming su larga scala in HDInsight

Le soluzioni Big Data in tempo reale agiscono sui dati in movimento. In genere, questi dati hanno il maggior valore al momento dell'arrivo. Se il flusso di dati in ingresso diventa più grande delle dimensioni gestibili in quel momento, potrebbe risultare necessario limitare le risorse. Un cluster HDInsight offre in alternativa funzioni di scalabilità verticale, consentendo di soddisfare le esigenze della soluzione con l'aggiunta di nodi su richiesta.

In un'applicazione per l'uso dei flussi, una o più origini dati generano eventi (talvolta milioni al secondo) che devono essere elaborati rapidamente senza eliminare le informazioni utili. Gli eventi in entrata vengono gestiti tramite la *memorizzazione nel buffer del flusso*, tecnica nota anche come *accodamento degli eventi*, eseguita da un servizio come [Apache Kafka](kafka/apache-kafka-introduction.md) o [Hub eventi](https://azure.microsoft.com/services/event-hubs/). Dopo aver raccolto gli eventi, è quindi possibile analizzare i dati tramite un sistema di analisi in tempo reale all'interno del livello di *elaborazione dei flussi*, come [Apache Storm](storm/apache-storm-overview.md) o [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). I dati elaborati possono essere archiviati in sistemi di archiviazione a lungo termine, come [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) e visualizzati in tempo reale in un dashboard di business intelligence, come [Power BI](https://powerbi.microsoft.com), Tableau o una pagina Web personalizzata.

:::image type="content" source="./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png" alt-text="Modelli di flusso di Azure HDInsight":::

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka offre un servizio di accodamento dei messaggi con velocità effettiva elevata e bassa latenza e fa ora parte della famiglia di prodotti software open source di Apache. Kafka usa un modello di messaggistica basato su pubblicazione e sottoscrizione e archivia i flussi di dati partizionati in modo sicuro in un cluster distribuito con replica. Kafka supporta la scalabilità lineare man mano che aumenta la velocità effettiva.

Per altre informazioni, vedere [Introduzione ad Apache Kafka in HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm è un sistema di calcolo distribuito, a tolleranza di errore e open source ottimizzato per l'elaborazione di flussi di dati in tempo reale con Hadoop. L'unità principale dei dati per un evento in ingresso è una tupla, ovvero un set non modificabile di coppie chiave/valore. Una sequenza illimitata di queste tuple forma un flusso, originato da uno spout. Lo spout esegue il wrapping di un'origine dati di flussi (ad esempio Kafka) e genera le tuple. Una topologia Storm è una sequenza di trasformazioni su questi flussi.

Per altre informazioni, vedere [Informazioni su Apache Storm in Azure HDInsight](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming è un'estensione di Spark, che consente di riutilizzare lo stesso codice usato per l'elaborazione batch. È possibile combinare sia query in batch che interattive nella stessa applicazione. A differenza di Storm, Spark streaming fornisce una semantica di elaborazione con stato esattamente una volta. Quando viene usato in combinazione con l' [API diretta Kafka](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), che garantisce che tutti i dati Kafka vengano ricevuti da Spark streaming esattamente una volta, è possibile ottenere una garanzia end-to-end esattamente una volta. Uno dei punti di forza di Spark Streaming è il supporto della tolleranza di errore, con ripristino rapido dei nodi con errori quando si usano più nodi all'interno del cluster.

Per altre informazioni, vedere [Informazioni su Apache Spark Streaming](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Scalabilità di un cluster

Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di [modificare il numero di nodi del cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). È possibile eliminare i cluster Spark senza alcuna perdita di dati, perché tutti i dati sono archiviati in Archiviazione di Azure o Data Lake Storage.

Esistono dei vantaggi rispetto alle tecnologie di separazione. Kafka, ad esempio, è una tecnologia di buffering degli eventi, quindi è molto impegnativo e non necessita di molta potenza di elaborazione. In confronto, gli elaboratori di flussi come Spark Streaming, richiedono grandi quantità di risorse di calcolo e quindi macchine virtuali più potenti. Separando queste tecnologie in cluster diversi è possibile gestirne la scalabilità in modo indipendente e usare al tempo stesso in modo ottimale le macchine virtuali.

### <a name="scale-the-stream-buffering-layer"></a>Scalabilità del livello di memorizzazione nel buffer dei flussi

Le tecnologie per la memorizzazione nel buffer dei flussi Hub eventi e Kafka usano entrambe le partizioni e i consumer leggono da tali partizioni. Per la scalabilità della velocità effettiva di input è necessario aumentare il numero di partizioni e l'aggiunta di partizioni comporta una parallelismo crescente. In hub eventi, il numero di partizioni non può essere modificato dopo la distribuzione, quindi è importante iniziare con la scalabilità di destinazione. Con Kafka è possibile [aggiungere partizioni](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), anche mentre Kafka elabora i dati. Kafka offre uno strumento per riassegnare le partizioni, `kafka-reassign-partitions.sh`. HDInsight fornisce uno [strumento di ribilanciamento della replica delle partizioni](https://github.com/hdinsight/hdinsight-kafka-tools),  `rebalance_rackaware.py` . Questo strumento di ribilanciamento chiama lo strumento `kafka-reassign-partitions.sh` in modo che ogni replica si trovi in un dominio di errore e un dominio di aggiornamento separati, rendendo Kafka in grado di riconoscere il rack e migliorando la tolleranza di errore.

### <a name="scale-the-stream-processing-layer"></a>Scalabilità del livello di elaborazione dei flussi

Sia Apache Storm che Spark Streaming supportano l'aggiunta di nodi di lavoro ai cluster, anche durante l'elaborazione dei dati.

Per sfruttare i vantaggi di nuovi nodi aggiunti tramite la scalabilità di Storm è necessario ribilanciare le eventuali topologie Storm avviate prima dell'aumento delle dimensioni del cluster. Questo ribilanciamento può essere eseguito tramite l'interfaccia utente Web di Storm o la relativa interfaccia della riga di comando. Per altre informazioni, vedere la [documentazione di Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark usa tre parametri chiave per la configurazione dell'ambiente, a seconda dei requisiti dell'applicazione: `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un *executor* è un processo avviato per un'applicazione Spark. Viene eseguito sul nodo di lavoro ed è responsabile dell'esecuzione delle attività dell'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi di lavoro e alle relative dimensioni. Questi numeri sono archiviati nel file `spark-defaults.conf` in ogni nodo head del cluster.

Questi tre parametri possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, e possono anche essere specificati per ogni singola applicazione. Per altre informazioni, vedere [Gestire le risorse del cluster Apache Spark in Azure HDInsight](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare e monitorare una topologia Apache Storm in Azure HDInsight](storm/apache-storm-quickstart.md)
* [Topologie di esempio per Apache Storm in HDInsight](storm/apache-storm-example-topology.md)
* [Introduzione ad Apache Spark in HDInsight](spark/apache-spark-overview.md)
* [Iniziare a usare Apache Kafka in HDInsight](kafka/apache-kafka-get-started.md)