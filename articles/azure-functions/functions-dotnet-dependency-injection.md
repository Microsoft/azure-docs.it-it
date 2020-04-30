---
title: Usare l'inserimento delle dipendenze in Funzioni di Azure .NET
description: Informazioni su come usare l'inserimento di dipendenze per la registrazione e l'uso di servizi nelle funzioni .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678447"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usare l'inserimento delle dipendenze in Funzioni di Azure .NET

Funzioni di Azure supporta il modello DI progettazione software per l'inserimento delle dipendenze, una tecnica per ottenere l' [inversione del controllo (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra le classi e le relative dipendenze.

- L'inserimento di dipendenze in funzioni di Azure si basa sulle funzionalità di inserimento delle dipendenze di .NET Core. È consigliabile acquisire familiarità con l' [inserimento di dipendenze di .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Esistono differenze nel modo in cui vengono ignorate le dipendenze e il modo in cui vengono letti i valori di configurazione con funzioni di Azure nel piano a consumo.

- Il supporto per l'inserimento delle dipendenze inizia con funzioni di Azure 2. x.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'inserimento di dipendenze, è necessario installare i pacchetti NuGet seguenti:

- [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. NET. Sdk. Functions Package](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) Version 1.0.28 o versioni successive

## <a name="register-services"></a>Registrare i servizi

Per registrare servizi, creare un metodo per configurare e aggiungere componenti a un' `IFunctionsHostBuilder` istanza di.  L'host di funzioni di Azure crea un' `IFunctionsHostBuilder` istanza di e la passa direttamente al metodo.

Per registrare il metodo, aggiungere l' `FunctionsStartup` attributo dell'assembly che specifica il nome del tipo usato durante l'avvio.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Precisazioni

Una serie di passaggi di registrazione viene eseguita prima e dopo l'elaborazione della classe Startup da parte del runtime. Tenere pertanto presenti gli elementi seguenti:

- *La classe startup è destinata solo alla configurazione e alla registrazione.* Evitare di usare i servizi registrati all'avvio durante il processo di avvio. Ad esempio, non provare a registrare un messaggio in un logger registrato durante l'avvio. Questo punto del processo di registrazione è troppo presto perché i servizi siano disponibili per l'uso. Dopo l' `Configure` esecuzione del metodo, il runtime di funzioni continua a registrare dipendenze aggiuntive, che possono influire sul funzionamento dei servizi.

- *Il contenitore di inserimento delle dipendenze include solo tipi registrati in modo esplicito*. Gli unici servizi disponibili come tipi iniettabili sono quelli che vengono impostati nel `Configure` metodo. Di conseguenza, i tipi specifici di funzioni come `BindingContext` e `ExecutionContext` non sono disponibili durante l'installazione o come tipi iniettabili.

## <a name="use-injected-dependencies"></a>Usa dipendenze inserite

L'inserimento del costruttore viene usato per rendere disponibili le dipendenze in una funzione. L'uso dell'inserimento del costruttore richiede che non si usino classi statiche.

Nell'esempio seguente viene illustrato come `IMyService` inserire `HttpClient` le dipendenze e in una funzione attivata tramite http. Questo esempio usa il pacchetto [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessario per registrare un `HttpClient` all'avvio.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Durate dei servizi

Le app di funzioni di Azure forniscono le stesse durate dei servizi dell' [inserimento delle dipendenze ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Per un'app per le funzioni, le diverse durate del servizio si comportano nel modo seguente:

- **Temporaneo**: i servizi temporanei vengono creati a ogni richiesta del servizio.
- Con **ambito**: la durata del servizio con ambito corrisponde a una durata di esecuzione della funzione. I servizi con ambito vengono creati una volta per ogni esecuzione. Le richieste successive per quel servizio durante l'esecuzione riutilizzeranno l'istanza del servizio esistente.
- **Singleton**: la durata del servizio singleton corrisponde alla durata dell'host e viene riutilizzata tra le esecuzioni di funzioni su tale istanza. I servizi di durata singleton sono consigliati per le connessioni e i `SqlConnection` client `HttpClient` , ad esempio le istanze di o.

Visualizzare o scaricare un [esempio di diverse durate dei servizi](https://aka.ms/functions/di-sample) su GitHub.

## <a name="logging-services"></a>Servizi di registrazione

Se è necessario un provider di registrazione personalizzato, registrare un tipo personalizzato come `ILoggerProvider` istanza di. Application Insights viene aggiunto automaticamente da funzioni di Azure.

> [!WARNING]
> - Non aggiungere `AddApplicationInsightsTelemetry()` alla raccolta di servizi durante la registrazione dei servizi in conflitto con i servizi forniti dall'ambiente.
> - Non registrare il proprio `TelemetryConfiguration` o `TelemetryClient` se si usa la funzionalità di Application Insights incorporata. Se è necessario configurare un'istanza personalizzata `TelemetryClient` , crearne una tramite il inserito `TelemetryConfiguration` come illustrato in [monitorare funzioni di Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

L'host inserisce `ILogger<T>` i servizi `ILoggerFactory` e nei costruttori.  Per impostazione predefinita, tuttavia, questi nuovi filtri di registrazione verranno filtrati all'esterno dei log di funzione.  Sarà necessario modificare il `host.json` file per acconsentire esplicitamente ad altri filtri e categorie.  Nell'esempio seguente viene illustrata `ILogger<HttpTrigger>` l'aggiunta di un oggetto con log che verranno esposti dall'host.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

E un `host.json` file che aggiunge il filtro del log.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Servizi forniti dall'app per le funzioni

L'host funzione registra molti servizi. I servizi seguenti possono essere considerati sicuri come una dipendenza nell'applicazione:

|Tipo di servizio|Durata|Descrizione|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configurazione Runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsabile per fornire l'ID dell'istanza host|

Se sono presenti altri servizi su cui si vuole creare una dipendenza, [creare un problema e proporrli in GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Override dei servizi host

La sostituzione dei servizi forniti dall'host non è attualmente supportata.  Se sono presenti servizi di cui si vuole eseguire l'override, [creare un problema e proporrli in GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Utilizzo di opzioni e impostazioni

I valori definiti nelle [impostazioni dell'app](./functions-how-to-use-azure-function-app-settings.md#settings) sono disponibili `IConfiguration` in un'istanza di, che consente di leggere i valori delle impostazioni dell'app nella classe Startup.

È possibile estrarre valori dall' `IConfiguration` istanza di in un tipo personalizzato. La copia dei valori delle impostazioni dell'app in un tipo personalizzato consente di testare facilmente i servizi rendendo questi valori inseribili. Le impostazioni lette nell'istanza di configurazione devono essere semplici coppie chiave/valore.

Si consideri la classe seguente che include una proprietà denominata coerente con un'impostazione dell'app:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E un `local.settings.json` file che potrebbe strutturare l'impostazione personalizzata come indicato di seguito:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Dall'interno del `Startup.Configure` metodo, è possibile estrarre i valori dall' `IConfiguration` istanza di nel tipo personalizzato usando il codice seguente:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

La `Bind` chiamata di copia i valori che hanno nomi di proprietà corrispondenti dalla configurazione all'istanza personalizzata. L'istanza options è ora disponibile nel contenitore IoC per inserire in una funzione.

L'oggetto Options viene inserito nella funzione come un'istanza dell'interfaccia generica `IOptions` . Utilizzare la `Value` proprietà per accedere ai valori presenti nella configurazione.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Per ulteriori informazioni sull'utilizzo delle opzioni, vedere il [modello di opzioni in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) .

> [!WARNING]
> Evitare di provare a leggere i valori da file come *local. Settings. JSON* o *appSettings. { Environment}. JSON* nel piano a consumo. I valori letti da questi file correlati alle connessioni trigger non sono disponibili perché l'app viene ridimensionata perché l'infrastruttura host non ha accesso alle informazioni di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

- [Come monitorare l'app per le funzioni](functions-monitoring.md)
- [Procedure consigliate per le funzioni](functions-best-practices.md)
