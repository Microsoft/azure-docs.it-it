---
title: Diagnostica con informazioni dettagliate Live Metrics Stream applicazione Azure
description: Monitorare l'app Web in tempo reale, con metriche personalizzate e diagnosticare problemi con un feed live di errori, tracce ed eventi.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 4b84088c1213801e61a4c669bccb1a983c999310
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321939"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorare e diagnosticare con una latenza di 1 secondo

Monitora l'applicazione Web Live in produzione usando Live Metrics Stream da [Application Insights](./app-insights-overview.md). Selezionare e filtrare le metriche e i contatori delle prestazioni in tempo reale, senza distorsioni del servizio. Esaminare le analisi dello stack da richieste ed eccezioni di esempio non riuscite. Insieme a [Profiler](./profiler.md) e [Snapshot debugger](./snapshot-debugger.md), Live Metrics Stream offre uno strumento di diagnostica potente e non invasivo per il sito Web Live.

Con Live Metrics Stream, è possibile:

* Convalidare una correzione durante il rilasciato, osservando i contatori delle prestazioni e degli errori.
* Osservare l'effetto dei carichi di test e diagnosticare i problemi live.
* Concentrarsi sulle sessioni di test specifiche o filtrare i problemi noti, selezionando e filtrando le metriche che si desidera osservare.
* Ottenere le eccezioni delle tracce in tempo reale.
* Provare a usare i filtri per individuare gli indicatori di prestazioni chiave più rilevanti.
* Monitorare ogni contatore delle prestazioni di Windows live.
* Identificare facilmente un server che presenta problemi e filtrare tutti gli indicatori KPI o i feed live solo per tale server.

![Scheda metriche attive](./media/live-stream/live-metric.png)

Le metriche attive sono attualmente supportate per le app ASP.NET, ASP.NET Core, funzioni di Azure, Java e Node.js.

## <a name="get-started"></a>Introduzione

1. [Installare Application Insights](../azure-monitor-app-hub.yml) nell'applicazione.
2. Oltre ai pacchetti standard di Application Insights, è necessario [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) per abilitare Live Metrics Stream.
3. **Eseguire l'aggiornamento alla versione più recente** del pacchetto Application Insights. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Aprire la scheda **Aggiornamenti** e selezionare tutti i pacchetti Microsoft.ApplicationInsights.*.

    Ridistribuire l'app.

3. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights per l'app e quindi Live Stream.

