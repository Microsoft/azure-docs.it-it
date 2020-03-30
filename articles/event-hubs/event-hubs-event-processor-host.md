---
title: Ricevere eventi tramite l'host del processore di eventi - Hub eventi di Azure | Microsoft Docs
description: Questo articolo descrive l'host del processore di eventi in Hub eventi di Azure, che semplifica la gestione di checkpoint e lease e la lettura in parallelo degli eventi.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372235"
---
# <a name="event-processor-host"></a>Host processore di eventi
> [!NOTE]
> Questo articolo si applica alla versione precedente di Azure Event Hubs SDK. Per informazioni su come eseguire la migrazione del codice alla versione più recente dell'SDK, vedere queste guide alla migrazione. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Vedere anche Bilanciare il carico delle [partizioni tra più istanze dell'applicazione.](event-processor-balance-partition-load.md)

Hub eventi di Azure è un potente servizio di inserimento di dati di telemetria che può essere usato per lo streaming di milioni di eventi a basso costo. Questo articolo descrive come usare gli eventi inseriti tramite *Host processore di eventi* (EPH); un agente consumer intelligente che semplifica la gestione dei checkpoint, dei leasing e dei lettori di eventi paralleli.  

La chiave per ridurre il numero di istanze di Hub eventi è l'idea di consumer partizionato. A differenza dei criteri relativi ai [consumer concorrenti](https://msdn.microsoft.com/library/dn568101.aspx), il modello consumer partizionato consente un'elevata scalabilità rimuovendo il collo di bottiglia dovuto alla contesa e agevolare il parallelismo end to end.

## <a name="home-security-scenario"></a>Scenario di sicurezza abitativa

Come scenario di esempio, si consideri una società per la sicurezza delle abitazioni che monitora 100.000 abitazioni. Ogni minuto, riceve dati da diversi sensori, ad esempio un rilevatore di movimento, un sensore di apertura di porte/finestre, un rilevatore di vetri rotti, e così via, installato in ogni abitazione. L'azienda fornisce un sito Web ai residenti per monitorare l'attività della propria abitazione quasi in tempo reale.

Ogni sensore esegue il push dei dati a un hub eventi. L'hub eventi è configurato con 16 partizioni. Dal lato consumer, è necessario un meccanismo che riesca a leggere questi eventi, consolidarli (filtrarli, aggregarli, ecc.) ed esegua il dump dell'aggregazione in un BLOB di archiviazione, previsto quindi in una pagina Web intuitiva.

## <a name="write-the-consumer-application"></a>Scrivere l'applicazione consumer

Quando si progetta il consumer in un ambiente distribuito, lo scenario deve gestire i requisiti seguenti:

1. **Scalabilità:** creare diversi consumer, ciascuno tra questi che dispone di proprietà di lettura derivanti da alcune partizioni di Hub eventi.
2. **Bilanciamento del carico:** aumentare o ridurre in modo dinamico i consumer. Ad esempio, quando un nuovo tipo di sensore (ad esempio, un rilevatore di monossido di carbonio) viene aggiunto a ciascuna abitazione, il numero di eventi aumenta. In tal caso, l'operatore (un essere umano) aumenta il numero di istanze del consumer. Quindi, il pool di consumer può ribilanciare il numero di partizioni di cui è proprietario, condividendo il carico con i consumer appena aggiunti.
3. **Recupero intuitivo dagli errori:** in caso di esito negativo da parte di un consumer (**consumer A**) (ad esempio, la macchina virtuale che ospita il consumer si arresta improvvisamente), gli altri consumers devono riuscire ad acquisire le partizioni delle quali è proprietario il **consumer A** e procedere. Inoltre, il punto di continuazione, chiamato *checkpoint* oppure *offset*, deve trovarsi nel punto esatto in cui il **consumer A** ha avuto esito negativo, o leggermente prima rispetto al punto.
4. **Uso di eventi:** mentre i tre punti precedenti affrontano la gestione del consumer, è necessario che esista un codice per gli eventi e che questo venga usato a uno scopo utile; ad esempio, aggregandolo e caricandolo nell'archiviazione BLOB.

