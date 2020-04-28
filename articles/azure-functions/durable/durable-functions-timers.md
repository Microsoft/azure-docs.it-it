---
title: Timer in Funzioni permanenti - Azure
description: Informazioni su come implementare timer permanenti nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261484"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Funzioni permanenti (Funzioni di Azure)

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer permanenti* da usare nelle funzioni di orchestrazione per implementare ritardi o per impostare timeout nelle azioni asincrone. I timer permanenti devono essere usati nelle funzioni dell'agente di orchestrazione al posto di `Thread.Sleep` e `Task.Delay` (C#) oppure di `setTimeout()` e `setInterval()` (JavaScript).

Si crea un timer durevole chiamando il `CreateTimer` metodo (.NET) o il `createTimer` metodo (JavaScript) dell'associazione del [trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). Il metodo restituisce un'attività che viene completata in una data e un'ora specificate.

## <a name="timer-limitations"></a>Limitazioni dei timer

Quando si crea un timer che scade alle 4:30 PM, il Framework di attività permanenti sottostante Accoda un messaggio che diventa visibile solo alle 4:30. Quando si esegue il piano a consumo di funzioni di Azure, il messaggio del timer appena visibile garantisce che l'app per le funzioni venga attivata in una macchina virtuale appropriata.

> [!NOTE]
> * I timer durevoli sono attualmente limitati a 7 giorni. Se sono necessari ritardi più lunghi, è possibile simularli usando le API del timer in `while` un ciclo.
> * Usare `CurrentUtcDateTime` `DateTime.UtcNow` sempre anziché in .NET o `currentUtcDateTime` al posto di `Date.now` o `Date.UTC` in JavaScript quando si calcola il tempo di attivazione per i timer durevoli. Per ulteriori informazioni, vedere l'articolo sui vincoli del codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Utilizzo per il ritardo

L'esempio seguente illustra come usare i timer permanenti per ritardare l'esecuzione. L'esempio sta inviando una notifica di fatturazione ogni giorno per 10 giorni.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> L'esempio C# precedente è destinato a Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext`. Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Evitare i cicli infiniti nelle funzioni di orchestrazione. Per informazioni su come implementare scenari di ciclo infinito in modo sicuro ed efficiente, vedere [Orchestrazioni perenni](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilizzo per i timeout

Questo esempio illustra come usare i timer permanenti per implementare timeout.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> L'esempio C# precedente è destinato a Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext`. Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

---

> [!WARNING]
> Usare `CancellationTokenSource` (.NET) o chiamare `cancel()` sull'oggetto restituito `TimerTask` (JavaScript) per annullare un timer durevole se il codice non ne attende il completamento. Il Framework di attività permanenti non modifica lo stato di un'orchestrazione su "completato" fino a quando tutte le attività in sospeso non vengono completate o annullate.

Questo meccanismo di annullamento non termina le esecuzioni della funzione di attività in corso o dell'orchestrazione secondaria. ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Se l'app per le funzioni usa il piano a consumo, verranno comunque addebitati tutti i tempi e la memoria utilizzati dalla funzione attività abbandonata. Per impostazione predefinita, le funzioni in esecuzione nel piano a consumo hanno un timeout di cinque minuti. Se questo limite viene superato, l'host di Funzioni di Azure viene riciclato per interrompere ogni esecuzione e impedire una fatturazione eccessiva. Il [timeout delle funzioni è configurabile](../functions-host-json.md#functiontimeout).

Per un esempio più dettagliato di come implementare i timeout nelle funzioni dell'agente di orchestrazione, vedere l'articolo relativo all' [interazione umana & timeouts-Verifica telefono](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come generare e gestire eventi esterni](durable-functions-external-events.md)
