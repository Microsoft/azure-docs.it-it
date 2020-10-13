---
title: Diagnostica in Funzioni permanenti - Azure
description: Informazioni su come eseguire la diagnostica dei problemi con l'estensione Funzioni permanenti per Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: f91cdaa81e18105eb39af442ab6152bfd2888ba9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319708"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostica in Durable Functions in Azure

Per la diagnostica dei problemi con [Funzioni permanenti](durable-functions-overview.md) sono disponibili diverse opzioni. Alcune opzioni sono le stesse disponibili per le funzioni normali e alcune sono specifiche di Funzioni permanenti.

## <a name="application-insights"></a>Application Insights

Per eseguire la diagnostica e il monitoraggio in Funzioni di Azure, è consigliabile usare [Application Insights](../../azure-monitor/app/app-insights-overview.md). Lo stesso consiglio vale per Funzioni permanenti. Per una panoramica su come usare Application Insights nell'app per le funzioni, vedere [Monitorare Funzioni di Azure](../functions-monitoring.md).

L'estensione Funzioni permanenti di Funzioni di Azure genera anche *eventi di rilevamento* che consentono di tenere traccia dell'esecuzione end-to-end di un'orchestrazione. Questi eventi di rilevamento possono essere trovati e sottoposti a query usando lo strumento [Application Insights Analytics](../../azure-monitor/log-query/log-query-overview.md) nel portale di Azure.

### <a name="tracking-data"></a>Dati di rilevamento

Ogni evento del ciclo di vita di un'istanza di orchestrazione genera la scrittura di un evento di rilevamento nella raccolta **traces** in Application Insights. Questo evento contiene un payload **customDimensions** con diversi campi.  Ai nomi dei campi viene anteposta la stringa `prop__`.

* **hubName**: il nome dell'hub attività in cui vengono eseguite le orchestrazioni.
* **appName**: il nome dell'app per le funzioni. Questo campo è utile quando più app per le funzioni condividono la stessa istanza di Application Insights.
* **slotName**: [slot di distribuzione](../functions-deployment-slots.md) in cui è in esecuzione l'app per le funzioni corrente. Questo campo è utile quando si utilizzano gli slot di distribuzione per la versione delle orchestrazioni.
* **functionName**: il nome della funzione dell'agente di orchestrazione o di attività.
* **functionType**: il tipo di funzione, ad esempio **agente di orchestrazione** o **attività**.
* **instanceId**: l'ID univoco dell'istanza di orchestrazione.
* **state**: lo stato di esecuzione del ciclo di vita dell'istanza. I valori validi includono:
  * **Scheduled**: la funzione è stata pianificata per l'esecuzione, ma non è stata ancora avviata.
  * **Started**: la funzione è stata avviata, ma non è ancora in stato di attesa o non è stata completata.
  * **Awaited**: l'agente di orchestrazione ha pianificato un lavoro ed è in attesa del completamento.
  * **Listening**: l'agente di orchestrazione è in ascolto di una notifica degli eventi esterni.
  * **Completed**: la funzione è stata completata.
  * **Failed**: la funzione ha avuto esito negativo generando un errore.
* **reason**: dati aggiuntivi associati all'evento di rilevamento. Ad esempio, se un'istanza è in attesa di una notifica degli eventi esterni, questo campo indica il nome dell'evento di cui è in attesa. Se una funzione ha avuto esito negativo, questo campo conterrà i dettagli dell'errore.
* **isReplay**: valore booleano che indica se per l'evento di rilevamento è prevista la riesecuzione.
* **extensionVersion**: la versione dell'estensione Durable Task. Le informazioni sulla versione sono dati particolarmente importanti quando si segnalano possibili bug nell'estensione. Le istanze con esecuzione prolungata possono segnalare più versioni, se si verifica un aggiornamento durante l'esecuzione.
* **sequenceNumber**: numero di sequenza di esecuzione per un evento. In combinazione con il timestamp consente di ordinare gli eventi per ora di esecuzione. *Si noti che questo numero verrà reimpostato su zero se l'host viene riavviato durante l'esecuzione dell'istanza. È quindi importante eseguire sempre l'ordinamento prima per timestamp e quindi per sequenceNumber.*

Il livello di dettaglio dei dati di rilevamento emessi per Application Insights può essere configurato nella `logger` sezione (Functions 1. x) o `logging` (functions 2,0) del `host.json` file.

#### <a name="functions-10"></a>Funzioni 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Funzioni 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Per impostazione predefinita vengono generati tutti gli eventi di rilevamento senza riesecuzione. Il volume dei dati può essere ridotto impostando `Host.Triggers.DurableTask` su `"Warning"` o `"Error"`; in questo caso gli eventi di rilevamento verranno generati solo per le situazioni eccezionali.

Per abilitare la creazione di eventi di riproduzione con orchestrazione verbose, `LogReplayEvents` può essere impostato su `true` nel file `host.json` sotto `durableTask` come illustrato:

