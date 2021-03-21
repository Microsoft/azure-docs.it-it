---
title: Analizzare la telemetria di funzioni di Azure in Application Insights
description: Informazioni su come visualizzare ed eseguire query per i dati di telemetria di funzioni di Azure raccolti da e archiviati in applicazione Azure Insights.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: d06fe64ddc0475b5ca7d9c16876c8dfc9acda544
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729369"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Analizzare la telemetria di funzioni di Azure in Application Insights 

Funzioni di Azure si integra con Application Insights per consentire un monitoraggio migliore delle app per le funzioni. Application Insights raccoglie i dati di telemetria generati dall'app per le funzioni, incluse le informazioni scritte dall'app nei log. L'integrazione di Application Insights viene in genere abilitata quando si crea l'app per le funzioni. Se l'app per le funzioni non ha il set di chiavi di strumentazione, è necessario innanzitutto [abilitare l'integrazione Application Insights](configure-monitoring.md#enable-application-insights-integration). 

Per impostazione predefinita, i dati raccolti dall'app per le funzioni vengono archiviati in Application Insights. Nella [portale di Azure](https://portal.azure.com)Application Insights fornisce un set completo di visualizzazioni dei dati di telemetria. È possibile esaminare i log degli errori ed eseguire query su eventi e metriche. Questo articolo fornisce esempi di base su come visualizzare ed eseguire query sui dati raccolti. Per altre informazioni sull'esplorazione dei dati delle app per le funzioni in Application Insights, vedere [che cos'è Application Insights?](../azure-monitor/app/app-insights-overview.md). 

Per altre informazioni sulla conservazione dei dati e sui costi di archiviazione potenziali, vedere [raccolta, conservazione e archiviazione dei dati in Application Insights](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>Visualizzazione della telemetria nella scheda monitoraggio

Con l'[integrazione di Application Insights abilitata](configure-monitoring.md#enable-application-insights-integration), è possibile visualizzare i dati di telemetria nella scheda **Monitor**.

1. Nella pagina dell'app per le funzioni selezionare una funzione eseguita almeno una volta dopo la configurazione di Application Insights, quindi selezionare **Monitor** nel riquadro a sinistra. Selezionare **Aggiorna** periodicamente fino a quando non viene visualizzato l'elenco di chiamate di funzione.

   ![Elenco di chiamate](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > La visualizzazione dell'elenco può richiedere fino a 5 minuti, il tempo necessario al client di telemetria di configurare in batch i dati da trasmettere al server. Questo ritardo non si applica al servizio [Live Metrics Stream](../azure-monitor/app/live-stream.md), che si connette all'host di Funzioni mentre si carica la pagina e, quindi, i log vengono trasmessi direttamente alla pagina.

1. Per visualizzare i log per una chiamata di funzione particolare, selezionare il collegamento alla colonna **Data (UTC)** per la chiamata. L'output di registrazione per tale chiamata viene visualizzato in una nuova pagina.

   ![Dettagli della chiamata](media/functions-monitoring/invocation-details-ai.png)

1. Scegliere **Esegui in Application Insights** per visualizzare l'origine della query che recupera i dati di log di Monitoraggio di Azure nel log di Azure. Se è la prima volta che si usa Azure Log Analytics nella propria sottoscrizione, viene chiesto di abilitarlo.

1. Dopo aver abilitato Log Analytics, viene visualizzata la query seguente. Si noterà che i risultati della query sono limitati agli ultimi 30 giorni ( `where timestamp > ago(30d)` ) e i risultati non mostrano più di 20 righe ( `take 20` ). Al contrario, l'elenco dei dettagli delle chiamate alla funzione è relativo agli ultimi 30 giorni senza alcun limite.

   ![Elenco di chiamate di analisi di Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Per altre informazioni, vedere [Eseguire query sui dati di telemetria](#query-telemetry-data) più avanti in questo articolo.

## <a name="view-telemetry-in-application-insights"></a>Visualizzare i dati di telemetria in Application Insights

Per aprire Application Insights da un'app per le funzioni nel [portale di Azure](https://portal.azure.com):

1. Passare all'app per le funzioni nel portale.

1. Selezionare **Application Insights** in **Impostazioni** nella pagina a sinistra. 

1. Se è la prima volta che si usa Application Insights con la sottoscrizione, verrà richiesto di abilitarla. A tale scopo, selezionare **attiva Application Insights** e quindi fare clic su **applica** nella pagina successiva.

![Aprire Application Insights dalla pagina di panoramica dell'app per le funzioni](media/functions-monitoring/ai-link.png)

Per informazioni su come usare Application Insights, vedere la [documentazione su Application Insights](/azure/application-insights/). Questa sezione mostra alcuni esempi su come visualizzare i dati in Application Insights. Se si ha già familiarità con Application Insights, è possibile passare direttamente alle [sezioni sulla configurazione e la personalizzazione dei dati di telemetria](configure-monitoring.md#configure-log-levels).

![Scheda di panoramica di Application Insights](media/functions-monitoring/metrics-explorer.png)

Le aree di Application Insights seguenti possono essere utili durante la valutazione del comportamento, delle prestazioni e degli errori delle funzioni:

| Analisi dei problemi | Descrizione |
| ---- | ----------- |
| **[Errori](../azure-monitor/app/asp-net-exceptions.md)** |  È possibile creare grafici e avvisi in base agli errori di funzione e alle eccezioni del server. Il **nome dell'operazione** corrisponde al nome della funzione. Gli errori nelle dipendenze non vengono mostrati a meno che non si implementino i dati di telemetria personalizzati per le dipendenze. |
| **[Prestazioni](../azure-monitor/app/performance-counters.md)** | È possibile analizzare i problemi di prestazioni visualizzando l'utilizzo delle risorse e la velocità effettiva per le **Istanze del ruolo del cloud**. Questi dati sulle prestazioni possono essere utili per gli scenari di debug in cui le funzioni complicare le risorse sottostanti. |
| **[Metriche](../azure-monitor/essentials/metrics-charts.md)** | È possibile creare grafici e avvisi basati sulle metriche, che includono il numero di chiamate di funzione, il tempo di esecuzione e le percentuali di riuscita. |
| **[Metriche attive](../azure-monitor/app/live-stream.md)** | Visualizza i dati delle metriche così come vengono creati quasi in tempo reale. |

## <a name="query-telemetry-data"></a>Query sui dati di telemetria

[Analytics di Application Insights](../azure-monitor/logs/log-query-overview.md) consente di accedere a tutti i dati di telemetria sotto forma di tabelle in un database. Analytics offre un linguaggio di query per l'estrazione, la manipolazione e la visualizzazione dei dati. 

Scegliere **Log** per esplorare o eseguire una query in merito agli eventi registrati.

![Esempio di Analytics](media/functions-monitoring/analytics-traces.png)

Questo è un esempio che mostra la distribuzione delle richieste per ruolo di lavoro negli ultimi 30 minuti.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Le tabelle disponibili vengono mostrate nella scheda **Schema** a sinistra. Nelle tabelle seguenti è possibile trovare i dati generati dalle chiamate alla funzione:

| Tabella | Descrizione |
| ----- | ----------- |
| **traces** | Log creati dal runtime, dal controller di scalabilità e dalle tracce del codice della funzione. |
| **requests** | una richiesta per ogni chiamata alla funzione. |
| **exceptions** | tutte le eccezioni generate dal runtime. |
| **customMetrics** | numero di chiamate con esito positivo e negativo, percentuale di riuscita, durata. |
| **customEvents** | eventi rilevati dal runtime, ad esempio: richieste HTTP che attivano una funzione. |
| **performanceCounters** | informazioni sulle prestazioni dei server su cui sono in esecuzione le funzioni. |

Le altre tabelle sono riservate ai test di disponibilità e ai dati di telemetria del browser e del client. È possibile implementare i dati di telemetria personalizzati per aggiungerne altri.

All'interno di ogni tabella alcuni dati specifici per Funzioni si trovano nel campo `customDimensions`.  Ad esempio, la query seguente recupera tutte le tracce con livello di registrazione `Error`.

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

Il runtime fornisce i campi `customDimensions.LogLevel` e `customDimensions.Category`. È possibile specificare campi aggiuntivi nei log in cui si scrive il codice funzione. Per un esempio in C#, vedere [registrazione strutturata](functions-dotnet-class-library.md#structured-logging) nella Guida per gli sviluppatori di librerie di classi .NET.

## <a name="query-scale-controller-logs"></a>Log del controller di scalabilità delle query

_Questa funzionalità è disponibile in anteprima._

Dopo aver abilitato la [registrazione del controller di scalabilità](configure-monitoring.md#configure-scale-controller-logs) e l' [integrazione di Application Insights](configure-monitoring.md#enable-application-insights-integration), è possibile usare la ricerca log Application Insights per eseguire una query per i log del controller di scala emessi. I log del controller di ridimensionamento vengono salvati nella `traces` raccolta nella categoria **ScaleControllerLogs** .

La query seguente può essere usata per cercare tutti i log del controller di scalabilità per l'app per le funzioni corrente entro il periodo di tempo specificato:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

La query seguente si espande nella query precedente per mostrare come ottenere solo i log che indicano una modifica della scala:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>Metriche specifiche del piano a consumo

Quando è in esecuzione in un [piano a consumo](consumption-plan.md), il *costo* di esecuzione di una singola esecuzione di funzione viene misurato in *GB-secondi*. Il costo di esecuzione viene calcolato combinando l'utilizzo della memoria con il relativo tempo di esecuzione. Per altre informazioni, vedere [stima dei costi del piano a consumo](functions-consumption-costs.md).

Le seguenti query di telemetria sono specifiche per le metriche che incidono sul costo delle funzioni in esecuzione nel piano a consumo.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="azure-monitor-metrics"></a>Metriche di monitoraggio di Azure

Oltre ai dati di telemetria raccolti da Application Insights, è anche possibile ottenere informazioni sul modo in cui l'app per le funzioni viene eseguita dalle [metriche di monitoraggio di Azure](../azure-monitor/essentials/data-platform-metrics.md). Insieme alle metriche consuete [disponibili per](../app-service/web-sites-monitor.md#understand-metrics)le app del servizio app, esistono due metriche specifiche per le funzioni di interesse:

| Metrica | Descrizione |
| ---- | ---- |
| **FunctionExecutionCount** | Conteggio esecuzioni di funzioni indica il numero di volte in cui l'app per le funzioni è stata eseguita. Questo è correlato al numero di volte in cui una funzione viene eseguita nell'app. Questa metrica non è attualmente supportata per i piani Premium e dedicati (servizio app) in esecuzione su Linux. |
| **FunctionExecutionUnits** | Le unità di esecuzione della funzione sono una combinazione di tempo di esecuzione e di utilizzo della memoria.  I dati di memoria non sono una metrica attualmente disponibile tramite monitoraggio di Azure. Tuttavia, se si vuole ottimizzare l'utilizzo della memoria dell'app, può usare i dati dei contatori delle prestazioni raccolti da Application Insights. Questa metrica non è attualmente supportata per i piani Premium e dedicati (servizio app) in esecuzione su Linux.|

Per altre informazioni sul calcolo dei costi per un piano a consumo usando i dati Application Insights, vedere [stima dei costi del piano a consumo](functions-consumption-costs.md). Per altre informazioni sull'uso di Esplora monitor per visualizzare le metriche, vedere [Introduzione ad Azure Esplora metriche](../azure-monitor/essentials/metrics-getting-started.md).


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul monitoraggio di funzioni di Azure:

+ [Monitorare Funzioni di Azure](functions-monitoring.md)
+ [Come configurare il monitoraggio per funzioni di Azure](configure-monitoring.md)