Al posto di creare una soluzione personalizzata a tal proposito, Hub eventi offre questa funzionalità tramite l'interfaccia [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) e la classe [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost).

## <a name="ieventprocessor-interface"></a>Interfaccia IEventProcessor

In primo luogo, l'uso delle applicazioni si serve dell'interfaccia [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) sfruttando quattro metodi: [OpenAsync, CloseAsync, ProcessErrorAsync e ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Questa interfaccia contiene il codice reale per usare gli eventi inviati da Hub eventi. L'implementazione del codice seguente è semplice:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Quindi, creare un'istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). In base all'overload, durante la creazione dell'istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) nel costruttore, vengono usati i parametri seguenti:

- **nome host:** il nome di ciascuna istanza del consumer. Ogni istanza di **EventProcessorHost** deve avere un valore univoco per questa variabile all'interno di un gruppo di consumer, pertanto non codificare questo valore.
- **eventHubPath:** il nome dell'hub eventi.
- **consumerGroupName:** Hub eventi usa **$Default** come nome predefinito del gruppo di consumer, ma è buona norma creare un gruppo di consumer per l'aspetto specifico dell'elaborazione.
- **eventHubConnectionString:** la stringa di connessione all'hub eventi che può essere recuperata dal portale di Azure. Questa stringa di connessione deve disporre delle autorizzazioni **Listen** per l'hub eventi.
- **storageConnectionString:** l'account di archiviazione usato per la gestione delle risorse interne.

Infine, il consumer registra le istanze di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) con il servizio Hub eventi. La registrazione di una classe del processore di eventi con un'istanza di EventProcessorHost avvia l'elaborazione di eventi. La registrazione indica al servizio Hub eventi di prevedere che l'app consumer faccia uso degli eventi provenienti da alcune delle sue partizioni e richiami il codice di implementazione di [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) ogniqualvolta effettui il push di eventi da usare. 


### <a name="example"></a>Esempio

Ad esempio, immaginiamo che esistano 5 macchine virtuali (VM) dedicate all'uso degli eventi e una semplice applicazione console in ogni macchina virtuale, che si occupa del lavoro effettivo di utilizzo. Ogni applicazione console crea quindi un'istanza [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) e la registra con il servizio Hub eventi.

In questo scenario di esempio, supponiamo che vengano allocate 16 partizioni per le 5 istanze di **EventProcessorHost**. Alcune istanze di **EventProcessorHost** potrebbero disporre di alcune partizioni in più rispetto ad altre. Per ogni partizione di cui dispone un'istanza di **EventProcessorHost**, viene creata un'istanza di classe `SimpleEventProcessor`. Pertanto, sono presenti complessivamente 16 istanze di `SimpleEventProcessor`, una per ciascuna partizione.

L'elenco seguente riepiloga l'esempio:

- 16 partizioni di Hub eventi.
- 5 macchine virtuali, app 1 consumer (ad esempio, Consumer.exe) in ogni macchina virtuale.
- 5 istanze EPH registrate, 1 in ogni macchina virtuale da Consumer.exe.
- 16 oggetti `SimpleEventProcessor` creati dalle 5 istanze di EPH.
- 1 app Consumer.exe potrebbe contenere 4 oggetti `SimpleEventProcessor`, poiché 1 istanza EPH può essere proprietaria di 4 partizioni.

## <a name="partition-ownership-tracking"></a>Rilevamento della proprietà di partizione

La proprietà di una partizione di un'istanza EPH (o un consumer) viene rilevata tramite l'account di Archiviazione di Azure che viene fornito a scopo di rilevamento. È possibile visualizzare il rilevamento come semplice tabella, come indicato di seguito. È possibile visualizzarne l'implementazione effettiva esaminando i BLOB nell'account di archiviazione specificato:

