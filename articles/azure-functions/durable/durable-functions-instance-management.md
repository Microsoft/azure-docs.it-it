---
title: Gestire le istanze in Funzioni permanenti - Azure
description: Informazioni su come gestire le istanze nell'estensione Funzioni permanenti per le Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80476824"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gestire le istanze in Durable Functions in Azure

Se si usa l'estensione [Durable Functions](durable-functions-overview.md) per funzioni di Azure o si vuole iniziare a eseguire questa operazione, assicurarsi di sfruttare al meglio le proprie esigenze. Per ottimizzare le istanze di orchestrazione Durable Functions, è possibile acquisire ulteriori informazioni su come gestirli. Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

È possibile avviare e terminare le istanze, ad esempio, ed è possibile eseguire query sulle istanze, inclusa la possibilità di eseguire query su tutte le istanze e le istanze di query con i filtri. Inoltre, è possibile inviare eventi alle istanze, attendere il completamento dell'orchestrazione e recuperare gli URL del webhook di gestione HTTP. Questo articolo descrive anche altre operazioni di gestione, incluse le istanze di riavvolgimento, l'eliminazione della cronologia delle istanze e l'eliminazione di un hub attività.

In Durable Functions sono disponibili opzioni per la modalità di implementazione di ciascuna di queste operazioni di gestione. Questo articolo fornisce esempi che usano la [Azure Functions Core Tools](../functions-run-local.md) sia per .NET (C#) che per JavaScript.

## <a name="start-instances"></a>Istanze di avvio

È importante poter avviare un'istanza di orchestrazione. Questa operazione viene in genere eseguita quando si usa un'associazione Durable Functions nel trigger di un'altra funzione.

Il `StartNewAsync` Metodo (.NET) o `startNew` (JavaScript) nell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) avvia una nuova istanza. Internamente, questo metodo Accoda un messaggio nella coda dei controlli, che quindi attiva l'inizio di una funzione con il nome specificato che usa l' [associazione del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger).

Questa operazione asincrona viene completata quando il processo di orchestrazione viene pianificato correttamente.

I parametri per l'avvio di una nuova istanza di orchestrazione sono i seguenti:

* **Nome**: il nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) l'ID univoco dell'istanza. Se non si specifica questo parametro, il metodo usa un ID casuale.

> [!TIP]
> Usare un identificatore casuale per l'ID istanza. Gli ID istanza casuali consentono di garantire una distribuzione del carico uguale quando si ridimensionano le funzioni dell'agente di orchestrazione in più VM. Il momento appropriato per usare gli ID istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il modello dell'agente di [orchestrazione singleton](durable-functions-singletons.md) .

