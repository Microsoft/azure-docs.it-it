---
title: ReliableConcurrentQueue in Azure Service Fabric
description: Coda reliableconcurrentqueue è una coda con velocità effettiva elevata che consente l'accodamento e la rimozione di code parallele.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784292"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introduzione a ReliableConcurrentQueue in Azure Service Fabric
La coda simultanea affidabile è una coda replicata, transazionale e asincrona che assicura concorrenza elevata per le operazioni di accodamento e rimozione dalla coda. È progettata per offrire velocità effettiva elevata e bassa latenza allentando il vincolo di ordinamento FIFO fornito dalla [coda affidabile](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) e fornisce invece un ordinamento in base al migliore sforzo.

## <a name="apis"></a>API

|Coda simultanea                |Coda simultanea affidabile                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queue"></a>Confronto con la [coda affidabile](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)

La coda simultanea affidabile è disponibile come alternativa alla [coda affidabile](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1). Deve essere usata nei casi in cui non è necessario l'ordine FIFO in modo vincolante poiché l'ordine FIFO richiede un compromesso in termini di concorrenza.  La [coda affidabile](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) usa i blocchi per applicare l'ordinamento FIFO, consentendo al massimo una transazione per l'accodamento e al massimo una transazione per la rimozione dalla coda. La coda simultanea affidabile allenta invece il vincolo di ordinamento e consente a un qualsiasi numero di transazioni simultanee di interfoliare le operazioni di accodamento e rimozione dalla coda. Viene fornito l'ordinamento in base al migliore sforzo; tuttavia non è mai possibile garantire l'ordinamento dei due valori in una coda simultanea affidabile.

La coda simultanea affidabile garantisce maggiore velocità effettiva e latenza ridotta rispetto alla [coda affidabile](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) ogni volta che sono presenti più transazioni simultanee che eseguono operazioni di accodamento e/o rimozione dalla coda.

Un esempio di caso d'uso per ReliableConcurrentQueue è lo scenario della [coda di messaggi](https://en.wikipedia.org/wiki/Message_queue). In questo scenario uno o più producer di messaggi creano e aggiungono elementi nella coda e uno o più consumer di messaggi prelevano i messaggi dalla coda e li elaborano. Più producer e consumer possono operare in modo indipendente usando le transazioni simultanee per elaborare la coda.

## <a name="usage-guidelines"></a>Linee guida per l'uso
* Il periodo di conservazione previsto per gli elementi nella coda è minimo, ovvero gli elementi non rimangono nella coda per molto tempo.
* La coda non garantisce l'ordine FIFO in modo vincolante.
* La coda non legge le proprie scritture. Se un elemento viene accodato all'interno di una transazione, non sarà visibile a un dequeuer all'interno della stessa transazione.
* Le rimozioni dalla coda non sono isolate tra loro. Se l'elemento *A* viene rimosso dalla coda nella transazione *txnA*, anche se non viene eseguito il commit di *txnA*, l'elemento *A* non sarà visibile a una transazione simultanea *txnB*.  Se la transazione *txnA* viene interrotta, *A* diventa immediatamente visibile alla transazione *txnB*.
* Il comportamento di *TryPeekAsync* può essere implementato usando un *TryDequeueAsync* e quindi interrompendo la transazione. Un esempio di questo comportamento è disponibile nella sezione modelli di programmazione.
* Il conteggio non è transazionale. Può dare un'idea del numero di elementi in una coda, ma rappresenta un valore temporizzato e non può essere ritenuto affidabile.
* L'elaborazione costosa sugli elementi rimossi dalla coda non deve essere eseguita mentre la transazione è attiva, per evitare transazioni con esecuzione prolungata che potrebbero avere un effetto sulle prestazioni del sistema.

## <a name="code-snippets"></a>Frammenti di codice
Ecco alcuni frammenti di codice e i relativi output previsti. La gestione delle eccezioni viene ignorata in questa sezione.

### <a name="instantiation"></a>Creazione di istanze
La creazione di un'istanza di una coda simultanea affidabile è simile a qualsiasi altra raccolta Reliable Collections.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Ecco alcuni frammenti di codice per l'uso di EnqueueAsync con i relativi output previsti.

- *Caso 1: Singola attività di accodamento*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Si supponga che l'attività sia stata completata e che non siano presenti transazioni simultanee che modificano la coda. L'utente può presupporre che la coda contenga gli elementi in uno dei seguenti ordini:

> 10, 20
> 
> 20, 10


- *Caso 2: Attività di accodamento in parallelo*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Si supponga che le attività siano state completate, che siano state eseguite in parallelo e che non sono presenti altre transazioni concorrenti che modificano la coda. Non è possibile eseguire alcuna inferenza sull'ordine degli elementi nella coda. Per questo frammento di codice, gli elementi possono comparire in uno qualsiasi dei 4 ordinamenti possibili.  La coda tenterà di mantenere gli elementi nell'ordine (in coda) originale, ma potrebbe essere necessario eseguire un riordino a causa di operazioni simultanee o errori.