| **Nome gruppo di consumer** | **Partition ID** | **Nome host (proprietario)** | **Orario del lease (o della proprietà) acquisito** | **Offset nella partizione (checkpoint)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

In questo caso, ogni host acquisisce la proprietà di una partizione per un determinato periodo di tempo (la durata del lease). Se un host ha esito negativo (la macchina virtuale si arresta), il lease scade. Altri host tentano di ottenere la proprietà della partizione e un host ha esito positivo. Questo processo ripristina il lease per la partizione con un nuovo proprietario. In questo modo, solo un unico lettore per volta può leggere in qualsiasi partizione specificata all'interno di un gruppo di consumer.

## <a name="receive-messages"></a>Ricevere messaggi

Ogni chiamata a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) prevede una raccolta di eventi. L'utente è responsabile di tali eventi. Se si vuole avere la certezza che l'host del processore elabori ogni messaggio almeno una volta, è necessario scrivere il proprio codice di ripetizione dei tentativi. Fare attenzione, tuttavia, a inserire messaggi non elaborabili.

Si consiglia di eseguire le operazioni con una certa rapidità; vale a dire, elaborandole il meno possibile. In caso contrario, usare i gruppi di consumer. Se è necessario scrivere nell'archiviazione ed eseguire alcune operazioni di routing, è preferibile usare due gruppi di consumer e avere due implementazioni [di IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) che vengono eseguite separatamente.

