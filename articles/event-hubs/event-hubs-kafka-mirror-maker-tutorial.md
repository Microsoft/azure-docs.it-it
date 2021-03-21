---
title: Utilizzo di Apache Kafka MirrorMaker - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come usare Kafka MirrorMaker per eseguire il mirroring di cluster Kafka in Hub eventi di Azure.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860996"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Usare Apache Kafka MirrorMaker con hub eventi

Questa esercitazione illustra come eseguire il mirroring di un broker Kafka in un hub eventi di Azure usando Kafka MirrorMaker. Se si ospita Apache Kafka in Kubernetes usando l'operatore CNCF Strimzi, è possibile fare riferimento all'esercitazione in [questo post di Blog](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) per informazioni su come configurare Kafka con Strimzi e il creatore di Mirror 2. 

   ![Kafka MirrorMaker con Hub eventi](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)

> [!NOTE]
> Questo articolo contiene riferimenti al termine *whitelist*, che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Configurare un cluster Kafka
> * Configurare Kafka MirrorMaker
> * Eseguire Kafka MirrorMaker

## <a name="introduction"></a>Introduzione
Questa esercitazione illustra come un hub eventi e Kafka MirrorMaker possono integrare una pipeline Kafka esistente in Azure tramite il "mirroring" del flusso di input Kafka nel servizio Hub eventi, che consente l'integrazione dei flussi di Apache Kafka usando diversi [modelli di Federazione](event-hubs-federation-overview.md). 

Un endpoint Kafka di Hub eventi di Azure consente la connessione a Hub eventi di Azure tramite il protocollo Kafka, ovvero i client Kafka. Apportando modifiche minime a un'applicazione Kafka, è possibile connettersi a Hub eventi di Azure e sfruttare i vantaggi dell'ecosistema di Azure. Hub eventi supporta attualmente il protocollo di Apache Kafka versioni 1,0 e successive.

È possibile Apache Kafka usare MirrorMaker 1 in modo unidirezionale da Apache Kafka a hub eventi. MirrorMaker 2 può essere usato in entrambe le direzioni, ma i [ `MirrorCheckpointConnector` e `MirrorHeartbeatConnector` configurabili in MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) devono essere configurati in modo da puntare al broker di Apache Kafka e non agli hub eventi. Questa esercitazione illustra la configurazione di MirrorMaker 1.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](https://maven.apache.org/download.cgi) e [installare](https://maven.apache.org/install.html) un archivio binario Maven
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/downloads)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Per l'invio e la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Per istruzioni su come creare uno spazio dei nomi e un hub eventi, vedere [creazione di un hub eventi](event-hubs-create.md) . Assicurarsi di copiare la stringa di connessione di Hub eventi per usarla in seguito.

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio

Ora che si dispone di una stringa di connessione di hub eventi, clonare gli hub eventi di Azure per il repository Kafka e passare alla `mirror-maker` sottocartella:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurare un cluster Kafka

Usare la [Guida introduttiva per Kafka](https://kafka.apache.org/quickstart) per configurare un cluster con le impostazioni desiderate (o usare un cluster Kafka esistente).

## <a name="configure-kafka-mirrormaker"></a>Configurare Kafka MirrorMaker

Kafka MirrorMaker consente il "mirroring" di un flusso. Dati i cluster Kafka di origine e di destinazione, MirrorMaker garantisce che qualsiasi messaggio inviato al cluster di origine venga ricevuto da entrambi i cluster di origine e di destinazione. Questo esempio illustra come eseguire il mirroring di un cluster Kafka di origine con un hub eventi di destinazione. Questo scenario può essere usato per inviare i dati da una pipeline Kafka esistente a Hub eventi senza interrompere il flusso dei dati. 

Per informazioni più dettagliate su Kafka MirrorMaker, vedere la guida [Kafka Mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (Mirroring Kafka/MirrorMaker).

Per configurare Kafka MirrorMaker, assegnargli un cluster Kafka come consumer/source e un hub eventi come produttore/destinazione.

#### <a name="consumer-configuration"></a>Configurazione del consumer

Aggiornare il file di configurazione del consumer `source-kafka.config`, che specifica a MirrorMaker le proprietà del cluster Kafka di origine.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configurazione del producer

Aggiornare quindi il file di configurazione del producer `mirror-eventhub.config`, che specifica a MirrorMaker di inviare i dati duplicati (o "con mirroring") al servizio Hub eventi. In particolare, modificare `bootstrap.servers` e `sasl.jaas.config` in modo che puntino all'endpoint Kafka di Hub eventi. Il servizio Hub eventi richiede la comunicazione sicura (SASL), che viene garantita impostando le ultime tre proprietà nella configurazione seguente: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Sostituire `{YOUR.EVENTHUBS.CONNECTION.STRING}` con la stringa di connessione per lo spazio dei nomi di Hub eventi. Per istruzioni su come ottenere la stringa di connessione, vedere [Ottenere una stringa di connessione ad Hub eventi](event-hubs-get-connection-string.md). Ecco un esempio di configurazione: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Eseguire Kafka MirrorMaker

Eseguire lo script di Kafka MirrorMaker dalla directory radice di Kafka usando i file di configurazione appena aggiornati. Assicurarsi di copiare i file di configurazione nella directory radice di Kafka o di aggiornare i relativi percorsi nel comando seguente.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Per verificare che gli eventi raggiungano l'hub eventi, vedere statistiche in ingresso nella [portale di Azure](https://azure.microsoft.com/features/azure-portal/)o eseguire un consumer nell'hub eventi.

Con MirrorMaker in esecuzione, tutti gli eventi inviati al cluster Kafka di origine vengono ricevuti sia dal cluster Kafka che dall'hub eventi con mirroring. Usando MirrorMaker e un endpoint Kafka di Hub eventi, è possibile migrare una pipeline Kafka esistente al servizio Hub eventi di Azure gestito senza modificare il cluster esistente o interrompere eventuali flussi di dati in corso.

## <a name="samples"></a>Esempi
Vedere gli esempi seguenti in GitHub:

- [Codice di esempio per questa esercitazione su GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Kafka MirrorMaker di hub eventi di Azure in esecuzione in un'istanza di contenitore di Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Integrare Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)