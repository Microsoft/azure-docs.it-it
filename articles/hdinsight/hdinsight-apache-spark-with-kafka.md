---
title: 'Streaming Apache Spark con Apache Kafka: Azure HDInsight'
description: Informazioni su come è possibile usare Apache Spark per trasmettere dati in streaming all'interno o all'esterno di Apache Kafka per mezzo di DStreams. In questo esempio i dati vengono trasmessi tramite un Jupyter Notebook da Spark in HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 6ef11e9c7907f57b3b8de0a042e1035bce638cf4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863276"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Esempio dello streaming Apache Spark (DStream) con Apache Kafka in HDInsight

Informazioni su come usare [Apache Spark](https://spark.apache.org/) per trasmettere dati all'interno o all'esterno di [Apache Kafka](https://kafka.apache.org/) in HDInsight usando [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Questo esempio usa un oggetto [Jupyter Notebook](https://jupyter.org/) che viene eseguito nel cluster Spark.

> [!NOTE]  
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
>
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

> [!IMPORTANT]  
> In questo esempio viene usata la tecnologia DStreams, una tecnologia di streaming Spark meno recente. Per un esempio con funzionalità di streaming Spark più recenti, vedere il documento [Streaming strutturato Spark con Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Spark e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure" border="false":::

> [!NOTE]  
> Anche se Kafka è limitato alle comunicazioni all'interno della rete virtuale, è possibile accedere ad altri servizi del cluster tramite Internet, ad esempio SSH e Ambari. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Spark e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Spark e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   Il modello di Azure Resource Manager è disponibile in **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

   > [!WARNING]
   > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Questo modello crea un cluster Kafka contenente tre nodi di lavoro.

   Questo modello crea un cluster HDInsight 3.6 sia per Kafka che per Spark.

1. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:

   |Proprietà |Valore |
   |---|---|
   |Resource group|creare un gruppo o selezionarne uno esistente.|
   |Location|scegliere una località geograficamente vicina.|
   |Nome del cluster di base|questo valore viene usato come nome di base per i cluster Spark e Kafka. Ad esempio, se si immette **hdistreaming** viene creato un cluster Spark denominato __spark-hdistreaming__ e un cluster Kafka denominato **kafka-hdistreaming**.|
   |Nome utente dell'account di accesso del cluster|nome utente amministratore per i cluster Spark e Kafka.|
   |Password di accesso al cluster|password dell'utente amministratore per i cluster Spark e Kafka.|
   |Nome utente SSH|utente SSH da creare per i cluster Spark e Kafka.|
   |Password SSH|password dell'utente SSH per i cluster Spark e Kafka.|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="HDInsight parametri di distribuzione personalizzati":::

1. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

1. Infine selezionare **Acquisto**. La creazione dei cluster richiede circa 20 minuti.

Dopo avere create le risorse, verrà visualizzata una pagina di riepilogo.

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="Riepilogo del gruppo di risorse per la rete virtuale e i cluster":::

> [!IMPORTANT]  
> Si noti che i nomi dei cluster HDInsight sono **spark-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="use-the-notebooks"></a>Usare i notebook

Il codice per l'esempio descritto in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio ha illustrato l'uso di Spark per leggere e scrivere in Kafka. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Introduzione ad Apache Kafka (anteprima) in HDInsight](kafka/apache-kafka-get-started.md)
* [Usare MirrorMaker per creare una replica di Apache Kafka in HDInsight](kafka/apache-kafka-mirroring.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)
