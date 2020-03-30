---
title: Registrare gli esperimenti mL & metricheLog ML experiments & metrics
titleSuffix: Azure Machine Learning
description: Monitorare gli esperimenti di Azure ML e monitorare le metriche di esecuzione per migliorare il processo di creazione del modello. Aggiungere la registrazione allo script di training e visualizzare i risultati registrati di un'esecuzione.  Utilizzare run.log, Run.start_logging o ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296966"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorare le esecuzioni e le metriche dell'esperimento di Azure MLMonitor Azure ML experiment runs and metrics
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Migliora il processo di creazione del modello monitorando gli esperimenti e monitorando le metriche di esecuzione. In this article, learn how to add logging code to your training script, submit an experiment run, monitor that run, and inspect the results in Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning può anche registrare informazioni da altre origini durante il training, ad esempio corse di apprendimento automatico automatizzate o il contenitore Docker che esegue il processo di formazione. Questi registri non sono documentati. Se si verificano problemi e contattare il supporto tecnico Microsoft, potrebbero essere in grado di utilizzare questi registri durante la risoluzione dei problemi.

> [!TIP]
> Le informazioni contenute in questo documento sono principalmente per i data scientist e gli sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato a monitorare l'utilizzo delle risorse e gli eventi di Azure Machine Learning, ad esempio quote, esecuzioni di training completate o distribuzioni di modelli completate, vedere Monitoraggio di Azure Machine Learning.If you are an administrator interested in monitoring resource usage and events from Azure Machine learning, such as quotas, completed training runs, or completed model deployments, see [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Metriche disponibili da monitorare

