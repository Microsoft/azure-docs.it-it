---
title: Filtri e pre-elaborazione in Azure Application Insights SDK | Microsoft Docs
description: Scrivere processori e inizializzatori di telemetria per l'SDK per filtrare i dati o aggiungere proprietà prima dell'invio della telemetria al portale di Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8b81849726ad546a24ce1bb56a139b384eb54c42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405369"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtri e pre-elaborazione della telemetria in Application Insights SDK

È possibile scrivere e configurare i plug-in per il Application Insights SDK per personalizzare il modo in cui i dati di telemetria possono essere arricchiti ed elaborati prima di essere inviati al servizio Application Insights.

* [campionamento](sampling.md) riduce il volume della telemetria senza effetti sulle statistiche. Tiene insieme i punti dati correlati per poter passare da uno all'altro quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
* L'applicazione di filtri con processori di telemetria consente di filtrare i dati di telemetria nell'SDK prima che vengano inviati al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. Il filtro è un approccio più semplice per ridurre il traffico rispetto al campionamento. Offre un controllo maggiore su ciò che viene trasmesso, ma è necessario tenere presente che influisce sulle statistiche, ad esempio se si filtrano tutte le richieste riuscite.
* Gli [inizializzatori di telemetria aggiungono o modificano le proprietà](#add-properties) ai dati di telemetria inviati dall'app, inclusi i dati di telemetria dei moduli standard. È possibile, ad esempio, aggiungere valori calcolati oppure i numeri di versione in base a cui filtrare i dati nel portale.
* [L'API SDK](../../azure-monitor/app/api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Prima di iniziare:

* Installare l'SDK appropriato per l'applicazione: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker per .NET/.NET Core](worker-service.md)o [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtri

Questa tecnica offre il controllo diretto sugli elementi inclusi o esclusi dal flusso di dati di telemetria. È possibile usare il filtro per eliminare gli elementi di telemetria dall'invio a Application Insights. È possibile usarla insieme al campionamento oppure separatamente.

Per filtrare i dati di telemetria, scrivere un processore di telemetria `TelemetryConfiguration`e registrarlo con il. Tutti i dati di telemetria passano attraverso il processore ed è possibile scegliere di eliminarli dal flusso o assegnarli al processore successivo nella catena. Sono inclusi i dati di telemetria dei moduli standard come l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze e i dati di telemetria rilevati. È possibile, ad esempio, filtrare la telemetria sulle richieste dei robot o le chiamate di dipendenza riuscite.

> [!WARNING]
> Se si filtra la telemetria inviata dall'SDK usando i processori, le statistiche visualizzate nel portale possono essere alterate e può risultare difficile seguire gli elementi correlati.
>
> In alternativa, valutare la possibilità di usare il [campionamento](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creare un processore di telemetria (C#)

1. Per creare un filtro, implementare `ITelemetryProcessor`.

    Si noti che i processori di telemetria creano una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, viene fornito un riferimento al processore successivo nella catena. Quando un punto dati di telemetria viene passato al metodo Process, esegue le operazioni e quindi chiama (o non chiama) il processore di telemetria successivo nella catena.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Aggiungere il processore.

**App ASP.NET** Inserire il frammento di codice in ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [!WARNING]
> Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente o una proprietà, l’SDK potrebbe automaticamente non riuscire a inviare nessuna telemetria.
>

**In alternativa** , è possibile inizializzare il filtro nel codice. In una classe di inizializzazione appropriata, ad esempio `Global.asax.cs` AppStart in, inserire il processore nella catena:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Gli elementi TelemetryClient creati dopo questo punto useranno i processori dell'utente.

**App del servizio ASP.NET Core/Worker**

> [!NOTE]
> L'aggiunta di `ApplicationInsights.config` processori tramite `TelemetryConfiguration.Active` o usando non è valida per le applicazioni ASP.NET Core o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte [con ASP.NET Core](asp-net-core.md#adding-telemetry-processors) o [WorkerService](worker-service.md#adding-telemetry-processors), l'aggiunta `TelemetryProcessor` di un nuovo viene `AddApplicationInsightsTelemetryProcessor` eseguita usando il `IServiceCollection`metodo di estensione su, come illustrato di seguito. Questo metodo viene chiamato nel `ConfigureServices` metodo della `Startup.cs` classe.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtri di esempio

#### <a name="synthetic-requests"></a>Richieste sintetiche

Filtrare i robot e i test Web. Anche se Esplora metriche offre la possibilità di filtrare le origini sintetiche, questa opzione riduce il traffico e le dimensioni di inserimento filtrando gli stessi nell'SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticazione non riuscita

Filtrare le richieste con una risposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrare le chiamate di dipendenza remote rapide

Per diagnosticare solo le chiamate lente, filtrare quelle rapide.

> [!NOTE]
> In questo modo le statistiche visualizzate nel portale verranno modificate.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticare i problemi di dipendenza

[Questo blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descrive un progetto per diagnosticare i problemi di dipendenza con l'invio automatico di ping regolari alle dipendenze.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Applicazioni Web JavaScript

**Applicazione di filtri tramite ITelemetryInitializer**

1. Creare una funzione di callback dell'inizializzatore di telemetria. La funzione di callback `ITelemetryItem` accetta come parametro, ovvero l'evento che viene elaborato. La `false` restituzione da questo callback comporta il filtraggio dell'elemento di telemetria.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Aggiungere il callback dell'inizializzatore di telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Aggiungi/modifica proprietà: ITelemetryInitializer


Usare gli inizializzatori di telemetria per arricchire la telemetria con altre informazioni e/o per eseguire l'override delle proprietà di telemetria impostate dai moduli di telemetria standard.

Il Application Insights per il pacchetto Web, ad esempio, raccoglie i dati di telemetria sulle richieste HTTP. per impostazione predefinita, contrassegna come non riuscita qualsiasi richiesta con un codice di risposta > = 400. Tuttavia, se si vuole considerare 400 come un risultato positivo, è possibile fornire un inizializzatore di telemetria che imposti la proprietà Success.

In tal modo, l'inizializzatore di telemetria verrà chiamato ogni volta che viene chiamato uno dei metodi Track*(). Sono inclusi `Track()` i metodi chiamati dai moduli di telemetria standard. Per convenzione, questi moduli non impostano le proprietà che sono già state impostate da un inizializzatore. Gli inizializzatori di telemetria vengono chiamati prima di chiamare i processori di telemetria. Quindi, eventuali arricchimenti eseguiti dagli inizializzatori sono visibili ai processori.

**Definire l'inizializzatore**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**App ASP.NET: caricare l'inizializzatore**

In ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*In alternativa,* è possibile creare un'istanza dell'inizializzatore nel codice, ad esempio nel file Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Vedere questo esempio nel dettaglio.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**App del servizio ASP.NET Core/Worker: caricare l'inizializzatore**

> [!NOTE]
> L'aggiunta di `ApplicationInsights.config` un inizializzatore usando o usando `TelemetryConfiguration.Active` non è valida per le applicazioni ASP.NET Core o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte con [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) o [WorkerService](worker-service.md#adding-telemetryinitializers), l'aggiunta `TelemetryInitializer` di un nuovo viene eseguita aggiungendola al contenitore di inserimento delle dipendenze, come illustrato di seguito. Questa operazione viene eseguita `Startup.ConfigureServices` nel metodo.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inizializzatori di telemetria JavaScript
*JavaScript*

Inserire un inizializzatore di telemetria immediatamente dopo il codice di inizializzazione ottenuto dal portale:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Per un riepilogo delle proprietà non personalizzate disponibili in telemetryItem, vedere [Modello di dati di esportazione di Application Insights](../../azure-monitor/app/export-data-model.md).

È possibile aggiungere tutti gli inizializzatori desiderati e vengono chiamati nell'ordine in cui vengono aggiunti.

### <a name="opencensus-python-telemetry-processors"></a>Processori di telemetria Python OpenCensus

I processori di telemetria in OpenCensus Python sono semplicemente funzioni di callback chiamate per elaborare i dati di telemetria prima di essere esportati. La funzione di callback deve accettare un tipo di dati [Envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) come parametro. Per filtrare i dati di telemetria dall'esportazione, verificare che la funzione `False`di callback venga restituita. È possibile vedere lo schema per i tipi di dati di monitoraggio di Azure nelle buste [qui](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> È possibile modificare la `cloud_RoleName` modificando l' `ai.cloud.role` attributo nel `tags` campo.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
È possibile aggiungere tutti i processori desiderati e vengono chiamati nell'ordine in cui vengono aggiunti. Se un processore deve generare un'eccezione, non ha alcun effetto sui processori seguenti.

### <a name="example-telemetryinitializers"></a>TelemetryInitializers di esempio

#### <a name="add-custom-property"></a>Aggiungi proprietà personalizzata

L'inizializzatore di esempio seguente aggiunge una proprietà personalizzata a tutti i dati di telemetria rilevati.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Aggiungere il nome del ruolo Cloud

Il seguente inizializzatore di esempio imposta il nome del ruolo Cloud su ogni telemetria rilevata.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Aggiungere informazioni da HttpContext

L'inizializzatore di esempio seguente legge i [`HttpContext`](https://docs.microsoft.com/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) dati da e li aggiunge a `RequestTelemetry` un'istanza di. Viene `IHttpContextAccessor` fornito automaticamente tramite l'inserimento di dipendenze del costruttore.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Sono presenti alcune sovrapposizioni in ciò che è possibile fare con questi elementi: entrambi possono essere usati per aggiungere o modificare le proprietà dei dati di telemetria, anche se è consigliabile usare gli inizializzatori a tale scopo.
* Gli inizializzatori di telemetria vengono sempre eseguiti prima dei processori di telemetria.
* TelemetryInitializers può essere chiamato più di una volta. Per convenzione, non imposta alcuna proprietà che sia già stata impostata.
* I processori di telemetria consentono di sostituire o rimuovere completamente un elemento di telemetria.
* Tutti i TelemetryInitializers registrati vengono chiamati per ogni elemento di telemetria. Per i processori di telemetria, SDK garantisce la chiamata del primo processore di telemetria. Se il resto dei processori viene chiamato o meno, viene deciso dai precedenti processori di telemetria.
* Usare TelemetryInitializers per arricchire la telemetria con proprietà aggiuntive o per eseguire l'override di quella esistente. Usare TelemetryProcessor per filtrare i dati di telemetria.

## <a name="troubleshooting-applicationinsightsconfig"></a>Risoluzione dei problemi relativi a ApplicationInsights.config

* Verificare che il nome di tipo completo e il nome di assembly siano corretti.
* Verificare che il file applicationinsights.config si trovi nella directory di output e includa le ultime modifiche apportate.

## <a name="reference-docs"></a>Documentazione di riferimento

* [Panoramica API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi
* [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md)
* [campionamento](../../azure-monitor/app/sampling.md)
* [Risoluzione dei problemi](../../azure-monitor/app/troubleshoot-faq.md)
