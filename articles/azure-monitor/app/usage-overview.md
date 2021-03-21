---
title: Analisi dell'utilizzo con Azure Application Insights | Documentazione Microsoft
description: Informazioni sugli utenti e le operazioni eseguite con l'app.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489039"
---
# <a name="usage-analysis-with-application-insights"></a>Uso dell'analisi con Application Insights

Quali sono le funzionalità dell'app Web o dell'app per dispositivi mobili più comuni? Gli utenti raggiungono i propri obiettivi con l'app? Escono in particolari punti e riaccedono in un secondo momento?  [Application Insights di Azure](./app-insights-overview.md) consente di ottenere importanti informazioni approfondite sull'uso dell'app da parte degli utenti. Ogni volta che si aggiorna l'app, è possibile valutarne il funzionamento per gli utenti. Con queste informazioni è possibile prendere decisioni in base ai dati sui cicli di sviluppo successivi.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Inviare dati di telemetria dall'app

La migliore esperienza viene ottenuta tramite l'installazione di Application Insights nel codice server dell'app e nelle pagine Web. I componenti client e server dell'app inviano dati di telemetria al portale di Azure per l'analisi.

1. **Codice server:** installare il modulo appropriato per l'app [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md) o per [altre](./platforms.md) app.

    * *Non si vuole installare il codice server? [Creare una risorsa di Azure Application Insights](./create-new-resource.md).*

