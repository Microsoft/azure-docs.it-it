---
title: Integrare Apache Kafka Connect - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come usare Kafka Connect con hub eventi di Azure per Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: b063bb36ec17c22c0f093f1b33f11597eed5ea68
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061666"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Integrare il supporto di Apache Kafka Connect in Hub eventi di Azure (anteprima)
Man mano che aumentano i volumi di inserimento per le esigenze aziendali, aumentano di pari passo i requisiti di inserimento per i vari sink e le varie origini esterne. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) fornisce un framework che consente di connettere e importare/esportare dati da/a qualsiasi sistema esterno come MySQL, HDFS e il file system tramite un cluster Kafka. Questa esercitazione illustra l'uso di Kafka Connect Framework con hub eventi.

Questa esercitazione illustra l'integrazione di Kafka Connect con un hub eventi e la distribuzione di connettori di base FileStreamSource e FileStreamSink. Questa funzionalità è attualmente in anteprima. Questi connettori non sono destinati all'uso in ambiente di produzione, ma servono semplicemente a dimostrare uno scenario completo di Kafka Connect in cui il servizio Hub eventi di Azure funge da broker Kafka.

> [!NOTE]
> Questo esempio è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare uno spazio dei nomi di Hub eventi
> * Clonare il progetto di esempio
> * Configurare Kafka Connect per Hub eventi
> * Eseguire Kafka Connect
> * Creare i connettori

## <a name="prerequisites"></a>Prerequisites
Per completare questa procedura dettagliata, verificare di disporre dei prerequisiti seguenti:

- Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka (versione 1.1.1, versione di Scala 2.11), disponibile su [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Leggere con attenzione l'articolo introduttivo [Hub eventi per Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi
Per l'invio e la ricezione da qualsiasi servizio Hub eventi è richiesto uno spazio dei nomi di Hub eventi. Per istruzioni su come creare uno spazio dei nomi e un hub eventi, vedere [creazione di un hub eventi](event-hubs-create.md) . Ottenere la stringa di connessione di Hub eventi e il nome di dominio completo (FQDN) da usare successivamente. Per istruzioni, vedere [Ottenere una stringa di connessione ad Hub eventi](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Clonare il progetto di esempio
Clonare il repository di Hub eventi di Azure e passare alla sottocartella tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Configurare Kafka Connect per Hub eventi
Quando si reindirizza la velocità effettiva di Kafka Connect da Kafka a Hub eventi, è necessaria una riconfigurazione minima.  Il codice di esempio `connect-distributed.properties` seguente illustra come configurare Connect per autenticare e comunicare con l'endpoint Kafka in Hub eventi:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Eseguire Kafka Connect

In questo passaggio un ruolo di lavoro Kafka Connect viene avviato localmente in modalità distribuita, usando Hub eventi per gestire lo stato del cluster.

1. Salvare il file `connect-distributed.properties` in locale.  Assicurarsi di sostituire tutti i valori racchiusi tra parentesi graffe.
2. Passare alla posizione della versione di Kafka nel computer.
4. Eseguire `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  Quando viene visualizzato l'avviso `'INFO Finished starting connectors and tasks'`, l'API REST del ruolo di lavoro Connect è pronta per l'interazione. 

> [!NOTE]
> Kafka Connect usa l'API Kafka AdminClient per creare automaticamente argomenti con le configurazioni consigliate, inclusa la compattazione. Un rapido controllo dello spazio dei nomi nel portale di Azure conforma che gli argomenti interni del ruolo di lavoro Connect sono stati creati automaticamente.
>
>Gli argomenti interni di Kafka Connect **devono usare la compattazione**.  Il team di Hub eventi non è responsabile della correzione di configurazioni non corrette se gli argomenti interni di Kafka Connect non sono configurati correttamente.

### <a name="create-connectors"></a>Creare i connettori
Questa sezione illustra come configurare i connettori FileStreamSource e FileStreamSink. 

1. Creare una directory per i file di dati di input e output.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Creare due file: uno con i dati di inizializzazione che vengono letti dal connettore FileStreamSource e l'altro in cui il connettore FileStreamSink scrive.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Creare un connettore FileStreamSource.  Assicurarsi di sostituire i valori nelle parentesi graffe con il percorso della home directory.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Dopo l'esecuzione del comando riportato sopra si dovrebbe vedere il comando `connect-quickstart` dell'hub eventi nell'istanza di Hub eventi.
4. Controllare lo stato del connettore di origine.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Facoltativamente, è possibile usare [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) per verificare che gli eventi abbiamo raggiunto l'argomento `connect-quickstart`.

5. Creare un connettore FileStreamSink.  Anche in questo caso, assicurarsi di sostituire i valori nelle parentesi graffe con il percorso della home directory.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Controllare lo stato del connettore sink.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verificare che i dati siano stati replicati tra i file e che siano identici in entrambi i file.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Pulizia
Kafka Connect crea argomenti di Hub eventi in cui archiviare le configurazioni, gli offset e lo stato che rimangono persistenti anche dopo che il cluster Connect viene disattivato. A meno che tale persistenza non sia voluta, è consigliabile eliminare questi argomenti. Si consiglia anche di eliminare il codice `connect-quickstart` di Hub eventi creato nel corso della procedura dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Guida per gli sviluppatori Apache Kafka per hub eventi di Azure](apache-kafka-developer-guide.md)
