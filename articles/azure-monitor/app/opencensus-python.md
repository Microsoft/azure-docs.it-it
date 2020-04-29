---
title: Monitorare le applicazioni Python con monitoraggio di Azure (anteprima) | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con monitoraggio di Azure
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537109"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurare monitoraggio di Azure per l'applicazione Python

Monitoraggio di Azure supporta la traccia distribuita, la raccolta delle metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra il processo di configurazione di OpenCensus per Python e l'invio dei dati di monitoraggio a monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. Questo articolo usa [Python 3.7.0](https://www.python.org/downloads/), anche se le versioni precedenti potrebbero funzionare con modifiche minime.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Creare una risorsa Application Insights in monitoraggio di Azure

Prima di tutto è necessario creare una risorsa Application Insights in monitoraggio di Azure, che genererà una chiave di strumentazione (IKey). IKey viene quindi usato per configurare OpenCensus SDK per inviare i dati di telemetria a monitoraggio di Azure.

1. Selezionare **Crea una risorsa** > **strumenti** > di sviluppo**Application Insights**.

   ![Aggiunta di una risorsa Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Viene visualizzata una finestra di configurazione. Usare la tabella seguente per compilare i campi di input.

   | Impostazione        | valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore univoco globale | Nome che identifica l'app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per ospitare i dati Application Insights |
   | **Posizione** | Stati Uniti orientali | Una località nelle vicinanze o vicino alla posizione in cui è ospitata l'app |

1. Selezionare **Crea**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentare con OpenCensus Python SDK per monitoraggio di Azure

Installare gli esportatori di monitoraggio di Azure OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

Per un elenco completo dei pacchetti e delle integrazioni, vedere [pacchetti OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Il `python -m pip install opencensus-ext-azure` comando presuppone che sia stata impostata `PATH` una variabile di ambiente per l'installazione di Python. Se questa variabile non è stata configurata, è necessario fornire il percorso completo della directory in cui si trova il file eseguibile di Python. Il risultato è un comando simile al seguente `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`:.

L'SDK usa tre utilità di esportazione di monitoraggio di Azure per inviare diversi tipi di dati di telemetria a monitoraggio di Azure: traccia, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere [Panoramica della piattaforma dati](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Usare le istruzioni seguenti per inviare questi tipi di dati di telemetria tramite i tre esportatori.

## <a name="telemetry-type-mappings"></a>Mapping dei tipi di telemetria

Ecco gli esportatori che OpenCensus fornisce mappato ai tipi di dati di telemetria che verranno visualizzati in monitoraggio di Azure.

![Screenshot del mapping dei tipi di dati di telemetria da OpenCensus a monitoraggio di Azure](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace`in OpenCensus si riferisce alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` Invia `requests` e `dependency` telemetria a monitoraggio di Azure.

1. Prima di tutto, è necessario generare alcuni dati di traccia localmente. In Python inattivo, o l'editor preferito, immettere il codice seguente.

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

2. L'esecuzione del codice genera una richiesta ripetuta di immissione di un valore. Con ogni voce, il valore verrà stampato nella shell e il modulo Python di `SpanData`OpenCensus genererà un componente corrispondente. Il progetto OpenCensus definisce una [traccia sotto forma di albero di intervalli](https://opencensus.io/core-concepts/tracing/).
    
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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si `SpanData` vuole creare il in monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure è possibile specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. A questo punto, quando si esegue lo script Python, viene comunque richiesto di immettere i valori, ma solo il valore viene stampato nella shell. Il creato `SpanData` verrà inviato a monitoraggio di Azure. È possibile trovare i dati dell'intervallo generato in `dependencies`. Per altri dettagli sulle richieste in uscita, vedere [dipendenze](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)Python di OpenCensus.
Per altri dettagli sulle richieste in ingresso, vedere OpenCensus Python [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlazione traccia

Per informazioni dettagliate sulla correlazione dei dati di telemetria nei dati di traccia, vedere [correlazione di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)Python di OpenCensus.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.

### <a name="metrics"></a>Metriche

1. Prima di tutto, è necessario generare alcuni dati sulle metriche locali. Verrà creata una metrica semplice per tenere traccia del numero di volte in cui l'utente preme INVIO.

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
2. L'esecuzione del codice richiederà ripetutamente di premere INVIO. Viene creata una metrica per tenere traccia del numero di volte in cui viene premuto INVIO. Con ogni voce, il valore verrà incrementato e le informazioni sulla metrica verranno visualizzate nella console di. Le informazioni includono il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare i dati delle metriche in monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure è possibile specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. L'utilità di esportazione invierà i dati delle metriche al monitoraggio di Azure a intervalli fissi. Il valore predefinito è ogni 15 secondi. Stiamo monitorando una singola metrica, quindi questi dati della metrica, con qualsiasi valore e timestamp in esso contenuti, verranno inviati ogni intervallo. È possibile trovare i dati in `customMetrics`.

#### <a name="standard-metrics"></a>Metriche standard

Per impostazione predefinita, l'utilità di esportazione delle metriche invierà un set di metriche standard a monitoraggio di Azure. È possibile disabilitare questa impostazione impostando `enable_standard_metrics` il flag `False` su nel costruttore dell'esportatore di metriche.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Di seguito è riportato un elenco di metriche standard attualmente inviate:

- Memoria disponibile (byte)
- Tempo processore CPU (percentuale)
- Frequenza richieste in ingresso (al secondo)
- Tempo medio di esecuzione della richiesta in ingresso (millisecondi)
- Frequenza delle richieste in uscita (al secondo)
- Elabora utilizzo CPU (percentuale)
- Byte privati processo (byte)

Dovrebbe essere possibile visualizzare queste metriche in `performanceCounters`. La frequenza delle richieste in ingresso è `customMetrics`inferiore a. Per ulteriori informazioni, vedere [contatori delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.

### <a name="logs"></a>Log

1. Prima di tutto, è necessario generare alcuni dati di log locali.

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

2.  Il codice richiederà continuamente l'immissione di un valore. Viene emessa una voce di log per ogni valore immesso.

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

3. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vuole creare i dati di log in monitoraggio di Azure. Passare la stringa di connessione direttamente all'utilità di esportazione oppure è possibile specificarla in una variabile `APPLICATIONINSIGHTS_CONNECTION_STRING`di ambiente. Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

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

4. L'utilità di esportazione invierà i dati di log a monitoraggio di Azure. È possibile trovare i dati in `traces`. 

> [!NOTE]
> `traces`in questo contesto non è uguale a `Tracing`. `traces`si riferisce al tipo di dati di telemetria che viene visualizzato in monitoraggio di `AzureLogHandler`Azure quando si utilizza. `Tracing`si riferisce a un concetto in OpenCensus e si riferisce alla [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Per formattare i messaggi di log, è possibile `formatters` usare nell' [API di registrazione](https://docs.python.org/3/library/logging.html#formatter-objects)Python incorporata.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. È anche possibile aggiungere proprietà personalizzate ai messaggi di log nell'argomento della parola chiave *supplementare* usando il campo custom_dimensions. Verranno visualizzate come coppie chiave-valore in `customDimensions` monitoraggio di Azure.
> [!NOTE]
> Per il corretto funzionamento di questa funzionalità, è necessario passare un dizionario al campo custom_dimensions. Se si passano argomenti di un altro tipo, il logger li ignorerà.

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

#### <a name="sending-exceptions"></a>Invio di eccezioni

OpenCensus Python non rileva e invia `exception` i dati di telemetria automaticamente. Vengono inviati tramite usando le `AzureLogHandler` eccezioni tramite la libreria di registrazione di Python. È possibile aggiungere proprietà personalizzate come con la registrazione normale.

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
Poiché è necessario registrare le eccezioni in modo esplicito, spetta all'utente nel modo in cui si desidera registrare le eccezioni non gestite. OpenCensus non applica restrizioni per il modo in cui un utente desidera eseguire questa operazione, purché registri in modo esplicito i dati di telemetria di un'eccezione.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlazione dei registri

Per informazioni dettagliate su come arricchire i log con i dati del contesto di traccia, vedere OpenCensus Python [logs Integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare la telemetria rilevata prima che venga inviata a monitoraggio di Azure, vedere [processori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)Python di OpenCensus.

## <a name="view-your-data-with-queries"></a>Visualizzare i dati con le query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **log (Analytics)** .

![Screenshot del riquadro della panoramica con "logs (Analytics)" selezionato in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco in **attivo**:

- Per la telemetria inviata con l'utilità di esportazione della traccia di monitoraggio di Azure `requests`, le richieste in ingresso vengono visualizzate sotto. Le richieste in uscita o in-process vengono `dependencies`visualizzate sotto.
- Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di monitoraggio di `customMetrics`Azure, le metriche inviate vengono visualizzate sotto.
- Per la telemetria inviata con l'utilità di esportazione dei log di monitoraggio `traces`di Azure, i log vengono visualizzati sotto. Le eccezioni vengono `exceptions`visualizzate sotto.

Per informazioni più dettagliate su come usare le query e i log, vedere [log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Scopri di più su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Utilità di esportazione di monitoraggio di Azure su GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrazioni di OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Applicazioni di esempio di monitoraggio di Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* [Rilevamento delle richieste in ingresso](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Rilevamento delle richieste in uscita](./../../azure-monitor/app/opencensus-python-request.md)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md): creare test per verificare che il sito sia visibile sul Web.
* [Diagnostica intelligente](../../azure-monitor/app/proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi metrica](../../azure-monitor/app/alerts.md): impostare gli avvisi in modo da ricevere un avviso se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.
