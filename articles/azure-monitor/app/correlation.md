---
title: Correlazione di dati di Azure Application Insights Telemetry | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 20e9ed7e83ff3359651acebc11a939a998f2889d
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607916"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. È possibile monitorare separatamente ognuno di questi componenti utilizzando [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights supporta la correlazione dei dati di telemetria distribuita, usata per rilevare i componenti responsabili degli errori o della riduzione del livello delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Viene inoltre illustrata l'implementazione di tattiche di correlazione su linguaggi e piattaforme diversi.

## <a name="data-model-for-telemetry-correlation"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](../../azure-monitor/app/data-model.md) per la correlazione di dati di telemetria distribuita. Per associare i dati di telemetria a un'operazione logica, ogni elemento di telemetria ha un campo di contesto denominato `operation_Id` . Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Quindi, anche se si perdono i dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita è in genere costituita da un set di operazioni più piccole che sono richieste elaborate da uno dei componenti. Queste operazioni sono definite dalla [telemetria delle richieste](../../azure-monitor/app/data-model-request-telemetry.md). Ogni elemento di telemetria delle richieste ha una propria proprietà `id` che lo identifica in modo univoco e globale. Tutti gli elementi di telemetria (ad esempio tracce ed eccezioni) associati alla richiesta devono impostare sul `operation_parentId` valore della richiesta `id` .

Ogni operazione in uscita, ad esempio una chiamata HTTP a un altro componente, è rappresentata dalla [telemetria delle dipendenze](../../azure-monitor/app/data-model-dependency-telemetry.md). La telemetria delle dipendenze definisce anche un oggetto `id` univoco globale. La telemetria delle richieste, avviata dalla chiamata di dipendenza, usa questo `id` come `operation_parentId`.

È possibile creare una visualizzazione dell'operazione logica distribuita usando `operation_Id`, `operation_parentId` e `request.id` con `dependency.id`. Questi campi definiscono anche l'ordine della causalità delle chiamate di telemetria.

In un ambiente di microservizi, le tracce dai componenti possono finire in elementi di archiviazione diversi. Ogni componente può avere la propria chiave di strumentazione in Application Insights. Per ottenere la telemetria per l'operazione logica, Application Insights esegue una query dei dati da ogni elemento di archiviazione. Quando il numero di elementi di archiviazione è elevato, è necessario un suggerimento su dove eseguire la ricerca successiva. Per risolvere questo problema, nel modello di dati di Application Insights sono definiti due campi: `request.source` e `dependency.target`. Il primo campo identifica il componente che ha avviato la richiesta di dipendenza. Il secondo campo identifica il componente che ha restituito la risposta della chiamata di dipendenza.

## <a name="example"></a>Esempio

Esaminiamo un esempio. Un'applicazione denominata prezzi azionari Mostra il prezzo di mercato corrente di un'azione usando un'API esterna denominata Stock. L'applicazione dei prezzi azionari include una pagina denominata pagina delle azioni che il browser Web del client si apre usando `GET /Home/Stock` . L'applicazione esegue una query sull'API azionario usando la chiamata HTTP `GET /api/stock/value` .

È possibile analizzare i dati di telemetria risultanti eseguendo una query:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nei risultati tutti gli elementi di telemetria condividono l'elemento `operation_Id` radice. Quando viene eseguita una chiamata AJAX dalla pagina, viene assegnato un nuovo ID univoco ( `qJSXU` ) ai dati di telemetria delle dipendenze e l'ID della pagina di visualizzazione viene utilizzato come `operation_ParentId` . La richiesta server usa quindi l'ID Ajax come `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando `GET /api/stock/value` viene effettuata la chiamata a un servizio esterno, è necessario conoscerne l'identità per poter impostare il campo in modo `dependency.target` appropriato. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio (ad esempio, `stock-prices-api.com`). Tuttavia, se il servizio viene identificato restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente Application Insights di compilare una traccia distribuita eseguendo una query sui dati di telemetria di tale servizio.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Intestazioni di correlazione con W3C TraceContext

