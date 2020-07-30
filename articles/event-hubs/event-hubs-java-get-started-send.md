---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Java (legacy)
description: Questo articolo illustra come creare un'applicazione Java che invia/riceve eventi a/da Hub eventi di Azure usando il pacchetto azure-eventhubs precedente.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6efa21f4869dddef9d54001a8669b4b039240910
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371975"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Usare Java per inviare o ricevere eventi da Hub eventi di Azure (azure-eventhubs)

Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto Java **azure-eventhubs**.

> [!WARNING]
> In questo argomento di avvio rapido si usano i pacchetti **azure-eventhubs** e **azure-eventhubs-eph** precedenti. Per una guida di avvio rapido che usa l'ultima versione del pacchetto **azure-messaging-eventhubs**, vedere [Inviare e ricevere eventi con azure-messaging-eventhubs](get-started-java-send-v2.md). Per fare in modo che l'applicazione usi il nuovo pacchetto al posto di quello precedente, vedere la [guida alla migrazione da azure-eventhubs ad azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 


## <a name="prerequisites"></a>Prerequisiti

Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Ambiente di sviluppo in Java. In questa guida di avvio rapido si usa [Eclipse](https://www.eclipse.org/).
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi il valore della chiave di accesso dell'hub eventi seguendo le istruzioni disponibili nell'articolo: [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La chiave di accesso nel codice scritto verrà usata più avanti in questa guida di avvio rapido. Il nome della chiave predefinita è: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Inviare eventi 
Questa sezione illustra come creare un'applicazione Java per inviare eventi a un hub eventi. 

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa guida di avvio rapido per creare una soluzione personalizzata.

### <a name="add-reference-to-azure-event-hubs-library"></a>Aggiungere il riferimento alla libreria di Hub eventi di Azure

La libreria client Java per Hub eventi è disponibile per l'uso in progetti Maven dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Per fare riferimento a questa libreria è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Per diversi tipi di ambienti di compilazione è possibile ottenere in modo esplicito i file JAR rilasciati più recenti dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Per un autore di eventi semplice, importare il pacchetto *com.microsoft.azure.eventhubs* per le classi di client di hub eventi e il pacchetto *com.microsoft.azure.servicebus* per le classi di utilità, ad esempio eccezioni comuni condivise con il client di messaggistica del bus di servizio di Azure. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. Aggiungere una classe denominata `SimpleSend` e includere il codice seguente nella classe:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Costruire la stringa di connessione

Usare la classe ConnectionStringBuilder per costruire un valore di stringa di connessione da passare all'istanza del client di Hub eventi. Sostituire i segnaposto con i valori ottenuti durante la creazione dello spazio dei nomi e dell'hub eventi:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Scrivere il codice per l'invio di eventi

Creare un evento singolare trasformando una stringa nella relativa codifica UTF-8. Creare quindi una nuova istanza del client di Hub eventi dalla stringa di connessione e inviare il messaggio:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Compilare ed eseguire il programma e verificare che non siano presenti errori.

Congratulazioni! Sono stati inviati messaggi a un hub eventi.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Appendice: Modalità di indirizzamento dei messaggi alle partizioni di Hub eventi

Prima che i messaggi vengano recuperati da parte dei consumatori, dovranno essere pubblicati nelle partizioni dagli editori. Quando vengono pubblicati i messaggi nell'Hub eventi in modo sincrono tramite il metodo sendSync() nell'oggetto com.microsoft.azure.eventhubs.EventHubClient, i messaggi possono essere inviati a una partizione specifica o distribuiti a tutte le partizioni disponibili in modalità round robin a seconda che la chiave di partizione sia specificata o meno.

Quando viene specificata una stringa che rappresenta la chiave di partizione, la chiave viene sottoposta a hashing per determinare a quale partizione inviare l'evento.

Quando la chiave di partizione non è impostata, i messaggi verranno distribuiti a round robin a tutte le partizioni disponibili

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Ricevere eventi
Il codice in questa esercitazione si basa sul [codice EventProcessorSample in GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ricevere messaggi con EventProcessorHost in Java

**EventProcessorHost** è una classe Java che semplifica la ricezione di eventi da Hub eventi tramite la gestione di checkpoint persistenti e ricezioni parallele da tali hub. Usando EventProcessorHost è possibile suddividere gli eventi tra più ricevitori, anche se ospitati in nodi diversi. Questo esempio illustra come usare EventProcessorHost per un ricevitore singolo.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per usare EventProcessorHost, è necessario avere un [account di archiviazione di Azure][account di archiviazione di Azure]:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **+ Crea una risorsa** a sinistra dello schermo.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione**. Nella finestra **Crea account di archiviazione** digitare un nome per l'account di archiviazione. Completare il resto dei campi, selezionare l'area geografica desiderata e quindi selezionare **Crea**.
   
    ![Creare un account di archiviazione nel portale di Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selezionare l'account di archiviazione appena creato e quindi **Chiavi di accesso**:
   
    ![Ottenere le chiavi di accesso nel portale di Azure](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Copiare il valore key1 in una posizione temporanea. perché sarà usato più avanti in questa esercitazione.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Creare un progetto Java usando EventProcessorHost

La libreria client Java per hub eventi è disponibile per l'utilizzo in progetti Maven dal [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)ed è possibile farvi riferimento utilizzando la seguente dichiarazione di dipendenza all'interno del file di progetto Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Per diversi tipi di ambienti di compilazione è possibile ottenere in modo esplicito i file JAR rilasciati più recenti dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. La classe è denominata `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Usare il codice seguente per creare una nuova classe denominata `EventProcessorSample`. Sostituire i segnaposto con i valori usati durante la creazione dell'Hub eventi e dell'account di archiviazione:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
   }
   ```
3. Creare un'altra classe denominata `EventProcessor` usando il codice seguente:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Questa esercitazione usa una singola istanza di EventProcessorHost. Per aumentare la velocità effettiva è consigliabile eseguire più istanze di EventProcessorHost, preferibilmente in computer separati.  In questo modo si ottiene anche la ridondanza. In questi casi, le varie istanze si coordinano automaticamente tra loro per ottenere il bilanciamento del carico relativo agli eventi ricevuti. Se si vuole che ognuno dei vari ricevitori elabori *tutti* gli eventi, è necessario usare il concetto **ConsumerGroup** . Quando si ricevono eventi da più macchine, potrebbe risultare utile specificare nomi per le istanze di EventProcessorHost in base alle macchine (o ai ruoli) in cui sono distribuite.

### <a name="publishing-messages-to-eventhub"></a>Pubblicare messaggi in Hub eventi

Prima che i messaggi vengano recuperati da parte dei consumatori, dovranno essere pubblicati nelle partizioni dagli editori. Vale la pena notare che quando vengono pubblicati i messaggi nell'Hub eventi in modo sincrono tramite il metodo sendSync() nell'oggetto com.microsoft.azure.eventhubs.EventHubClient, il messaggio può essere inviato a una partizione specifica o distribuito a tutte le partizioni disponibili in modalità round robin a seconda che la chiave di partizione sia specificata o meno.

Quando viene specificata una stringa che rappresenta la chiave di partizione, la chiave viene sottoposta a hashing per determinare a quale partizione inviare l'evento.

Quando la chiave di partizione non è impostata, i messaggi vengono distribuiti come round robin a tutte le partizioni disponibili

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementazione di un CheckpointManager personalizzato per EventProcessorHost (EPH)

L'API fornisce un meccanismo per implementare la gestione del checkpoint personalizzati per gli scenari in cui l'implementazione predefinita non è compatibile con il caso d'uso.

La gestione del checkpoint predefinito sfrutta l'archiviazione dei blob, ma se si esegue l'override di gestione di checkpoint utilizzata dall'EPH con la propria implementazione, è possibile usare qualsiasi archivio che si desidera per sostenere la realizzazione dell'implementazione del gestore del checkpoint.

Creare una classe che implementi l'interfaccia com.microsoft.azure.eventprocessorhost.ICheckpointManager

Usare l'implementazione personalizzata di gestione del checkpoint (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

All'interno dell'implementazione è possibile ignorare il meccanismo di checkpoint predefinito e implementare checkpoint basati sul proprio archivio dati, come SQL Server, CosmosDB, Azure Cache for Redis e così via. È consigliabile che l'archivio utilizzato per sostenere la realizzazione dell'implementazione del manager del checkpoint sia accessibile a tutte le istanze EPH che elaborano gli eventi per il gruppo di consumer.

È possibile usare qualsiasi archivio dati disponibile nell'ambiente in uso.

La classe com.microsoft.azure.eventprocessorhost.EventProcessorHost offre 2 costruttori che consentono di eseguire l'override di gestione del checkpoint per EventProcessorHost.


## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funzionalità e terminologia di Hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)

