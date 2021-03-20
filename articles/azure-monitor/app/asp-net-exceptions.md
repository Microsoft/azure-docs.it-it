---
title: Diagnosticare errori ed eccezioni con applicazione Azure Insights
description: Acquisire le eccezioni da app ASP.NET insieme ai dati di telemetria della richiesta.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936504"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticare eccezioni nelle app Web con Application Insights
Le eccezioni nell'applicazione Web attiva vengono segnalate da [Application Insights](./app-insights-overview.md). È possibile correlare le richieste non riuscite con le eccezioni e altri eventi nel client e nel server, in modo da poter diagnosticare rapidamente le cause.

## <a name="set-up-exception-reporting"></a>Configurare la creazione di report sulle eccezioni
* Per segnalare le eccezioni dall'app del server:
  * App Web di Azure: aggiungere l'[estensione di Application Insights](./azure-web-apps.md)
  * VM di Azure e set di scalabilità di macchine virtuali di Azure-App ospitate in IIS: aggiungere l' [estensione di monitoraggio delle applicazioni](./azure-vm-vmss-apps.md)
  * Installare [Application Insights SDK](./asp-net.md) nel codice dell'app, oppure
  * Server Web IIS: eseguire l'[Agente di Application Insights](./monitor-performance-live-website-now.md); o
  * App Web Java: Abilita l' [agente Java](./java-in-process-agent.md)
