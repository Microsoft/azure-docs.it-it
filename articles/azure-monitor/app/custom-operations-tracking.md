---
title: Tenere traccia delle operazioni personalizzate con applicazione Azure .NET SDK di Insights
description: Verifica delle operazioni personalizzate con Azure Application Insights .NET SDK
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bd30f60928df3644b215f185d620393d1edda8c7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320375"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Verifica delle operazioni personalizzate con Application Insights .NET SDK

Gli SDK di Application Insights di Azure verificano automaticamente le richieste HTTP in ingresso e le chiamate ai servizi dipendenti, ovvero richieste HTTP, query SQL e così via. La verifica e la correlazione di richieste e dipendenze offre visibilità sui tempi di risposta e sull'affidabilità dell'intera applicazione in tutti i microservizi che combinano questa applicazione. 

Esiste una classe di modelli di applicazione che non può essere supportata in modo generico. Per il corretto monitoraggio di tali modelli, è necessaria la strumentazione manuale del codice. Questo articolo descrive alcuni criteri che potrebbero richiedere una strumentazione manuale, ad esempio per l’elaborazione di code personalizzate e l'esecuzione prolungata di attività in background.

Questo documento offre indicazioni su come tenere traccia delle operazioni personalizzate con Application Insights SDK. Questa documentazione è relativa a:

- Application Insights per .NET (noto anche come Base SDK) versione 2.4+.
- Application Insights per applicazioni Web (che esegue ASP.NET) versione 2.4+.
- Application Insights per ASP.NET Core versione 2.1+.

## <a name="overview"></a>Panoramica
Un'operazione è un lavoro logico eseguito da un'applicazione. Sono indicati nome, ora di avvio, durata, risultato e contesto dell'esecuzione, ad esempio nome utente, proprietà e risultato. Se l'operazione A è stata avviata dall'operazione B, l'operazione B è impostata come elemento padre per A. Un'operazione può avere un solo padre, ma può avere molte operazioni figlio. Per ulteriori informazioni sulle operazioni e la correlazione dei dati di telemetria, vedere [Correlazione dei dati di telemetria di Azure Application Insights](correlation.md).

Nell’SDK di Application Insights .NET l'operazione viene descritta dalla classe astratta [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) e dai discendenti [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Verifica delle operazioni in ingresso 
Application Insights Web SDK raccoglie automaticamente le richieste HTTP per le applicazioni ASP.NET eseguite nella pipeline di IIS e per tutte le applicazioni ASP.NET Core. Sono disponibili soluzioni supportate dalla community per altre piattaforme e altri framework. Se tuttavia l'applicazione non è supportata dalle soluzioni standard o supportate dalla community, è possibile instrumentarla manualmente.

Un altro esempio che richiede la verifica personalizzata è offerto dal ruolo di lavoro che riceve gli elementi dalla coda. Per alcune code, la chiamata per aggiungere un messaggio a questa coda viene registrata come dipendenza. L'operazione di alto livello che descrive l'elaborazione del messaggio tuttavia non viene raccolta automaticamente.

Di seguito è illustrato come si può tenere traccia di queste operazioni.

A un livello elevato, l'attività consiste nel creare `RequestTelemetry` e impostare le proprietà note. Al termine dell'operazione, tenere traccia dei dati di telemetria. L'esempio seguente illustra questa attività.

