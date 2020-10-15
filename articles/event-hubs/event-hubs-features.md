---
title: Panoramica delle funzionalità - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni dettagliate sulle funzionalità e la terminologia di Hub eventi di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9e004b3a8a9dd454eae5a20564a1ab74a26b66d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936232"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funzionalità e terminologia di Hub eventi di Azure

Hub eventi di Azure è una servizio di elaborazione degli eventi scalabile che inserisce ed elabora grandi volumi di eventi e dati, con bassa latenza e affidabilità elevata. Vedere [Che cos'è Hub eventi?](./event-hubs-about.md) per una panoramica generale.

Questo articolo si basa sulle informazioni presenti nella [panoramica](./event-hubs-about.md) e contiene dettagli tecnici e informazioni sull'implementazione relativi ai componenti e alle funzionalità di Hub eventi.

## <a name="namespace"></a>Spazio dei nomi
Uno spazio dei nomi di Hub eventi specifica un contenitore di ambito univoco, identificato dal [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), in cui si crea uno o più hub eventi o argomenti Kafka. 

## <a name="event-hubs-for-apache-kafka"></a>Hub eventi per Apache Kafka

[Questa funzionalità](event-hubs-for-kafka-ecosystem-overview.md) offre un endpoint che consente ai clienti di parlare con gli Hub eventi usando il protocollo Kafka. Questa integrazione offre ai clienti un endpoint Kafka. Ciò consente ai clienti di configurare le proprie applicazioni Kafka esistenti per parlare con gli Hub eventi offrendo un'alternativa all'esecuzione dei propri cluster Kafka. Hub eventi per Apache Kafka supporta il protocollo Kafka 1.0 e versioni successive. 

Con questa integrazione, non è necessario eseguire i cluster Kafka o gestirli con Zookeeper. In questo modo è possibile usare alcune delle funzionalità più complesse di Hub eventi, ad esempio acquisizione, aumento automatico e ripristino di emergenza geografico.

Questa integrazione consente inoltre alle applicazioni come Mirror Maker o ai framework come Kafka Connect di lavorare senza cluster solo con le modifiche alla configurazione. 

## <a name="event-publishers"></a>Autori di eventi

Qualsiasi entità che invia dati a un hub eventi è un produttore di eventi o *autore di eventi*. Gli autori di eventi possono pubblicare eventi usando HTTPS o AMQP 1.0 o Kafka 1.0 (e versioni successive). Gli autori di eventi usano un token di firma di accesso condiviso per identificarsi con un hub eventi e possono avere un'identità univoca oppure usare un token di firma di accesso condiviso comune.

### <a name="publishing-an-event"></a>Pubblicazione di un evento

È possibile pubblicare un evento tramite AMQP 1.0, Kafka 1.0 (e versioni successive) o HTTPS. Hub eventi offre [classi e librerie client](./event-hubs-dotnet-framework-getstarted-send.md) per la pubblicazione di eventi in un hub eventi dai client .NET. Per altre piattaforme e runtime, è possibile utilizzare qualsiasi client AMQP 1.0, ad esempio [Apache Qpid](https://qpid.apache.org/). È possibile pubblicare eventi singolarmente o in batch. Una singola pubblicazione (istanza dei dati dell'evento) ha un limite di 1 MB, indipendentemente dal fatto che si tratti di un singolo evento o di un batch. La pubblicazione di eventi di dimensioni superiori alla soglia determina un errore. È consigliabile che i Publisher non siano a conoscenza delle partizioni all'interno dell'hub eventi e specifichino solo una *chiave di partizione* (introdotta nella sezione successiva) o la relativa identità tramite il token SAS.

La scelta di utilizzare AMQP o HTTPS dipende dallo scenario di utilizzo. AMQP richiede di stabilire un socket bidirezionale persistente oltre alla sicurezza a livello di trasporto (TLS) o SSL/TLS. AMQP presenta costi di rete maggiori durante l'inizializzazione della sessione, tuttavia HTTPS richiede un sovraccarico TLS aggiuntivo per ogni richiesta. AMQP offre prestazioni più elevate per i server di pubblicazione più attivi.

![Chiavi di partizione](./media/event-hubs-features/partition_keys.png)

Hub eventi garantisce che tutti gli eventi che condividono un valore di chiave di partizione vengano recapitati in ordine e alla stessa partizione. Se si usano chiavi di partizione con i criteri di autore, l'identità dell’autore e il valore della chiave di partizione devono corrispondere. In caso contrario si verificherà un errore.

### <a name="publisher-policy"></a>Criteri di autore

Hub eventi consente un controllo granulare degli autori di eventi tramite *criteri di autore*. I criteri di autore sono funzionalità di runtime progettate per consentire un numero elevato di autori di eventi indipendenti. Con i criteri di autore, ogni autore usa il proprio identificatore univoco durante la pubblicazione di eventi in un hub eventi, con il meccanismo seguente:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Non è necessario creare nomi di autore prima di procedere, ma devono corrispondere al token SAS utilizzato quando si pubblica un evento, al fine di garantire le identità di autore indipendenti. Quando si usano i criteri dei publisher, il valore di **PartitionKey** è impostato sul nome del publisher. Per il corretto funzionamento, questi valori devono corrispondere.

## <a name="capture"></a>Acquisizione

[Acquisizione di Hub eventi](event-hubs-capture-overview.md) consente di acquisire automaticamente i dati in streaming in Hub eventi e salvarli, a propria scelta, in un account di archiviazione BLOB o un account del servizio Azure Data Lake. È possibile abilitare la funzione di acquisizione dal portale di Azure e specificare una dimensione minima e l'intervallo di tempo per eseguire l'acquisizione. L'acquisizione di Hub eventi consente di specificare un contenitore e un account di Archiviazione BLOB di Azure oppure un account del servizio Azure Data Lake da usare per archiviare i dati acquisiti. I dati acquisiti vengono scritti nel formato di Apache Avro.

## <a name="partitions"></a>Partizioni
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Token di firma di accesso condiviso

Hub eventi usa le *firme di accesso condiviso*, disponibili a livello di spazio dei nomi e hub eventi. Un token SAS viene generato da una chiave SAS ed è un hash SHA di un URL, codificato in un formato specifico. Usando il nome della chiave (criterio) e il token, Hub eventi può rigenerare l'hash e quindi autenticare il mittente. In genere, i token di firma di accesso condiviso per gli autori di eventi vengono creati con privilegi di **invio** solo in un hub eventi specifico. Questo meccanismo di URL token SAS costituisce la base per l'identificazione dell’autore introdotta nei criteri di autore. Per altre informazioni sull'uso di SAS, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumer di eventi

Qualsiasi entità che legge i dati dell'evento da un hub eventi è un *consumer di eventi*. Tutti i consumer di Hub eventi si connettono tramite la sessione AMQP 1.0 e gli eventi vengono recapitati tramite la sessione appena disponibili. Il client non deve eseguire il polling per la disponibilità dei dati.

### <a name="consumer-groups"></a>Gruppi di consumer

Il meccanismo di pubblicazione/sottoscrizione di Hub eventi viene abilitato tramite *gruppi di consumer*. Un gruppo di consumer è una vista (stato, posizione o offset) di un intero hub eventi. I gruppi di consumer consentono a più applicazioni costose di avere una visualizzazione separata del flusso di eventi e di leggere il flusso in modo indipendente in base alle proprie esigenze e con i propri gli offset.

In un’architettura di elaborazione flusso, ogni applicazione a valle equivale a un gruppo di consumer. Se si desidera scrivere i dati dell’evento nell’archiviazione a lungo termine, tale applicazione writer di archiviazione è un gruppo di consumer. L'elaborazione di eventi complessi può essere quindi eseguita da un altro gruppo di consumer separato. È possibile accedere alla partizioni solo tramite un gruppo di consumer. In un hub eventi è sempre presente un gruppo di consumer predefinito e per un hub eventi di livello Standard è possibile creare fino a 20 gruppi di consumer.

In una partizione per un gruppo di consumer ci possono essere al massimo cinque lettori simultanei; è tuttavia **consigliabile che in una partizione per un gruppo di consumer ci sia solo un ricevitore attivo**. All'interno di una singola partizione, ogni lettore riceve tutti i messaggi. Se si hanno più lettori nella stessa partizione, si elaborano i messaggi duplicati. È necessario gestire questa situazione nel codice. Benché tale operazione possa non rivelarsi semplice, si tratta di un approccio valido in alcuni scenari.

Alcuni client offerti dagli Azure SDK sono agenti consumer intelligenti che gestiscono automaticamente i dettagli per verificare che ogni partizione disponga di un singolo lettore e che tutte le partizioni per un hub eventi vengano lette da. Ciò consente al codice di concentrarsi sull'elaborazione degli eventi letti dall'hub eventi, in modo che possa ignorare molti dei dettagli delle partizioni. Per altre informazioni, vedere [connettersi a una partizione](#connect-to-a-partition).

Gli esempi seguenti illustrano la convenzione dell'URI del gruppo di consumer:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

La figura seguente illustra l'architettura di elaborazione del flusso di Hub eventi:

![Architettura di hub eventi](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offset di flusso

Un *offset* è la posizione di un evento all'interno di una partizione. Un offset può essere considerato come un cursore sul lato client. L'offset è la numerazione di byte dell'evento. Questo offset consente a un consumer di eventi (lettore) di specificare un punto nel flusso di eventi da cui iniziare la lettura degli eventi. È possibile specificare l'offset come un timestamp o un valore di offset. I consumer sono responsabili di archiviare i propri valori di offset all'esterno del servizio Hub eventi. All'interno di una partizione, ogni evento include un offset.

![Offset partizione](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Checkpoint

*Checkpoint* è un processo mediante il quale i lettori contrassegnano o eseguono il commit della propria posizione all'interno di una sequenza di eventi di partizione. Il checkpoint è responsabilità del consumer e si verifica per partizione all'interno di un gruppo di consumer. Questa responsabilità significa che per ogni gruppo di consumer, ogni lettore di partizione deve tenere traccia della posizione corrente nel flusso di eventi e può informare il servizio quando considera completo il flusso di dati.

Se un lettore si disconnette da una partizione, quando riconnette inizia a leggere in corrispondenza del checkpoint inviato in precedenza dall’ulitimo lettore di tale partizione in tale gruppo di consumer. Quando il lettore si connette, passa l'offset all'hub eventi per specificare la posizione da cui iniziare la lettura. In questo modo è possibile usare la funzionalità di checkpoint sia per contrassegnare gli eventi come "completi" dalle applicazioni a valle sia per fornire la resilienza in caso di failover tra i lettori in esecuzione in computer diversi. È possibile tornare a dati precedenti specificando un offset inferiore da questo processo di checkpoint. Tramite questo meccanismo il checkpoint consente sia la resilienza del failover che la riproduzione del flusso di eventi.

> [!NOTE]
> Se si usa l'archivio BLOB di Azure come archivio di checkpoint in un ambiente che supporta una versione diversa di storage BLOB SDK rispetto a quelli generalmente disponibili in Azure, sarà necessario usare il codice per modificare la versione dell'API del servizio di archiviazione nella versione specifica supportata da tale ambiente. Ad esempio, se si esegue [Hub eventi in un hub Azure stack versione 2002](/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è la versione 2017-11-09. In questo caso, è necessario usare il codice per fare riferimento alla versione dell'API del servizio di archiviazione a 2017-11-09. Per un esempio su come definire come destinazione una versione specifica dell'API di archiviazione, vedere questi esempi su GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) o  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Attività comuni del consumer

Tutti i consumer di Hub eventi si connettono tramite una sessione AMQP 1.0 e un canale di comunicazione bidirezionale in grado di riconoscere lo stato. Ogni partizione ha una sessione AMQP 1.0 che facilita il trasporto di eventi separati dalla partizione.

#### <a name="connect-to-a-partition"></a>Connettersi a una partizione

Quando ci si connette alle partizioni, è pratica comune usare un meccanismo di leasing per coordinare le connessioni di lettura a partizioni specifiche. In questo modo, è possibile che ogni partizione in un gruppo di consumer disponga di un solo lettore attivo. Il checkpoint, il leasing e la gestione dei lettori sono semplificati tramite i client all'interno degli SDK di hub eventi, che fungono da agenti di consumer intelligenti. Si tratta di:

- [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) per .NET
- [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) per Java
- [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) per Python
- [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) per JavaScript/typescript

#### <a name="read-events"></a>Leggere gli eventi

Dopo l'apertura di una sessione AMQP 1.0 e del collegamento per una partizione specifica, gli eventi vengono recapitati al client AMQP 1.0 dal servizio Hub eventi. Questo meccanismo di recapito permette una velocità effettiva più elevata e una latenza più bassa rispetto ai meccanismi basati su pull, ad esempio HTTP GET. Quando gli eventi vengono inviati al client, ogni istanza dei dati dell'evento contiene metadati importanti, ad esempio l’offset e il numero di sequenza utilizzati per facilitare il checkpoint sulla in sequenza di eventi.

Dati evento:
* Offset
* Numero di sequenza
* Corpo
* Proprietà utente
* Proprietà di sistema

L'utente è responsabile della gestione dell'offset.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

- Introduzione all'Hub eventi
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
* [Disponibilità e coerenza nell'Hub eventi](event-hubs-availability-and-consistency.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Esempi di Hub eventi](event-hubs-samples.md)