Il codice seguente è una funzione di esempio che avvia una nuova istanza di orchestrazione:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Se non specificato diversamente, gli esempi in questa pagina usano il trigger HTTP con le function.jsseguenti.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Questo esempio è destinato Durable Functions versione 2. x. Nella versione 1. x, usare `orchestrationClient` anziché `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile avviare un'istanza direttamente usando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . È necessario specificare i seguenti parametri:

* ** `function-name` (obbligatorio)**: nome della funzione da avviare.
* ** `input` (facoltativo)**: input della funzione, inline o tramite un file JSON. Per i file, aggiungere un prefisso al percorso del file con `@` , ad esempio `@path/to/file.json` .
* ** `id` (facoltativo)**: ID dell'istanza dell'orchestrazione. Se non si specifica questo parametro, il comando usa un GUID casuale.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è AzureWebJobsStorage.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è DurableFunctionsHub. È anche possibile impostare questa impostazione in [host.json](durable-functions-bindings.md#host-json) usando DurableTask: HubName.

> [!NOTE]
> I comandi degli strumenti di base presuppongono che vengano eseguiti dalla directory radice di un'app per le funzioni. Se si specificano in modo esplicito i `connection-string-setting` `task-hub-name` parametri e, è possibile eseguire i comandi da qualsiasi directory. Sebbene sia possibile eseguire questi comandi senza un host di app per le funzioni in esecuzione, è possibile che non si riesca a osservare alcuni effetti a meno che l'host non sia in esecuzione. Ad esempio, il `start-new` comando Accoda un messaggio di avvio nell'hub attività di destinazione, ma l'orchestrazione non viene eseguita a meno che non sia presente un processo host di app per le funzioni in esecuzione in grado di elaborare il messaggio.

Il comando seguente avvia la funzione denominata HelloWorld e la passa al contenuto del file `counter-data.json` :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Istanze di query

Come parte del lavoro richiesto per gestire le orchestrazioni, è molto probabile che sia necessario raccogliere informazioni sullo stato di un'istanza di orchestrazione (ad esempio, se è stata completata normalmente o non riuscita).

Il `GetStatusAsync` Metodo (.NET) o `getStatus` (JavaScript) nell'associazione del [client di orchestrazione](durable-functions-bindings.md#orchestration-client) esegue una query sullo stato di un'istanza di orchestrazione.

Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo), `showHistoryOutput` (facoltativo) e `showInput` (facoltativo) come parametri.

* **`showHistory`**: Se è impostato su `true` , la risposta contiene la cronologia di esecuzione.
* **`showHistoryOutput`**: Se è impostato su `true` , la cronologia di esecuzione contiene gli output delle attività.
* **`showInput`**: Se è impostato su `false` , la risposta non conterrà l'input della funzione. Il valore predefinito è `true`.

Il metodo restituisce un oggetto con le proprietà seguenti:

* **Name**: il nome della funzione dell'agente di orchestrazione.
* **InstanceId**: l'ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: l'ora in cui la funzione dell'agente di orchestrazione ha iniziato l'esecuzione.
* **LastUpdatedTime**: l'ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: l'input della funzione come valore JSON. Questo campo non viene popolato se `showInput` è false.
* **CustomStatus**: stato personalizzato dell'orchestrazione in formato JSON.
* **Output**: l'output della funzione come valore JSON (se la funzione è stata completata). Se la funzione dell'agente di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione dell'agente di orchestrazione è stata terminata, questa proprietà include il motivo della terminazione (se presente).
* **RuntimeStatus**: uno dei valori seguenti:
  * **In sospeso**: l'istanza è stata pianificata ma non è ancora iniziata l'esecuzione.
  * **Running**: l'istanza ha iniziato l'esecuzione.
  * **Completed**: l'istanza è stata completata normalmente.
  * **ContinuedAsNew**: l'istanza si è riavviata con una nuova cronologia. Questo stato è temporaneo.
  * **Failed**: l'esecuzione dell'istanza non è riuscita e ha generato un errore.
  * **Terminata**: l'istanza è stata terminata in modo anomalo.
* **History**: cronologia di esecuzione dell'orchestrazione. Questo campo viene popolato solo se `showHistory` è impostato su `true`.

Questo metodo restituisce `null` (.NET) o `undefined` (JavaScript) se l'istanza non esiste.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile ottenere lo stato di un'istanza di orchestrazione direttamente usando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . È necessario specificare i seguenti parametri:

* ** `id` (obbligatorio)**: ID dell'istanza dell'orchestrazione.
* ** `show-input` (facoltativo)**: se impostato su `true` , la risposta contiene l'input della funzione. Il valore predefinito è `false`.
* ** `show-output` (facoltativo)**: se impostato su `true` , la risposta contiene l'output della funzione. Il valore predefinito è `false`.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Il comando seguente recupera lo stato (incluso l'input e l'output) di un'istanza di con l'ID dell'istanza di orchestrazione 0ab8c55a66644d68a3a8b220b12d209c. Si presuppone che il comando venga eseguito `func` dalla directory radice dell'app per le funzioni:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

È possibile utilizzare il `durable get-history` comando per recuperare la cronologia di un'istanza di orchestrazione. È necessario specificare i seguenti parametri:

* ** `id` (obbligatorio)**: ID dell'istanza dell'orchestrazione.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in host.json, usando durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Eseguire query su tutte le istanze

Anziché eseguire una query su un'istanza dell'orchestrazione alla volta, potrebbe risultare più efficiente eseguire una query su tutti gli elementi contemporaneamente.

È possibile usare il metodo `GetStatusAsync` (.NET) o il metodo `getStatusAll` (JavaScript) per eseguire query sugli stati delle istanze di orchestrazione. In .NET è possibile passare un `CancellationToken` oggetto nel caso in cui si desideri annullarlo. Il metodo restituisce oggetti con le stesse proprietà del metodo `GetStatusAsync` con parametri.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile eseguire query direttamente sulle istanze usando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . È necessario specificare i seguenti parametri:

* ** `top` (facoltativo)**: questo comando supporta il paging. Questo parametro corrisponde al numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* ** `continuation-token` (facoltativo)**: token per indicare la pagina o la sezione di istanze da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Eseguire query su istanze con filtri

Cosa accade se non sono necessarie tutte le informazioni che possono essere fornite da una query di istanza standard? Ad esempio, cosa accade se si sta cercando solo l'ora di creazione dell'orchestrazione o lo stato di runtime dell'orchestrazione? È possibile limitare la query applicando filtri.

Utilizzare il `GetStatusAsync` Metodo (.NET) o `getStatusBy` (JavaScript) per ottenere un elenco di istanze di orchestrazione che corrispondono a un set di filtri predefiniti.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Nel Azure Functions Core Tools è anche possibile usare il `durable get-instances` comando con i filtri. Oltre ai parametri,, `top` e sopra indicati `continuation-token` `connection-string-setting` `task-hub-name` , è possibile usare tre parametri di filtro ( `created-after` , `created-before` e `runtime-status` ).

* ** `created-after` (facoltativo)**: recuperare le istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* ** `created-before` (facoltativo)**: recuperare le istanze create prima della data/ora (UTC). Datetime in formato ISO 8601 accettati.
* ** `runtime-status` (facoltativo)**: recuperare le istanze con un determinato stato (ad esempio, in esecuzione o completata). Può fornire più stati (separati da spazi).
* ** `top` (facoltativo)**: numero di istanze recuperate per ogni richiesta. Il valore predefinito è 10.
* ** `continuation-token` (facoltativo)**: token per indicare la pagina o la sezione di istanze da recuperare. Ogni esecuzione `get-instances` restituisce un token per il set successivo di istanze.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Se non si specifica alcun filtro ( `created-after` , `created-before` o `runtime-status` ), il comando recupera semplicemente le `top` istanze, senza considerare lo stato di runtime o l'ora di creazione.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Termina istanze

Se è presente un'istanza di orchestrazione che impiega troppo tempo per l'esecuzione oppure è sufficiente arrestarla prima del completamento per qualsiasi motivo, è possibile terminarla.

`TerminateAsync`Per terminare le istanze di, è possibile utilizzare il metodo (.NET) o `terminate` (JavaScript) dell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) . I due parametri sono un `instanceId` e una `reason` stringa, che vengono scritti nei log e nello stato dell'istanza.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

Un'istanza terminata passerà infine allo `Terminated` stato. Questa transizione non verrà tuttavia eseguita immediatamente. L'operazione di terminazione verrà invece accodata nell'hub attività insieme ad altre operazioni per tale istanza. È possibile utilizzare le API di [query dell'istanza](#query-instances) per sapere quando un'istanza terminata ha effettivamente raggiunto lo `Terminated` stato.

> [!NOTE]
> La chiusura dell'istanza non viene attualmente propagata. Le funzioni di attività e le sottoorchestrazioni vengono eseguite fino al completamento, indipendentemente dal fatto che sia stata terminata l'istanza di orchestrazione che li ha chiamati.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile terminare direttamente un'istanza di orchestrazione usando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . È necessario specificare i seguenti parametri:

* ** `id` (obbligatorio)**: ID dell'istanza di orchestrazione da terminare.
* ** `reason` (facoltativo)**: motivo della terminazione.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

Il comando seguente termina un'istanza di orchestrazione con ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Inviare eventi a istanze

In alcuni scenari è importante che le funzioni dell'agente di orchestrazione siano in grado di attendere e restare in ascolto di eventi esterni. Sono incluse [funzioni di monitoraggio](durable-functions-overview.md#monitoring) e funzioni in attesa di [interazione umana](durable-functions-overview.md#human).

Inviare notifiche degli eventi alle istanze in esecuzione utilizzando il `RaiseEventAsync` Metodo (.NET) o il `raiseEvent` Metodo (JavaScript) dell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client). Le istanze in grado di gestire questi eventi sono quelle in attesa di una chiamata a `WaitForExternalEvent` (.NET) o il cedimento a una `waitForExternalEvent` chiamata (JavaScript).

I parametri per `RaiseEventAsync` (.NET) e `raiseEvent` (JavaScript) sono i seguenti:

* **InstanceId**: l'ID univoco dell'istanza.
* **EventName**: il nome dell'evento da inviare.
* **EventData**: un payload serializzabile in JSON da inviare all'istanza.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

> [!NOTE]
> Se non esiste alcuna istanza di orchestrazione con l'ID istanza specificato, il messaggio di evento viene rimosso. Se un'istanza esiste ma non è ancora in attesa dell'evento, l'evento verrà archiviato nello stato dell'istanza fino a quando non sarà pronto per la ricezione e l'elaborazione.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È inoltre possibile generare direttamente un evento in un'istanza di orchestrazione utilizzando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . È necessario specificare i seguenti parametri:

* ** `id` (obbligatorio)**: ID dell'istanza dell'orchestrazione.
* **`event-name`**: Nome dell'evento da generare.
* ** `event-data` (facoltativo)**: dati da inviare all'istanza dell'orchestrazione. Questo può essere il percorso di un file JSON oppure è possibile fornire i dati direttamente nella riga di comando.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Il valore predefinito è `DurableFunctionsHub`. Può anche essere impostato in [host.json](durable-functions-bindings.md#host-json), usando DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

Nelle orchestrazioni con esecuzione prolungata può essere necessario attendere e ottenere i risultati di un'orchestrazione. In questi casi, è utile anche poter definire un periodo di timeout nell'orchestrazione. Se il timeout viene superato, viene restituito lo stato dell'orchestrazione anziché i risultati.

Il `WaitForCompletionOrCreateCheckStatusResponseAsync` Metodo (.NET) o `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) può essere utilizzato per ottenere l'output effettivo da un'istanza di orchestrazione in modo sincrono. Per impostazione predefinita, questi metodi utilizzano un valore predefinito di 10 secondi per `timeout` e 1 secondo per `retryInterval` .  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

