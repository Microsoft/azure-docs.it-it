---
title: Rientranza in Azure Service Fabric Actors
description: Introduzione alla rientranza per Service Fabric Reliable Actors, un modo per evitare il blocco logico in base al contesto di chiamata.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645532"
---
# <a name="reliable-actors-reentrancy"></a>Rientranza di Reliable Actors
Per impostazione predefinita, il runtime di Reliable Actors consente la reentrancy basata sul contesto di chiamata logico. Ciò consente agli attori di essere rientranti se si trovano nella stessa catena del contesto di chiamata. Ad esempio, l'attore A invia un messaggio all'attore B che invia un messaggio all'attore C. Durante l'elaborazione del messaggio, se l'attore C chiama l'attore A, il messaggio è rientrante e sarà quindi consentito. Tutti gli altri messaggi che fanno parte di un contesto di chiamata diverso verranno bloccati sull'attore A fino al completamento dell'elaborazione.

Per la reentrancy degli attori sono disponibili due opzioni, definite nell'enumerazione `ActorReentrancyMode` :

* `LogicalCallContext` (comportamento predefinito)
* `Disallowed` : disabilita la reentrancy

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
La reentrancy può essere configurata nelle impostazioni di `ActorService`durante la registrazione. L'impostazione si applica a tutte le istanze degli attori create nel servizio Actor.

L'esempio seguente illustra un servizio Actor che imposta la modalità di reentrancy su `ActorReentrancyMode.Disallowed`. In questo caso, se un attore invia un messaggio rientrante a un altro attore verrà generata un'eccezione di tipo `FabricException` .

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Passaggi successivi
* Acquisire altre informazioni sulla reentrancy nella [documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
