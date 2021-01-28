---
title: 'Esercitazione: API Apache Kafka Producer e Consumer - Azure HDInsight'
description: Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight. In questa esercitazione si apprenderà come usare queste API con Kafka in HDInsight da un'applicazione Java.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: e5a635a8837aadaf423c6f3a0925dbac4080e60f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945170"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Esercitazione: Usare le API Apache Kafka Producer e Consumer

Informazioni su come usare l'API Apache Kafka Producer e Consumer con Kafka in HDInsight.

L'API Kafka Producer consente alle applicazioni di inviare flussi di dati al cluster Kafka. L'API Kafka Consumer consente alle applicazioni di leggere flussi di dati dal cluster Kafka.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Prerequisiti
> * Informazioni sul codice
> * Compilare e distribuire l'applicazione
> * Eseguire l'applicazione nel cluster

Per altre informazioni sulle API, vedere la documentazione Apache sull'[API Producer](https://kafka.apache.org/documentation/#producerapi) e l'[API Consumer](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Prerequisiti

* Cluster Apache Kafka in HDInsight. Per informazioni su come creare il cluster, vedere [Iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).
* [Java Developer Kit (JDK) versione 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) o equivalente, ad esempio OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.
* Un client SSH come Putty. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Informazioni sul codice

L'applicazione di esempio si trova in [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) nella sottodirectory `Producer-Consumer`. Se si usa il cluster Kafka abilitato per **Enterprise Security Package (ESP)** , è necessario usare la versione dell'applicazione che si trova nella sottodirectory `DomainJoined-Producer-Consumer`.

L'applicazione è costituita principalmente da quattro file:
* `pom.xml`: Questo file definisce le dipendenze progetto, la versione Java e i metodi di creazione pacchetti.
* `Producer.java`: Questo file invia frasi casuali a Kafka usando l'API Producer.
* `Consumer.java`: Questo file usa l'API Consumer per leggere i dati da Kafka ed esportarli in STDOUT.
* `AdminClientWrapper.java`: Questo file usa l'API di amministrazione per creare, descrivere ed eliminare gli argomenti di Kafka.
* `Run.java`: L'interfaccia della riga di comando usata per eseguire il codice producer e consumer.

### <a name="pomxml"></a>Pom.xml

Gli aspetti importanti da comprendere nel file `pom.xml` sono:

* Dipendenze: Questo progetto si basa sulle API Producer e Consumer Kafka, che sono fornite dal pacchetto `kafka-clients`. Il codice XML seguente definisce questa dipendenza:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    La voce `${kafka.version}` viene dichiarata nella sezione `<properties>..</properties>` di `pom.xml` ed è configurata per la versione Kafka del cluster HDInsight.

* Plug-in: I plug-in Maven offrono varie funzionalità. In questo progetto vengono usati i plug-in seguenti:

    * `maven-compiler-plugin`: Usato per impostare su 8 la versione Java usata dal progetto. Si tratta della versione di Java usata da HDInsight 3.6.
    * `maven-shade-plugin`: Usato per generare un file uberjar che contiene questa applicazione, nonché eventuali dipendenze. Viene inoltre usato per impostare il punto di ingresso dell'applicazione, in modo che sia possibile eseguire il file con estensione jar direttamente senza dover specificare la classe principale.

### <a name="producerjava"></a>Producer.java

Il producer comunica con gli host broker di Kafka (nodi di lavoro) e invia i dati a un argomento Kafka. Il frammento di codice seguente è tratto dal file [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) del [repository GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra come impostare le proprietà del producer. Per i cluster abilitati per Enterprise Security è necessario aggiungere un'ulteriore proprietà "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Il consumer comunica con gli host broker Kafka (nodi di lavoro) e legge i record in un ciclo. Il frammento di codice seguente tratto dal file [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) imposta le proprietà del consumer. Per i cluster abilitati per Enterprise Security è necessario aggiungere un'ulteriore proprietà "properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");"

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

In questo codice, il consumer è configurato per la lettura dall'inizio dell'argomento (`auto.offset.reset` è impostato su `earliest`.)

### <a name="runjava"></a>Run.java

Il file [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornisce un'interfaccia della riga di comando che esegue il codice del producer o del consumer. È necessario fornire le informazioni sull'host broker di Kafka come parametro. È facoltativamente possibile includere un valore di ID gruppo che viene usato dal processo consumer. Se si creano più istanze di consumer usando lo stesso ID gruppo, esse bilanceranno il carico durante la lettura dall'argomento.

## <a name="build-and-deploy-the-example"></a>Creare e distribuire l'esempio

### <a name="use-pre-built-jar-files"></a>Usare i file JAR predefiniti

Scaricare i file JAR dall'[esempio di introduzione ad Azure per Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Se il cluster è abilitato per **Enterprise Security Package (ESP)** , usare kafka-producer-consumer-esp.jar. Per copiare i file JAR nel cluster, usare il comando seguente.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Compilare i file JAR dal codice

Se si vuole ignorare questo passaggio, è possibile scaricare i file jar predefiniti dalla sottodirectory `Prebuilt-Jars`. Scaricare il file kafka-producer-consumer.jar. Se il cluster è abilitato per **Enterprise Security Package (ESP)** , usare kafka-producer-consumer-esp.jar. Eseguire il passaggio 3 per copiare il file jar nel cluster HDInsight.

1. Scaricare ed estrarre gli esempi da [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Impostare la directory corrente sulla posizione della directory `hdinsight-kafka-java-get-started\Producer-Consumer`. Se si usa il cluster Kafka abilitato per **Enterprise Security Package (ESP)** , è necessario impostare la posizione sulla sottodirectory `DomainJoined-Producer-Consumer`. Usare il comando seguente per compilare l'applicazione:

    ```cmd
    mvn clean package
    ```

    Questo comando crea una directory denominata `target`, che contiene un file denominato `kafka-producer-consumer-1.0-SNAPSHOT.jar`. Per i cluster ESP il file sarà `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Immettere il comando seguente per copiare il file `kafka-producer-consumer-1.0-SNAPSHOT.jar` nel cluster HDInsight. Quando richiesto, immettere la password per l'utente SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Eseguire l'esempio

1. Sostituire `sshuser` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Per aprire una connessione SSH al cluster, immettere il comando seguente. Quando richiesto, immettere la password per l'account utente SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Per ottenere gli host del broker Kafka, sostituire i valori per `<clustername>` e `<password>` nel comando seguente ed eseguirlo. Usare la stessa combinazione di maiuscole e minuscole per `<clustername>` come illustrato nel portale di Azure. Sostituire `<password>` con la password di accesso al cluster, quindi eseguire il comando:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Questo comando richiede l'accesso ad Ambari. Se il cluster è protetto da un gruppo NSG, eseguire questo comando da un computer in grado di accedere ad Ambari.

1. Per creare l'argomento Kafka `myTest`, immettere il comando seguente:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Per eseguire il producer e scrivere i dati nell'argomento, usare il comando seguente:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Quando l'esecuzione del producer è terminata, usare il comando seguente per leggere dall'argomento:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Verranno visualizzati i record letti e il numero di record.

1. Usare __Ctrl + C__ per uscire dal consumer.

### <a name="multiple-consumers"></a>Consumer multipli

I consumer Kafka usano un gruppo di consumer durante la lettura dei record. L'uso dello stesso gruppo con più consumer consente di ottenere operazioni di lettura con bilanciamento del carico da un argomento. Ogni consumer nel gruppo riceve una parte dei record.

L'applicazione Consumer accetta un parametro che viene usato come ID del gruppo. Ad esempio, il comando seguente avvia un consumer usando un ID gruppo denominato `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Usare __Ctrl + C__ per uscire dal consumer.

Per verificare il funzionamento del processo, usare il comando seguente:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Questo comando usa `tmux` per suddividere il terminale in due colonne. Viene avviato un consumer in ogni colonna, con lo stesso valore di ID di gruppo. Dopo che i consumer hanno completato la lettura, si noti che ognuno di essi ha letto solo una parte dei record. Usare __Ctrl + C__ due volte per uscire da `tmux`.

Il consumo da parte dei client dello stesso gruppo viene gestito tramite le partizioni dell'argomento. In questo esempio di codice per l'argomento `test` creato in precedenza sono presenti otto partizioni. Se si avviano otto consumer, ognuno di essi legge i record da una singola partizione dell'argomento.

> [!IMPORTANT]  
> Un gruppo di consumer non può contenere un numero di istanze di consumer maggiore del numero di partizioni. In questo esempio, un gruppo di consumer può contenere fino a otto consumer perché è questo il numero di partizioni nell'argomento. In alternativa è possibile avere più gruppi di consumer, ognuno con al massimo otto consumer.

I record vengono archiviati in Kafka nell'ordine in cui vengono ricevuti in una partizione. Per ottenere il recapito dei record nell'ordine *all'interno di una partizione*, creare un gruppo di consumer con un numero di istanze corrispondente al numero di partizioni. Per ottenere il recapito dei record nell'ordine *all'interno dell'argomento*, creare un gruppo di consumer con una sola istanza.

## <a name="common-issues-faced"></a>Problemi comuni rilevati

1. **La creazione dell'argomento non riesce** Se il cluster è abilitato per Enterprise Security Pack, usare i [file JAR predefiniti per producer e consumer](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). Il file JAR ESP può essere compilato dal codice nella [sottodirectory `DomainJoined-Producer-Consumer`](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). Le proprietà producer e consumer hanno una proprietà aggiuntiva `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` per i cluster abilitati per ESP.

2. **Errore nei cluster abilitati per ESP**: se le operazioni di producer e consumer non riescono e si usa un cluster abilitato per ESP, verificare che l'utente `kafka` sia presente in tutti i criteri di Ranger. Se non è presente, aggiungerlo a tutti i criteri di Ranger.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le API Apache Kafka Producer e Consumer con Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Usare il proxy REST Kafka](rest-proxy.md)
* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)