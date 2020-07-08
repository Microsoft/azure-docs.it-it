---
title: Monitorare le applicazioni Python con Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con Monitoraggio di Azure
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.custom: tracking-python
ms.openlocfilehash: bef2f1c48241a3f0215481aeb0da3fcc237ddb50
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076626"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurare Monitoraggio di Azure per l'applicazione Python

Monitoraggio di Azure supporta la traccia distribuita, la raccolta di metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra il processo di configurazione di OpenCensus per Python e l'invio dei dati di monitoraggio a monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. Questo articolo usa [Python 3.7.0](https://www.python.org/downloads/release/python-370/), sebbene le altre versioni possano funzionare con modifiche minime. SDK supporta solo Python v 2.7 e v 3.4-v 3.7.
- Creazione di una[risorsa](./create-new-resource.md) di Application Insights. Verrà assegnata la propria chiave di strumentazione (IKey) per la risorsa.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentare con l'SDK OpenCensus Python per Monitoraggio di Azure

Installare le utilità di esportazione di Monitoraggio di Azure per OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

Per un elenco completo dei pacchetti e delle integrazioni, vedere i [pacchetti OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Il comando `python -m pip install opencensus-ext-azure` presuppone la presenza di una variabile di ambiente `PATH` impostata per l'installazione di Python. Se questa non è stata configurata, sarà necessario specificare il percorso completo della directory in cui si trova l'eseguibile di Python. Il risultato è un comando simile al seguente: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

L'SDK usa tre utilità di esportazione di monitoraggio di Azure per inviare diversi tipi di dati di telemetria a monitoraggio di Azure. Si tratta di tracce, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere la [panoramica della piattaforma dati](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Usare le istruzioni seguenti per inviare questi tipi di dati di telemetria tramite le tre utilità di esportazione.

## <a name="telemetry-type-mappings"></a>Mapping dei tipi di telemetria

Ecco gli esportatori che OpenCensus fornisce mappato ai tipi di dati di telemetria visualizzati in monitoraggio di Azure.

| Pilastro di osservabilità | Tipo di telemetria in monitoraggio di Azure    | Spiegazione                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Log                    | Tracce, eccezioni, customEvents   | Telemetria di log, telemetria delle eccezioni, telemetria degli eventi |
| Metriche                 | customMetrics, performanceCounters | Contatori delle prestazioni delle metriche personalizzate                |
| Traccia                 | Richieste dipendenze             | Richieste in ingresso, richieste in uscita                |

### <a name="logs"></a>Log

1. Prima di tutto, generare alcuni dati di log locali.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Il codice richiede continuamente l'immissione di un valore. Per ogni valore immesso viene generata una voce di log.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati di log in Monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione. In alternativa, è possibile specificarla in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

1. L'utilità di esportazione invia i dati di log a monitoraggio di Azure. È possibile trovare i dati in `traces`. 

    > [!NOTE]
    > In questo contesto, `traces` non è uguale a `tracing` . In questo caso, `traces` si riferisce al tipo di dati di telemetria che verrà visualizzato in monitoraggio di Azure quando si usa `AzureLogHandler` . Ma `tracing` si riferisce a un concetto di OpenCensus e si riferisce alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

    > [!NOTE]
    > Il logger radice viene configurato con il livello di avviso. Ciò significa che qualsiasi log inviato che ha meno gravità viene ignorato e, a sua volta, non verrà inviato a monitoraggio di Azure. Per altre informazioni, vedere la [documentazione](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. È anche possibile aggiungere proprietà personalizzate ai messaggi di log nell'argomento della parola chiave *supplementare* usando il campo custom_dimensions. Queste proprietà vengono visualizzate come coppie chiave-valore in `customDimensions` monitoraggio di Azure.
    > [!NOTE]
    > Per il corretto funzionamento di questa caratteristica, è necessario passare un dizionario al campo custom_dimensions. Se si passano argomenti di qualsiasi altro tipo, il logger li ignora.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Configurare la registrazione per le applicazioni Django

È possibile configurare la registrazione in modo esplicito nel codice dell'applicazione come sopra per le applicazioni Django oppure è possibile specificarla nella configurazione di registrazione di Django. Questo codice consente di accedere a qualsiasi file usato per la configurazione delle impostazioni Django. Per informazioni su come configurare le impostazioni Django, vedere [Impostazioni Django](https://docs.djangoproject.com/en/3.0/topics/settings/). Per altre informazioni sulla configurazione della registrazione, vedere la pagina relativa alla [registrazione di Django](https://docs.djangoproject.com/en/3.0/topics/logging/).

```python
 LOGGING = {
     "handlers": {
         "azure": {
             "level": "DEBUG",
          "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
             "instrumentation_key": "<your-ikey-here>",
          },
         "console": {
             "level": "DEBUG",
             "class": "logging.StreamHandler",
             "stream": sys.stdout,
          },
       },
     "loggers": {
         "logger_name": {"handlers": ["azure", "console"]},
     },
 }
```

Assicurarsi di usare il logger con lo stesso nome di quello specificato nella configurazione.

```python
 import logging
        
 logger = logging.getLogger("logger_name")
 logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Invia eccezioni

OpenCensus Python non rileva e invia automaticamente i dati di `exception` telemetria. Vengono inviati `AzureLogHandler` usando le eccezioni tramite la libreria di registrazione di Python. È possibile aggiungere proprietà personalizzate, come con la registrazione normale.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Poiché è necessario registrare le eccezioni in modo esplicito, spetta all'utente come si desidera registrare le eccezioni non gestite. OpenCensus non applica restrizioni per il modo in cui un utente desidera eseguire questa operazione, purché registri in modo esplicito i dati di telemetria di un'eccezione.

#### <a name="send-events"></a>Inviare eventi

È possibile inviare dati `customEvent` di telemetria esattamente allo stesso modo in cui si inviano i dati `trace` di telemetria, ad eccezione di `AzureEventHandler` .

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlazione dei log

Per informazioni dettagliate su come arricchire i log con i dati del contesto di traccia, vedere l'[integrazione dei log](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation) OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.


### <a name="metrics"></a>Metriche

1. Prima di tutto, generare alcuni dati di metrica locali. Verrà creata una metrica semplice per tenere traccia del numero di volte in cui l'utente seleziona il tasto **invio** .

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. L'esecuzione del codice richiede ripetutamente di selezionare **invio**. Viene creata una metrica per tenere traccia del numero di volte in cui è stata selezionata l'opzione **invio** . Con ogni voce, il valore viene incrementato e le informazioni sulla metrica vengono visualizzate nella console. Le informazioni includono il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati della metrica in Monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione. In alternativa, è possibile specificarla in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. L'utilità di esportazione invia i dati delle metriche al monitoraggio di Azure a intervalli fissi. Il valore predefinito è ogni 15 secondi. Stiamo monitorando una singola metrica, quindi questi dati della metrica, con qualsiasi valore e timestamp in esso contenuti, vengono inviati ogni intervallo. È possibile trovare i dati in `customMetrics`.

#### <a name="performance-counters"></a>Contatori delle prestazioni

Per impostazione predefinita, l'utilità di esportazione delle metriche Invia un set di contatori delle prestazioni a monitoraggio di Azure. Si può disabilitare questa impostazione impostando il flag di `enable_standard_metrics` su `False` nel costruttore dell'utilità di esportazione delle metriche.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Questi contatori delle prestazioni sono attualmente inviati:

- Memoria disponibile (byte)
- Tempo processore CPU (percentuale)
- Frequenza richieste in ingresso (al secondo)
- Tempo di esecuzione medio richieste in ingresso (millisecondi)
- Utilizzo CPU processi (percentuale)
- Byte privati processi (byte)

Dovrebbe essere possibile visualizzare queste metriche in `performanceCounters`. Per altre informazioni, vedere i [contatori delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.

### <a name="tracing"></a>Traccia

> [!NOTE]
> In OpenCensus `tracing` si riferisce alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` invia la telemetria per `requests` e `dependency` a Monitoraggio di Azure.

1. Prima di tutto, è necessario generare alcuni dati di traccia in locale. In Python IDLE o nell'editor preferito immettere il codice seguente:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. L'esecuzione ripetuta del codice richiede l'immissione di un valore. Con ogni voce, il valore viene stampato nella shell. Il modulo OpenCensus Python genera un componente corrispondente `SpanData` . Nel progetto OpenCensus, una [trace è un albero di span](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare il `SpanData` monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione. In alternativa, è possibile specificarla in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A questo punto, quando si esegue lo script Python, verrà ancora richiesto di immettere valori, ma verrà stampato nella shell solo il valore. L'oggetto creato `SpanData` viene inviato a monitoraggio di Azure. È possibile trovare i dati di span emessi in `dependencies`. Per altre informazioni sulle richieste in uscita, vedere [dipendenze](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)Python di OpenCensus.
Per altre informazioni sulle richieste in ingresso, vedere OpenCensus Python [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlazione delle tracce

Per altre informazioni sulla correlazione dei dati di telemetria nei dati di traccia, vedere [correlazione di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)Python OpenCensus.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per altre informazioni su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.

## <a name="configure-azure-monitor-exporters"></a>Configurare gli esportatori di monitoraggio di Azure

Come illustrato, sono disponibili tre diversi esportatori di monitoraggio di Azure che supportano OpenCensus. Ognuna di esse invia diversi tipi di dati di telemetria a monitoraggio di Azure. Per visualizzare i tipi di dati di telemetria inviati da ogni utilità di esportazione, vedere l'elenco seguente.

Ogni utilità di esportazione accetta gli stessi argomenti per la configurazione, passati attraverso i costruttori. È possibile visualizzare i dettagli su ciascuno di essi:

- `connection_string`: Stringa di connessione usata per la connessione alla risorsa di monitoraggio di Azure. Ha la priorità `instrumentation_key` .
- `enable_standard_metrics`: Usato per `AzureMetricsExporter` . Segnala all'utilità di esportazione di inviare automaticamente le metriche del [contatore delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/platform/app-insights-metrics#performance-counters) a monitoraggio di Azure. Il valore predefinito è `True`.
- `export_interval`: Usato per specificare la frequenza in secondi di esportazione.
- `instrumentation_key`: Chiave di strumentazione usata per connettersi alla risorsa di monitoraggio di Azure.
- `logging_sampling_rate`: Usato per `AzureLogHandler` . Fornisce una frequenza di campionamento [0, 1.0] per l'esportazione dei log. Il valore predefinito è 1,0.
- `max_batch_size`: Specifica la dimensione massima dei dati di telemetria esportati contemporaneamente.
- `proxies`: Specifica una sequenza di proxy da usare per l'invio di dati a monitoraggio di Azure. Per altre informazioni, vedere [proxy](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: Percorso in cui esiste la cartella di archiviazione locale (telemetria non inviata). A partire dalla `opencensus-ext-azure` versione 1.0.3, il percorso predefinito è la directory temporanea del sistema operativo + `opencensus-python`  +  `your-ikey` . Prima della versione 1.0.3, il percorso predefinito è $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Visualizzare i dati usando query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **Log (Analisi)** .

![Screenshot del riquadro della panoramica con "Log (Analisi)" selezionato in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco sotto **Attiva**:

- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, le richieste in ingresso vengono visualizzate in `requests`. Le richieste in uscita o in elaborazione vengono visualizzate in `dependencies`.
- Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di Monitoraggio di Azure, le metriche inviate vengono visualizzate in `customMetrics`.
- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, i log vengono visualizzati in `traces`. Le eccezioni vengono visualizzate in `exceptions`.

Per informazioni più dettagliate su come usare le query e i log, vedere [Log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Altre informazioni su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Utilità di esportazione di Monitoraggio di Azure su GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrazioni di OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Applicazioni di esempio di Monitoraggio di Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* [Rilevamento delle richieste in ingresso](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Rilevamento delle richieste in uscita](./../../azure-monitor/app/opencensus-python-request.md)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per assicurarsi che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi delle metriche](../../azure-monitor/platform/alerts-log.md): Impostare avvisi per essere avvertiti se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.