Le metriche seguenti possono essere aggiunte a un'esecuzione durante il training di un esperimento. Per visualizzare un elenco più dettagliato di cosa è possibile monitorare in un'esecuzione, vedere la [documentazione di riferimento della classe Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Funzione Python | Note|
|----|:----|:----|
|Valori scalari |Funzione:<br>`run.log(name, value, description='')`<br><br>Esempio:<br>run.log("accuracy", 0.95) |Registrare un valore numerico o stringa per l'esecuzione con il nome specificato. La registrazione di una metrica per un'esecuzione fa sì che tale metrica venga archiviata nel record esecuzione nell'esperimento.  È possibile registrare la stessa metrica più volte all'interno di un'esecuzione. Il risultato verrà considerato un vettore di tale metrica.|
|Elenchi|Funzione:<br>`run.log_list(name, value, description='')`<br><br>Esempio:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Registrare un elenco di valori per l'esecuzione con il nome specificato.|
|Riga|Funzione:<br>`run.log_row(name, description=None, **kwargs)`<br>Esempio:<br>run.log_row("Y over X", x=1, y=0.4) | L'uso di *log_row* crea una metrica con più colonne come descritto in kwargs. Ogni parametro denominato genera una colonna con il valore specificato.  *log_row* può essere chiamato una volta per registrare una tupla arbitraria o più volte in un ciclo per generare una tabella completa.|
|Tabella|Funzione:<br>`run.log_table(name, value, description='')`<br><br>Esempio:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registrare un oggetto dizionario per l'esecuzione con il nome specificato. |
|Immagini|Funzione:<br>`run.log_image(name, path=None, plot=None)`<br><br>Esempio:<br>`run.log_image("ROC", plot=plt)` | Registrare un'immagine per il record esecuzione. Usare log_image per registrare un file di immagine o un tracciato matplotlib per l'esecuzione.  Queste immagini saranno visibili e confrontabili nel record esecuzione.|
|Assegnare un tag a un'esecuzione|Funzione:<br>`run.tag(key, value=None)`<br><br>Esempio:<br>run.tag("selected", "yes") | Assegnare all'esecuzione una chiave stringa e un valore di stringa facoltativo.|
|Caricare un file o una directory|Funzione:<br>`run.upload_file(name, path_or_stream)`<br> <br> Esempio:<br>run.upload_file("best_model.pkl", "./model.pkl") | Caricare un file per il record esecuzione. Consente di eseguire automaticamente il file di acquisizione nella directory di output specificata, che per impostazione predefinita corrisponde a "./outputs" per la maggior parte dei tipi di esecuzione.  Usare upload_file solo quando è necessario caricare altri file o non è specificata una directory di output. È consigliabile aggiungere `outputs` al nome, in modo che venga caricato nella directory outputs. È possibile elencare tutti i file associati a questo record esecuzione chiamando `run.get_file_names()`|

> [!NOTE]
> Le metriche per valori scalari, elenchi, righe e tabelle possono essere di tipo float, integer o string.

## <a name="choose-a-logging-option"></a>Scegliere un'opzione di registrazione

Se si vuole tenere traccia dell'esperimento o monitorarlo, è necessario aggiungere codice per avviare la registrazione quando si invia l'esecuzione. Di seguito sono elencati diversi modi per attivare l'invio dell'esecuzione:
* __Run.start_logging__: aggiungere funzioni di registrazione allo script di training e avviare una sessione di registrazione interattiva nell'esperimento specificato. **start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.
* __ScriptRunConfig__: aggiungere funzioni di registrazione allo script di training e caricare l'intera cartella dello script con l'esecuzione.  **ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

## <a name="set-up-the-workspace"></a>Configurare l'area di lavoro
Prima di aggiungere la registrazione e inviare un esperimento, è necessario configurare l'area di lavoro.

1. Caricare l'area di lavoro. Per ulteriori informazioni sull'impostazione della configurazione dell'area di lavoro, vedere File di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

[!notebook-python[] (sezione/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name load_ws)]


## <a name="option-1-use-start_logging"></a>Opzione 1: usare start_logging

**start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.

Nell'esempio seguente viene eseguito il training di un semplice modello sklearn Ridge localmente in un'istanza di Jupyter Notebook locale. Per altre informazioni sull'invio di esperimenti in ambienti diversi, vedere Configurare gli obiettivi di calcolo per il training dei modelli con Azure Machine Learning.To learn more about submitting experiments to different environments, see [Set up compute targets for model training with Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)

### <a name="load-the-data"></a>Caricare i dati

Questo esempio usa il dataset per il diabete, un set di dati di piccole dimensioni noto fornito con scikit-learn. Questa cella carica il set di dati e lo suddivide in set di training e test casuali.

[!notebook-python[] (sezione/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name-load_data)]

### <a name="add-tracking"></a>Aggiungi tracciamento
Aggiungere il monitoraggio dell'esperimento usando Azure Machine Learning SDK e caricare un modello persistente nel record di esecuzione dell'esperimento. Il codice seguente aggiunge tag e log e consente di caricare un file di modello nell'esecuzione dell'esperimento.

[!notebook-python[] (sezione/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name-create_experiment)]

Lo script termina con ```run.complete()```, che contrassegna l'esecuzione come completata.  Questa funzione viene usata in genere in scenari di notebook interattivi.

## <a name="option-2-use-scriptrunconfig"></a>Opzione 2: usare ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) è una classe per l'impostazione delle configurazioni per le esecuzioni di script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

Questo esempio si espande a partire dal modello sklearn Ridge di base dell'esempio precedente. Esegue un semplice sweep di parametri su valori alfa del modello per acquisire le metriche e i modelli sottoposti a training nelle esecuzioni nell'ambito dell'esperimento. L'esempio viene eseguito localmente in un ambiente gestito dall'utente. 

1. Creare uno script di training `train.py`.

   [!code-python[] (sezione /MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Lo script `train.py` fa riferimento a `mylib.py` che consente di ottenere l'elenco dei valori alfa da usare nel modello ridge.

   [!code-python[] (sezione /MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configurare un ambiente locale gestito dall'utente.

   [!notebook-python[] (sezione /MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name-user_managed_env)]


4. Inviare lo script ```train.py``` da eseguire nell'ambiente gestito dall'utente. L'intera cartella dello script viene inviata per il training, incluso il file ```mylib.py```.

   [!notebook-python[] (sezione /MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name-src)] [!notebook-python[] (sezione /MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name-run)]




## <a name="manage-a-run"></a>Gestire una corsa

L'articolo [Avviare, monitorare e annullare la formazione esegue](how-to-manage-runs.md) l'articolo evidenzia specifici flussi di lavoro di Azure Machine Learning per informazioni su come gestire gli esperimenti.

## <a name="view-run-details"></a>Visualizzare i dettagli dell'esecuzione

### <a name="view-activequeued-runs-from-the-browser"></a>Visualizzare le esecuzioni attive/in coda dal browser

Gli obiettivi di calcolo usati per eseguire il training dei modelli sono una risorsa condivisa. Di conseguenza, possono avere più esecuzioni in coda o attive in un determinato momento. Per visualizzare le esecuzioni per una destinazione di calcolo specifica dal browser, attenersi alla seguente procedura:

1. In [Azure Machine Learning Studio](https://ml.azure.com/)selezionare l'area di lavoro e quindi selezionare __Calcolo__ sul lato sinistro della pagina.

1. Selezionare __Gruppi di formazione__ per visualizzare un elenco di destinazioni di calcolo usate per il training. Quindi selezionare il cluster.

    ![Selezionare il cluster di training](./media/how-to-track-experiments/select-training-compute.png)

1. Selezionare __Runs__. Viene visualizzato l'elenco delle esecuzioni che utilizzano questo cluster. Per visualizzare i dettagli di un'esecuzione specifica, utilizzare il collegamento nella colonna __Esegui.__ Per visualizzare i dettagli dell'esperimento, usa il link nella colonna __Esperimento.__

    ![Selezionare le esecuzioni per il cluster di trainingSelect runs for training cluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Un'esecuzione può contenere esecuzioni figlio, pertanto un processo di training può comportare più voci.

Al termine di un'esecuzione, questa non viene più visualizzata in questa pagina. Per visualizzare le informazioni sulle esecuzioni completate, visita la sezione __Esperimenti__ dello studio e seleziona l'esperimento ed esegui. Per altre informazioni, vedere la sezione [Metriche di esecuzione](#queryrunmetrics) di query.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor eseguito con widget taccuino Jupyter
Quando si utilizza il metodo **ScriptRunConfig** per inviare le esecuzioni, è possibile osservare l'avanzamento della conduttura con un [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.

1. Visualizzare il widget di Jupyter durante l'attesa del completamento dell'esecuzione.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Screenshot del widget di Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

   È inoltre possibile ottenere un collegamento alla stessa visualizzazione nell'area di lavoro.

   ```python
   print(run.get_portal_url())
   ```

2. **[Per le esecuzioni di Machine Learning automatizzato]** Per accedere ai grafici di un'esecuzione precedente, Sostituire `<<experiment_name>>` con il nome dell'esperimento appropriato:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget di notebook di Jupyter per Machine Learning automatizzato](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Per visualizzare ulteriori dettagli di una pipeline, fare clic sulla pipeline che si vuole esplorare nella tabella e i grafici verranno visualizzati in un popup di Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Ottenere i risultati del log dopo il completamento

Il training e il monitoraggio del modello si verificano in background, pertanto è possibile eseguire altre attività durante l'attesa. È anche possibile attendere il completamento del training del modello prima di eseguire altro codice. Quando si usa **ScriptRunConfig**, è possibile usare ```run.wait_for_completion(show_output = True)``` per visualizzare quando viene completato il training del modello. Il flag ```show_output``` fornisce output dettagliato. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Eseguire query sulle metriche di esecuzione

È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. È ora possibile ottenere tutte le metriche registrate nell'esempio precedente per determinare il modello migliore.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Visualizzare l'esperimento nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com)

Al termine dell'esecuzione di un esperimento, è possibile passare al record esecuzione dell'esperimento registrato. È possibile accedere alla cronologia da [Azure Machine Learning Studio](https://ml.azure.com).

Accedi alla scheda Esperimenti e seleziona l'esperimento. Viene visualizzata la dashboard di esecuzione dell'esperimento, in cui è possibile visualizzare le metriche e i grafici tracciati registrati per ogni esecuzione. In questo caso sono stati registrati i valori alfa e MSE.

  ![Eseguire i dettagli nello studio di Azure Machine LearningRun details in the Azure Machine Learning studio](./media/how-to-track-experiments/experiment-dashboard.png)

È possibile eseguire il drill-down a un'esecuzione specifica per visualizzarne gli output o i log oppure scaricare lo snapshot dell'esperimento inviato in modo da poter condividere la cartella dell'esperimento con altri utenti.

### <a name="viewing-charts-in-run-details"></a>Visualizzazione dei grafici nei dettagli dell'esecuzione

Esistono vari modi per usare le API di registrazione per registrare diversi tipi di metriche durante un'esecuzione e visualizzarle come grafici in Azure Machine Learning Studio.There are various ways to use the logging APIs to record different types of metrics during a run and view them as charts in Azure Machine Learning studio.

|Valore registrato|Codice di esempio| Visualizzazione nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|


## <a name="example-notebooks"></a>Notebook di esempio
I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Eseguire i passaggi seguenti per imparare a usare Azure Machine Learning SDK per Python:

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Altre informazioni su come [eseguire il training di modelli di PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
