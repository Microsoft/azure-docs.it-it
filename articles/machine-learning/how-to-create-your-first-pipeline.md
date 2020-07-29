---
title: Creare, eseguire e monitorare pipeline di Machine Learning
titleSuffix: Azure Machine Learning
description: Creare ed eseguire una pipeline di Machine Learning con l'SDK di Azure Machine Learning per Python. Usare le pipeline di ML per creare e gestire i flussi di lavoro che uniscono le fasi di Machine Learning (ML). Queste fasi includono la preparazione dei dati, il training del modello, la distribuzione del modello e l'inferenza e il punteggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: dff86176bac56063c9b588fb27e3549069ed380e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326359"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare, pubblicare, eseguire e monitorare una [pipeline di Machine Learning](concept-ml-pipelines.md) usando l'[SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Usare le **pipeline** di machine learning per creare un flusso di lavoro che unisce varie fasi di machine learning e quindi pubblicare tale pipeline nell'area di lavoro di Azure Machine Learning per accedere in un secondo momento o condividerla con altri utenti.  Le pipeline di ML sono ideali per gli scenari di assegnazione dei punteggi in batch, l'uso di diversi calcoli, il riutilizzo dei passaggi anziché la loro esecuzione, nonché la condivisione di flussi di lavoro ML con altri utenti.

Sebbene sia possibile usare un tipo diverso di pipeline denominato [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) per l'automazione ci/CD di attività di Machine Learning, tale tipo di pipeline non viene mai archiviato nell'area di lavoro. [Confrontare queste diverse pipeline](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Ogni fase di una pipeline ML, ad esempio la preparazione dei dati e il training del modello, può includere uno o più passaggi.

Le pipeline di ML create sono visibili ai membri dell' [area di lavoro](how-to-manage-workspace.md)Azure Machine Learning. 

Le pipeline ML usano le destinazioni di calcolo Remote per il calcolo e l'archiviazione dei dati intermedi e finali associati alla pipeline. Possono leggere e scrivere dati in e da percorsi di [archiviazione di Azure](https://docs.microsoft.com/azure/storage/) supportati.

Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) che conterrà tutte le risorse della pipeline.

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) con l'SDK già installato.

Per iniziare, aggiungere l'area di lavoro:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Configurare le risorse di Machine Learning

Creare le risorse necessarie per eseguire una pipeline di ML:

* Configurare un archivio dati che verrà usato per accedere ai dati necessari nei passaggi della pipeline.

* Configurare un `Dataset` oggetto in modo che punti ai dati persistenti che si trovano in o è accessibile in un archivio dati. Configurare un `PipelineData` oggetto per i dati temporanei passati tra i passaggi della pipeline. 

* Configurare le [destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) in cui verranno eseguiti i passaggi della pipeline.

### <a name="set-up-a-datastore"></a>Configurare un archivio dati

Un archivio dati contiene i dati a cui accede la pipeline. Ogni area di lavoro ha un archivio dati predefinito. È possibile registrare altri archivi dati. 

Quando si crea l'area di lavoro, [file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e l' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) sono collegati all'area di lavoro. Un archivio dati predefinito è registrato per la connessione all'archivio BLOB di Azure. Per altre informazioni, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Caricare le directory o i file di dati nell'archivio dati per renderli accessibili dalle pipeline. Questo esempio usa l'archiviazione BLOB come archivio dati:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Una pipeline è costituita da uno o più passaggi. Un passaggio è un'unità di esecuzione in una destinazione di calcolo. I passaggi possono utilizzare origini dati e produrre dati "intermedi". Un passaggio può creare dati, ad esempio un modello, una directory con modello e file dipendenti o dati temporanei. Questi dati sono quindi disponibili per altri passaggi successivi nella pipeline.

Per altre informazioni sulla connessione della pipeline ai dati, vedere gli articoli [come accedere ai dati](how-to-access-data.md) e [come registrare i set](how-to-create-register-datasets.md)di dati. 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Configurare i dati `Dataset` mediante `PipelineData` oggetti e

Si è appena creata un'origine dati a cui si può fare riferimento in una pipeline come input per un passaggio. Il modo migliore per fornire i dati a una pipeline è un oggetto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) . L' `Dataset` oggetto punta ai dati presenti in o è accessibile da un archivio dati o da un URL Web. La `Dataset` classe è astratta, quindi si creerà un'istanza di un `FileDataset` (che fa riferimento a uno o più file) o di un oggetto `TabularDataset` creato da da uno o più file con colonne di dati delimitate.

