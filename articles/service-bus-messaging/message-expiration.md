---
title: Bus di servizio di Azure-scadenza messaggio
description: Questo articolo illustra la scadenza e l'ora di vita dei messaggi del bus di servizio di Azure. Dopo tale scadenza, il messaggio non viene più recapitato.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 47f8bdb4440adfeb5197f90cdad5358a442ce6a7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569914"
---
# <a name="message-expiration-time-to-live"></a>Scadenza dei messaggi (durata)

Il payload all'interno di un messaggio, oppure di un comando o di una richiesta che un messaggio trasmette a un ricevitore, è quasi sempre soggetto a un qualche tipo di scadenza a livello di applicazione. Dopo tale scadenza, il contenuto non viene più recapitato oppure l'operazione richiesta non viene più eseguita.

Per gli ambienti di sviluppo e test in cui code e argomenti vengono spesso usati nel contesto di esecuzioni parziali di applicazioni o parti di applicazioni, è anche consigliabile che i messaggi di test abbandonati vengano automaticamente sottoposti a Garbage Collection, in modo che l'esecuzione dei test successiva possa iniziare in modo pulito.

La scadenza dei singoli messaggi può essere controllata impostando la proprietà di sistema [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive), che specifica una durata relativa. La scadenza diventa un istante assoluto quando il messaggio viene accodato nell'entità. In quel momento, la proprietà [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) assume il valore [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc)  +  [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). L'impostazione TTL (time-to-Live) in un messaggio negoziato non viene applicata quando non ci sono client in ascolto attivo.

Passato l'istante definito da **ExpiresAtUtc**, i messaggi non sono più idonei per il recupero. La scadenza non influisce sui messaggi attualmente bloccati per il recapito. Tali messaggi vengono comunque gestiti normalmente. Se il blocco scade o il messaggio viene abbandonato, la scadenza ha effetto immediato.

Quando il messaggio è bloccato, l'applicazione potrebbe essere in possesso di un messaggio che è scaduto. Il fatto che l'applicazione proceda con l'elaborazione o scelga di abbandonare il messaggio dipende dall'implementatore.

## <a name="entity-level-expiration"></a>Scadenza a livello di entità

Tutti i messaggi inviati a una coda o un argomento sono soggetti a una scadenza predefinita che viene impostata a livello di entità con la proprietà [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) e che può essere impostata anche nel portale durante la creazione e modificata in un secondo momento. La scadenza predefinita viene usata per tutti i messaggi inviati all'entità in cui il valore di [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) non è impostato in modo esplicito. La scadenza predefinita funge anche da limite massimo per il valore **TimeToLive**. I messaggi con un valore della scadenza **TimeToLive** superiore rispetto al valore predefinito vengono modificati automaticamente impostando il valore **defaultMessageTimeToLive** prima di essere accodati.

> [!NOTE]
> Il valore predefinito di [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) per un messaggio negoziato è [TimeSpan. max](/dotnet/api/system.timespan.maxvalue) se non diversamente specificato.
>
> Per le entità di messaggistica (code e argomenti), l'ora di scadenza predefinita è anche [TimeSpan. max](/dotnet/api/system.timespan.maxvalue) per i livelli standard e Premium del bus di servizio. Per il livello **Basic** , l'ora di scadenza predefinita (anche massima) è di **14 giorni**.

Facoltativamente, i messaggi scaduti possono essere spostati in una [coda di messaggi non recapitabili](service-bus-dead-letter-queues.md) impostando la proprietà [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) oppure selezionando la casella corrispondente nel portale. Se l'opzione viene lasciata disabilitata, i messaggi scaduti vengono eliminati. I messaggi scaduti spostati nella coda di messaggi non recapitabili possono essere distinti dagli altri messaggi della coda di messaggi non recapitabili valutando la proprietà [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) che il broker archivia nella sezione delle proprietà utente. In questo caso, il valore è [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq).

Nel caso menzionato in precedenza in cui il messaggio è protetto da scadenza fino a quando è bloccato e se il flag è impostato sull'entità, il messaggio viene spostato nella coda di messaggi non recapitabili non appena il blocco viene abbandonato o scade. Non viene tuttavia spostato se il messaggio viene finalizzato correttamente, operazione che presuppone che l'applicazione lo abbia gestito correttamente nonostante la scadenza nominale.

La combinazione di [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e inserimento automatico (e transazionale) nella coda di messaggi non recapitabili alla scadenza rappresenta uno strumento prezioso per stabilire in modo affidabile se un processo assegnato a un gestore o a un gruppo di gestori con una determinata scadenza viene recuperato per l'elaborazione quando la scadenza viene raggiunta.

Si consideri, ad esempio, un sito Web che deve eseguire in modo affidabile i processi in un back-end con vincoli di scalabilità e che in alcuni casi è soggetto a picchi di traffico o richiede un isolamento rispetto a episodi di disponibilità di tale back-end. In una situazione normale, il gestore sul lato server per i dati utente inviati esegue il push delle informazioni in una coda e successivamente riceve una risposta che conferma la corretta gestione della transazione in una coda di risposta. Se c'è un picco di traffico e il gestore di back-end non è in grado di elaborare gli elementi del backlog in tempo, i processi scaduti vengono restituiti nella coda di messaggi non recapitabili. L'utente interattivo può ricevere una notifica che indica che l'operazione richiesta impiegherà un po' più tempo del solito e la richiesta può quindi essere inserita in un'altra coda per un percorso di elaborazione in cui il risultato dell'elaborazione finale viene inviato all'utente tramite posta elettronica. 


## <a name="temporary-entities"></a>Entità temporanee

È possibile creare sottoscrizioni, argomenti e code del bus di servizio come entità temporanee, che vengono rimosse automaticamente se non vengono usate per un periodo di tempo specificato.
 
La pulizia automatica è utile negli scenari di sviluppo e test in cui le entità vengono create in modo dinamico e non vengono eliminate dopo l'uso, a causa di un'interruzione dell'esecuzione dei test o del debug. È utile anche quando un'applicazione crea entità dinamiche, ad esempio una coda di risposta, per la ricezione di risposte in un processo del server Web o in un altro oggetto di durata relativamente breve, in cui è difficile eseguire la pulizia delle entità in modo affidabile quando l'istanza dell'oggetto non è più presente.

La funzionalità viene abilitata tramite la proprietà [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues). Questa proprietà deve essere impostata sulla durata per cui l'entità deve rimanere inattiva (non usata) prima di essere eliminata automaticamente. Il valore minimo per questa proprietà è 5.
 
La proprietà **autoDeleteOnIdle** deve essere impostata tramite un'operazione di Azure Resource Manager o tramite le API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del client .NET Framework. Non è possibile impostarla nel portale.

## <a name="idleness"></a>Inattività

Ecco cosa identifica le entità (code, argomenti e sottoscrizioni) come inattive:

- Code
    - Nessun invio  
    - Nessuna ricezione  
    - Nessun aggiornamento alla coda  
    - Nessun messaggio pianificato  
    - Nessuna esplorazione/visualizzazione 
- Argomenti  
    - Nessun invio  
    - Nessun aggiornamento all'argomento  
    - Nessun messaggio pianificato 
- Sottoscrizioni
    - Nessuna ricezione  
    - Nessun aggiornamento alla sottoscrizione  
    - Nessuna nuova regola aggiunta alla sottoscrizione  
    - Nessuna esplorazione/visualizzazione  
 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)