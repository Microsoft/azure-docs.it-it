---
title: Uso di Akka Streams per Apache Kafka - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come connettere Akka Streams a un hub eventi di Azure abilitato per Apache Kafka.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 729e78959f93b1aa1563a049a64c553929c4f97e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283634"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Uso di Akka Streams con Hub eventi per Apache Kafka
Questa esercitazione illustra come connettere a un hub eventi i streams senza modificare i client di protocollo o eseguire i propri cluster. Hub di eventi di Azure per Kafka supporta [Apache Kafka versione 1.0.Azure](https://kafka.apache.org/10/documentation.html) Event Hubs for the Kafka supports Apache Kafka version 1.0.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire il producer di Akka Streams 
> * Eseguire il consumer di Akka Streams

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di disporre dei prerequisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio o la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Vedere [Creazione di un hub eventi con supporto per Kafka](event-hubs-create.md) per informazioni su come ottenere un endpoint Kafka di Hub eventi. Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Ora che si dispone di una stringa di connessione hub eventi, clonare `akka` il repository Hub eventi di Azure per Kafka e passare alla sottocartella:Now that you have a Event Hubs connection string, clone the Azure Event Hubs for Kafka repository and navigate to the subfolder:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Eseguire il producer di Akka Streams

Usando l'esempio del producer Akka Streams fornito, inviare messaggi al servizio Hub eventi.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="producer-applicationconf"></a>Producer application.conf

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `producer/src/main/resources/application.conf` per indirizzare il producer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Eseguire il producer dalla riga di comando

Per eseguire il producer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Il producer inizia a inviare `test`eventi all'hub eventi in corrispondenza dell'argomento e stampa gli eventi in stdout.

## <a name="run-akka-streams-consumer"></a>Eseguire il consumer di Akka Streams

Usando l'esempio di consumer fornito, ricevere messaggi dall'hub eventi.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornire un endpoint Kafka di Hub eventi

#### <a name="consumer-applicationconf"></a>Consumer application.conf

Aggiornare i valori `bootstrap.servers` e `sasl.jaas.config` in `consumer/src/main/resources/application.conf` per indirizzare il consumer all'endpoint Kafka di Hub eventi con l'autenticazione corretta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Eseguire il consumer dalla riga di comando

Per eseguire il consumer dalla riga di comando, generare il file JAR e quindi eseguirlo dall'interno di Maven (o generare il file JAR usando Maven, quindi eseguirlo in Java aggiungendo i JAR Kafka necessari al classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se l'hub eventi include eventi (ad esempio, se è in esecuzione `test`anche il producer), il consumer inizia a ricevere eventi dall'argomento . 

Consultare il manuale [Akka Streams Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) per altre informazioni su Akka Streams.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come connettere aKka Streams all'hub eventi senza modificare i client di protocollo o eseguire i propri cluster. Hub eventi di Azure per Kafka supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html). Nell'ambito di questa esercitazione sono state eseguite le azioni seguenti: 

> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Eseguire il producer di Akka Streams 
> * Eseguire il consumer di Akka Streams

Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere l'argomento seguente:  

- [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
- [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Come creare un hub eventiHow to create an event hub](event-hubs-create.md)
- [Trasmettere in Hub eventi dalle applicazioni Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Integrazione di Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