2. **Codice della pagina Web:** aggiungere lo script seguente nella pagina Web prima del tag ``</head>`` di chiusura. Sostituire la chiave di strumentazione con il valore appropriato della risorsa di Application Insights:
    
    Il frammento corrente (elencato di seguito) è la versione "5", la versione è codificata nel frammento come SV: "#" e la [versione corrente è disponibile anche in GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Per altre informazioni sulle configurazioni più avanzate per il monitoraggio di siti Web, consultare l'[articolo di riferimento su JavaScript SDK](./javascript.md).

3. **Codice app per dispositivi mobili:** usare App Center SDK per raccogliere gli eventi dall'app, quindi inviare copie di questi eventi ad Application Insights per l'analisi [seguendo le istruzioni in questa guida](../app/mobile-center-quickstart.md).

4. **Ottenere i dati di telemetria:** eseguire il progetto in modalità di debug per alcuni minuti e quindi cercare i risultati nel pannello Panoramica in Application Insights.

    Pubblicare l'app per monitorare le prestazioni dell'app ed esaminare le operazioni eseguite dagli utenti con l'app.

## <a name="explore-usage-demographics-and-statistics"></a>Esplorare le statistiche e i dati demografici di uso
Scoprire quando le persone usano l'app, a quali pagine sono più interessati, dove si trovano, quali browser e sistemi operativi usano. 

I report sugli utenti e le sessioni filtrano i dati in base alle pagine o agli eventi personalizzati e li segmentano in base a proprietà quali posizione, ambiente e pagina. È anche possibile aggiungere filtri personalizzati.

![L'acquisizione schermo mostra la pagina Panoramica utenti per una società fittizia.](./media/usage-overview/users.png)  

Informazioni approfondite sui modelli di segnalazione corretti nel set di dati.  

* Il report sugli **utenti** conta il numero di utenti univoci che accedono alle pagine nei periodi di tempo scelti. Per le app Web gli utenti vengono conteggiati usando i cookie. Se un utente accede al sito con browser o computer client diversi o cancella i cookie, verrà conteggiato più volte.
* Il report sulle **sessioni** conteggia il numero di sessioni dell'utente che accedono al sito. Una sessione è un periodo di attività dell'utente, che termina quando si verifica un periodo di inattività di più di mezz'ora.

[Altre informazioni sugli strumenti di Utenti, Sessioni ed Eventi](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Conservazione: numero di utenti che ritornano

La conservazione consente di comprendere la frequenza con cui gli utenti tornano a usare l'app, in base alle coorti di utenti che hanno eseguito un'azione aziendale in un determinato intervallo di tempo. 

- Comprendere le funzionalità specifiche per cui alcuni utenti ritornano di più rispetto ad altri 
- Fare ipotesi in base a dati utente reali 
- Determinare se la conservazione è un problema per il prodotto 

![L'acquisizione schermo mostra la pagina Panoramica conservazione che visualizza informazioni sulla frequenza con cui gli utenti tornano a usare l'app.](./media/usage-overview/retention.png) 

I controlli di conservazione nella parte superiore consentono di definire l'intervallo di tempo e gli eventi specifici per il calcolo della conservazione. Il grafico nella parte centrale offre una rappresentazione visiva della percentuale della conservazione generale in base all'intervallo di tempo specificato. Il grafico nella parte inferiore rappresenta la singola conservazione in un determinato periodo di tempo. Questo livello di dettagli consente di capire le operazioni eseguite dagli utenti e le possibili motivazioni per cui un utente sceglie di ritornare con una granularità più dettagliata.  

[Altre informazioni sullo strumento di conservazione](usage-retention.md)

## <a name="custom-business-events"></a>Eventi aziendali personalizzati

Per ottenere una descrizione chiara delle operazioni che gli utenti eseguono con l'app, è utile inserire righe di codice per registrare eventi personalizzati. Questi eventi possono tenere traccia di qualsiasi operazione, da azioni dell'utente dettagliate, ad esempio fare clic su pulsanti specifici, agli eventi aziendali più importanti, ad esempio effettuare un acquisto o vincere a un gioco.

È anche possibile usare il [plug-](javascript-click-analytics-plugin.md) in per la raccolta automatica di Analytics per raccogliere eventi personalizzati.

Sebbene in alcuni casi, le visualizzazioni della pagina possano rappresentare eventi utili, questo non è vero in generale. Un utente può aprire la pagina di un prodotto senza acquistarlo. 

Con eventi aziendali specifici, è possibile rappresentare in un grafico lo stato di avanzamento degli utenti all'interno del sito. È possibile scoprirne le preferenze per le diverse opzioni e i punti in cui abbandonano il sito o hanno difficoltà. Con queste informazioni si possano prendere decisioni consapevoli sulle priorità nel backlog di sviluppo.

Gli eventi possono essere registrati dal lato client dell'app:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

O dal lato server:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

È possibile collegare i valori delle proprietà a questi eventi, in modo da poter filtrare o suddividere gli eventi quando li si ispeziona nel portale. A ogni evento viene anche associato un set standard di proprietà, ad esempio un'ID utente anonimo, che consente di analizzare la sequenza delle attività di un singolo utente.

Altre informazioni sugli [eventi personalizzati](./api-custom-events-metrics.md#trackevent) e le [proprietà](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Analisi approfondita degli eventi

Negli strumenti Utenti, Sessioni ed Eventi, è possibile eseguire un'analisi approfondita degli eventi personalizzati per utente, nome dell'evento e proprietà.
![L'acquisizione schermo mostra la pagina Panoramica utenti per una società fittizia.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Progettare i dati di telemetria con l'app

Quando si progettano le funzionalità dell'app, prendere in considerazione le modalità in cui si desidera misurarne il successo con gli utenti. Decidere quali eventi aziendali è necessario registrare e codificare il monitoraggio delle chiamate per questi eventi nell'app sin dall'inizio.

## <a name="a--b-testing"></a>Test A | B
Se non si sa quale variante di una funzionalità sarà più efficace, rilasciarle entrambe, rendendo ognuna accessibile a utenti diversi. Valutare la riuscita di ognuna e quindi passare a una versione unificata.

Per questa tecnica è possibile collegare valori per le proprietà differenti per tutti i dati di telemetria inviati da ogni versione dell'app. A questo scopo, definire le proprietà nel TelemetryContext attivo. Queste proprietà predefinite vengono aggiunte a ogni messaggio di telemetria inviato dall'applicazione, non solo ai messaggi personalizzati, ma anche ai dati di telemetria standard.

Nel portale Application Insights è possibile filtrare e dividere i dati sui valori delle proprietà, in modo da confrontare versioni diverse.

A tale scopo, [configurare un inizializzatore di telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**App ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Nell'inizializzatore dell'app Web, ad esempio Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**App ASP.NET Core**

> [!NOTE]
> L'aggiunta di un inizializzatore con `ApplicationInsights.config` o `TelemetryConfiguration.Active` non è valida per le applicazioni ASP.NET Core. 

Per le applicazioni [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), l'aggiunta di un nuovo `TelemetryInitializer` viene eseguita aggiungendolo al contenitore di inserimento delle dipendenze, come illustrato di seguito. Questa operazione viene eseguita nel metodo `ConfigureServices` della classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Tutti i nuovi TelemetryClients aggiungono automaticamente il valore di proprietà specificato. I singoli eventi di telemetria possono eseguire la sostituzione dei valori predefiniti.

## <a name="next-steps"></a>Passaggi successivi
   - [Utenti, sessioni ed eventi](usage-segmentation.md)
   - [Grafici a imbuto](usage-funnels.md)
   - [Conservazione](usage-retention.md)
   - [Flussi degli utenti](usage-flows.md)
   - [Cartelle di lavoro](../visualize/workbooks-overview.md)