Chiamare la funzione con la riga seguente. Usare 2 secondi per il timeout e 0,5 secondi per l'intervallo tra tentativi:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A seconda del tempo necessario per ottenere la risposta dall'istanza di orchestrazione, si verificano due casi:

* Le istanze di orchestrazione vengono completate entro il timeout definito (in questo caso 2 secondi) e la risposta è l'output effettivo dell'istanza di orchestrazione, recapitato in modo sincrono:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Le istanze di orchestrazione non possono essere completate entro il timeout definito e la risposta è quella predefinita descritta in [individuazione URL API HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Il formato degli URL del webhook potrebbe variare a seconda della versione dell'host di funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Funzioni di Azure 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recupera URL webhook di gestione HTTP

È possibile utilizzare un sistema esterno per monitorare o generare eventi in un'orchestrazione. I sistemi esterni possono comunicare con Durable Functions tramite gli URL del webhook che fanno parte della risposta predefinita descritta nell' [individuazione dell'URL dell'API HTTP](durable-functions-http-features.md#http-api-url-discovery). Gli URL del webhook sono in alternativa accessibili a livello di codice tramite l' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client). `CreateHttpManagementPayload`È possibile usare i metodi (.NET) o `createHttpManagementPayload` (JavaScript) per ottenere un oggetto serializzabile che contiene questi URL del webhook.

I `CreateHttpManagementPayload` metodi (.NET) e `createHttpManagementPayload` (JavaScript) hanno un parametro:

* **InstanceID**: ID univoco dell'istanza.

I metodi restituiscono un oggetto con le proprietà di stringa seguenti:

* **Id**: ID istanza dell'orchestrazione (deve essere lo stesso dell'input `InstanceId`).
* **StatusQueryGetUri**: URL di stato dell'istanza di orchestrazione.
* **SendEventPostUri**: URL di generazione di eventi dell'istanza di orchestrazione.
* **TerminatePostUri**: URL di terminazione dell'istanza di orchestrazione.
* **PurgeHistoryDeleteUri**: URL "Elimina cronologia" dell'istanza di orchestrazione.

Le funzioni possono inviare istanze di questi oggetti a sistemi esterni per monitorare o generare eventi sulle orchestrazioni corrispondenti, come illustrato negli esempi seguenti:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `DurableActivityContext` invece di `IDurableActivityContext` , è necessario utilizzare l' `OrchestrationClient` attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

## <a name="rewind-instances-preview"></a>Riavvolgi istanze (anteprima)

Se si verifica un errore dell'orchestrazione per un motivo imprevisto, è possibile *riavvolgere* l'istanza in uno stato integro in precedenza usando un'API compilata a tale scopo.

> [!NOTE]
> Questa API non deve sostituire una corretta gestione degli errori e i criteri di ripetizione dei tentativi, ma deve essere usata solo nei casi in cui le istanze di orchestrazione hanno esito negativo per motivi non previsti. Per ulteriori informazioni sulla gestione degli errori e sui criteri di ripetizione dei tentativi, vedere l'articolo relativo alla [gestione degli errori](durable-functions-error-handling.md) .

Utilizzare il `RewindAsync` Metodo (.NET) o `rewind` (JavaScript) dell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) per ripristinare lo stato di *esecuzione* dell'orchestrazione. Questo metodo ripeterà anche l'attività o gli errori di esecuzione dell'orchestrazione secondaria che hanno causato l'errore dell'orchestrazione.

