---
title: 'Informazioni di riferimento su ApplicationInsights.config: Azure | Documentazione Microsoft'
description: Abilitare o disabilitare i moduli di raccolta dati e aggiungere i contatori delle prestazioni e altri parametri.
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: c6d51210867e83c6acc74ca890ab65b195dc64fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176672"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurazione di Application Insights SDK con ApplicationInsights.config o .xml
Application Insights .NET SDK è costituito da alcuni pacchetti NuGet. Il [pacchetto di base](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornisce l'API per l'invio di dati di telemetria ad Application Insights. [Altri pacchetti](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) forniscono *moduli* e *inizializzatori* di telemetria per il rilevamento automatico dei dati di telemetria dall'applicazione e dal rispettivo contesto. Modificando il file di configurazione, è possibile abilitare o disabilitare i moduli e gli inizializzatori di telemetria e impostare i parametri per alcuni di essi.

Il file di configurazione è denominato `ApplicationInsights.config` o `ApplicationInsights.xml`, a seconda del tipo di applicazione. Viene aggiunto automaticamente al progetto quando si [installano alcune versioni dell'SDK][start]. Per impostazione predefinita, quando si usa l'esperienza automatizzata dei progetti modello di Visual Studio che supportano l' **aggiunta di > Application Insights Telemetry**, il file di ApplicationInsights.config viene creato nella cartella radice del progetto e quando compilato viene copiato nella cartella bin. Viene anche aggiunto a un'app Web da [Status Monitor su un server IIS][redfield]. Il file di configurazione viene ignorato se si usa l' [estensione per il sito Web di Azure](azure-web-apps.md) o l' [estensione per VM di Azure e set di scalabilità di macchine virtuali](azure-vm-vmss-apps.md) .

Non esiste un file equivalente per controllare l'[SDK in una pagina Web][client].

Questo documento illustra le sezioni visibili nel file di configurazione, il modo in cui esse controllano i componenti SDK, e quali pacchetti NuGet caricano questi componenti.

> [!NOTE]
> I file di istruzioni applicationInsights.config e .xml non si applicano a .NET Core SDK. Per la configurazione delle applicazioni .NET Core, seguire [questa](./asp-net-core.md) guida.

## <a name="telemetry-modules-aspnet"></a>Moduli di telemetria (ASP.NET)
Ogni modulo di telemetria raccoglie un tipo specifico di dati e usa l'API di base per inviare i dati. I moduli sono installati da diversi pacchetti NuGet che aggiungono anche le linee necessarie al file .config.

Nel file di configurazione è presente un nodo per ogni modulo. Per disabilitare un modulo, eliminare il nodo o impostarlo come commento.

### <a name="dependency-tracking"></a>Rilevamento delle dipendenze
[Rilevamento delle dipendenze](./asp-net-dependencies.md) raccoglie la telemetria delle chiamate effettuate dall’applicazione ai database e ai database e servizi esterni. Per far funzionare questo modulo in un server IIS, è necessario [installare Status Monitor][redfield].

È anche possibile scrivere codice personalizzato per il rilevamento delle dipendenze mediante l' [API TrackDependency](./api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

Le dipendenze possono essere raccolte automaticamente senza modificare il codice utilizzando la connessione basata su agenti (senza codice). Per usarlo in app Web di Azure, abilitare l' [estensione Application Insights](azure-web-apps.md). Per usarlo nella VM di Azure o nel set di scalabilità di macchine virtuali di Azure, abilitare l' [estensione di monitoraggio dell'applicazione per la macchina virtuale e il set di scalabilità](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Agente di raccolta dati delle prestazioni
[Raccoglie i contatori delle prestazioni di sistema](./performance-counters.md) , ad esempio CPU, memoria e carico di rete dalle installazioni di IIS. E’ possibile specificare quali contatori raccogliere, inclusi i contatori delle prestazioni installati.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria delle diagnostiche Application Insights
Il modulo `DiagnosticsTelemetryModule` segnala errori nel codice di strumentazione stesso di Application Insights, ad esempio, se il codice non è in grado di accedere ai contatori delle prestazioni o se un `ITelemetryInitializer` genera un'eccezione. La telemetria di traccia rilevata da questo modulo viene visualizzata nella [Ricerca diagnostica][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Modalità sviluppatore
`DeveloperModeWithDebuggerAttachedTelemetryModule` impone a `TelemetryChannel` di Application Insights di inviare immediatamente i dati, un elemento di telemetria alla volta, quando un debugger viene collegato al processo dell'applicazione. Ciò permette di ridurre la quantità di tempo tra il momento in cui l'applicazione rileva i dati di telemetria e il momento in cui vengono visualizzati nel portale di Application Insights. Questo causa un costo significativo per la CPU e la larghezza di banda di rete.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)

### <a name="web-request-tracking"></a>Rilevamento delle richieste web
Riporta il [tempo di risposta e il codice dei risultati](../../azure-monitor/app/asp-net.md) delle richieste HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Rilevamento delle eccezioni
`ExceptionTrackingTelemetryModule` rileva le eccezioni non gestite nell'app Web. Vedere [Errori ed eccezioni][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -rileva le eccezioni di attività non osservate
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - rileva le eccezioni non gestite per i ruoli di lavoro, servizi windows, e applicazioni della console.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacchetto NuGet.

### <a name="eventsource-tracking"></a>Rilevamento EventSource
`EventSourceTelemetryModule` consente di configurare eventi EventSource da inviare ad Application Insights come tracce. Per informazioni su eventi EventSource di rilevamento, vedere [Using EventSource Events](./asp-net-trace-logs.md#use-eventsource-events) (uso degli eventi EventSource).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Registrazione degli eventi ETW
`EtwCollectorTelemetryModule` consente di configurare gli eventi dai provider ETW da inviare ad Application Insights come tracce. Per informazioni sul rilevamento di eventi ETW, vedere [Using ETW Events](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events) (Uso di eventi ETW).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Il pacchetto Microsoft.ApplicationInsights include l' [API principale](/dotnet/api/microsoft.applicationinsights) dell'SDK. Gli altri moduli di telemetria lo usano ed è anche possibile [usarlo per definire i propri dati di telemetria](./api-custom-events-metrics.md).

* Non ci sono voci in ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se si installa questo NuGet, non viene generato nessun file .config.

## <a name="telemetry-channel"></a>Canale di telemetria
Il [canale di telemetria](telemetry-channels.md) gestisce il buffering e la trasmissione dei dati di telemetria al servizio Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` è il canale predefinito per le applicazioni Web. Memorizza nel buffer i dati in memoria e usa meccanismi di ripetizione dei tentativi e archiviazione su disco locale per il recapito di telemetria più affidabile.
* `Microsoft.ApplicationInsights.InMemoryChannel` è un canale di telemetria leggero, che viene usato se non è stato configurato alcun altro canale. 

## <a name="telemetry-initializers-aspnet"></a>Inizializzatori di telemetria (ASP.NET)
Gli inizializzatori di telemetria impostano le proprietà di contesto che vengono inviate insieme a ogni elemento di telemetria.

E’ possibile [scrivere i propri inizializzatori](./api-filtering-sampling.md#add-properties) per impostare proprietà di contesto.

Gli inizializzatori standard sono tutti impostati dal Web o dai pacchetti NuGet WindowsServer:

* `AccountIdTelemetryInitializer` imposta la proprietà AccountId.
* `AuthenticatedUserIdTelemetryInitializer` imposta la proprietà AuthenticatedUserId come impostata dal SDK di JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` aggiorna le proprietà `RoleName` e `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con le informazioni estratte dall'ambiente di runtime di Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aggiorna la proprietà `Version` del contesto `Component` per tutti gli elementi di telemetria con il valore estratto dal file `BuildInfo.config` prodotto dalla compilazione MS.
* `ClientIpHeaderTelemetryInitializer` aggiorna le proprietà `Ip` del contesto `Location` di tutti gli elementi di telemetria in base all'intestazione HTTP `X-Forwarded-For` della richiesta.
* `DeviceTelemetryInitializer` aggiorna le proprietà seguenti del contesto `Device` per tutti gli elementi di telemetria.
  * `Type` viene impostato su "PC".
  * `Id` viene impostato sul nome di dominio del computer in cui è in esecuzione l'applicazione Web.
  * `OemName` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Manufacturer` mediante WMI.
  * `Model` è impostato sul valore estratto dal campo `Win32_ComputerSystem.Model` mediante WMI.
  * `NetworkType` è impostato sul valore estratto da `NetworkInterface`.
  * `Language` è impostato sul nome di `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` aggiorna la proprietà `RoleInstance` del contesto `Device` per tutti gli elementi di telemetria con il nome di dominio del computer in cui è in esecuzione l'applicazione Web.
* `OperationNameTelemetryInitializer` aggiorna la proprietà `Name` di `RequestTelemetry` e la proprietà `Name` del contesto `Operation` di tutti gli elementi di telemetria in base al metodo HTTP, oltre ai nomi del controller MVC ASP.NET e all'azione richiamata per elaborare la richiesta.
* `OperationIdTelemetryInitializer` o `OperationCorrelationTelemetryInitializer` aggiorna la proprietà di contesto `Operation.Id` di tutti gli elementi di telemetria rilevati durante la gestione di una richiesta con il `RequestTelemetry.Id` generato automaticamente.
* `SessionTelemetryInitializer` aggiorna la proprietà `Id` del contesto `Session` per tutti gli elementi di telemetria con il valore estratto dal cookie `ai_session` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente.
* `SyntheticTelemetryInitializer` o `SyntheticUserAgentTelemetryInitializer` aggiorna le proprietà di contesto `User`, `Session` e `Operation` di tutti gli elementi di telemetria rilevati durante la gestione di una richiesta da un'origine sintetica, ad esempio un test di disponibilità o un robot del motore di ricerca. Per impostazione predefinita, [Esplora metriche](../essentials/metrics-charts.md) non mostra la telemetria sintetica.

    `<Filters>` imposta le proprietà di identificazione delle richieste.
* `UserTelemetryInitializer` aggiorna le proprietà `Id` e `AcquisitionDate` del contesto `User` per tutti gli elementi di telemetria con i valori estratti dal cookie `ai_user` generato dal codice di strumentazione JavaScript di Application Insights in esecuzione nel browser dell'utente.
* `WebTestTelemetryInitializer` imposta le proprietà ID utente, ID sessione e origine sintetica per le richieste HTTP provenienti dai [test di disponibilità](./monitor-web-app-availability.md).
  `<Filters>` imposta le proprietà di identificazione delle richieste.

Per le applicazioni .NET in esecuzione in Service Fabric, è possibile includere il pacchetto NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Questo pacchetto include `FabricTelemetryInitializer`, che aggiunge le proprietà di Service Fabric per gli elementi di telemetria. Per ulteriori informazioni, vedere la [pagina GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sulle proprietà aggiunte dal pacchetto NuGet.

## <a name="telemetry-processors-aspnet"></a>Processori di telemetria (ASP.NET)
I processori di telemetria possono filtrare e modificare ogni elemento di telemetria immediatamente prima che venga inviato dall'SDK al portale.

È possibile [scrivere processori di telemetria personalizzati](./api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processore di telemetria di campionamento adattivo (da 2.0.0-beta3)
Questa opzione è abilitata per impostazione predefinita. Se l'app invia molti dati di telemetria, questo processore rimuove alcune di esse.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Il parametro fornisce la destinazione che l'algoritmo tenta di ottenere. Ogni istanza di SDK funziona in modo indipendente, pertanto se il server è un cluster di più computer, il volume dei dati di telemetria verrà di conseguenza moltiplicato.

[Altre informazioni sul campionamento](./sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processore di telemetria di campionamento a frequenza fissa (da 2.0.0-beta1)
È disponibile anche un [processore di telemetria di campionamento](./api-filtering-sampling.md) standard (da 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey
Determina la risorsa di Application Insights in cui vengono visualizzati i dati. In genere, viene creata una risorsa separata, con una chiave separata, per ognuna delle applicazioni.

Se si vuole impostare la chiave in modo dinamico, ad esempio se si intende inviare i risultati dall'applicazione a diverse risorse, è possibile omettere la chiave dal file di configurazione e impostarla nel codice.

Per impostare la chiave per tutte le istanze di TelemetryClient, inclusi i moduli di telemetria standard. Eseguire questa operazione in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Se si vuole inviare un set specifico di eventi a una risorsa diversa, è possibile impostare la chiave per un oggetto TelemetryClient specifico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Per ottenere una nuova chiave, [creare una nuova risorsa nel portale di Application Insights][new].



## <a name="applicationid-provider"></a>Provider ApplicationId

_Disponibile a partire dalla versione v2.6.0_

Lo scopo di questo provider è quello di cercare un ID applicazione in base a una chiave di strumentazione. L'ID applicazione è incluso in RequestTelemetry e DependencyTelemetry e usato per determinare la correlazione nel portale.

È disponibile impostando `TelemetryConfiguration.ApplicationIdProvider` nel codice o nella configurazione.

### <a name="interface-iapplicationidprovider"></a>Interfaccia: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Vengono fornite due implementazioni nell'SDK [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights): `ApplicationInsightsApplicationIdProvider` e `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Wrapper dell'API di profilatura. Limita le richieste e i risultati nella cache.

Questo provider viene aggiunto al file config quando si installa [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Questa classe ha una proprietà `ProfileQueryEndpoint` facoltativa.
L'impostazione predefinita è `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Se è necessario configurare un proxy per questa configurazione, è consigliabile inoltrare i dati all'indirizzo di base e includere "/api/profiles/{0}/appId". '{0}' viene sostituito in fase di esecuzione per ogni richiesta con la chiave di strumentazione.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Configurazione di esempio tramite ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Configurazione di esempio tramite codice:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Si tratta di un provider statico che si basa sulle coppie di chiave di strumentazione/ID applicazione configurate.

Questa classe ha una proprietà `Defined` che corrisponde a un oggetto Dictionary<stringa,stringa> di coppie di chiave di strumentazione/ID applicazione.

Questa classe ha una proprietà `Next` facoltativa che può essere usata per configurare un altro provider da usare quando viene richiesta una chiave di strumentazione che non esiste nella configurazione.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Configurazione di esempio tramite ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Configurazione di esempio tramite codice:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sull'API][api].

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