#### <a name="functions-10"></a>Funzioni 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funzioni 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Per impostazione predefinita, la telemetria di Application Insights viene campionata dal runtime di Funzioni di Azure per evitare di generare dati con eccessiva frequenza. In questo modo è possibile che le informazioni di rilevamento vengano perse quando si verificano molti eventi del ciclo di vita in un breve periodo di tempo. L'[articolo sul monitoraggio in Funzioni di Azure](../functions-monitoring.md#configure-sampling) illustra come configurare questo comportamento.

### <a name="single-instance-query"></a>Query per singola istanza

La query seguente mostra i dati di rilevamento cronologici per una singola istanza di orchestrazione della funzione [Hello Sequence](durable-functions-sequence.md). Viene scritto usando il [linguaggio di query kusto](/azure/data-explorer/kusto/query/). Esclude la riesecuzione in modo da mostrare solo il percorso di esecuzione *logico*. Gli eventi possono essere ordinati per `timestamp` e `sequenceNumber`, come illustrato nella query seguente:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Il risultato è un elenco di eventi di traccia che mostrano il percorso di esecuzione dell'orchestrazione, incluse eventuali funzioni di attività ordinate per ora di esecuzione in ordine ascendente.

![Query ordinata Application Insights istanza singola](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Query di riepilogo dell'istanza

La query seguente mostra lo stato di tutte le istanze di orchestrazione eseguite in un intervallo di tempo specificato.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Il risultato è un elenco di ID istanza e dello stato di runtime corrente.

![Application Insights query a istanza singola](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Registrazione durevole del framework delle attività

I registri estensioni durevoli sono utili per comprendere il comportamento della logica dell'orchestrazione. Tuttavia, questi log non contengono sempre informazioni sufficienti per eseguire il debug di problemi di affidabilità e prestazioni a livello di Framework. A partire da **v 2.3.0** dell'estensione durevole, per la raccolta sono disponibili anche i log generati dal framework di attività permanente sottostante (DTFx).

Quando si esaminano i log generati da DTFx, è importante comprendere che il motore DTFx è costituito da due componenti: il motore di invio principale ( `DurableTask.Core` ) e uno dei numerosi provider di archiviazione supportati (Durable Functions USA `DurableTask.AzureStorage` per impostazione predefinita).

* **DurableTask. Core**: contiene informazioni sull'esecuzione dell'orchestrazione e la pianificazione di basso livello.
* **DurableTask. AzureStorage**: contiene informazioni correlate alle interazioni con gli elementi di archiviazione di Azure, tra cui le code interne, i BLOB e le tabelle di archiviazione usate per archiviare e recuperare lo stato interno dell'orchestrazione.

È possibile abilitare questi log aggiornando la `logging/logLevel` sezione del **host.js** del file dell'app per le funzioni. Nell'esempio seguente viene illustrato come abilitare i log degli errori e degli avvisi da `DurableTask.Core` e `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Se Application Insights è abilitato, questi log verranno aggiunti automaticamente alla `trace` raccolta. È possibile cercarli nello stesso modo in cui si cercano altri `trace` log usando le query kusto.

> [!NOTE]
> Per le applicazioni di produzione, è consigliabile abilitare `DurableTask.Core` e `DurableTask.AzureStorage` registrare usando il `"Warning"` filtro. I filtri di dettaglio più elevati, ad esempio, `"Information"` sono molto utili per il debug dei problemi di prestazioni. Tuttavia, questi eventi di log sono un volume elevato e possono aumentare significativamente Application Insights i costi di archiviazione.

La query kusto seguente mostra come eseguire una query per i log di DTFx. La parte più importante della query è rappresentata `where customerDimensions.Category startswith "DurableTask"` dal filtraggio dei risultati nei log delle `DurableTask.Core` `DurableTask.AzureStorage` categorie e.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
Il risultato è un set di log scritti dai provider di log durevoli del framework delle attività.

![Application Insights risultati della query DTFx](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Per ulteriori informazioni sugli eventi di log disponibili, vedere la [documentazione relativa alla registrazione strutturata di Task Framework durevole su GitHub](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>Registrazione dell'app

È importante tenere presente il comportamento di riesecuzione dell'agente di orchestrazione quando si scrivono i log direttamente da una funzione dell'agente di orchestrazione. Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

I dati di log risultanti hanno un aspetto simile all'output di esempio seguente:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Tenere presente che mentre i log contengono la dichiarazione di chiamata F1, F2 e F3, queste funzioni vengono chiamate *effettivamente* solo al primo rilevamento. Le chiamate successive eseguite durante la riesecuzione vengono ignorate e gli output vengono rieseguiti nella logica dell'agente di orchestrazione.

Se si desidera scrivere solo i log sulle esecuzioni non di riproduzione, è possibile scrivere un'espressione condizionale in log solo se il flag "is Replaying" è `false` . Si consideri l'esempio precedente, ma questa volta con i controlli di riesecuzione.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

A partire da Durable Functions 2,0, le funzioni dell'agente di orchestrazione .NET hanno anche la possibilità di creare un `ILogger` che filtra automaticamente le istruzioni di log durante la riproduzione. Questo filtro automatico viene eseguito tramite l'API [IDurableOrchestrationContext. CreateReplaySafeLogger (ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) .

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> Gli esempi di C# precedenti sono per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Con le modifiche indicate in precedenza, l'output del log è il seguente:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Stato personalizzato

Lo stato dell'orchestrazione personalizzato consente di impostare un valore di stato per la funzione dell'agente di orchestrazione. Questo stato personalizzato è quindi visibile ai client esterni tramite l' [API di query di stato http](durable-functions-http-api.md#get-instance-status) o tramite chiamate API specifiche della lingua. Lo stato di un'orchestrazione personalizzato consente di eseguire il monitoraggio in modo più completo per le funzioni dell'agente di orchestrazione. Ad esempio, il codice della funzione dell'agente di orchestrazione può richiamare l'API "impostare lo stato personalizzato" per aggiornare lo stato di avanzamento di un'operazione a esecuzione prolungata. Un client, ad esempio una pagina Web o un sistema esterno, può eseguire una query periodicamente sulle API di query dello stato HTTP per ottenere informazioni più dettagliate. Di seguito è riportato il codice di esempio per l'impostazione di un valore di stato personalizzato in una funzione di orchestrazione:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> L'esempio C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Mentre l'orchestrazione è in esecuzione, i client esterni possono recuperare questo stato personalizzato:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

I client visualizzano la risposta seguente:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Il payload dello stato personalizzato è limitato a 16 kB di testo JSON UTF-16, perché deve rientrare in una colonna dell'archivio tabelle di Azure. È possibile usare l'archiviazione esterna se è necessario un payload di dimensioni maggiori.

## <a name="debugging"></a>Debug

Funzioni di Azure supporta direttamente il debug del codice della funzione e lo stesso supporto si estende a Funzioni permanenti, in esecuzione in Azure o in locale. Quando si esegue il debug, è tuttavia opportuno conoscere alcuni comportamenti:

* **Riproduzione**: le funzioni dell'agente di orchestrazione vengono [riprodotte](durable-functions-orchestrations.md#reliability) regolarmente quando vengono ricevuti nuovi input. Questo comportamento significa che una singola esecuzione *logica* di una funzione dell'agente di orchestrazione può comportare il raggiungimento dello stesso punto di interruzione più volte, soprattutto se viene impostata all'inizio del codice della funzione.
* **Await**: ogni volta `await` che viene rilevato un oggetto in una funzione dell'agente di orchestrazione, restituisce il controllo al dispatcher di Durable Task Framework. Se è la prima volta che si verifica un particolare oggetto `await` , l'attività associata *non viene mai* ripresa. Poiché l'attività non riprende mai, l'esecuzione di un'istruzione */* routine di await (F10 in Visual Studio) non è possibile. Questa operazione funziona solo se un'attività è in corso di riesecuzione.
* **Timeout della messaggistica**: Durable Functions usa internamente i messaggi in coda per l'esecuzione di funzioni di orchestrazione, attività e entità. In un ambiente con più macchine virtuali, a causa dell'interruzione del debug per lunghi periodi di tempo è possibile che un'altra macchina virtuale prelevi il messaggio, generando un'esecuzione duplicata. Questo comportamento esiste anche per le normali funzioni attivate da coda, ma è importante sottolinearlo in questo contesto poiché le code sono un dettaglio dell'implementazione.
* **Arresto e avvio**: i messaggi nelle funzioni permanenti vengono mantenuti tra le sessioni di debug. Se si interrompe il debug e si termina il processo host locale mentre è in esecuzione una funzione durevole, la funzione potrebbe essere rieseguita automaticamente in una sessione di debug futura. Questo comportamento può generare confusione quando non è previsto. La cancellazione di tutti i messaggi dalle [code di archiviazione interne](durable-functions-perf-and-scale.md#internal-queue-triggers) tra le sessioni di debug è una tecnica per evitare questo comportamento.

> [!TIP]
> Quando si impostano punti di interruzione nelle funzioni dell'agente di orchestrazione, se si desidera interrompere l'esecuzione solo in caso di esecuzione non di riproduzione, è possibile impostare un punto di interruzione condizionale che si interrompe solo se il valore "sta riproducendo" `false` .

## <a name="storage"></a>Archiviazione

Per impostazione predefinita, Funzioni permanenti archivia lo stato in Archiviazione di Azure. Questo comportamento significa che è possibile controllare lo stato delle orchestrazioni utilizzando strumenti come [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Schermata Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Risulta utile per il debug perché viene visualizzato esattamente lo stato in cui potrebbe trovarsi un'orchestrazione. È possibile esaminare i messaggi nelle code anche per individuare le attività in sospeso o, in alcuni casi, bloccate.

> [!WARNING]
> Sebbene sia utile esaminare la cronologia di esecuzioni in archiviazione tabelle, evitare di creare qualsiasi dipendenza in questa tabella, che può cambiare con l'evoluzione dell'estensione Funzioni permanenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sul monitoraggio in funzioni di Azure](../functions-monitoring.md)