`Dataset`gli oggetti supportano il controllo delle versioni, le differenze e le statistiche di riepilogo. `Dataset`le istanze di vengono valutate in modo differito, ad esempio i generatori Python, ed è efficiente sottoposte a subset mediante suddivisione o filtro. 

Si crea un oggetto `Dataset` usando metodi come [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) o [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

I dati intermedi (o output di un passaggio) sono rappresentati da un oggetto [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` viene generato come output di un passaggio e usato come input di uno o più passaggi successivi. `PipelineData` introduce una dipendenza dei dati tra i vari passaggi e crea un ordine di esecuzione implicito nella pipeline. Questo oggetto verrà usato in un secondo momento durante la creazione di passaggi della pipeline.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Per informazioni dettagliate e codice di esempio per l'uso di set di dati e dati della pipeline, vedere la pagina relativa allo sviluppo di [dati in e tra i passaggi della pipeline di ml (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Configurare una destinazione di calcolo

In Azure Machine Learning il termine __ambiente di calcolo__ (o __destinazione di calcolo__) si riferisce ai computer o ai cluster che eseguono i passaggi di calcolo nella pipeline di Machine Learning.   Consultare l'articolo sulle[destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md) per un elenco completo delle destinazioni di calcolo e per informazioni su come crearle e collegarle all'area di lavoro.  Il processo per la creazione e il collegamento di una destinazione di calcolo è lo stesso indipendentemente dal fatto che si stia eseguendo il passaggio di una pipeline o il training di un modello. Dopo aver creato e collegato la destinazione di calcolo, usare l'oggetto `ComputeTarget` nel [passaggio pipeline](#steps).

> [!IMPORTANT]
> L'esecuzione di operazioni di gestione su destinazioni di calcolo non è supportata all'interno di processi remoti. Poiché le pipeline di Machine Learning vengono inviate come processo remoto, non usare le operazioni di gestione in destinazioni di calcolo all'interno della pipeline.

Di seguito sono riportati esempi di creazione e collegamento di destinazioni di calcolo per:

* Ambiente di calcolo di Azure Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics.

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="azure-machine-learning-compute"></a>Ambiente di calcolo di Azure Machine Learning

È possibile creare un ambiente di calcolo di Azure Machine Learning per eseguire i passaggi.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

Creare un'area di lavoro di Azure Databricks prima di usarlo. Per creare una risorsa dell'area di lavoro, vedere il documento [eseguire un processo Spark in Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Per collegare Azure Databricks come destinazione di calcolo, fornire le informazioni seguenti:

* __Databricks nome di calcolo__: il nome che si vuole assegnare a questa risorsa di calcolo.
* __Nome area di lavoro databricks__: nome dell'area di lavoro Azure Databricks.
* __Token di accesso di databricks__: il token di accesso usato per l'autenticazione Azure Databricks. Per generare un token di accesso vedere il documento [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) (Autenticazione).

Il codice seguente illustra come associare Azure Databricks come destinazione di calcolo con Azure Machine Learning SDK (__l'area di lavoro databricks deve essere presente nella stessa sottoscrizione dell'area di lavoro di AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-databricks) in GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics.

Azure Data Lake Analytics è una piattaforma di analisi dei Big Data nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

Creare un account di Azure Data Lake Analytics prima di usarla. Per creare questa risorsa vedere il documento [Introduzione ad Azure Data Lake Analytics con il portale di Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Per connettere Data Lake Analytics come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

* __Nome del calcolo__: il nome da assegnare a questa risorsa di calcolo.
* __Gruppo di risorse__: il gruppo di risorse contenente l'account data Lake Analytics.
* __Nome account__: il nome dell'account data Lake Analytics.

Il codice seguente illustra in che modo connettere Data Lake Analytics come destinazione di calcolo:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-adla) in GitHub.

> [!TIP]
> Le pipeline di Azure Machine Learning possono funzionare solo con i dati archiviati nell'archivio dati predefinito dell'account Data Lake Analytics. Se i dati che è necessario utilizzare si trova in un archivio non predefinito, è possibile utilizzare un oggetto [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) per copiare i dati prima del training.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Creare i passaggi della pipeline

Dopo aver creato e collegato una destinazione di calcolo all'area di lavoro, è possibile definire un passaggio della pipeline. Con l'SDK di Azure Machine Learning sono disponibili numerosi passaggi predefiniti. Il modo più semplice di questi passaggi è [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), che esegue uno script Python in una destinazione di calcolo specificata:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

Il riutilizzo dei risultati precedenti ( `allow_reuse` ) è fondamentale quando si usano le pipeline in un ambiente di collaborazione, poiché l'eliminazione di riesecuzioni non necessarie offre flessibilità. Il riutilizzo è il comportamento predefinito quando il script_name, gli input e i parametri di un passaggio rimangono invariati. Quando l'output del passaggio viene riutilizzato, il processo non viene inviato al calcolo, bensì i risultati dell'esecuzione precedente sono immediatamente disponibili per l'esecuzione del passaggio successivo. Se `allow_reuse` è impostato su false, durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio. 

Dopo la definizione dei passaggi, si crea la pipeline usando alcuni o tutti i passaggi definiti.

> [!NOTE]
> Nessun file o dati viene caricato in Azure Machine Learning quando si definiscono i passaggi o si compila la pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

L'esempio seguente mostra come usare la destinazione di calcolo di Azure Databricks creata in precedenza: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Uso di un set di dati 

I set di dati creati da archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL possono essere usati come input per qualsiasi passaggio della pipeline. È possibile scrivere l'output in [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)o se si desidera scrivere dati in un archivio dati specifico, utilizzare [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). 

> [!IMPORTANT]
> La scrittura di dati di output in un archivio dati usando PipelineData è supportata solo per gli archivi dati di Azure BLOB e condivisione file di Azure. Questa funzionalità non è al momento supportata per gli [archivi dati ADLS di generazione 2](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Il set di dati viene quindi recuperato nella pipeline utilizzando il dizionario [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

`Run.get_context()`È opportuno evidenziare la riga. Questa funzione recupera un oggetto `Run` che rappresenta l'esecuzione sperimentale corrente. Nell'esempio precedente viene usato per recuperare un set di dati registrato. Un altro uso comune dell' `Run` oggetto consiste nel recuperare sia l'esperimento che l'area di lavoro in cui si trova l'esperimento: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Per informazioni più dettagliate, incluse le modalità alternative per passare e accedere ai dati, vedere lo stato di [trasferimento dei dati in e tra i passaggi della pipeline di ml (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Inviare la pipeline

Quando si invia la pipeline, Azure Machine Learning controlla le dipendenze per ogni passaggio e carica uno snapshot della directory di origine specificata. Se la directory di origine non è specificata, viene caricata la directory locale corrente. Lo snapshot viene inoltre archiviato come parte dell'esperimento nell'area di lavoro.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Per altre informazioni, vedere [Snapshot](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando si esegue una pipeline per la prima volta, Azure Machine Learning:

* Scarica lo snapshot del progetto nella destinazione di calcolo dalla risorsa di archiviazione BLOB associata all'area di lavoro.
* Crea un'immagine Docker corrispondente a ogni passaggio nella pipeline.
* Scarica l'immagine Docker per ogni passaggio nella destinazione di calcolo dal registro contenitori.
* Configura l'accesso agli `Dataset` `PipelineData` oggetti e. Per come `as_mount()` modalità di accesso, il fusibile viene usato per fornire l'accesso virtuale. Se il montaggio non è supportato o se l'utente ha specificato l'accesso come `as_download()` , i dati vengono invece copiati nella destinazione di calcolo.
* Esegue il passaggio nella destinazione di calcolo specificata nella definizione del passaggio. 
* Crea gli artefatti, ad esempio i log, stdout e stderr, le metriche e l'output specificati dal passaggio. Questi artefatti vengono quindi caricati e conservati nell'archivio dati predefinito dell'utente.

![Diagramma dell'esecuzione di un esperimento come pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Per ulteriori informazioni, vedere la Guida di riferimento alla [classe Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Visualizzare i risultati di una pipeline

Vedere l'elenco di tutte le pipeline e i relativi dettagli di esecuzione in studio:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visualizzare l'area di lavoro](how-to-manage-workspace.md#view).

1. A sinistra selezionare **pipeline** per visualizzare tutte le esecuzioni della pipeline.
 ![elenco delle pipeline di Machine Learning](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selezionare una pipeline specifica per visualizzare i risultati dell'esecuzione.

## <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Pubblicare una pipeline

È possibile pubblicare una pipeline in modo da eseguirla in un secondo momento con input diversi. Per consentire all'endpoint REST di una pipeline già pubblicata di accettare parametri, è necessario impostare parametri per la pipeline prima della pubblicazione.

1. Per creare un parametro per la pipeline, usare un oggetto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) con un valore predefinito.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Aggiungere l'oggetto `PipelineParameter` come parametro a uno dei passaggi della pipeline, come indicato di seguito:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Pubblicare la pipeline che accetterà un parametro quando richiamata.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Eseguire una pipeline pubblicata

Tutte le pipeline pubblicate hanno un endpoint REST Con l'endpoint della pipeline, è possibile attivare un'esecuzione della pipeline da qualsiasi sistema esterno, inclusi i client non Python. Questo endpoint consente la "ripetibilità gestita" in scenari di ripetizione del training e assegnazione di punteggi in batch.

Per richiamare l'esecuzione della pipeline precedente, è necessario un token di intestazione Azure Active Directory Authentication, come descritto in [AzureCliAuthentication Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) Reference o ottenere altri dettagli nell' [autenticazione in Azure Machine Learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Creare un endpoint della pipeline con versione

È possibile creare un endpoint della pipeline con più pipeline pubblicate. Questa operazione può essere usata come una pipeline pubblicata ma fornisce un endpoint REST fisso durante l'iterazione e l'aggiornamento delle pipeline di ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Inviare un processo a un endpoint della pipeline

È possibile inviare un processo alla versione predefinita di un endpoint della pipeline:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

È anche possibile inviare un processo a una versione specifica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

La stessa operazione può essere eseguita usando l'API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Usare le pipeline pubblicate in studio

È anche possibile eseguire una pipeline pubblicata da studio:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visualizzare l'area di lavoro](how-to-manage-workspace.md#view).

1. A sinistra selezionare **endpoint**.

1. Nella parte superiore selezionare **endpoint della pipeline**.
 ![elenco di pipeline pubblicate di Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Consente di selezionare una pipeline specifica per l'esecuzione, l'utilizzo o la verifica dei risultati delle esecuzioni precedenti dell'endpoint della pipeline.

### <a name="disable-a-published-pipeline"></a>Disabilitare una pipeline pubblicata

Per nascondere una pipeline dall'elenco di pipeline pubblicate, è possibile disabilitarla, in studio o nell'SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

È possibile abilitarla di nuovo con `p.enable()` . Per altre informazioni, vedere riferimento alla [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .

## <a name="caching--reuse"></a>Memorizzazione nella cache & riutilizzo  

Per ottimizzare e personalizzare il comportamento delle pipeline, è possibile eseguire alcune operazioni per la memorizzazione nella cache e il riutilizzo. Ad esempio, è possibile scegliere di:
+ **Disattivare il riutilizzo predefinito del passaggio Esegui output** impostando `allow_reuse=False` durante la [definizione del passaggio](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Il riutilizzo è fondamentale quando si utilizzano pipeline in un ambiente di collaborazione, perché l'eliminazione di esecuzioni non necessarie offre flessibilità. Tuttavia, è possibile rifiutare esplicitamente il riutilizzo.
+ **Forzare la rigenerazione dell'output per tutti i passaggi di un'esecuzione** con`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Per impostazione predefinita, `allow_reuse` per i passaggi è abilitato e viene eseguito l' `source_directory` hashing dell'oggetto specificato nella definizione del passaggio. Quindi, se lo script per un determinato passaggio rimane lo stesso ( `script_name` , gli input e i parametri) e nessun altro elemento in ` source_directory` è stato modificato, viene riutilizzato l'output di un'esecuzione del passaggio precedente, il processo non viene inviato al calcolo e i risultati dell'esecuzione precedente sono immediatamente disponibili al passaggio successivo.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Passaggi successivi

- Usare [questi notebook di Jupyter in GitHub](https://aka.ms/aml-pipeline-readme) per esplorare più in dettaglio le pipeline di Machine Learning.
- Vedere la Guida di riferimento per l'SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Vedere le [procedure](how-to-debug-pipelines.md) consigliate per il debug e la risoluzione dei problemi relativi alle pipeline.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
