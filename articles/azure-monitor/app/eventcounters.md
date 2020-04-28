---
title: Contatori di eventi in Application Insights | Microsoft Docs
description: Monitorare il sistema e i EventCounters .NET/.NET Core personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e1037766587f58a30c20f614726e1241c16e5a16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187096"
---
# <a name="eventcounters-introduction"></a>Introduzione a EventCounters

`EventCounter`è il meccanismo .NET/.NET Core per la pubblicazione e l'utilizzo di contatori o statistiche. [Questo](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornisce una panoramica `EventCounters` ed esempi su come pubblicarli e utilizzarli. EventCounters sono supportati in tutte le piattaforme del sistema operativo: Windows, Linux e macOS. Può essere considerato come un equivalente multipiattaforma per [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) supportato solo nei sistemi Windows.

Sebbene gli utenti possano pubblicare qualsiasi `EventCounters` personalizzata per soddisfare le proprie esigenze, il runtime di .net core 3,0 pubblica un set di questi contatori per impostazione predefinita. Nel documento vengono illustrati i passaggi necessari per raccogliere e visualizzare `EventCounters` (definito dal sistema o definito dall'utente) in applicazione Azure Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Uso di Application Insights per la raccolta di EventCounters

Application Insights supporta la `EventCounters` raccolta con `EventCounterCollectionModule`la relativa, che fa parte del nuovo pacchetto NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`viene abilitato automaticamente quando si usa [AspNetCore](asp-net-core.md) o [WorkerService](worker-service.md). `EventCounterCollectionModule`raccoglie i contatori con una frequenza di raccolta non configurabile di 60 secondi. Non sono necessarie autorizzazioni speciali per la raccolta di EventCounters.

## <a name="default-counters-collected"></a>Contatori predefiniti raccolti

Per le app in esecuzione in .NET Core 3,0, i contatori seguenti vengono raccolti automaticamente dall'SDK. Il nome del contatore avrà il formato "Category | Contatore ".

|Category | Contatore|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> I contatori della categoria Microsoft. AspNetCore. Hosting vengono aggiunti solo nelle applicazioni ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizzazione dei contatori da raccogliere

Nell'esempio seguente viene illustrato come aggiungere o rimuovere i contatori. Questa personalizzazione viene eseguita nel metodo dell' `ConfigureServices` applicazione dopo l'abilitazione della raccolta di dati di telemetria `AddApplicationInsightsTelemetry()` Application Insights `AddApplicationInsightsWorkerService()`tramite o. Di seguito è riportato un esempio di codice di un'applicazione ASP.NET Core. Per altri tipi di applicazioni, fare riferimento a [questo](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Contatori di eventi in Esplora metriche

Per visualizzare le metriche di EventCounter in [Esplora metriche](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), selezionare Application Insights risorsa e scegliere metriche basate su log come spazio dei nomi metrica. Quindi, le metriche EventCounter vengono visualizzate in categoria personalizzata.

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contatori di eventi in Analytics

È anche possibile cercare e visualizzare i report dei contatori di eventi in [Analytics](../../azure-monitor/app/analytics.md)nella tabella **customMetrics** .

Eseguire ad esempio la query seguente per visualizzare i contatori raccolti e disponibili per le query:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights](./media/event-counters/analytics-event-counters.png)

Per ottenere un grafico di un contatore specifico (ad esempio: `ThreadPool Completed Work Item Count`) nel periodo recente, eseguire la query seguente.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat di un contatore singolo in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Analogamente ad altri dati **customMetrics** di telemetria, `cloud_RoleInstance` customMetrics include anche una colonna che indica l'identità dell'istanza del server host in cui è in esecuzione l'app. La query precedente Mostra il valore del contatore per ogni istanza e può essere utilizzata per confrontare le prestazioni delle diverse istanze del server.

## <a name="alerts"></a>Avvisi
Analogamente ad altre metriche, è possibile [impostare un avviso](../../azure-monitor/app/alerts.md) per avvisare l'utente se un contatore di eventi supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-see-eventcounters-in-live-metrics"></a>È possibile visualizzare EventCounters in metriche attive?

Le metriche in tempo reale non mostrano EventCounters a partire da oggi. Usare Esplora metriche o Analytics per visualizzare i dati di telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Quali piattaforme è possibile visualizzare l'elenco predefinito dei contatori di .NET Core 3,0?

EventCounter non richiede alcuna autorizzazione speciale ed è supportato in tutte le piattaforme .NET Core 3,0 è supportato. ad esempio:

* **Sistema operativo**: Windows, Linux o MacOS.
* **Metodo di hosting**: in-process o out-of-process.
* **Metodo di distribuzione**: dipendente dal Framework o indipendente.
* **Server Web**: IIS (Internet Information Server) o gheppio.
* **Piattaforma di hosting**: la funzionalità app Web di app Azure servizio, VM di Azure, Docker, Azure Kubernetes Service (AKS) e così via.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Ho abilitato Application Insights dal portale app Web di Azure. Ma non è possibile vedere EventCounters.

 [Application Insights estensione](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) per ASP.NET Core non supporta ancora questa funzionalità. Questo documento verrà aggiornato quando questa funzionalità è supportata.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md)