4. [Proteggere il canale di controllo](#secure-the-control-channel) se è possibile usare i dati sensibili, ad esempio i nomi dei clienti, nei filtri.

### <a name="no-data-check-your-server-firewall"></a>Dati non visualizzati Controllare il firewall del server

Controllare che [le porte in uscita di Live Metrics Stream](./ip-addresses.md#outgoing-ports) siano aperte nel firewall del server.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Differenze tra Live Metrics Stream ed Esplora metriche e Analisi

| |Live Stream | Esplora metriche e Analisi |
|---|---|---|
|**Latency**|Dati visualizzati in un secondo|Aggregati in minuti|
|**Nessuna conservazione**|I dati vengono mantenuti finché si trovano nel grafico, poi vengono eliminati|[Dati mantenuti per 90 giorni](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Su richiesta**|I dati vengono trasmessi solo quando il riquadro metriche attive è aperto |I dati vengono inviati ogni volta che l'SDK viene installato e attivato|
|**Free**|Non sono previste spese per i dati di Live Stream|Soggetto al [piano tariffario](./pricing.md)
|**Campionamento**|Tutte le metriche selezionate e i contatori vengono trasmessi. Gli errori e le analisi dello stack vengono usati come esempi. TelemetryProcessors non viene applicato.|Eventi potrebbero essere usati come [esempi](./api-filtering-sampling.md)|
|**Canale di controllo**|I segnali di controllo del filtro vengono inviati all'SDK. È consigliabile proteggere questo canale.|La comunicazione è unidirezionale, al portale|

## <a name="select-and-filter-your-metrics"></a>Selezionare e filtrare le metriche

Disponibile con ASP.NET, ASP.NET Core e Funzioni di Azure (v2).

È possibile monitorare gli indicatori KPI personalizzati live applicando filtri arbitrari su eventuali dati di Application Insights Telemetry dal portale. Fare clic sul controllo del filtro che viene mostrato quando il puntatore del mouse passa su un grafico. Il grafico seguente traccia un indicatore KPI del conteggio di richieste personalizzato con filtri sugli attributi dell'URL e della durata. Convalidare i filtri nella sezione Anteprima flusso che mostra un feed live di telemetria che corrisponde ai criteri specificati in qualsiasi punto nel tempo.

![Frequenza di richieste di filtro](./media/live-stream/filter-request.png)

È possibile monitorare un valore diverso dal conteggio. Le opzioni dipendono dal tipo di flusso, che può essere una qualsiasi Application Insights Telemetry: richieste, dipendenze, eccezioni, tracce, eventi o metriche. Può essere la propria [misura personalizzata](./api-custom-events-metrics.md#properties):

![Generatore di query sulla frequenza delle richieste con metrica personalizzata](./media/live-stream/query-builder-request.png)

Oltre ai dati di Application Insights Telemetry, è anche possibile monitorare un contatore delle prestazioni di Windows selezionandolo dalle opzioni di flusso e inserendo il nome del contatore delle prestazioni.

Le metriche attive vengono aggregate in due punti: in locale su ciascun server e quindi su tutti i server. È possibile modificare l'impostazione predefinita per entrambi selezionando altre opzioni nei rispettivi elenchi a discesa.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria di esempio: eventi di diagnostica live personalizzati
Per impostazione predefinita, il feed live degli eventi mostra esempi di richieste non riuscite e chiamate di dipendenza, eccezioni, eventi e tracce. Fare clic sull'icona del filtro per visualizzare i criteri applicati in un punto qualsiasi nel tempo.

![Pulsante Filter (Filtro)](./media/live-stream/filter.png)

Così come con le metriche, è possibile specificare i criteri arbitrari per i tipi di dati di Application Insights Telemetry. In questo esempio vengono selezionati errori di richiesta specifici ed eventi.

![Generatore di query](./media/live-stream/query-builder.png)

> [!NOTE]
> attualmente, per i criteri di eccezione basati sul messaggio, usare il messaggio dell'eccezione più esterna. Nell'esempio precedente, per filtrare l'eccezione di tipo benigno con messaggio di eccezione interna, segue il delimitatore "<--", "Il client si è disconnesso." usare un criterio che non contiene il messaggio "Errore durante la lettura del contenuto della richiesta".

Visualizzare i dettagli di un elemento nel feed live facendovi clic sopra. È possibile sospendere il feed facendo clic su **Sospendi** o semplicemente scorrendo verso il basso o facendo clic su un elemento. Il feed live verrà ripreso se, scorrendo, si torna all'inizio o facendo clic sul contatore degli elementi raccolti che era stato sospeso.

![Errori live campionati](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtro per istanza di server

Se si vuole monitorare un'istanza specifica del ruolo server, è possibile applicare un filtro per server. Per filtrare, selezionare il nome del server in *Server*.

![Errori live campionati](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Proteggere il canale di controllo

> [!NOTE]
> Attualmente, è possibile configurare solo un canale autenticato usando il monitoraggio codebase e non è in grado di autenticare i server che usano la connessione senza codice.

I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave privata API e con la chiave di strumentazione.
### <a name="create-an-api-key"></a>Creare una chiave API

![Chiave API > creare la chiave API ](./media/live-stream/api-key.png)
 ![ creare la scheda chiave API. Selezionare "autenticare il canale di controllo SDK" quindi "Genera chiave"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Aggiungere una chiave API alla configurazione

### <a name="classic-aspnet"></a>ASP.NET classico

Nel file applicationinsights.config aggiungere AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Oppure nel codice impostarla in QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>App per le funzioni di Azure

Per le app per le funzioni di Azure (v2), la protezione del canale con una chiave API può essere eseguita con una variabile di ambiente.

Creare una chiave API dalla risorsa di Application Insights e passare a **Impostazioni applicazione** per l'app per le funzioni. Selezionare **Aggiungi nuova impostazione** e immettere il nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e un valore corrispondente alla chiave API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (richiede Application Insights ASP.NET Core SDK 2.3.0 o versione successiva)

Modificare il file startup.cs come indicato di seguito:

Prima aggiungere

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Poi, all'interno del metodo ConfigureServices, aggiungere:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Tuttavia, se l'utente riconosce tutti i server collegati e li ritiene affidabili, è possibile provare i filtri personalizzati senza il canale autenticato. Questa opzione è disponibile per sei mesi. Questa sostituzione è necessaria una volta per ogni nuova sessione oppure quando un nuovo server è online.

![Opzioni di autenticazione delle metriche attive](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>È consigliabile configurare il canale autenticato prima di immettere informazioni potenzialmente riservate, ad esempio CustomerID nei criteri di filtro.
>

## <a name="supported-features-table"></a>Tabella delle funzionalità supportate

| Linguaggio                         | Metriche di base       | Metriche delle prestazioni | Filtro personalizzato    | Telemetria di esempio    | Suddivisione CPU per processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +) | Supportato (V 2.7.2 +)  |
| .NET Core (target =. NET Framework)| Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +)  |
| .NET Core (target =. NET Core)     | Supportato (versione 2.4.1 +) | Supportato*          | Supportato (versione 2.4.1 +) | Supportato (versione 2.4.1 +) | **Non supportato**    |
| Funzioni di Azure v2               | Supportato           | Supportato           | Supportato           | Supportato           | **Non supportato**    |
| Java                             | Supportato (versione 2.0.0 +) | Supportato (versione 2.0.0 +) | **Non supportato**   | **Non supportato**   | **Non supportato**    |
| Node.js                          | Supportato (V 1.3.0 +) | Supportato (V 1.3.0 +) | **Non supportato**   | Supportato (V 1.3.0 +) | **Non supportato**    |

Le metriche di base includono la richiesta, la dipendenza e la frequenza delle eccezioni. Le metriche delle prestazioni (contatori delle prestazioni) includono memoria e CPU. La telemetria di esempio mostra un flusso di informazioni dettagliate per le richieste e le dipendenze non riuscite, le eccezioni, gli eventi e le tracce.

 \*Il supporto di PerfCounters varia leggermente tra le versioni di .NET Core che non sono destinate al .NET Framework:

- Le metriche PerfCounters sono supportate durante l'esecuzione nel servizio app Azure per Windows. (AspNetCore SDK versione 2.4.1 o successiva)
- PerfCounters sono supportati quando l'app viene eseguita in qualsiasi computer Windows (VM o servizio cloud o in locale). (AspNetCore SDK versione 2.7.1 o successiva), ma per le app destinate a .NET Core 2,0 o versioni successive.
- PerfCounters sono supportati quando l'app è in esecuzione ovunque (Linux, Windows, servizio app per Linux, contenitori e così via) nella versione beta più recente (ad esempio AspNetCore SDK versione 2.8.0-beta1 o successiva), ma per le app destinate a .NET Core 2,0 o versioni successive.

Per impostazione predefinita, la metrica dinamica è disabilitata in Node.js SDK. Per abilitare le metriche in tempo reale, aggiungere `setSendLiveMetrics(true)` i [metodi di configurazione](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) durante l'inizializzazione dell'SDK.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Dati non visualizzati Se l'applicazione si trova in una rete protetta: Live Metrics Stream USA indirizzi IP diversi rispetto ad altri dati di telemetria Application Insights. Assicurarsi che [tali indirizzi IP](./ip-addresses.md) siano aperti nel firewall.

## <a name="next-steps"></a>Passaggi successivi
* [Monitoraggio dell'utilizzo con Application Insights](./usage-overview.md)
* [Uso di Ricerca diagnostica](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Debugger di snapshot](./snapshot-debugger.md)

