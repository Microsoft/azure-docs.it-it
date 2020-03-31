---
title: 'Guida introduttiva: Monitorare siti Web con Application Insights di Monitoraggio di Azure'
description: Istruzioni di avvio rapido per configurare il monitoraggio di siti Web sul lato client/browser con Application Insights di Monitoraggio di Azure
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 495c40ca8e383dd5a3cf3ba9e5bd42e2936ea015
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132358"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Guida introduttiva: Avviare il monitoraggio del sito Web con Application Insights di Monitoraggio di Azure

Questo argomento di avvio rapido illustra come aggiungere JavaScript SDK per Application Insights open source al sito Web. Descrive inoltre in maggior dettaglio l'esperienza lato client/browser per i visitatori del sito Web.

Con Application Insights di Monitoraggio di Azure è possibile monitorare facilmente la disponibilità, le prestazioni e l'utilizzo di un sito Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente. Application Insights offre funzionalità di monitoraggio sia lato server che lato client/browser.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un sito Web a cui è possibile aggiungere JavaScript SDK per Application Insights.

## <a name="enable-application-insights"></a>Abilitare Application Insights

Application Insights può raccogliere dati di telemetria da qualsiasi applicazione connessa a Internet, in esecuzione in locale o nel cloud. Usare la procedura seguente per iniziare a visualizzare questi dati.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa** > **Strumenti di gestione** > **Application Insights**.

   > [!NOTE]
   >Se è la prima volta che si crea una risorsa di Application Insights, vedere l'articolo [Creare una risorsa di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) per altre informazioni.

   Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Impostazioni        | valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse in cui ospitare i dati di Application Insights. è possibile creare un nuovo gruppo di risorse o selezionarne uno esistente. |
   | **Posizione** | Stati Uniti orientali | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

3. Fare clic su **Crea**.

## <a name="create-an-html-file"></a>Creare un file HTML

1. Nel computer locale creare un file denominato ``hello_world.html``. In questo esempio il file verrà inserito nella radice dell'unità C: in ``C:\hello_world.html``.
2. Copiare lo script seguente in ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configura Application Insights SDK

1. Selezionare **Panoramica** > **Informazioni di base** > copiare la **chiave di strumentazione** dell'applicazione.

   ![Modulo per la nuova risorsa di Application Insights](media/website-monitoring/instrumentation-key-001.png)

2. Aggiungere lo script seguente al file ``hello_world.html`` prima del tag ``</head>`` di chiusura:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Modificare il file ``hello_world.html`` e aggiungere la chiave di strumentazione.

4. Aprire ``hello_world.html`` in una sessione del browser locale. Questa azione crea una singola visualizzazione di pagina. È possibile aggiornare il browser per generare più visualizzazioni pagina di test.

## <a name="start-monitoring-in-the-azure-portal"></a>Avviare il monitoraggio nel portale di Azure

1. È ora possibile riaprire la pagina **Panoramica** di Application Insights nel portale di Azure per visualizzare i dettagli relativi all'applicazione attualmente in esecuzione. La pagina **Panoramica** è la posizione in cui è stata recuperata la chiave di strumentazione. I quattro grafici predefiniti nella pagina di panoramica hanno come ambiti i dati dell'applicazione lato server. Dato che instrumentano le interazioni lato client/browser con JavaScript SDK, questa specifica visualizzazione non si applica a meno che non è installato anche un SDK lato server.

2. Fare clic su ![Icona in Mappa delle applicazioni](media/website-monitoring/006.png) **Analisi**.  Questa azione apre la finestra **Analisi**, che fornisce un linguaggio di query avanzato per l'analisi di tutti i dati raccolti da Application Insights. Per visualizzare i dati relativi alle richieste del browser sul lato client, eseguire la query seguente:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Grafico di analisi delle richieste di un utente in un periodo di tempo](./media/website-monitoring/analytics-query.png)

3. Tornare alla pagina **Panoramica**. Fare clic su **Browser** nell'intestazione **Analisi** e quindi selezionare **Prestazioni**. In questa sezione sono disponibili le metriche correlate alle prestazioni del sito Web. È anche disponibile una visualizzazione corrispondente per l'analisi degli errori e delle eccezioni nel sito Web. È possibile fare clic su **Esempi** per esaminare i dettagli di singole transazioni. Da qui è possibile accedere all'esperienza [Dettagli sulle transazioni end-to-end](../../azure-monitor/app/transaction-diagnostics.md).

   ![Grafico delle metriche del server](./media/website-monitoring/browser-performance.png)

4. Per iniziare l'esplorazione degli [strumenti di analisi dei comportamenti utente](../../azure-monitor/app/usage-overview.md), dal menu principale di Application Insights scegliere [**Utenti**](../../azure-monitor/app/usage-segmentation.md) sotto l'intestazione **Utilizzo**. Poiché i test vengono eseguiti da un unico computer, si vedranno i dati per un singolo utente. Per un sito Web reale, la distribuzione degli utenti potrebbe essere simile alla seguente:

     ![Grafico degli utenti](./media/website-monitoring/usage-users.png)

5. Per l'instrumentazione di un sito Web più complesso con più pagine, un altro strumento utile sarebbe [**Flussi utente**](../../azure-monitor/app/usage-flows.md). Con **Flussi utente** è possibile tenere traccia del percorso scelto dai visitatori attraverso le varie parti del sito Web.

   ![Visualizzazione di Flussi utente](./media/website-monitoring/user-flows.png)

Per altre informazioni sulle configurazioni più avanzate per il monitoraggio di siti Web, vedere le [informazioni di riferimento sull'API di JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare altri argomenti di avvio rapido o le esercitazioni, non pulire le risorse create in questa guida. In caso contrario, se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create con questo argomento di avvio rapido nel portale di Azure.

> [!NOTE]
> Se si è usato un gruppo di risorse esistente, le istruzioni riportate di seguito non sono applicabili e sarà sufficiente eliminare la singola risorsa di Application Insights. Tenere presente che ogni volta che si elimina un gruppo di risorse vengono eliminate tutte le risorse sottostanti appartenenti a tale gruppo.

1. Fare clic su **Gruppi di risorse** nel menu a sinistra nel portale di Azure e quindi su **myResourceGroup** oppure sul nome di un proprio gruppo di risorse temporaneo.
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **myResourceGroup** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Rilevare e diagnosticare i problemi di prestazioni](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