### <a name="http-request-in-owin-self-hosted-app"></a>Richiesta HTTP nell'app con self-hosting Owin
In questo esempio il contesto della traccia viene propagato in base al [protocollo HTTP per la correlazione](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Si deve prevedere di ricevere le intestazioni descritte qui.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Il protocollo HTTP per la correlazione dichiara inoltre l’intestazione `Correlation-Context`. Tuttavia, è omesso qui per motivi di semplicità.

## <a name="queue-instrumentation"></a>Strumentazione della coda
Sebbene esistano il [contesto di traccia W3C](https://www.w3.org/TR/trace-context/) e il [protocollo HTTP per la correlazione](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) per passare i dettagli della correlazione con la richiesta HTTP, ogni protocollo di coda deve definire la modalità di passaggio degli stessi dettagli lungo il messaggio della coda. Alcuni protocolli di accodamento, ad esempio AMQP, consentono il passaggio di metadati aggiuntivi, mentre altri, ad esempio quello della coda di archiviazione di Azure, richiedono la codifica del contesto nel payload dei messaggi.

> [!NOTE]
> * **La traccia tra componenti non è ancora supportata per le code** Con HTTP, se il producer e il consumer inviano dati di telemetria a diverse risorse Application Insights, l'esperienza di diagnostica delle transazioni e la mappa delle applicazioni mostrano le transazioni e la mappa end-to-end In caso di code non è ancora supportata. 

### <a name="service-bus-queue"></a>Coda del bus di servizio
Application Insights tiene traccia delle chiamate di messaggistica del bus di servizio con il nuovo [client del bus di servizio di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versione 3.0.0 e successive.
Se per elaborare i messaggi si usa il [criterio del gestore di messaggi](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), non ci sono altre operazioni da eseguire. Tutte le chiamate al bus di servizio eseguite dal servizio vengono automaticamente verificate e correlate con altri elementi di telemetria. Se i messaggi vengono elaborati manualmente, vedere [Service Bus client tracing with Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) (Traccia del client del bus di servizio con Microsoft Application Insights).

Se si usa il pacchetto [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/), continuare a leggere. Gli esempi seguenti illustrano come verificare e correlare le chiamate al bus di servizio dal momento che la coda del bus di servizio usa il protocollo AMQP mentre Application Insights non tiene traccia automaticamente delle operazioni nella coda.
Gli identificatori di correlazione vengono passati nelle proprietà del messaggio.

#### <a name="enqueue"></a>Accodare

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Coda di archiviazione di Azure
L'esempio seguente illustra come tenere traccia delle operazioni della [coda di archiviazione di Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md) e correlare i dati di telemetria tra producer, consumer e Archiviazione di Azure. 

La coda di archiviazione ha un'API HTTP. Tutte le chiamate alla coda vengono tracciate dall'agente di raccolta di dipendenze Application Insights per le richieste HTTP.
Viene configurato per impostazione predefinita nelle applicazioni ASP.NET e ASP.NET Core, con altri tipi di applicazione, è possibile fare riferimento alla [documentazione delle applicazioni console](./console.md)

Inoltre è possibile correlare l'ID operazione di Application Insights con l'ID di richiesta di Archiviazione. Per informazioni su come impostare e ottenere un client di richiesta di Archivazione e un ID di richiesta del server, vedere [Monitoraggio, diagnosi e risoluzione dei problemi dell'archiviazione di Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Accodare
Poiché le code di archiviazione di Azure supportano l'API HTTP, tutte le operazioni con la coda vengono automaticamente registrate da Application Insights. In molti casi, questa strumentazione dovrebbe essere sufficiente. Per correlare le tracce sul lato consumer con le tracce del producer, è necessario passare parte del contesto di correlazione in modo simile a quanto avviene nel protocollo HTTP per la correlazione. 

Questo esempio illustra come tenere traccia dell'operazione `Enqueue`. È possibile:

 - **Correlare gli eventuali tentativi**, che hanno tutti un'operazione padre comune, ovvero `Enqueue`. In caso contrario, vengono registrati come elementi figlio della richiesta in ingresso. Se sono presenti più richieste logiche per la coda, potrebbe risultare difficile trovare la chiamata che ha restituito i tentativi.
 - **Correlare i log di archiviazione (se e quando necessario)** con i dati di telemetria di Application Insights.

L'operazione `Enqueue` è l’elemento figlio di un'operazione padre (ad esempio, una richiesta HTTP in ingresso). La chiamata di dipendenza HTTP è l'elemento figlio dell'operazione `Enqueue` e nipote della richiesta in ingresso:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Per ridurre la quantità di dati di telemetria segnalati dall'applicazione o per non tenere traccia dell'operazione `Enqueue` per altri motivi, è possibile usare direttamente l'API `Activity`:

- Creare (e avviare) un nuovo `Activity` anziché avviare l'operazione Application Insights. *Non* è necessario assegnare proprietà ad essa, tranne il nome dell'operazione.
- Serializzare `yourActivity.Id` nel payload dei messaggi invece di `operation.Telemetry.Id`. È anche possibile usare `Activity.Current.Id`.


#### <a name="dequeue"></a>Rimuovere dalla coda
In modo simile a `Enqueue`, la richiesta HTTP effettiva per la coda di archiviazione viene automaticamente registrata da Application Insights. L'operazione `Enqueue` tuttavia viene probabilmente eseguita nel contesto padre, ad esempio il contesto della richiesta in ingresso. Gli SDK di Application Insights correlano automaticamente tale operazione (e la parte HTTP) con la richiesta padre e gli altri dati di telemetria segnalati nello stesso ambito.

L'operazione `Dequeue` è un'operazione complessa. L’SDK Application Insights tiene automaticamente traccia delle richieste HTTP. Tuttavia, non conosce il contesto di correlazione fino a quando non viene analizzato il messaggio. Non è possibile correlare la richiesta HTTP per ottenere il messaggio con gli altri dati di telemetria soprattutto quando viene ricevuto più di un messaggio.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Process

Nell'esempio seguente un messaggio in arrivo viene verificato in maniera simile a quanto avviene per una richiesta HTTP in ingresso:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Analogamente, è possibile instrumentare le altre operazioni della coda. L'operazione di visualizzazione deve essere instrumentata in modo simile a quella di rimozione dalla coda. Non è necessario instrumentare operazioni di gestione della coda. Application Insights tiene traccia di operazioni come HTTP e nella maggior parte dei casi è sufficiente.

Quando si instrumenta l'eliminazione di un messaggio, assicurarsi di impostare gli identificatori delle operazioni (correlazione). In alternativa, è possibile usare l'API `Activity`. Non è quindi necessario impostare gli identificatori delle operazioni negli elementi di telemetria perché Application Insights SDK esegue questa operazione automaticamente:

- Creare un nuovo oggetto `Activity` dopo avere ottenuto un elemento dalla coda.
- Usare `Activity.SetParentId(message.ParentId)` per correlare i log del consumer e del producer.
- Avviare il `Activity`.
- Tenere traccia delle operazioni di rimozione dalla coda, elaborazione ed eliminazione usando gli helper `Start/StopOperation`. dallo stesso flusso di controllo asincrono (contesto di esecuzione). In questo modo la correlazione sarà corretta.
- Arrestare il `Activity`.
- Usare `Start/StopOperation` o chiamare `Track` telemetry manualmente.

### <a name="dependency-types"></a>Tipi di dipendenza

Application Insights usa il tipo di dipendenza per personalizzare le esperienze dell'interfaccia utente. Per le code, riconosce i tipi seguenti di `DependencyTelemetry` che migliorano l' [esperienza di diagnostica delle transazioni](./transaction-diagnostics.md):
- `Azure queue`per le code di archiviazione di Azure
- `Azure Event Hubs`per hub eventi di Azure
- `Azure Service Bus`per il bus di servizio di Azure

### <a name="batch-processing"></a>Elaborazione batch
Per alcune code, è possibile una rimozione dalla coda di più messaggi con una singola richiesta. L'elaborazione di tali messaggi è presumibilmente indipendente e appartiene a diverse operazioni logiche. Non è possibile correlare l' `Dequeue` operazione a un determinato messaggio in fase di elaborazione.

Ogni elaborazione dei messaggi deve essere eseguita nel proprio flusso di controllo asincrono. Per ulteriori informazioni, vedere la sezione [Verifica delle dipendenze in uscita](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Attività in background a esecuzione prolungata

Alcune applicazioni avviano operazioni a esecuzione prolungata che possono essere causate dalle richieste degli utenti. Dal punto di vista della verifica/strumentazione, non c'è differenza dalla strumentazione delle richieste o delle dipendenze: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In questo esempio `telemetryClient.StartOperation` crea `DependencyTelemetry` e specifica il contesto di correlazione. Si supponga di avere un'operazione padre creata dalle richieste in ingresso che hanno pianificato l'operazione. `BackgroundTask`, purché venga avviato nello stesso flusso di controllo asincrono di una richiesta in ingresso, viene correlato con tale operazione padre. `BackgroundTask` e tutti gli elementi di telemetria annidati vengono automaticamente correlati alla richiesta che l'ha generato anche dopo la fine della richiesta.

Quando l'attività viene avviata dal thread in background a cui non sono associate operazioni (`Activity`), `BackgroundTask` non ha elementi padre. Tuttavia, può avere operazioni annidate. Tutti gli elementi di telemetria segnalati dall'attività sono correlati a `DependencyTelemetry` creato in `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Verifica delle dipendenze in uscita
È possibile tenere traccia della propria tipologia di dipendenza o di operazioni non supportate da Application Insights.

Il metodo `Enqueue` nella coda del bus di servizio o nella coda di archiviazione è un esempio di tale verifica personalizzata.

L'approccio generale per la verifica personalizzata delle dipendenze è:

- Chiamare il metodo `TelemetryClient.StartOperation` (estensione) che riempie le proprietà `DependencyTelemetry` necessarie per la correlazione e altre proprietà (timestamp di avvio, durata).
- Impostare le altre proprietà personalizzate in `DependencyTelemetry`, ad esempio nome e altri contesti necessari.
- Effettuare una chiamata di dipendenza e attendere.
- Al termine, arrestare l'operazione con `StopOperation`.
- Gestire le eccezioni.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

L'eliminazione provoca l'interruzione dell'operazione, quindi è possibile procedere in questo modo anziché chiamare `StopOperation`.

*Avviso*: in alcuni casi un'eccezione non gestita può [impedire che venga chiamato ](/dotnet/csharp/language-reference/keywords/try-finally) `finally` rendendo impossibile la verifica delle operazioni.

### <a name="parallel-operations-processing-and-tracking"></a>Elaborazione e verifica di operazioni parallele

`StopOperation` arresta solo l'operazione che è stata avviata. Se l'operazione corrente in esecuzione non corrisponde all'operazione che si desidera arrestare, `StopOperation` non esegue alcuna operazione. Questa situazione può verificarsi se si avviano più operazioni in parallelo nello stesso contesto di esecuzione:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Chiamare sempre `StartOperation` ed elaborare l'operazione nello stesso metodo **async** per isolare le operazioni eseguite in parallelo. Se l'operazione è sincrona, o non asincrona, eseguire il wrapping del processo e verificare con `Task.Run`:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>Operazioni ApplicationInsights rispetto a System. Diagnostics. Activity
`System.Diagnostics.Activity`rappresenta il contesto della traccia distribuita e viene usato da Framework e librerie per creare e propagare il contesto all'interno e all'esterno del processo e correlare gli elementi di telemetria. L'attività interagisce con `System.Diagnostics.DiagnosticSource` : il meccanismo di notifica tra il Framework o la libreria per notificare gli eventi interessanti (richieste in ingresso o in uscita, eccezioni e così via).

Le attività sono i cittadini di prima classe in Application Insights e la dipendenza automatica e la raccolta di richieste si basa in modo significativo su di essi insieme agli `DiagnosticSource` eventi. Se si crea un'attività nell'applicazione, non si verificherà la creazione di dati di telemetria Application Insights. Application Insights deve ricevere eventi DiagnosticSource e conosce i nomi e i payload degli eventi per tradurre l'attività nei dati di telemetria.

Ogni operazione di Application Insights (richiesta o dipendenza) implica `Activity` -quando `StartOperation` viene chiamato, crea un'attività sottostante. `StartOperation`è il metodo consigliato per tenere traccia delle telemetrie delle richieste o delle dipendenze manualmente e garantire che tutti gli elementi siano correlati.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni di base sulla [correlazione di dati di telemetria](correlation.md) in Application Insights.
- Verificare il modo in cui i dati correlati hanno l' [esperienza di diagnostica delle transazioni](./transaction-diagnostics.md) e la [mappa delle applicazioni](./app-map.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](./data-model.md).
- Segnalare [metriche ed eventi](./api-custom-events-metrics.md) personalizzati ad Application Insights.
- Estrarre la [configurazione](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) standard di una raccolta di proprietà di contesto.
- Vedere [System.Diagnostics.Activity User Guide](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) (Guida dell'utente di System.Diagnostics.Activity) per informazioni su come correlare i dati di telemetria.

