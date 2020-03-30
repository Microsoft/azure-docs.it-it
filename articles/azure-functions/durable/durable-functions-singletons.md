---
title: Singleton per Funzioni permanenti - Azure
description: Informazioni su come usare i singleton nell'estensione Funzioni permanenti di Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262810"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Agenti di orchestrazione singleton in Funzioni permanenti (Funzioni di Azure)

Per i processi in background, è spesso necessario assicurarsi che venga eseguita una sola istanza di un determinato agente di orchestrazione alla volta. È possibile garantire questo tipo di comportamento singleton in [funzioni durevoli](durable-functions-overview.md) assegnando un ID istanza specifico a un agente di orchestrazione durante la creazione.

## <a name="singleton-example"></a>Esempio di singleton

Nell'esempio seguente viene illustrata una funzione trigger HTTP che crea un'orchestrazione di processi in background singleton. Il codice garantisce l'esistenza di una sola istanza per un ID istanza specificato.

# <a name="c"></a>[C #](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Il precedente codice in Cè è per Funzioni durevoli 2.x. Per Funzioni durevoli 1.x, è `OrchestrationClient` `DurableClient` necessario utilizzare l'attributo anziché l'attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di `IDurableOrchestrationClient`parametro anziché . Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

**function.json (funzione.json)**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

Per impostazione predefinita, gli ID delle istanze sono GUID generati in modo casuale. Nell'esempio precedente, tuttavia, l'ID istanza viene passato nei dati della route dall'URL. Il codice `GetStatusAsync`chiama (C) o `getStatus` (JavaScript) per verificare se un'istanza con l'ID specificato è già in esecuzione. Se tale istanza non è in esecuzione, viene creata una nuova istanza con tale ID.

> [!NOTE]
> In questo esempio c'è una possibile race condition. Se due istanze di **HttpStartSingle** vengono eseguite contemporaneamente, entrambe le chiamate di funzione segnaleranno l'esito positivo, ma verrà effettivamente avviata solo un'istanza dell'orchestrazione. A seconda dei requisiti, questo potrebbe avere effetti indesiderati. Per questo motivo è importante assicurarsi che due richieste non possano eseguire questa funzione trigger contemporaneamente.

I dettagli di implementazione della funzione dell'agente di orchestrazione in realtà non contano. Può trattarsi di una funzione di agente di orchestrazione regolare che viene avviata e completata oppure può essere eseguita in modo permanente (si tratta di un'[orchestrazione perenne](durable-functions-eternal-orchestrations.md)). L'aspetto importante è che in esecuzione un'unica istanza alla volta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle funzionalità HTTP native delle orchestrazioni](durable-functions-http-features.md)
