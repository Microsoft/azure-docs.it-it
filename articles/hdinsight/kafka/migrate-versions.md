---
title: Eseguire la migrazione di carichi di lavoro Apache Kafka ad Azure HDInsight 4,0
description: Informazioni su come eseguire la migrazione di carichi di lavoro Apache Kafka in HDInsight 3,6 in HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864126"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro Apache Kafka ad Azure HDInsight 4,0

Azure HDInsight 4,0 offre i componenti open source più recenti con miglioramenti significativi in merito a prestazioni, connettività e sicurezza. Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Kafka in HDInsight 3,6 a HDInsight 4,0. Dopo la migrazione dei carichi di lavoro a HDInsight 4,0, è possibile usare molte delle nuove funzionalità che non sono disponibili in HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Percorsi di migrazione di HDInsight 3,6 Kafka

HDInsight 3,6 supporta due versioni di Kafka: 1.0.0 e 1.1.0. HDInsight 4,0 supporta le versioni 1.1.0 e 2.1.0. A seconda della versione di Kafka e della versione di HDInsight che si vuole eseguire, sono disponibili più percorsi di migrazione supportati. Questi percorsi vengono illustrati di seguito e illustrati nella figura seguente.

* **Eseguire sia Kafka che HDInsight nelle versioni più recenti (scelta consigliata)**: eseguire la migrazione di un'applicazione HDInsight 3,6 e Kafka 1.0.0 o 1.1.0 a HDInsight 4,0 con Kafka 2.1.0 (percorsi D ed e di seguito).
* **Eseguire HDInsight sulla versione più recente, ma Kafka solo in una versione più recente**: eseguire la migrazione di un'applicazione HDInsight 3,6 e Kafka 1.0.0 a HDInsight 4,0 con Kafka 1.1.0 (percorso B di seguito).
* **Eseguire HDInsight nella versione più recente, mantenere la versione di Kafka**: eseguire la migrazione di un'applicazione HDInsight 3,6 e Kafka 1.1.0 a HDInsight 4,0 con Kafka 1.1.0 (percorso C riportato di seguito).
* **Eseguire Kafka in una versione più recente, mantenere la versione di HDInsight**: eseguire la migrazione di un'applicazione Kafka 1.0.0 a 1.1.0 e rimanere in HDInsight 3,6 (percorso a seguito). Si noti che questa opzione richiede comunque la distribuzione di un nuovo cluster. L'aggiornamento della versione Kafka in un cluster esistente non è supportato. Dopo aver creato un cluster con la versione desiderata, migrare i client Kafka per l'uso del nuovo cluster.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="Percorsi di aggiornamento per Apache Kafka su 3,6" border="false":::

## <a name="apache-kafka-versions"></a>Versioni Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se si esegue la migrazione da Kafka 1.0.0 a 1.1.0, è possibile sfruttare le nuove funzionalità seguenti:

* I miglioramenti apportati al controller Kafka accelerano l'arresto controllato, quindi è possibile riavviare i broker e risolvere i problemi più velocemente. 
* Miglioramenti della [logica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) che consentono di avere più partizioni (e, di conseguenza, altri argomenti) nel cluster. 
* Kafka Connect supporta le [intestazioni dei record](https://issues.apache.org/jira/browse/KAFKA-5142) e le [espressioni regolari](https://issues.apache.org/jira/browse/KAFKA-3073) per gli argomenti. 

Per un elenco completo degli aggiornamenti, vedere le [Note sulla versione di Apache Kafka 1,1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se si esegue la migrazione a Kafka 2,1, è possibile sfruttare le funzionalità seguenti:

* Maggiore resilienza del broker a causa di un protocollo di replica migliorato.
* Nuove funzionalità nell'API KafkaAdminClient.
* Gestione delle quote configurabile.
* Supporto per la compressione Zstandard.

Per un elenco completo degli aggiornamenti, vedere [Note sulla versione di Apache Kafka 2,0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e [Note sulla versione di Apache Kafka 2,1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Compatibilità del client Kafka

I nuovi broker Kafka supportano i client meno recenti. [Kip-35-il recupero della versione del protocollo](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) ha introdotto un meccanismo per determinare in modo dinamico le funzionalità di un broker Kafka e di [Kip-97: i criteri di compatibilità RPC del client migliorato](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) hanno introdotto nuovi criteri di compatibilità e garanzie per il client Java. In precedenza, un client Kafka doveva interagire con un broker della stessa versione o di una versione più recente. Ora le versioni più recenti dei client Java e di altri client che supportano KIP-35, ad esempio, `librdkafka` possono eseguire il fallback ai tipi di richieste precedenti o generare errori appropriati se la funzionalità non è disponibile.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Aggiornare la compatibilità del client Kafka" border="false":::

Si noti che non significa che il client supporta i broker meno recenti.  Per ulteriori informazioni, vedere [Compatibility Matrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Processo di migrazione generale

Le linee guida per la migrazione seguenti presuppongono un cluster Apache Kafka 1.0.0 o 1.1.0 distribuito in HDInsight 3,6 in una singola rete virtuale. Il Broker esistente presenta alcuni argomenti e viene usato attivamente da Producer e consumer.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="Ambiente presunto Kafka attuale" border="false":::

Per completare la migrazione, attenersi alla procedura seguente:

1. **Distribuire un nuovo cluster HDInsight 4,0 e i client per il test.** Distribuire un nuovo cluster HDInsight 4,0 Kafka. Se è possibile selezionare più versioni del cluster Kafka, è consigliabile selezionare la versione più recente. Dopo la distribuzione, impostare alcuni parametri in base alle esigenze e creare un argomento con lo stesso nome dell'ambiente esistente. Inoltre, impostare TLS e la crittografia Bring your own key (BYOK) in base alle esigenze. Verificare quindi se funziona correttamente con il nuovo cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="Distribuire nuovi cluster HDInsight 4,0" border="false":::

1. **Cambiare il cluster per l'applicazione Producer e attendere che tutti i dati della coda vengano utilizzati dai consumer correnti.** Quando il nuovo cluster HDInsight 4,0 Kafka è pronto, passare la destinazione Producer esistente al nuovo cluster. Lasciarlo invariato finché l'app consumer esistente non ha usato tutti i dati del cluster esistente.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="Scambia cluster per app Producer" border="false":::

1. **Cambiare il cluster nell'applicazione consumer.** Dopo aver confermato che l'applicazione consumer esistente ha terminato l'utilizzo di tutti i dati dal cluster esistente, passare la connessione al nuovo cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="Passa a cluster nell'app consumer" border="false":::

1. **Rimuovere il cluster precedente e testare le applicazioni in base alle esigenze.** Una volta che il Commuter è stato completato e funziona correttamente, rimuovere il vecchio cluster HDInsight 3,6 Kafka e i producer e i consumer usati nel test, in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Avvio rapido: Creare cluster Apache Kafka in HDInsight di Azure usando il portale di Azure](apache-kafka-get-started.md)
