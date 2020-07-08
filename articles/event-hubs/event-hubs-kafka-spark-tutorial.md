---
title: Connettersi all'app Apache Spark - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come usare Apache Spark con Hub eventi di Azure per Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 00925242d5685749aba27ad2fc537ffb07f4c68d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320105"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Connettere l'applicazione Apache Spark con hub eventi di Azure
Questa esercitazione illustra la connessione dell'applicazione Spark a hub eventi per lo streaming in tempo reale. Questa integrazione consente lo streaming senza bisogno di modificare i client di protocollo o eseguire cluster Kafka o Zookeeper personalizzati. Questa esercitazione richiede Apache Spark v2.4 e versioni successive e Apache Kafka v2.0 e versioni successive.

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire Spark
> * Leggere dati da Hub eventi per Kafka
> * Scrivere dati in Hub eventi per Kafka

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare questa esercitazione, assicurarsi di avere:
-   Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> L'adapter Spark-Kafka è stato aggiornato per supportare Kafka v2.0 a partire da Spark v2.4. Nelle versioni precedenti di Spark l'adapter supportava Kafka v0.10 e versioni successive ma dipendeva specificamente dalle API di Kafka v0.10. Poiché Hub eventi per Kafka non supporta Kafka v0.10, gli adapter Spark-Kafka di versioni di Spark precedenti alla v2.4 non sono supportati da Hub eventi per gli ecosistemi Kafka.


## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Per l'invio e la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Per istruzioni su come creare uno spazio dei nomi e un hub eventi, vedere [creazione di un hub eventi](event-hubs-create.md) . Ottenere la stringa di connessione di Hub eventi e il nome di dominio completo (FQDN) da usare successivamente. Per istruzioni, vedere [Ottenere una stringa di connessione ad Hub eventi](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio
Clonare il repository di Hub eventi di Azure e passare alla sottocartella `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Leggere dati da Hub eventi per Kafka
Con poche modifiche alla configurazione è possibile iniziare a leggere i dati da Hub eventi per Kafka. Dopo aver aggiornato **BOOTSTRAP_SERVERS** e **EH_SASL** con i dettagli dello spazio dei nomi, è possibile avviare lo streaming con Hub eventi allo stesso modo di Kafka. Per il codice di esempio completo, vedere il file sparkConsumer.scala su GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Scrivere dati in Hub eventi per Kafka
È anche possibile scrivere in Hub eventi allo stesso modo in cui si scrive in Kafka. Non dimenticare di aggiornare la configurazione in modo da modificare **BOOTSTRAP_SERVERS** e **EH_SASL** con le informazioni dello spazio dei nomi di Hub eventi.  Per il codice di esempio completo, vedere il file sparkProducer.scala su GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Integrare Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)

