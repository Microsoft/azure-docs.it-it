---
title: Usare AMQP con l'API del servizio messaggi Java & il bus di servizio di Azure
description: Come usare JMS (Java Message Service ) con il bus di servizio di Azure e il protocollo AMQP (Advanced Message Queuing Protocol) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ccea6175d0baec56b609538d15c32892bb2edff0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341732"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Usare Java Message Service (JMS) con il bus di servizio di Azure e AMQP 1,0
Questo articolo illustra come usare le funzionalità di messaggistica del bus di servizio di Azure (code e argomenti di pubblicazione/sottoscrizione) dalle applicazioni Java usando lo standard API JMS (Java Message Service) più diffuso. È disponibile un [Articolo complementare](service-bus-amqp-dotnet.md) che illustra come eseguire la stessa operazione usando l'API .NET del bus di servizio di Azure. È possibile consultare queste due guide per acquisire informazioni sulla messaggistica multipiattaforma con AMQP 1.0.

AMQP (Advanced Message Queuing Protocol) 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare potenti applicazioni di messaggistica multipiattaforma.

Il supporto per AMQP 1,0 nel bus di servizio di Azure significa che è possibile usare le funzionalità di Accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione da una vasta gamma di piattaforme usando un protocollo binario efficiente. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

## <a name="get-started-with-service-bus"></a>Introduzione al bus di servizio
Questa guida presuppone che sia già presente uno spazio dei nomi del bus di servizio contenente una coda denominata `basicqueue` . In caso contrario, è possibile [creare lo spazio dei nomi e la coda](service-bus-create-namespace-portal.md) usando il [portale di Azure](https://portal.azure.com). Per altre informazioni su come creare spazi dei nomi e code del bus di servizio, vedere [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Le code e gli argomenti partizionati supportano anche AMQP. Per altre informazioni, vedere le [entità di messaggistica partizionate](service-bus-partitioning.md) e [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Download della libreria client JMS basata su AMQP 1.0
Per informazioni su dove scaricare la versione più recente della libreria client di Apache Qpid JMS AMQP 1,0, visitare [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html) .

È necessario aggiungere i seguenti quattro file JAR dall'archivio di distribuzione AMQP 1.0 di Apache Qpid al CLASSPATH Java durante la compilazione e l'esecuzione di applicazioni JMS con il bus di servizio:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[versione].jar

> [!NOTE]
> Le versioni e i nomi JAR JMS possono essere stati modificati. Per informazioni dettagliate, vedere [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Compilazione di applicazioni Java
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface (JNDI)
JMS usa l'interfaccia JNDI (Java Naming and Directory Interface) per creare una separazione tra i nomi logici e i nomi fisici. Con JNDI vengono risolti due tipi di oggetti JMS: ConnectionFactory e Destination. JNDI utilizza un modello di provider in cui è possibile inserire diversi servizi directory per gestire le attività di risoluzione dei nomi. La libreria JMS AMQP 1,0 di Apache Qpid viene fornita con un semplice provider JNDI basato su file di proprietà configurato usando un file delle proprietà con il formato seguente:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Impostare il contesto JNDI e configurare ConnectionFactory

**ConnectionString** a cui viene fatto riferimento in quello disponibile nei ' criteri di accesso condiviso ' nel [portale di Azure](https://portal.azure.com) nella **stringa di connessione primaria**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurare le code di destinazione di producer e consumer
La voce usata per definire una destinazione nel provider JNDI basato sul file delle proprietà Qpid è nel formato seguente:

Per creare la coda di destinazione per il producer: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Per creare una coda di destinazione per il consumer: 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Scrivere l'applicazione JMS
Non esistono API speciali oppure opzioni obbligatorie quando si utilizza JMS con il bus di servizio. Tuttavia, esistono alcune limitazioni che verranno illustrate più avanti. Come per qualsiasi applicazione JMS, la prima operazione da eseguire è la configurazione dell'ambiente JNDI, in modo da poter risolvere gli oggetti di tipo **ConnectionFactory** e le destinazioni.

#### <a name="configure-the-jndi-initialcontext"></a>Configurare il contesto JNDI iniziale
La configurazione dell'ambiente JNDI viene eseguita passando una tabella hash di informazioni di configurazione al costruttore della classe javax.naming.InitialContext. I due elementi necessari nella tabella hash sono il nome della classe della factory del contesto iniziale e l'URL del provider. Nel codice seguente viene illustrato come configurare l'ambiente JNDI per usare il provider JNDI basato sul file delle proprietà Qpid con un file delle proprietà denominato **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Semplice applicazione JMS che usa la coda del bus di servizio
L'applicazione di esempio seguente consente di inviare e ricevere messaggi di testo JMS verso e da una coda del bus di servizio con il nome JNDI logico QUEUE.

È possibile accedere a tutte le informazioni sul codice sorgente e sulla configurazione dalla [Guida introduttiva della coda JMS degli esempi del bus di servizio di Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Eseguire l'applicazione
Passare il valore di **Stringa di connessione** in Criteri di accesso condiviso per eseguire l'applicazione.
Eseguendo l'applicazione viene generato l'output del modulo seguente:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mapping della disposizione AMQP e dell'operazione del bus di servizio
Una disposizione AMQP si traduce in un'operazione del bus di servizio nel modo indicato di seguito:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Argomenti di JMS e argomenti del bus di servizio
L'uso di argomenti e sottoscrizioni del bus di servizio di Azure tramite l'API JMS (Java Message Service) fornisce funzionalità di base per l'invio e la ricezione. Si tratta di una scelta comoda quando si trasferiscono le applicazioni da altri broker di messaggi con API conformi a JMS, anche se gli argomenti del bus di servizio differiscono dagli argomenti JMS e richiedono alcune modifiche. 

Gli argomenti del bus di servizio di Azure instradano i messaggi in sottoscrizioni denominate, condivise e durevoli gestite tramite l'interfaccia di gestione risorse di Azure, gli strumenti da riga di comando di Azure o tramite la portale di Azure. Ogni sottoscrizione consente un massimo di 2000 regole di selezione, ciascuna delle quali può avere una condizione di filtro e, per i filtri SQL, anche un'azione di trasformazione dei metadati. Ogni corrispondenza di condizione di filtro consente di selezionare il messaggio di input da copiare nella sottoscrizione.  

La ricezione di messaggi dalle sottoscrizioni è identico a ricevere messaggi dalle code. A ogni sottoscrizione è associata una coda di messaggi non recapitabili e la possibilità di inviare automaticamente i messaggi a un'altra coda o a altri argomenti. 

Gli argomenti JMS consentono ai client di creare in modo dinamico Sottoscrittori non durevoli e durevoli che facoltativamente consentono di filtrare messaggi con selettori di messaggi. Queste entità non condivise non sono supportate dal bus di servizio. La sintassi della regola di filtro SQL per il bus di servizio è, tuttavia, simile alla sintassi del selettore dei messaggi supportata da JMS. 

Il lato Publisher dell'argomento JMS è compatibile con il bus di servizio, come illustrato in questo esempio, ma i sottoscrittori dinamici non lo sono. Le API JMS correlate alla topologia seguenti non sono supportate con il bus di servizio. 

## <a name="unsupported-features-and-restrictions"></a>Funzionalità non supportate e restrizioni
Quando si utilizza JMS su AMQP 1.0 con il bus di servizio esistono le seguenti restrizioni:

* È consentito solo un oggetto **MessageProducer** o **MessageConsumer** per **sessione**. Se si vuole creare più oggetti **MessageProducers** o **MessageConsumers** in un'applicazione, creare una **sessione** dedicata per ognuno di essi.
* Le sottoscrizioni di argomenti volatili non sono attualmente supportate.
* **MessageSelectors** non sono attualmente supportati.
* Le transazioni distribuite non sono supportate, ma le sessioni transazionali sono supportate.

Inoltre, il bus di servizio di Azure separa il piano di controllo dal piano dati e pertanto non supporta alcune delle funzioni di topologia dinamica di JMS:

| Metodo non supportato          | Sostituire con                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Creare una sottoscrizione dell'argomento con portabilità del selettore di messaggi                                 |
| createDurableConsumer       | Creare una sottoscrizione dell'argomento con portabilità del selettore di messaggi                                 |
| createSharedConsumer        | Gli argomenti del bus di servizio sono sempre condivisibili, vedere le sezioni precedenti                                       |
| createSharedDurableConsumer | Gli argomenti del bus di servizio sono sempre condivisibili, vedere le sezioni precedenti                                       |
| createTemporaryTopic        | Creare un argomento tramite l'API, gli strumenti o il portale di gestione con *AutoDeleteOnIdle* impostato su un periodo di scadenza |
| createTopic                 | Creare un argomento tramite l'API, gli strumenti o il portale di gestione                                           |
| unsubscribe                 | Eliminare l'argomento tramite l'API, gli strumenti o il portale di gestione                                             |
| createBrowser               | Non supportato. Usare la funzionalità Peek() dell'API del bus di servizio                         |
| createQueue                 | Creare una coda tramite portale/strumenti/API di gestione                                           | 
| createTemporaryQueue        | Creare una coda tramite portale/strumenti/API di gestione con *AutoDeleteOnIdle* impostato su un periodo di scadenza |
| receiveNoWait               | usare il metodo Receive () fornito da Service Bus SDK e specificare un timeout molto basso o zero |

## <a name="summary"></a>Summary
Questa guida dettagliata ha illustrato come accedere alle funzionalità di messaggistica negoziata (code e pubblicazione/sottoscrizione di argomenti) del bus di servizio da Java usando la diffusa API JMS e AMQP 1.0.

È anche possibile utilizzare AMQP 1.0 per il bus di servizio da altri linguaggi, tra cui .NET, C, Python e PHP. I componenti creati con questi linguaggi possono scambiare messaggi in modo affidabile e con la massima fedeltà grazie al supporto per AMQP 1.0 nel bus di servizio.

## <a name="next-steps"></a>Passaggi successivi
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio di Azure](service-bus-amqp-overview.md)
* [Come usare AMQP 1.0 con l'API .NET del bus di servizio](service-bus-dotnet-advanced-message-queuing.md)
* [Guida per sviluppatori di AMQP 1.0 per il bus di servizio](service-bus-amqp-dotnet.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Centro per sviluppatori Java](https://azure.microsoft.com/develop/java/)

