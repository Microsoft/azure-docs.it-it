---
title: Contatori degli eventi in Application Insights | Microsoft Docs
description: È possibile monitorare i contatori degli eventi di sistema e di .NET/.NET Core personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579531"
---
# <a name="eventcounters-introduction"></a>Introduzione di EventCounter

[`EventCounter`](/dotnet/core/diagnostics/event-counters) è il meccanismo .NET/.NET Core per la pubblicazione e l'utilizzo di contatori o statistiche. Il supporto per EventCounter è disponibile in tutte le piattaforme del sistema operativo, ovvero Windows, Linux e macOS. Possono essere considerati un equivalente multipiattaforma per [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) che è supportato solo nei sistemi Windows.

Sebbene gli utenti possano pubblicare qualsiasi personalizzata `EventCounters` per soddisfare le proprie esigenze, .NET Core 3,0 e versioni successive di runtime pubblica un set di questi contatori per impostazione predefinita. In questo documento vengono illustrati i passaggi necessari per raccogliere e visualizzare `EventCounters` (definito dal sistema o definito dall'utente) in applicazione Azure Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Uso di Application Insights per raccogliere EventCounters

Application Insights supporta `EventCounters` la raccolta con la relativa `EventCounterCollectionModule` , che fa parte del nuovo pacchetto NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` viene abilitato automaticamente quando si usa [AspNetCore](asp-net-core.md) o [WorkerService](worker-service.md). `EventCounterCollectionModule` raccoglie contatori con una frequenza di raccolta non configurabile pari a 60 secondi. Non sono richieste autorizzazioni speciali per raccogliere EventCounters.

## <a name="default-counters-collected"></a>Contatori predefiniti raccolti

A partire dalla versione 2.15.0 di [ASPNETCORE SDK](asp-net-core.md) o [WorkerService SDK](worker-service.md), per impostazione predefinita non viene raccolto alcun contatore. Il modulo stesso è abilitato, quindi gli utenti possono semplicemente aggiungere i contatori desiderati per raccoglierli.

Per ottenere un elenco di contatori noti pubblicati dal runtime .NET, vedere il documento [contatori disponibili](/dotnet/core/diagnostics/event-counters#available-counters) .

## <a name="customizing-counters-to-be-collected"></a>Personalizzazione dei contatori da raccogliere

L'esempio seguente illustra come aggiungere/rimuovere contatori. Questa personalizzazione viene eseguita nel metodo `ConfigureServices` dell'applicazione dopo l'abilitazione della telemetria di Application Insights con `AddApplicationInsightsTelemetry()` o `AddApplicationInsightsWorkerService()`. Di seguito è disponibile un esempio di codice da un'applicazione ASP.NET Core. Per altri tipi di applicazioni, vedere [questo](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Disabilitazione del modulo della raccolta EventCounter

`EventCounterCollectionModule` può essere disabilitato usando `ApplicationInsightsServiceOptions` . Di seguito è riportato un esempio di utilizzo di ASP.NET Core SDK.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Un approccio simile può essere usato anche per WorkerService SDK, ma lo spazio dei nomi deve essere modificato come illustrato nell'esempio riportato di seguito.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Contatori degli eventi nello strumento di esplorazione metriche

Per visualizzare le metriche di EventCounter nello [strumento di esplorazione metriche](../essentials/metrics-charts.md), selezionare la risorsa di Application Insights e scegliere le metriche basate su log come spazio dei nomi per la metrica. Le metriche di EventCounter vengono visualizzate nella categoria Personalizzata.

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights Esplora metriche](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contatori degli eventi in Analytics

È anche possibile cercare e visualizzare report sui contatori degli eventi in [Analytics](../logs/log-query-overview.md) nella scheda **customMetrics**.

Eseguire ad esempio la query seguente per visualizzare i contatori raccolti e disponibili per le query:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights Analytics](./media/event-counters/analytics-event-counters.png)

Per ottenere un grafico di un contatore specifico, ad esempio `ThreadPool Completed Work Item Count`, nel periodo recente, eseguire la query seguente.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Grafico di un singolo contatore in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Come altri dati di telemetria, **customMetrics** contiene anche una colonna `cloud_RoleInstance` che indica l'identità dell'istanza del server host in cui viene eseguita l'app. La query precedente mostra il valore del contatore per ogni istanza e può essere usata per confrontare le prestazioni di diverse istanze del server.

## <a name="alerts"></a>Avvisi
Come per altre metriche, è possibile [impostare un avviso](../alerts/alerts-log.md) per ricevere una notifica se un contatore degli eventi supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-see-eventcounters-in-live-metrics"></a>È possibile visualizzare EventCounters in Metriche attive?

In Metriche attive non vengono mostrati EventCounters a oggi. Usare lo strumento di esplorazione metriche o Analytics per visualizzare i dati di telemetria.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Application Insights è stato abilitato dal portale di App Web di Azure. Non è tuttavia possibile visualizzare EventCounters.

 L'[estensione di Application Insights](./azure-web-apps.md) per ASP.NET Core non supporta ancora questa funzionalità. Questo documento verrà aggiornato quando la funzionalità sarà supportata.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](./asp-net-dependencies.md)

