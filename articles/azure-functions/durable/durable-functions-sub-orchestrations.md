---
title: Orchestrazioni secondarie in Funzioni permanenti - Azure
description: Come chiamare le orchestrazioni presenti nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76261518"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni secondarie in Funzioni permanenti (Funzioni di Azure)

Oltre a chiamare le funzioni di attività, le funzioni dell'agente di orchestrazione possono chiamare altre funzioni dell'agente di orchestrazione. Ad esempio, è possibile creare un'orchestrazione più ampia da una libreria di funzioni di orchestrazione più piccole. Oppure è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

Una funzione dell'agente di orchestrazione può chiamare un' `CallSubOrchestratorAsync` altra funzione `CallSubOrchestratorWithRetryAsync` dell'agente di orchestrazione usando `callSubOrchestrator` i `callSubOrchestratorWithRetry` metodi o in .NET oppure i metodi o in JavaScript. L'articolo [Error Handling & Compensation](durable-functions-error-handling.md#automatic-retry-on-failure) (Gestione e compensazione degli errori) contiene informazioni sulla ripetizione automatica.

Le funzioni secondarie dell'agente di orchestrazione si comportano come le funzioni di attività dal punto di vista del chiamante. Possono restituire un valore, generare un'eccezione e possono essere attese dalla funzione dell'agente di orchestrazione padre. 
## <a name="example"></a>Esempio

L'esempio seguente illustra uno scenario di IoT ("Internet delle cose") in cui sono presenti più dispositivi di cui è necessario eseguire il provisioning. La funzione seguente rappresenta il flusso di lavoro di provisioning che deve essere eseguito per ogni dispositivo:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Questa funzione dell'agente di orchestrazione può essere usata così com'è per il provisioning occasionale di dispositivi o può essere parte di un'orchestrazione di dimensioni maggiori. Nel secondo caso, la funzione dell'agente di orchestrazione padre può `DeviceProvisioningOrchestration` pianificare le `CallSubOrchestratorAsync` istanze di usando l' `callSubOrchestrator` API (.NET) o (JavaScript).

Di seguito è riportato un esempio che illustra come eseguire più funzioni dell'agente di orchestrazione in parallelo.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Gli esempi di C# precedenti sono per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext`. Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Le orchestrazioni secondarie devono essere definite nella stessa app per le funzioni dell'orchestrazione padre. Se è necessario chiamare e attendere le orchestrazioni in un'altra app per le funzioni, provare a usare il supporto incorporato per le API HTTP e il modello di consumer di polling HTTP 202. Per ulteriori informazioni, vedere l'argomento [funzionalità http](durable-functions-http-features.md) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come impostare lo stato di un'orchestrazione personalizzata](durable-functions-custom-orchestration-status.md)