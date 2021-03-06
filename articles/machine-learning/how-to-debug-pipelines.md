---
title: Risoluzione dei problemi relativi alle pipeline di Machine Learning
titleSuffix: Azure Machine Learning
description: Come risolvere i problemi quando si verificano errori durante l'esecuzione di una pipeline di Machine Learning. Insidie comuni e suggerimenti per eseguire il debug degli script prima e durante l'esecuzione remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817342"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Risoluzione dei problemi relativi alle pipeline di Machine Learning

Questo articolo illustra come risolvere i problemi quando si verificano errori durante l'esecuzione di una pipeline di [Machine Learning](concept-ml-pipelines.md) in [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) e Azure Machine Learning [designer.](./concept-designer.md) 

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

La tabella seguente contiene problemi comuni durante lo sviluppo di pipeline, con potenziali soluzioni.

| Problema | Possibile soluzione |
|--|--|
| Impossibile passare i dati alla `PipelineData` directory | Assicurarsi di aver creato una directory nello script che corrisponde alla posizione in cui la pipeline prevede i dati di output del passaggio. Nella maggior parte dei casi, un argomento di input definirà la directory di output, quindi la directory verrà creata in modo esplicito. Usare `os.makedirs(args.output_dir, exist_ok=True)` per creare la directory di output. Vedere [l'esercitazione](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) per un esempio di script di assegnazione dei punteggi che illustra questo modello di progettazione. |
| Bug della dipendenza | Se vengono visualizzati errori della dipendenza nella pipeline remota che non si sono verificati durante il test in locale, verificare che le dipendenze e le versioni dell'ambiente remoto corrispondano a quelle presenti nell'ambiente di test. Vedere [Creazione, memorizzazione nella cache e riutilizzo dell'ambiente](./concept-environments.md#environment-building-caching-and-reuse)|
| Errori ambigui con destinazioni di calcolo | Provare a eliminare e ri-creare le destinazioni di calcolo. La ri-creazione delle destinazioni di calcolo è rapida e può risolvere alcuni problemi temporanei. |
| Passaggi di non riutilizzo della pipeline | Il riutilizzo dei passaggi è abilitato per impostazione predefinita, ma assicurarsi che non sia stato disabilitato in un passaggio della pipeline. Se il riutilizzo è disabilitato, `allow_reuse` il parametro nel passaggio verrà impostato su `False` . |
| La pipeline viene rieseguita inutilmente | Per assicurarsi che i passaggi siano eseguiti di nuovo solo quando cambiano i dati o gli script sottostanti, separare le directory del codice sorgente per ogni passaggio. Se si usa la stessa directory di origine per più passaggi, è possibile che si verifichino rieseguizioni non necessarie. Usare il parametro in un oggetto passaggio della pipeline per puntare alla directory isolata per tale passaggio e assicurarsi di non usare lo stesso `source_directory` `source_directory` percorso per più passaggi. |
| Rallentamento del passaggio rispetto a epoche di training o ad altri comportamenti di ciclo | Provare a cambiare qualsiasi scrittura di file, inclusa la registrazione, da `as_mount()` a `as_upload()` . La **modalità di** montaggio usa un file system virtualizzato remoto e carica l'intero file ogni volta che viene aggiunto. |
| L'avvio della destinazione di calcolo richiede molto tempo | Le immagini Docker per le destinazioni di calcolo vengono caricate da Registro Azure Container (ACR). Per impostazione predefinita, Azure Machine Learning crea un ACR che usa il livello *di servizio* Basic. La modifica di ACR per l'area di lavoro al livello Standard o Premium può ridurre il tempo necessario per compilare e caricare le immagini. Per altre informazioni, vedere [Livelli di servizio di Registro Azure Container](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Errori di autenticazione

Se si esegue un'operazione di gestione in una destinazione di calcolo da un processo remoto, si riceverà uno degli errori seguenti: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ad esempio, si riceverà un errore se si prova a creare o collegare una destinazione di calcolo da una pipeline di Machine Learning che viene inviata per l'esecuzione remota.
## <a name="troubleshooting-parallelrunstep"></a>Risoluzione dei problemi relativi `ParallelRunStep` 

Lo script per un `ParallelRunStep` *oggetto deve contenere* due funzioni:
- `init()`: usare questa funzione per qualsiasi preparazione dispendiosa o comune per un'inferenza successiva. Usarla ad esempio per caricare il modello in un oggetto globale. Questa funzione verrà chiamata solo una volta all'inizio del processo.
-  `run(mini_batch)`: la funzione viene eseguita per ogni istanza di `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` richiama il metodo run e passa un elenco o un `DataFrame` Pandas come argomento al metodo. Ogni voce in mini_batch sarà un percorso file se l'input è un oggetto `FileDataset` oppure un `DataFrame` Pandas se l'input è un oggetto `TabularDataset`.
    -  `response`: il metodo run() deve restituire un `DataFrame` Pandas o una matrice. Per append_row output_action, questi elementi restituiti vengono accodati nel file di output comune. Per summary_only, il contenuto degli elementi viene ignorato. Per tutte le azioni di output, ogni elemento di output restituito indica un'esecuzione riuscita dell'elemento di input nel mini-batch di input. Verificare che nel risultato dell'esecuzione siano inclusi dati sufficienti per eseguire il mapping dell'input al risultato dell'output dell'esecuzione. L'output dell'esecuzione verrà scritto nel file di output ma non sarà necessariamente in ordine, quindi sarà necessario usare una chiave nell'output per eseguirne il mapping all'input.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se è presente un altro file o un'altra cartella nella stessa directory dello script di inferenza, è possibile farvi riferimento individuando la directory di lavoro corrente.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametri per ParallelRunConfig

`ParallelRunConfig` è la configurazione principale per l'istanza di `ParallelRunStep` all'interno della pipeline di Azure Machine Learning. Si usa per eseguire il wrapping dello script e configurare i parametri necessari, incluse tutte le voci seguenti:
- `entry_script`: script utente come percorso di file locale che verrà eseguito in parallelo su più nodi. Se `source_directory` è presente, usare un percorso relativo. In caso contrario, usare qualsiasi percorso accessibile nel computer.
- `mini_batch_size`: dimensioni del mini-batch passato a una singola chiamata di `run()` (Facoltativo; il valore predefinito è `10` file per `FileDataset` e `1MB` per `TabularDataset`.)
    - Per `FileDataset`, è il numero di file con un valore minimo di `1`. È possibile combinare più file in un solo mini-batch.
    - Per `TabularDataset`, sono le dimensioni dei dati. Valori di esempio sono `1024`, `1024KB`, `10MB` e `1GB`. Il valore consigliato è `1MB`. Il mini-batch di `TabularDataset` non supererà mai i limiti dei file. Supponiamo ad esempio di avere una serie di file CSV di varie dimensioni, comprese tra 100 KB e 10 MB. Se si imposta `mini_batch_size = 1MB`, i file di dimensioni inferiori a 1 MB verranno considerati come un mini-batch. I file di dimensioni maggiori di 1 MB verranno suddivisi in più mini-batch.
- `error_threshold`: numero di errori di record per `TabularDataset` e di errori di file per `FileDataset` che devono essere ignorati durante l'elaborazione. Se il numero di errori per l'intero input supera questo valore, il processo viene interrotto. La soglia di errore è per l'intero input e non per il singolo mini-batch inviato al metodo `run()`. L'intervallo è `[-1, int.max]`. La parte `-1` indica di ignorare tutti gli errori durante l'elaborazione.
- `output_action`: uno dei valori seguenti indica come verrà organizzato l'output:
    - `summary_only`: l'output viene archiviato dallo script utente. `ParallelRunStep` userà l'output solo per il calcolo della soglia di errore.
    - `append_row`: per tutti gli input, nella cartella di output verrà creato un solo file in cui accodare tutti gli output separati per riga.
- `append_row_file_name`: per personalizzare il nome del file di output per append_row output_action (facoltativo; il valore predefinito è `parallel_run_step.txt`).
- `source_directory`: percorsi delle cartelle che contengono tutti i file da eseguire nella destinazione di calcolo (facoltativo).
- `compute_target`: È supportato solo `AmlCompute`.
- `node_count`: numero di nodi di calcolo da usare per l'esecuzione dello script utente.
- `process_count_per_node`: numero di processi per nodo. È consigliabile impostare questo valore sul numero di GPU o CPU di un nodo (facoltativo; il valore predefinito è `1`).
- `environment`: definizione dell'ambiente Python. È possibile configurarlo per usare un ambiente Python esistente o per configurare un ambiente temporaneo. La definizione è anche responsabile dell'impostazione delle dipendenze dell'applicazione necessarie (facoltativo).
- `logging_level`: livello di dettaglio del log. I valori in ordine crescente di livello di dettaglio sono: `WARNING`, `INFO` e `DEBUG`. (Facoltativo. Il valore predefinito è `INFO`)
- `run_invocation_timeout`: timeout della chiamata del metodo `run()` in secondi. (Facoltativo. Il valore predefinito è`60`)
- `run_max_try`: Numero massimo di tentativi di `run()` per un mini-batch. Una `run()` viene considerata non riuscita se viene generata un'eccezione o se non viene restituito nulla quando viene raggiunto `run_invocation_timeout` (facoltativo; il valore predefinito è `3`). 

È possibile specificare `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` e `run_max_try` come `PipelineParameter` in modo che, quando si ripete l'esecuzione della pipeline, sia possibile ottimizzare i valori dei parametri. In questo esempio si usa `PipelineParameter` per `mini_batch_size` e `Process_count_per_node` e questi valori verranno cambiati quando si ripete l'esecuzione in un secondo momento. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametri per la creazione di ParallelRunStep

Creare ParallelRunStep usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già collegata all'area di lavoro come destinazione di esecuzione dello script di inferenza. Usare `ParallelRunStep` per creare il passaggio della pipeline di inferenza batch, che accetta tutti i parametri seguenti:
- `name`: nome del passaggio, con le restrizioni di denominazione seguenti: univoco, 3-32 caratteri ed espressione regolare ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: oggetto `ParallelRunConfig`, come definito in precedenza.
- `inputs`: uno o più set di dati di Azure Machine Learning di tipo singolo da partizionare per l'elaborazione parallela.
- `side_inputs`: uno o più set di dati o dati di riferimento usati come input secondari senza necessità di partizionamento.
- `output`: oggetto `OutputFileDatasetConfig` che corrisponde alla directory di output.
- `arguments`: elenco di argomenti passati allo script utente. Usare unknown_args per recuperarli nello script di immissione (facoltativo).
- `allow_reuse`: indica se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito con le stesse impostazioni o gli stessi input. Se il parametro è `False`, durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio (Facoltativo. Il valore predefinito è `True`).

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Tecniche di debug

Esistono tre tecniche principali per il debug delle pipeline: 

* Eseguire il debug di singoli passaggi della pipeline nel computer locale
* Usare la registrazione e Application Insights per isolare e diagnosticare l'origine del problema
* Collegare un debugger remoto a una pipeline in esecuzione in Azure

### <a name="debug-scripts-locally"></a>Eseguire il debug degli script in locale

Uno degli errori più comuni in una pipeline è che lo script di dominio non viene eseguito come previsto o contiene errori di runtime nel contesto di calcolo remoto di cui è difficile eseguire il debug.

Le pipeline stesse non possono essere eseguite in locale, ma l'esecuzione degli script in isolamento nel computer locale consente di eseguire il debug più velocemente perché non è necessario attendere il processo di compilazione dell'ambiente e di calcolo. A tale scopo, è necessario eseguire alcune operazioni di sviluppo:

* Se i dati si trova in un archivio dati cloud, è necessario scaricare i dati e renderli disponibili per lo script. L'uso di un piccolo campione di dati è un buon modo per ridurre il runtime e ottenere rapidamente commenti e suggerimenti sul comportamento degli script
* Se si sta tentando di simulare un passaggio intermedio della pipeline, potrebbe essere necessario compilare manualmente i tipi di oggetto che lo script specifico prevede dal passaggio precedente
* Sarà anche necessario definire il proprio ambiente e replicare le dipendenze definite nell'ambiente di calcolo remoto

Dopo aver creato uno script da eseguire nell'ambiente locale, è molto più semplice eseguire attività di debug come:

* Collegamento di una configurazione di debug personalizzata
* Sospensione dell'esecuzione e controllo dello stato degli oggetti
* Rilevamento di errori di tipo o logici che non verranno esposti fino al runtime

> [!TIP] 
> Dopo aver verificato che lo script sia in esecuzione come previsto, un buon passaggio successivo consiste nell'eseguire lo script in una pipeline a passaggio singolo prima di tentare di eseguirlo in una pipeline con più passaggi.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Configurare, scrivere ed esaminare i log della pipeline

Il test degli script in locale è un ottimo modo per eseguire il debug di frammenti di codice principali e logica complessa prima di iniziare a compilare una pipeline, ma a un certo punto è probabile che sia necessario eseguire il debug degli script durante l'esecuzione effettiva della pipeline, soprattutto quando si diagnostica il comportamento che si verifica durante l'interazione tra i passaggi della pipeline. È consigliabile usare istruzioni negli script del passaggio in modo da visualizzare lo stato dell'oggetto e i valori previsti durante l'esecuzione remota, in modo simile a come si eseguirebbe il `print()` debug del codice JavaScript.

### <a name="logging-options-and-behavior"></a>Opzioni di registrazione e comportamento

La tabella seguente fornisce informazioni sulle diverse opzioni di debug per le pipeline. Non è un elenco esaustivo, perché esistono altre opzioni oltre a quelle Azure Machine Learning, Python e OpenCensus mostrate qui.

| Libreria                    | Type   | Esempio                                                          | Destination                                  | Risorse                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrica | `run.log(name, val)`                                             | Azure Machine Learning dell'interfaccia utente del portale             | [Come tenere traccia degli esperimenti](how-to-log-view-metrics.md)<br>[Classe azureml.core.Run](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Stampa/registrazione Python    | Log    | `print(val)`<br>`logging.info(message)`                          | Log driver, progettazione Azure Machine Learning driver | [Come tenere traccia degli esperimenti](how-to-log-view-metrics.md)<br><br>[Registrazione python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights : tracce                | [Debug delle pipeline in Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[Esportatori di Monitoraggio di Azure per OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Guida di cookbook per la registrazione in Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Esempio di opzioni di registrazione

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione di Azure Machine Learning

Per le pipeline create nella finestra di progettazione, è possibile trovare il file **70_driver_log** nella pagina di creazione o nella pagina dei dettagli dell'esecuzione della pipeline.

### <a name="enable-logging-for-real-time-endpoints"></a>Abilitare la registrazione per gli endpoint in tempo reale

Per risolvere i problemi ed eseguire il debug degli endpoint in tempo reale nella finestra di progettazione, è necessario abilitare la registrazione di Application Insight usando l'SDK. La registrazione consente di risolvere i problemi di distribuzione e utilizzo del modello ed eseguirne il debug. Per altre informazioni, vedere [Registrazione per i modelli distribuiti.](./how-to-enable-app-insights.md) 

### <a name="get-logs-from-the-authoring-page"></a>Ottenere i log dalla pagina di creazione

Quando si invia un'esecuzione della pipeline e si rimane nella pagina di creazione, è possibile trovare i file di log generati per ogni modulo al termine dell'esecuzione di ogni modulo.

1. Selezionare un modulo che ha terminato l'esecuzione nell'area di disegno di creazione.
1. Nel riquadro destro del modulo passare alla  **scheda Output e** log.
1. Espandere il riquadro destro e selezionare la70_driver_log.txt **per** visualizzare il file nel browser. È anche possibile scaricare i log in locale.

    ![Riquadro di output espanso nella finestra di progettazione](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Ottenere i log dalle esecuzioni della pipeline

È anche possibile trovare i file di log per esecuzioni specifiche nella pagina dei dettagli dell'esecuzione della pipeline, disponibile nella sezione **Pipeline** **o** Esperimenti di Studio.

1. Selezionare un'esecuzione della pipeline creata nella finestra di progettazione.

    ![Pagina esecuzione pipeline](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selezionare un modulo nel riquadro di anteprima.
1. Nel riquadro destro del modulo passare alla scheda  **Output e** log.
1. Espandere il riquadro destro per visualizzare il **file70_driver_log.txt** nel browser oppure selezionare il file per scaricare i log in locale.

> [!IMPORTANT]
> Per aggiornare una pipeline dalla pagina dei dettagli dell'esecuzione della pipeline, è necessario **clonare** l'esecuzione della pipeline in una nuova bozza di pipeline. Un'esecuzione della pipeline è uno snapshot della pipeline. È simile a un file di log e non può essere modificato. 

## <a name="application-insights"></a>Application Insights
Per altre informazioni sull'uso della libreria Python OpenCensus in questo modo, vedere questa guida: Eseguire il debug e risolvere i problemi delle pipeline di [Machine Learning in Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Debug interattivo con Visual Studio Code

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python usato nella pipeline di Machine Learning. Usando Visual Studio Code (VS Code) e debugpy, è possibile connettersi al codice durante l'esecuzione nell'ambiente di training. Per altre informazioni, vedere il [debug interattivo in VS Code guida](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione completa sull'uso di , vedere `ParallelRunStep` [Esercitazione: Compilare una pipeline Azure Machine Learning per l'assegnazione dei punteggi in batch.](tutorial-pipeline-batch-scoring-classification.md)

* Per un esempio completo che mostra machine learning automatizzato nelle pipeline di Machine Learning, vedere Usare Machine Learning automatizzato in una pipeline Azure Machine Learning [in Python](how-to-use-automlstep-in-pipelines.md).

* Vedere le informazioni di riferimento sull'SDK per informazioni sul [pacchetto azureml-pipelines-core](/python/api/azureml-pipeline-core/) e [sul pacchetto azureml-pipelines-steps.](/python/api/azureml-pipeline-steps/)

* Vedere l'elenco delle [eccezioni della finestra di progettazione e dei codici di errore](algorithm-module-reference/designer-error-codes.md).