A un certo punto durante l'elaborazione, si potrebbe voler tenere traccia di ciò che è stato letto e completato. Tenere traccia è essenziale qualora sia necessario riavviare la lettura, in modo da non dover tornare all'inizio del flusso. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) semplifica il rilevamento mediante i *checkpoint*. Un checkpoint è una posizione o un offset, per una data partizione, all'interno di un gruppo di consumer specifico, in cui si è soddisfatti di aver eseguito l'elaborazione dei messaggi. Per contrassegnare un checkpoint in **EventProcessorHost** chiamare il metodo [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) nell'oggetto [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext). Questa operazione viene eseguita all'interno del metodo [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync), ma è possibile anche in [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Checkpoint

Il metodo [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) presenta due overload: il primo, senza parametri, esegue il checkpoint dell'offset di eventi più elevato all'interno della raccolta restituita da [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Questo offset è un contrassegno di limite "massimo", si presuppone quindi che siano stati elaborati tutti gli eventi recenti quando questi vengono chiamati. Se questo metodo viene usato in questo modo, tenere presente che l'utente dovrà chiamarlo dopo aver restituito il codice di elaborazione di altri eventi. Il secondo overload consente di specificare un'istanza [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) per eseguire il checkpoint. Questo metodo consente di usare un diverso tipo di limite a per eseguire il checkpoint. Con questo limite, è possibile sfruttare un limite "minimo": è stato elaborato l'evento minimo in sequenza di cui si è certi. Questo overload viene fornito per consentire la flessibilità nella gestione dell'offset.

Quando il checkpoint viene eseguito, viene scritto un file JSON con informazioni specifiche sulla partizione (in particolare, l'offset), nell'account di archiviazione fornito nel costruttore per [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Questo file viene aggiornato continuamente. È fondamentale prendere in considerazione il checkpoint in contesto, sarebbe pertanto poco saggio eseguire il checkpoint di tutti i messaggi. L'account di archiviazione usato per eseguire il checkpoint probabilmente non gestirà il carico corrente, ma, ancora più importante, eseguire il checkpoint di ogni singolo evento è indicativo di un criterio di messaggistica in coda per il quale una coda del bus di servizio potrebbe essere un'opzione migliore rispetto a un hub eventi. L'idea alla base di Hub eventi è di ottenere "almeno un" recapito su larga scala. Per rendere idempotenti i sistemi a valle, è facile eseguire il ripristino a seguito di errori o il riavvio del risultato negli stessi eventi ricevuti più volte.

## <a name="thread-safety-and-processor-instances"></a>Istanze di elaborazione e sicurezza del thread

Per impostazione predefinita [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) è thread-safe e si comporta in modo sincrono rispetto all'istanza di [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Quando vengono ricevuti gli eventi per una partizione, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) viene chiamato sull'istanza **IEventProcessor** per quella partizione e bloccherà altre chiamate a **ProcessEventsAsync** per la partizione. I messaggi successivi e le chiamate a **ProcessEventsAsync** rimangono in coda senza risultare chiaramente visibili mentre message pump continua a essere eseguito in background in altri thread. Questa sicurezza per i thread elimina la necessità di raccolte thread-safe e un aumenta significativamente le prestazioni.

## <a name="shut-down-gracefully"></a>Arresto graduale

Infine [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) consente una chiusura normale di tutti i lettori di partizioni e deve sempre essere chiamato quando si arresta un'istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). In caso contrario, potrebbero verificarsi ritardi durante l'avvio di altre istanze di **EventProcessorHost** a causa della scadenza del lease e di conflitti come valore Epoch. La gestione delle epoch è trattata in dettaglio nella sezione [Epoch](#epoch) dell'articolo. 

## <a name="lease-management"></a>Gestione del lease
La registrazione di una classe del processore di eventi con un'istanza di EventProcessorHost avvia l'elaborazione di eventi. L'istanza host ottiene lease in alcune partizioni dell'Hub eventi, possibilmente recuperandone alcuni da altre istanze host, in modo da eseguire la convergenza su una distribuzione uniforme delle partizioni tra tutte le istanze di host. Per ogni partizione con lease, l'istanza dell'host crea un'istanza della classe di evento del processore, quindi riceve gli eventi dalla partizione e li passa all'istanza del processore di eventi. Con l'aggiunta di più istanze e il recupero di più lease EventProcessorHost infine bilancia il carico tra tutti i consumer.

Come spiegato in precedenza, la tabella di rilevamento semplifica notevolmente la natura di scalabilità automatica di [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Quando un'istanza di **EventProcessorHost** viene avviata, acquisisce il maggior numero di lease possibile e inizia la lettura degli eventi. Quando i lease si avvicinano alla scadenza, **EventProcessorHost** tenta di rinnovarli inserendo una prenotazione. Se il lease è disponibile per il rinnovo, il processore continua la lettura, ma, in caso contrario, il lettore viene chiuso e viene chiamato [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync). **CloseAsync** è un buon momento per eseguire la pulizia finale della partizione.

**EventProcessorHost** include una proprietà [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions). Questa proprietà consente un controllo sulla gestione del lease. Impostare queste opzioni prima di registrare l'implementazione [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

## <a name="control-event-processor-host-options"></a>Controllo delle opzioni dell'host processore di eventi

Inoltre, uno degli overload di [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) accetta un oggetto [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) come parametro. Usare questo parametro per controllare il comportamento di [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definisce un evento e quattro proprietà:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): le dimensioni massime della raccolta che si vogliono ricevere in una chiamata di [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Questa dimensione non è il valore minimo, solo la dimensione massima. Se sono presenti meno messaggi da ricevere **ProcessEventsAsync** viene eseguita con il numero massimo di messaggi disponibili in quel momento.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): un valore usato dal canale AMQP sottostante per determinare il limite massimo di messaggi che deve ricevere il client. Questo valore deve essere maggiore o uguale a [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): se questo parametro è **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) viene chiamato quando si verifica il timeout della chiamata sottostante per ricevere eventi in una partizione. Questo metodo è utile per intraprendere azioni basate sul tempo durante i periodi di inattività nella partizione.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): consente l'impostazione di un puntatore alla funzione o un'espressione lambda, che viene chiamata per specificare l'offset iniziale quando un lettore inizia la lettura di una partizione. Senza la specifica di questo offset, il lettore inizia con l'evento meno recente, a meno che non sia già stato salvato un file JSON con un offset nell'account di archiviazione fornito al costruttore di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Questo metodo è utile quando si vuole modificare il comportamento di avvio del lettore. Quando questo metodo viene richiamato, il parametro dell'oggetto contiene l'ID di partizione per cui viene avviato il lettore.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): consente di ricevere una notifica di eventuali eccezioni sottostanti che si verificano in [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se la situazione non è quella prevista, questo evento è un buon punto di avviare la ricerca.

## <a name="epoch"></a>Epoca

Ecco come funziona l'epoca di ricezione:

### <a name="with-epoch"></a>Con Epoch
Epoch è un identificatore univoco (valore epoch) utilizzato dal servizio per applicare la proprietà di partizione/lease. Creare un ricevitore basato su Epoch utilizzando il metodo [CreateEpochReceiver.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) Questo metodo crea un ricevitore basato su Epoch. Il ricevitore viene creato per una partizione dell'hub eventi specifica dal gruppo di consumer specificato.

La funzione epoca offre agli utenti la possibilità di garantire che vi sia un solo ricevitore in un gruppo di consumer in qualsiasi momento, con le seguenti regole:

- Se non esiste alcun ricevitore in un gruppo di consumer, l'utente può creare un ricevitore con qualsiasi valore di epoca.
- Se è presente un ricevitore con un valore di epoca e1 e viene creato un nuovo ricevitore con un valore e2 in cui e1 <e2, il ricevitore con e1 verrà disconnesso automaticamente, il ricevitore con e2 verrà creato correttamente.
- Se è presente un ricevitore con un valore di epoca e1 e viene creato un nuovo ricevitore con un valore e2 in cui e1 > e2, la creazione di e2 con esito negativo con l'errore: esiste già un ricevitore con epoca e1.

### <a name="no-epoch"></a>Nessuna epoca
Creare un ricevitore non basato su Epoch utilizzando il [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metodo. 

Esistono alcuni scenari nell'elaborazione del flusso in cui gli utenti desiderano creare più ricevitori in un singolo gruppo di consumer. Per supportare tali scenari, abbiamo la capacità di creare un ricevitore senza epoca e in questo caso permettiamo fino a 5 ricevitori simultanei nel gruppo di consumer.

### <a name="mixed-mode"></a>Modalità mista
Non è consigliabile utilizzare l'applicazione in cui si crea un ricevitore con epoca e quindi si passa a no-epoch o viceversa nello stesso gruppo di consumer. Tuttavia, quando si verifica questo comportamento, il servizio gestisce utilizzando le regole seguenti:However, when this behavior, the service handles it using the following rules:

- Se è già stato creato un ricevitore con epoca e1 e sta ricevendo attivamente eventi e un nuovo ricevitore viene creato senza epoca, la creazione di un nuovo ricevitore avrà esito negativo. I ricevitori Epoch hanno sempre la precedenza nel sistema.
- Se c'era un ricevitore già creato con epoca e1 e ottenuto disconnesso e un nuovo ricevitore viene creato senza epoche su un nuovo MessagingFactory, la creazione di un nuovo ricevitore avrà esito positivo. C'è un avvertimento qui che il nostro sistema rileverà la "disconnessione del ricevitore" dopo 10 dollari.
- Se sono presenti uno o più ricevitori creati senza epoca e un nuovo ricevitore viene creato con epoch e1, tutti i vecchi ricevitori vengono disconnessi.


> [!NOTE]
> È consigliabile utilizzare gruppi di consumer diversi per le applicazioni che utilizzano le epoche e per quelle che non utilizzano le epoch per evitare errori. 


## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con l'host processore di eventi, vedere gli articoli seguenti per altre informazioni su Hub eventi:

- Introduzione all'Hub eventi
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
* [Disponibilità e coerenza nell'Hub eventi](event-hubs-availability-and-consistency.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Esempi di Hub eventi in GitHubEvent Hubs samples on GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