Application Insights viene eseguita la transizione a [W3C Trace-context](https://w3c.github.io/trace-context/), che definisce:

- `traceparent`: Contiene l'ID operazione univoco globale e l'identificatore univoco della chiamata.
- `tracestate`: Contiene il contesto di traccia specifico del sistema.

La versione più recente di Application Insights SDK supporta il protocollo Trace-Context, ma potrebbe essere necessario acconsentire esplicitamente. La compatibilità con il protocollo di correlazione precedente supportato da Application Insights SDK verrà mantenuta.

Il [protocollo http di correlazione, denominato anche Request-ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), verrà deprecato. Questo protocollo definisce due intestazioni:

- `Request-Id`: Contiene l'ID univoco globale della chiamata.
- `Correlation-Context`: Contiene la raccolta di coppie nome-valore delle proprietà della traccia distribuita.

Application Insights definisce anche l' [estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo http di correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. L'SDK Application Insights usa questa intestazione per impostare i `dependency.target` `request.source` campi e.

I modelli di dati del [contesto di traccia W3C](https://w3c.github.io/trace-context/) e Application Insights vengono mappati nel modo seguente:

| Application Insights                   | TraceContext per W3C                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` di `Request` e `Dependency`     | [ID padre](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [Trace-ID](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [ID padre](https://w3c.github.io/trace-context/#parent-id) dell'intervallo padre di questo intervallo. Se si tratta di un intervallo radice, questo campo deve essere vuoto.     |

Per altre informazioni, vedere [Application Insights modello di dati di telemetria](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Abilitare il supporto per la traccia distribuita W3C per le app .NET

Per impostazione predefinita, la funzionalità di analisi distribuita basata su TraceContext di W3C è abilitata in tutti gli SDK di .NET Framework/.NET Core recenti, oltre alla compatibilità con il protocollo legacy Request-Id.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app Java

#### <a name="java-30-agent"></a>Agente Java 3,0

  Java 3,0 Agent supporta W3C predefinito e non è necessaria alcuna configurazione aggiuntiva. 

#### <a name="java-sdk"></a>SDK per Java
- **Configurazione in ingresso**

  - Per le app Java EE, aggiungere quanto segue al `<TelemetryModules>` tag nel ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```

  - Per le app Spring boot, aggiungere queste proprietà:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configurazione in uscita**

  Aggiungere il codice seguente al file AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > La modalità di compatibilità con le versioni precedenti è abilitata per impostazione predefinita e il parametro `enableW3CBackCompat` è facoltativo. Usarlo solo per disattivare la compatibilità con le versioni precedenti.
  >
  > È opportuno disattivarla, ad esempio, quando tutti i servizi sono stati aggiornati a versioni più recenti degli SDK che supportano il protocollo W3C. È consigliabile passare a questi SDK più recenti il prima possibile.

> [!IMPORTANT]
> Verificare che le configurazioni in entrata e in uscita siano identiche.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Abilitare il supporto per la traccia distribuita W3C per le app Web

Questa funzionalità è in `Microsoft.ApplicationInsights.JavaScript` . È disabilitata per impostazione predefinita. Per abilitarla, usare `distributedTracingMode` config. AI_AND_W3C viene fornito per la compatibilità con le versioni precedenti di tutti i servizi legacy instrumentati da Application Insights.

- **[configurazione basata su NPM](./javascript.md#npm-based-setup)**

Aggiungere la configurazione seguente:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[Configurazione basata su frammenti](./javascript.md#snippet-based-setup)**

Aggiungere la configurazione seguente:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Per visualizzare tutte le configurazioni necessarie per abilitare la correlazione, vedere la [documentazione correlata a JavaScript](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlazione di dati di telemetria in OpenCensus Python

OpenCensus Python supporta [W3C Trace-context](https://w3c.github.io/trace-context/) senza richiedere una configurazione aggiuntiva.

Come riferimento, il modello di dati OpenCensus è disponibile [qui](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace).

### <a name="incoming-request-correlation"></a>Correlazione delle richieste in ingresso

OpenCensus Python mette in correlazione le intestazioni W3C Trace-Context dalle richieste in ingresso agli intervalli generati dalle richieste stesse. OpenCensus eseguirà questa operazione automaticamente con integrazioni per questi framework di applicazioni Web comuni: Flask, Django e Pyramid. È sufficiente popolare le intestazioni W3C Trace-Context con il [formato corretto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e inviarle con la richiesta. Ecco un'applicazione Flask di esempio che dimostra quanto segue:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Questo codice esegue un'applicazione Flask di esempio nel computer locale, ascoltando la porta `8080` . Per correlare il contesto della traccia, inviare una richiesta all'endpoint. In questo esempio, è possibile usare un `curl` comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Esaminando il [formato dell'intestazione del contesto di traccia](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), è possibile derivare le informazioni seguenti:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se si esamina la voce della richiesta inviata a monitoraggio di Azure, è possibile visualizzare i campi popolati con le informazioni di intestazione della traccia. È possibile trovare questi dati in log (Analytics) nella risorsa di monitoraggio di Azure Application Insights.

![Telemetria delle richieste nei log (analisi)](./media/opencensus-python/0011-correlation.png)

Il `id` campo ha il formato `<trace-id>.<span-id>` , dove `trace-id` è tratto dall'intestazione di traccia passata nella richiesta e `span-id` è una matrice a 8 byte generata per questo intervallo.

Il `operation_ParentId` campo è nel formato `<trace-id>.<parent-id>` , in cui sia `trace-id` `parent-id` che sono ricavati dall'intestazione della traccia passata nella richiesta.

### <a name="log-correlation"></a>Correlazione dei log

OpenCensus Python consente di correlare i log aggiungendo un ID traccia, un ID span e un flag di campionamento ai record di log. Per aggiungere questi attributi, installare OpenCensus [Logging Integration](https://pypi.org/project/opencensus-ext-logging/). Gli attributi seguenti verranno aggiunti agli oggetti Python `LogRecord` : `traceId` , `spanId` e `traceSampled` . Si noti che questa operazione ha effetto solo per i logger creati dopo l'integrazione.

Ecco un'applicazione di esempio che dimostra quanto segue:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando viene eseguito questo codice, nella console vengono stampate le seguenti opzioni:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Si noti che è presente un oggetto `spanId` per il messaggio di log compreso nell'intervallo. Questo è lo stesso `spanId` che appartiene all'intervallo denominato `hello` .

È possibile esportare i dati di log usando `AzureLogHandler` . Per altre informazioni, vedi [questo articolo](./opencensus-python.md#logs).

## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Il Runtime .NET supporta la distribuzione con l'ausilio dell' [attività](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

Il Application Insights .NET SDK USA `DiagnosticSource` e `Activity` per raccogliere e correlare i dati di telemetria.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Correlazione di dati di telemetria in Java

[L'agente Java](./java-in-process-agent.md) e [Java SDK](../../azure-monitor/app/java-get-started.md) versione 2.0.0 o successiva supportano la correlazione automatica dei dati di telemetria. Viene popolato automaticamente `operation_id` per tutti i dati di telemetria (ad esempio, tracce, eccezioni ed eventi personalizzati) rilasciati nell'ambito di una richiesta. Propaga anche le intestazioni di correlazione (descritte in precedenza) per le chiamate da servizio a servizio tramite HTTP, se è configurato l' [agente Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Application Insights agente Java raccoglie automaticamente le richieste e le dipendenze per JMS, Kafka, Netty/webflux e altro ancora. Per Java SDK sono supportate solo le chiamate effettuate tramite Apache HttpClient per la funzionalità di correlazione. La propagazione del contesto automatica tra le tecnologie di messaggistica (ad esempio Kafka, RabbitMQ e il bus di servizio di Azure) non è supportata nell'SDK. 

> [!NOTE]
> Per raccogliere dati di telemetria personalizzati, è necessario instrumentare l'applicazione con Java 2,6 SDK. 

### <a name="role-names"></a>Nomi dei ruoli

Potrebbe essere necessario personalizzare il modo in cui i nomi dei componenti vengono visualizzati nella [mappa delle applicazioni](../../azure-monitor/app/app-map.md). A tale scopo, è possibile impostare manualmente `cloud_RoleName` eseguendo una delle azioni seguenti:

- Per Application Insights agente Java 3,0, impostare il nome del ruolo cloud come segue:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    È anche possibile impostare il nome del ruolo cloud usando la variabile di ambiente `APPLICATIONINSIGHTS_ROLE_NAME` .

- Con Application Insights SDK Java 2.5.0 e versioni successive, è possibile specificare `cloud_RoleName` aggiungendo `<RoleName>` al file di ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se si usa Spring boot con Application Insights Spring boot Starter, è sufficiente impostare il nome personalizzato per l'applicazione nel file Application. Properties:

  `spring.application.name=<name-of-app>`

  L'Starter Spring boot assegna automaticamente il `cloudRoleName` valore immesso per la `spring.application.name` Proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Scrivere dati di [telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md).
- Per gli scenari di correlazione avanzati in ASP.NET Core e ASP.NET, vedere [tenere traccia delle operazioni personalizzate](custom-operations-tracking.md).
- Altre informazioni sull'[impostazione di cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) per altri SDK.
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](./platforms.md).
- Per informazioni sui tipi di Application Insights, vedere il [modello di dati](./data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](./api-filtering-sampling.md).
- Vedere le [informazioni di riferimento sulla configurazione di Application Insights](configuration-with-applicationinsights-config.md).