### <a name="dequeueasync"></a>DequeueAsync
Ecco alcuni frammenti di codice per l'uso di TryDequeueAsync con i relativi output previsti. Si supponga che la coda sia già stata popolata con i seguenti elementi nella coda:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Singola attività di rimozione dalla coda*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Si supponga che l'attività sia stata completata e che non siano presenti transazioni simultanee che modificano la coda. Poiché non è possibile eseguire alcuna inferenza sull'ordine degli elementi nella coda, tutti e tre gli elementi possono essere rimossi dalla coda in qualsiasi ordine. La coda tenterà di mantenere gli elementi nell'ordine (in coda) originale, ma potrebbe essere necessario eseguire un riordino a causa di operazioni simultanee o errori.  

- *Caso 2: Attività di rimozione dalla coda in parallelo*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Si supponga che le attività siano state completate, che siano state eseguite in parallelo e che non sono presenti altre transazioni concorrenti che modificano la coda. Poiché non può essere eseguita alcuna inferenza sull'ordine degli elementi nella coda, gli elenchi *dequeue1* e *dequeue2* conterranno i due elementi in qualsiasi ordine.

Lo stesso elemento *non* sarà presente in entrambi gli elenchi. Di conseguenza, se dequeue1 contiene i valori *10*, *30*, dequeue2 conterrà i valori *20* e *40*.

- *Caso 3: ordine di rimozione dalla coda con interruzione della transazione*

L'interruzione di una transazione con rimozioni delle coda in transito riporta gli elementi all'inizio della coda. L'ordine in cui gli elementi vengono reinseriti all'inizio della coda non è garantito. Esaminare il codice seguente:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Si supponga che gli elementi siano stati rimossi dalla coda nell'ordine seguente:
> 10, 20

Quando si interrompe la transazione, gli elementi vengono inseriti di nuovo all'inizio della coda in uno dei seguenti ordini:
> 10, 20
> 
> 20, 10

Lo stesso vale per tutti i casi in cui la transazione non è stata *eseguita* correttamente.

## <a name="programming-patterns"></a>Modelli di programmazione
In questa sezione verranno esaminati alcuni modelli di programmazione che possono risultare utili usando ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Rimozioni dalla coda in batch
Un modello di programmazione consigliato per l'attività di tipo consumer è inserire in batch le operazioni di rimozione dalla coda, anziché eseguire una rimozione alla volta. L'utente può scegliere di limitare i ritardi tra ogni batch o la dimensione del batch. Il frammento di codice seguente illustra questo modello di programmazione. Tenere presente che, in questo esempio, l'elaborazione viene eseguita dopo il commit della transazione, pertanto se si verifica un errore durante l'elaborazione, gli elementi non elaborati andranno persi senza essere stati elaborati.  In alternativa, l'elaborazione può essere eseguita nell'ambito della transazione, ma potrebbe avere un impatto negativo sulle prestazioni e richiede la gestione degli elementi già elaborati.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Elaborazione basata su notifica con il migliore sforzo
Un altro modello di programmazione interessante usa l'API di conteggio. In questo caso è possibile implementare l'elaborazione basata su notifica con il migliore sforzo per la coda. Il conteggio della coda consente di limitare un'attività di accodamento o di rimozione dalla coda.  Si noti che, come nell'esempio precedente, poiché l'elaborazione si verifica all'esterno della transazione, gli elementi non elaborati potrebbero essere persi in caso di errore durante l'elaborazione.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Svuotamento in base al migliore sforzo
Lo svuotamento della coda non può essere garantito a causa della natura simultanea della struttura di dati.  È possibile che, anche se non sono in corso operazioni utente nella coda, una particolare chiamata a TryDequeueAsync non può restituire un elemento accodato in precedenza e di cui è stato eseguito il commit.  L'elemento accodato *alla fine* diventerà visibile per la rimozione dalla coda; tuttavia senza un meccanismo di comunicazione fuori banda, un consumer indipendente non può sapere se la coda ha raggiunto uno stato stabile, anche se sono stati arrestati tutti i producer e non sono consentite nuove operazione di accodamento. Di conseguenza l'operazione di svuotamento avviene in base al migliore sforzo, come implementato di seguito.

L'utente deve arrestare tutte le successive attività di producer e consumer e attendere il commit o l'interruzione delle transazioni in transito prima di tentare di svuotare la coda.  Se l'utente conosce il numero previsto di elementi della coda, può impostare una notifica che segnala che tutti gli elementi sono stati rimossi dalla coda.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Visualizzazione
ReliableConcurrentQueue non fornisce l'API *TryPeekAsync*. Gli utenti possono ottenere la visualizzazione semantica usando *TryDequeueAsync* e quindi interrompendo la transazione. In questo esempio le rimozioni dalla coda vengono elaborate solo se il valore dell'elemento è maggiore di *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Da leggere
* [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Lavorare con le raccolte Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notifiche di Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services backup e ripristino (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
* [Configurazione di Reliable state Manager](service-fabric-reliable-services-configuration.md)
* [Introduzione con Service Fabric servizi API Web](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Uso avanzato del modello di programmazione Reliable Services](./service-fabric-reliable-services-lifecycle.md)
* [Guida di riferimento per gli sviluppatori per Reliable Collections](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