* Installare il [frammento di JavaScript](./javascript.md) nelle pagine Web per intercettare le eccezioni del browser.
* In certi framework applicazione o con alcune impostazioni è necessario eseguire alcuni passaggi aggiuntivi per intercettare più eccezioni:
  * [Web Form](#web-forms)
  * [MVC](#mvc)
  * [API Web 1.*](#web-api-1x)
  * [API Web 2.*](#web-api-2x)
  * [WCF](#wcf)

  Questo articolo è incentrato specificamente sulle app .NET Framework dal punto di vista di un esempio di codice. Alcuni dei metodi che funzionano per .NET Framework sono obsoleti nel .NET Core SDK. Vedere la [documentazione di .NET Core SDK](./asp-net-core.md) se si dispone di un'app .NET Core.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticare le eccezioni con Visual Studio
Aprire la soluzione dell'app in Visual Studio per facilitare il debug.

Eseguire l'app nel server o nel computer di sviluppo usando F5.

Aprire la finestra di ricerca di Application Insights in Visual Studio e impostare la visualizzazione degli eventi dall'app. A questo scopo, durante il debug è sufficiente fare clic sul pulsante Application Insights.

![Fare clic con il pulsante destro del mouse sul progetto e scegliere Application Insights, Apri.](./media/asp-net-exceptions/34.png)

Si noti che è possibile filtrare il report per visualizzare solo le eccezioni.

*Non vengono visualizzate eccezioni? Vedere [acquisire eccezioni](#exceptions).*

Fare clic su un report di eccezione per visualizzarne l'analisi dello stack.
Fare clic su un riferimento di riga nell'analisi dello stack per aprire il relativo file.

Nel codice, si noti che CodeLens mostra i dati relativi alle eccezioni:

![Notifica CodeLens di eccezioni.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosi degli errori con il portale di Azure
Application Insights viene fornito con un'esperienza APM (Application Performance Monitoring) dedicata per semplificare la diagnosi degli errori nelle applicazioni monitorate. Per iniziare, scegliere l'opzione Failures (Errori) dal menu delle risorse di Application Insights, che si trova nella sezione Investigate (Analisi dei problemi).
Apparirà una visualizzazione a schermo intero che mostra le tendenze in termini di frequenza degli errori per le richieste, indicando quante non sono riuscite e quanti sono gli utenti interessati. A destra verranno visualizzate alcune delle distribuzioni più utili specifiche per l'operazione non riuscita selezionata, tra cui i primi tre codici di risposta, i primi tre tipi di eccezione e i primi tre tipi di dipendenza non riusciti.

![Visualizzazione di valutazione degli errori (scheda delle operazioni)](./media/asp-net-exceptions/failures0719.png)

Con un solo clic è possibile esaminare i campioni rappresentativi per ognuno di questi subset di operazioni. In particolare, per diagnosticare le eccezioni, è possibile fare clic sul numero di una particolare eccezione da presentare con la scheda Dettagli transazione end-to-end, ad esempio:

![Scheda dettagli transazione end-to-end](./media/asp-net-exceptions/end-to-end.png)

**In alternativa** , anziché esaminare le eccezioni di un'operazione di errore specifica, è possibile iniziare dalla visualizzazione complessiva delle eccezioni passando alla scheda eccezioni nella parte superiore. Qui è possibile visualizzare tutte le eccezioni raccolte per l'app monitorata.

*Non vengono visualizzate eccezioni? Vedere [acquisire eccezioni](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Dati di traccia e di log personalizzati
Per ottenere dati di diagnostica specifici per l'app, è possibile inserire codice per l'invio di dati di telemetria personalizzati. Visualizzato in ricerca diagnostica insieme alla richiesta, alla visualizzazione pagina e ad altri dati raccolti automaticamente.

Sono disponibili diverse opzioni:

* [TrackEvent()](./api-custom-events-metrics.md#trackevent) viene usato in genere per il monitoraggio dei modelli di utilizzo, ma i dati inviati vengono visualizzati anche in Eventi personalizzati nella ricerca diagnostica. Gli eventi sono denominati e possono includere proprietà di stringa e metriche numeriche, in base alle quali è possibile [filtrare le ricerche diagnostiche](./diagnostic-search.md).
* [TrackTrace()](./api-custom-events-metrics.md#tracktrace) permette di inviare dati più lunghi, ad esempio informazioni POST.
* [TrackException()](#exceptions) invia analisi dello stack. [Altre informazioni sulle eccezioni](#exceptions).
* Se si usa già un framework di registrazione come Log4Net o NLog, è possibile [acquisire tali log](asp-net-trace-logs.md) e visualizzarli nella ricerca diagnostica insieme ai dati relativi alle richieste e alle eccezioni.

Per visualizzare questi eventi, aprire [Cerca](./diagnostic-search.md) dal menu a sinistra, selezionare i **tipi di evento** del menu a discesa, quindi scegliere evento personalizzato, traccia o eccezione.

![Drill through](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Se l’app genera molti dati di telemetria, il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Gli eventi che fanno parte della stessa operazione verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. [Informazioni sul campionamento.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Come visualizzare i dati POST di una richiesta
I dettagli della richiesta non includono i dati inviati all'app in una chiamata POST. Per poter ottenere questi dati:

* [Installare l'SDK](./asp-net.md) nel progetto dell'applicazione.
* Inserire il codice nell'applicazione per chiamare [Microsoft.ApplicationInsights.TrackTrace()](./api-custom-events-metrics.md#tracktrace). Inviare i dati POST nel parametro del messaggio. Esiste un limite per le dimensioni consentite, quindi è consigliabile provare a inviare solo i dati essenziali.
* Quando si esamina una richiesta non riuscita, trovare le tracce associate.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Acquisizione delle eccezioni e dei relativi dati di diagnostica
Inizialmente, nel portale non verranno visualizzate tutte le eccezioni che causano errori nell'app. Verranno visualizzate tutte le eccezioni del browser (se si usa [JavaScript SDK](./javascript.md) nelle pagine Web). La maggior parte delle eccezioni del server viene rilevata da IIS, ma è necessario scrivere qualche riga di codice per visualizzarle.

È possibile:

* **Registrare le eccezioni in modo esplicito** inserendo il codice nei gestori di eccezioni per segnalare le eccezioni.
* **Acquisire automaticamente le eccezioni** configurando il framework di ASP.NET. Gli elementi da aggiungere variano a seconda dei diversi tipi di framework.

## <a name="reporting-exceptions-explicitly"></a>Segnalazione di eccezioni in modo esplicito
Il modo più semplice consiste nell'inserire una chiamata a TrackException() in un gestore di eccezioni.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

I parametri delle proprietà e delle misurazioni sono facoltativi, ma sono utili per [filtrare e aggiungere](./diagnostic-search.md) altre informazioni. Ad esempio, per un'app in grado di eseguire diversi giochi, è possibile trovare tutti i report di eccezione correlati a un gioco specifico. È possibile aggiungere qualsiasi numero di elementi a ogni dizionario.

## <a name="browser-exceptions"></a>Eccezioni del browser
Viene segnalata la maggior parte delle eccezioni del browser.

Se la pagina Web include file di script provenienti da reti per la distribuzione di contenuti o da altri domini, verificare che il tag dello script contenga l'attributo ```crossorigin="anonymous"``` e che il server invii [intestazioni CORS](https://enable-cors.org/). Ciò consentirà di ottenere un'analisi dello stack e i dettagli per le eccezioni JavaScript non gestite da queste risorse.

## <a name="reuse-your-telemetry-client"></a>Riutilizzare il client di telemetria

> [!NOTE]
> È consigliabile creare un'istanza di TelemetryClient una sola volta e riutilizzarlo per tutta la durata di un'applicazione.

Di seguito è riportato un esempio di utilizzo corretto di TelemetryClient.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Web Form
Per Web Form, il modulo HTTP potrà raccogliere le eccezioni quando non sono configurati reindirizzamenti con CustomErrors.

Se però sono presenti reindirizzamenti attivi, aggiungere le righe seguenti alla funzione Application_Error in Global.asax.cs. Aggiungere un file Global.asax se non ne esiste già uno.

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
A partire da Application Insights Web SDK versione 2.6 (beta 3 e versioni successive), Application Insights raccoglie le eccezioni non gestite generate automaticamente nei metodi dei controller MVC 5+. Se in precedenza è stato aggiunto un gestore personalizzato per tenere traccia di tali eccezioni (come descritto negli esempi seguenti), è possibile rimuoverlo per evitare il doppio rilevamento delle eccezioni.

Alcuni casi non possono essere gestiti dai filtri eccezioni. Ad esempio:

* Eccezioni generate dai costruttori dei controller.
* Eccezioni generate dai gestori di messaggi.
* Eccezioni generate durante il routing.
* Eccezioni generate durante la serializzazione del contenuto della risposta.
* Eccezione generata durante l'avvio dell'applicazione.
* Eccezione generata in attività in background.

È ancora necessario tenere traccia manualmente di tutte le eccezioni *gestite* dall'applicazione.
Le eccezioni non gestite provenienti da controller restituiscono in genera la risposta 500 "Errore interno del server". Se tale risposta viene costruita manualmente in seguito a un'eccezione gestita (o in assenza di eccezioni) viene registrata nei dati di telemetria della richiesta corrispondenti con `ResultCode` 500. Application Insights SDK non è tuttavia in grado di tenere traccia dell'eccezione corrispondente.

### <a name="prior-versions-support"></a>Supporto nelle versioni precedenti
Se si usa MVC 4 (e versioni precedenti) di Application Insights Web SDK 2.5 (e versioni precedenti), vedere gli esempi seguenti per tenere traccia delle eccezioni.

Se la configurazione di [CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) è `Off`, le eccezioni potranno essere raccolte dal [modulo HTTP](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)). Se tuttavia è `RemoteOnly` (impostazione predefinita) o `On`, l'eccezione verrà cancellata e non potrà essere raccolta automaticamente da Application Insights. È possibile risolvere questo problema eseguendo l'override della [classe System.Web.Mvc.HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2) e applicando la classe di cui è stato eseguito l'override, come illustrato per le diverse versioni MVC riportate di seguito ([origine github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Sostituire l'attributo HandleError con il nuovo attributo nei controller.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Esempio](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrare `AiHandleErrorAttribute` come filtro globale in Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Esempio](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registrare AiHandleErrorAttribute come filtro globale in FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Esempio](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>API Web
A partire da Application Insights Web SDK versione 2.6 (beta 3 e versioni successive), Application Insights raccoglie le eccezioni non gestite generate automaticamente nei metodi dei controller per WebAPI 2+. Se in precedenza è stato aggiunto un gestore personalizzato per tenere traccia di tali eccezioni (come descritto negli esempi seguenti), è possibile rimuoverlo per evitare il doppio rilevamento delle eccezioni.

Alcuni casi non possono essere gestiti dai filtri eccezioni. Ad esempio:

* Eccezioni generate dai costruttori dei controller.
* Eccezioni generate dai gestori di messaggi.
* Eccezioni generate durante il routing.
* Eccezioni generate durante la serializzazione del contenuto della risposta.
* Eccezione generata durante l'avvio dell'applicazione.
* Eccezione generata in attività in background.

È ancora necessario tenere traccia manualmente di tutte le eccezioni *gestite* dall'applicazione.
Le eccezioni non gestite provenienti da controller restituiscono in genera la risposta 500 "Errore interno del server". Se tale risposta viene costruita manualmente in seguito a un'eccezione gestita (o in assenza di eccezioni) viene registrata nei dati di telemetria della richiesta corrispondenti con `ResultCode` 500. Application Insights SDK non è tuttavia in grado di tenere traccia dell'eccezione corrispondente.

### <a name="prior-versions-support"></a>Supporto nelle versioni precedenti
Se si usa WebAPI 1 (e versioni precedenti) di Application Insights Web SDK 2.5 (e versioni precedenti), vedere gli esempi seguenti per tenere traccia delle eccezioni.

#### <a name="web-api-1x"></a>API Web 1.x
Eseguire l'override di System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Sarà possibile aggiungere questo attributo di cui è stato eseguito l'override ai controller specifici o alla configurazione del filtro globale nella classe WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Esempio](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>API Web 2.x
Aggiungere un'implementazione di IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Aggiungere il codice seguente ai servizi in WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Esempio](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

In alternativa, è possibile:

1. Sostituire ExceptionHandler con un'implementazione personalizzata di IExceptionHandler. Questo elemento viene chiamato solo quando il framework può ancora scegliere il messaggio di risposta da inviare, non quando la connessione viene interrotta.
2. Come descritto nella sezione relativa ai controller Web API 1.x precedente, i filtri eccezioni non vengono chiamati in tutti i casi.

## <a name="wcf"></a>WCF
Aggiungere una classe che estenda Attribute e implementi IErrorHandler e IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Esempio](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contatori delle prestazioni delle eccezioni
L'installazione [dell'Agente di Application Insights](./monitor-performance-live-website-now.md) nel server permette di ottenere un grafico della frequenza delle eccezioni, misurata da .NET. Il grafico include le eccezioni .NET gestite e non gestite.

Aprire una scheda Esplora metriche, aggiungere un nuovo grafico e selezionare **frequenza eccezioni**, elencata in contatori delle prestazioni.

.NET framework calcola la frequenza contando il numero delle eccezioni in un intervallo e dividendolo per la lunghezza dell'intervallo.

Questo conteggio è diverso dal conteggio delle eccezioni calcolato dal portale di Application Insights, che conteggia i report TrackException. Gli intervalli di campionamento sono diversi e il SDK non invia report di TrackException per tutte le eccezioni gestite e non gestite.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare REST, SQL e altre chiamate alle dipendenze](./asp-net-dependencies.md)
* [Monitorare i tempi di caricamento delle pagina, le eccezioni del browser e le chiamate AJAX](./javascript.md)
* [Monitorare i contatori delle prestazioni](./performance-counters.md)

