---
title: Eliminare gli attori di Azure Service FabricDelete Azure Service Fabric actors
description: Informazioni su come eliminare manualmente e completamente Reliable Actors e il relativo stato in un'applicazione di Azure Service Fabric.Learn how to manually and fully delete Reliable Actors and their state in an Azure Service Fabric application.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645617"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminare Reliable Actors e il relativo stato
L'operazione di Garbage Collection degli attori disattivati pulisce solo l'oggetto attore, ma non rimuove i dati archiviati in State Manager dell'attore. Quando un attore viene riattivato, i suoi dati vengono nuovamente resi disponibili tramite il gestore di stato. Nei casi in cui gli attori archiviano dati nel gestore di stato e vengono disattivati ma mai riattivati, può essere necessario pulire i relativi dati.

Il [servizio Actor](service-fabric-reliable-actors-platform.md) fornisce anche una funzione per l'eliminazione di attori da un chiamante remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

L'eliminazione di un attore produce gli effetti seguenti, a seconda del fatto che l'attore sia attualmente attivo o meno:

* **Attore attivo**
  * L'attore viene rimosso dall'elenco di attori attivi e viene disattivato.
  * Il suo stato viene eliminato definitivamente.
* **Attore inattivo**
  * Il suo stato viene eliminato definitivamente.

Un attore non può chiamare un'operazione di eliminazione su se stesso da uno dei suoi metodi attore perché l'attore non può essere eliminato mentre è in esecuzione in un contesto di chiamata attore, in cui il runtime ha ottenuto un blocco per le chiamate dell'attore per applicare l'accesso a thread singolo.

Per altre informazioni su Reliable Actors, vedere:
* [Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)
* [Eventi attori](service-fabric-reliable-actors-events.md)
* [Rientranza attore](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio di C](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio JavaJava Sample code](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