Si immagini, ad esempio, di avere un flusso di lavoro che interessa una serie di [approvazioni umane](durable-functions-overview.md#human). Si supponga che esista una serie di funzioni di attività che notificano a un utente che è necessaria l'approvazione e attendono la risposta in tempo reale. Una volta ricevute le risposte o si è verificato un timeout per tutte le attività di approvazione, si supponga che un'altra attività non riesca a causa di una configurazione errata dell'applicazione, ad esempio una stringa di connessione al database non Ne consegue un errore di orchestrazione nel flusso di lavoro. Con l' `RewindAsync` API (.NET) o `rewind` (JavaScript), un amministratore dell'applicazione può correggere l'errore di configurazione e riavvolgere l'orchestrazione non riuscita allo stato immediatamente prima dell'errore. Nessuno dei passaggi di interazione umana deve essere nuovamente approvato e l'orchestrazione può essere completata correttamente.

> [!NOTE]
> La funzionalità *Rewind* non supporta la rimozione di istanze di orchestrazione che usano timer durevoli.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È anche possibile riavvolgere un'istanza di orchestrazione direttamente usando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . È necessario specificare i seguenti parametri:

* ** `id` (obbligatorio)**: ID dell'istanza dell'orchestrazione.
* ** `reason` (facoltativo)**: motivo per la rimozione dell'istanza di orchestrazione.
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Per impostazione predefinita, viene usato il nome dell'hub attività nella [host.jssu](durable-functions-bindings.md#host-json) file.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Elimina cronologia istanze

Per rimuovere tutti i dati associati a un'orchestrazione, è possibile eliminare la cronologia dell'istanza. È possibile, ad esempio, eliminare eventuali righe di tabelle di Azure e BLOB di messaggi di grandi dimensioni associati a un'istanza completata. A tale scopo, utilizzare il `PurgeInstanceHistoryAsync` Metodo (.NET) o `purgeInstanceHistory` (JavaScript) dell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client).

Questo metodo presenta due overload. Il primo overload Elimina la cronologia in base all'ID dell'istanza di orchestrazione:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Vedere [avviare le istanze](#javascript-function-json) per il function.jsnella configurazione.

---

Nell'esempio seguente viene illustrata una funzione attivata dal timer che elimina la cronologia per tutte le istanze di orchestrazione completate dopo l'intervallo di tempo specificato. In questo caso, rimuove i dati per tutte le istanze completate 30 o più giorni fa. Viene pianificata per essere eseguita una volta al giorno, alle 12.00:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il `purgeInstanceHistoryBy` metodo può essere utilizzato per ripulire in modo condizionale la cronologia delle istanze per più istanze.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Questo esempio è destinato Durable Functions versione 2. x. Nella versione 1. x, usare `orchestrationClient` anziché `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Affinché l'operazione di ripulitura della cronologia abbia esito positivo, lo stato di runtime dell'istanza di destinazione deve essere **completato**, **terminato**o **non riuscito**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

È possibile ripulire la cronologia di un'istanza di orchestrazione utilizzando il comando [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Analogamente al secondo esempio C# della sezione precedente, viene rieliminata la cronologia per tutte le istanze di orchestrazione create durante un intervallo di tempo specificato. È possibile filtrare ulteriormente le istanze eliminate in base allo stato di Runtime. Il comando ha diversi parametri:

* ** `created-after` (facoltativo)**: ripulire la cronologia delle istanze create dopo questa data/ora (UTC). Datetime in formato ISO 8601 accettati.
* ** `created-before` (facoltativo)**: ripulire la cronologia delle istanze create prima della data/ora (UTC). Datetime in formato ISO 8601 accettati.
* ** `runtime-status` (facoltativo)**: ripulire la cronologia delle istanze con un determinato stato (ad esempio, in esecuzione o completata). Può fornire più stati (separati da spazi).
* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Per impostazione predefinita, viene usato il nome dell'hub attività nella [host.jssu](durable-functions-bindings.md#host-json) file.

Il comando seguente elimina la cronologia di tutte le istanze non riuscite create prima del 14 novembre, 2018 alle 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Eliminare un hub attività

Con il comando [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` è possibile eliminare tutti gli elementi di archiviazione associati a un particolare Hub attività, tra cui tabelle, code e BLOB di archiviazione di Azure. Il comando presenta due parametri:

* ** `connection-string-setting` (facoltativo)**: nome dell'impostazione dell'applicazione che contiene la stringa di connessione di archiviazione da usare. Il valore predefinito è `AzureWebJobsStorage`.
* ** `task-hub-name` (facoltativo)**: nome del Durable Functions Hub attività da usare. Per impostazione predefinita, viene usato il nome dell'hub attività nella [host.jssu](durable-functions-bindings.md#host-json) file.

Il comando seguente elimina tutti i dati di archiviazione di Azure associati all' `UserTest` Hub attività.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API HTTP predefinite per la gestione delle istanze](durable-functions-http-api.md)
