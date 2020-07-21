---
title: 'Come progettare la distribuzione di Application Insights: una o più risorse?'
description: Telemetria diretta a risorse diverse per indicatori di sviluppo, test e produzione.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: ff301887aebf64d26d0fb391a8a16adefc8a3860
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516720"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Quante risorse Application Insights è necessario distribuire

Quando si sviluppa la versione successiva di un'applicazione Web, non è desiderabile combinare la telemetria di [Application Insights](../../azure-monitor/app/app-insights-overview.md) della nuova versione con quella della versione già rilasciata. Per evitare confusione, inviare i dati di telemetria da diverse fasi di sviluppo per separare le risorse di Application Insights, con chiavi di strumentazione separate (iKey). Per rendere più semplice la modifica della chiave di strumentazione man mano che una versione si sposta da una fase all'altra, può essere utile impostare il valore ikey nel codice anziché nel file di configurazione.

Se il sistema è un servizio cloud di Azure, è disponibile [un altro metodo di impostazione di valori iKey separati](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sulle risorse e le chiavi di strumentazione

Quando si configura il monitoraggio di Application Insights per l'app Web, viene creata una *risorsa* di Application Insights in Microsoft Azure. Aprire questa risorsa nel portale di Azure per visualizzare e analizzare i dati di telemetria raccolti dall'app. La risorsa viene identificata da una *chiave di strumentazione* (iKey). Quando si installa il pacchetto Application Insights per monitorare l'applicazione, questo viene configurato con la chiave di strumentazione, in modo che sappia dove inviare la telemetria.

Ogni risorsa di Application Insights viene fornita con metriche disponibili. Se componenti completamente separati inviano report alla stessa risorsa di Application Insights, potrebbe non essere sensato usare queste metriche per dashboard e avvisi.

### <a name="when-to-use-a-single-application-insights-resource"></a>Quando usare una singola risorsa di Application Insights

-   Per i componenti dell'applicazione distribuiti insieme. In genere sviluppata da un singolo team, gestita dallo stesso gruppo di utenti DevOps/ITOps.
-   Se è opportuno aggregare gli indicatori di prestazioni chiave (KPI), ad esempio le durate di risposta, le percentuali di errore nel dashboard e così via, per impostazione predefinita. È possibile scegliere di segmentare in base al nome del ruolo nell'esperienza Esplora metriche.
-   Se non è necessario gestire il controllo degli accessi in base al ruolo in modo diverso tra i componenti dell'applicazione.
-   Se non sono necessari criteri di avviso per le metriche diversi tra i componenti.
-   Se non è necessario gestire le esportazioni continue in modo diverso tra i componenti.
-   Se non è necessario gestire la fatturazione e le quote in modo diverso tra i componenti.
-   Se è corretto avere una chiave API con lo stesso accesso ai dati di tutti i componenti e 10 chiavi API sono sufficienti per soddisfare le esigenze di tutti i componenti.
-   Se è corretto avere le stesse impostazioni di rilevamento intelligente e di integrazione degli elementi di lavoro in tutti i ruoli.

### <a name="other-things-to-keep-in-mind"></a>Altri aspetti da considerare

-   Potrebbe essere necessario aggiungere codice personalizzato per assicurarsi che vengano impostati valori significativi nell'attributo [Cloud_RoleName](./app-map.md?tabs=net#set-cloud-role-name). Senza valori significativi impostati per questo attributo, *NESSUNA* delle esperienze del portale funzionerà.
- Per le applicazioni Service Fabric e i servizi cloud classici, l'SDK legge automaticamente l'ambiente dei ruoli di Azure e procede all'impostazione. Per tutti gli altri tipi di app sarà probabilmente necessario definire queste impostazioni in modo esplicito.
-   L'esperienza Metriche attive non supporta la suddivisione in base al nome del ruolo.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chiave di strumentazione dinamica

Per rendere più semplice la modifica del valore ikey man mano che il codice attraversa le fasi di produzione, impostarlo nel codice anziché nel file di configurazione.

Impostare la chiave in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

In questo esempio i valori ikey per le diverse risorse vengono inseriti in versioni diverse del file di configurazione Web. Lo scambio del file di configurazione Web, che è possibile eseguire nell'ambito dello script di rilascio, consente di scambiare la risorsa di destinazione.

### <a name="web-pages"></a>Pagina Web
Il valore iKey viene usato anche nelle pagine Web dell'app, nello [script ottenuto dal riquadro Avvio rapido](../../azure-monitor/app/javascript.md). Invece di codificarlo letteralmente nello script, generarlo dallo stato del server. Ad esempio, in un'app ASP.NET:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Creare risorse di Application Insights aggiuntive

Per creare una risorsa di Application Insights, seguire la [guida alla creazione di risorse](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>Ottenere la chiave di strumentazione
La chiave di strumentazione identifica la risorsa creata.

Sono necessarie le chiavi di strumentazione di tutte le risorse a cui l'app invierà dati.

## <a name="filter-on-build-number"></a>Filtrare in base al numero di build
Quando si pubblica una nuova versione dell'app, potrebbe essere opportuno separare i dati telemetrici delle diverse build.

È possibile impostare la proprietà della versione dell'applicazione in modo che sia possibile filtrare i risultati della [ricerca](../../azure-monitor/app/diagnostic-search.md) e di [Esplora metriche](../../azure-monitor/platform/metrics-charts.md).

Esistono diversi metodi di impostazione della proprietà della versione dell'applicazione.

* Impostare direttamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Eseguire il wrapping di tale riga in un [inizializzatore di telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) per assicurarsi che tutte le istanze di TelemetryClient siano impostate in modo coerente.
* [ASP.NET] Impostare la versione `BuildInfo.config`. Il modulo Web selezionerà la versione dal nodo BuildLabel. Includere questo file nel progetto e ricordarsi di impostare la proprietà Copia sempre in Esplora soluzioni.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generare automaticamente BuildInfo.config in MSBuild. A tale scopo, aggiungere alcune righe al file `.csproj`:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Viene generato un file denominato *NomeProgetto*.BuildInfo.config. Il processo di pubblicazione rinomina il file in BuildInfo.config.

    L'etichetta di compilazione contiene un segnaposto (AutoGen_) se la compilazione viene eseguita in Visual Studio. Se la compilazione viene eseguita con MSBuild, viene inserito il numero di versione corretto.

    Per consentire a MSBuild di generare i numeri di versione, in AssemblyReference.cs impostare la versione come, ad esempio, `1.0.*` .

## <a name="version-and-release-tracking"></a>Verifica della versione
Per tenere traccia della versione dell'applicazione, assicurarsi che il processo di Microsoft Build Engine generi `buildinfo.config`. Nel file `.csproj` aggiungere:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Quando ha le informazioni di compilazione, il modulo Web di Application Insights aggiunge automaticamente la **versione dell'applicazione** come proprietà a ogni elemento dei dati di telemetria. Questo consente di applicare filtri in base alla versione quando si eseguono [ricerche diagnostiche](../../azure-monitor/app/diagnostic-search.md) o quando si [esaminano le metriche](../../azure-monitor/platform/metrics-charts.md).

Si noti tuttavia che il numero di versione della build viene generato solo da Microsoft Build Engine, non dalla build dello sviluppatore di Visual Studio.

### <a name="release-annotations"></a>Annotazioni sulle versioni
Se si usa Azure DevOps, è possibile [visualizzare un marcatore di annotazione](../../azure-monitor/app/annotations.md) aggiunto ai grafici quando si rilascia una nuova versione. 

## <a name="next-steps"></a>Passaggi successivi

* [Risorse condivise da più ruoli](../../azure-monitor/app/app-map.md)
* [Creare un inizializzatore di telemetria per distinguere le varianti A|B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
