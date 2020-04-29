---
title: Visualizzare esperimenti con TensorBoard
titleSuffix: Azure Machine Learning
description: Avviare TensorBoard per visualizzare le cronologie di esecuzione degli esperimenti e identificare le aree potenziali per l'ottimizzazione e la ripetizione del training degli iperparametri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78195380"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizzare le esecuzioni e le metriche degli esperimenti con TensorBoard e Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come visualizzare le esecuzioni dell'esperimento e le metriche in TensorBoard usando [il `tensorboard` pacchetto](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) nell'SDK principale del Azure Machine Learning. Una volta esaminate le esecuzioni dell'esperimento, è possibile ottimizzare e ripetere il training dei modelli di machine learning.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) è una suite di applicazioni Web per esaminare e comprendere le prestazioni e la struttura dell'esperimento.

La modalità di avvio di TensorBoard con Azure Machine Learning esperimenti dipende dal tipo di esperimento:
+ Se l'esperimento genera in modo nativo i file di log che possono essere utilizzati da TensorBoard, ad esempio PyTorch, Chainer e TensorFlow esperimenti, è possibile [avviare TensorBoard direttamente](#direct) dalla cronologia di esecuzione dell'esperimento. 

+ Per gli esperimenti che non restituiscono in modo nativo file TensorBoard utilizzabili, come ad esempio Scikit-learn o Azure Machine Learning esperimenti, usare [il `export_to_tensorboard()` metodo](#export) per esportare le cronologie di esecuzione come log di TensorBoard e avviare TensorBoard da questa posizione. 

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato al monitoraggio dell'utilizzo delle risorse e degli eventi di Azure Machine Learning, ad esempio le quote, le esecuzioni di training completate o le distribuzioni del modello completate, vedere [monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

* Per avviare TensorBoard e visualizzare le cronologie di esecuzione dell'esperimento, è necessario che gli esperimenti abbiano abilitato in precedenza la registrazione per tenere traccia delle metriche e delle prestazioni.  

* Il codice di questo documento può essere eseguito in uno degli ambienti seguenti: 

    * Azure Machine Learning istanza di calcolo: nessun download o installazione necessaria

        * Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.

        * Nella cartella Samples sul server notebook trovare due notebook completati e espansi passando a queste directory:
            * **How-to-use-azureml > training-with-Deep-Learning > Export-Run-History-to-tensorboard > Export-Run-History-to-tensorboard. ipynb**

            * **How-to-use-azureml > Track-and-monitor-Experiments > tensorboard. ipynb**

    * Il proprio server Juptyer notebook
       * [Installare l'SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) con `tensorboard` il
        * [Creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).  
        * [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opzione 1: visualizzare direttamente la cronologia di esecuzione in TensorBoard

Questa opzione è valida per gli esperimenti che restituiscono in modo nativo i file di log utilizzabili da TensorBoard, ad esempio gli esperimenti PyTorch, Chainer e TensorFlow. Se questo non è il caso dell'esperimento, usare invece [il `export_to_tensorboard()` metodo](#export) .

Il codice di esempio seguente usa l' [esperimento demo MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) del repository di TensorFlow in una destinazione di calcolo remota, Azure Machine Learning COMPUTE. Si eseguirà quindi il training del modello con lo strumento di [stima TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)personalizzato dell'SDK e quindi si avvierà TensorBoard in questo esperimento TensorFlow, ovvero un esperimento che restituisce in modo nativo i file di evento TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Imposta il nome dell'esperimento e crea la cartella del progetto

Qui viene denominato l'esperimento e viene creata la relativa cartella. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Scaricare il codice dell'esperimento demo di TensorFlow

Il repository di TensorFlow include una demo MNIST con una vasta strumentazione TensorBoard. Non è necessario modificare il codice di questa demo per l'uso con Azure Machine Learning. Nel codice seguente viene scaricato il codice MNIST e salvato nella cartella dell'esperimento appena creata.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Nel file di codice MNIST, mnist_with_summaries. py, si noti che sono presenti righe che `tf.summary.scalar()`chiamano `tf.summary.histogram()`, `tf.summary.FileWriter()` e così via. Questi metodi raggruppano, registrano e contrassegnano le metriche chiave degli esperimenti nella cronologia di esecuzione. `tf.summary.FileWriter()` È particolarmente importante perché serializza i dati dalle metriche dell'esperimento registrato, che consente a TensorBoard di generare le visualizzazioni.

 ### <a name="configure-experiment"></a>Configurare l'esperimento

Nell'esempio seguente si configura l'esperimento e si configurano le directory per i log e i dati. Questi log verranno caricati nel servizio artefatto, che TensorBoard l'accesso in un secondo momento.

>[!Note]
> Per questo esempio di TensorFlow, sarà necessario installare TensorFlow nel computer locale. Inoltre, il modulo TensorBoard, ovvero quello incluso in TensorFlow, deve essere accessibile al kernel del notebook, perché il computer locale è quello che esegue TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Creare un cluster per l'esperimento
Per questo esperimento viene creato un cluster AmlCompute, tuttavia gli esperimenti possono essere creati in qualsiasi ambiente e si è ancora in grado di avviare TensorBoard con la cronologia di esecuzione dell'esperimento. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Invia esecuzione con TensorFlow Estimator

TensorFlow Estimator fornisce un modo semplice per avviare un processo di formazione TensorFlow su una destinazione di calcolo. Viene implementato tramite la classe generica [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , che può essere usata per supportare qualsiasi Framework. Per altre informazioni sui modelli di training con lo strumento di stima generico, vedere eseguire il training dei [modelli con Azure Machine Learning usando Estimator](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Avviare TensorBoard

È possibile avviare TensorBoard durante l'esecuzione o dopo il completamento. Nell'esempio seguente viene creata un'istanza dell'oggetto TensorBoard, `tb`, che accetta la cronologia di esecuzione dell'esperimento caricata in `run`, quindi avvia TensorBoard con `start()` il metodo. 
  
Il [Costruttore TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) accetta una matrice di esecuzioni, quindi assicurarsi di passarla come matrice a elemento singolo.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Anche se in questo esempio si usa TensorFlow, TensorBoard può essere usato con facilità con i modelli PyTorch o Chainer. TensorFlow deve essere disponibile nel computer in cui è in esecuzione TensorBoard, ma non è necessario nel computer che esegue i calcoli PyTorch o Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opzione 2: esportare la cronologia come log per la visualizzazione in TensorBoard

Il codice seguente configura un esperimento di esempio, avvia il processo di registrazione usando le API della cronologia di esecuzione Azure Machine Learning ed Esporta la cronologia di esecuzione dell'esperimento nei log utilizzabili da TensorBoard per la visualizzazione. 

### <a name="set-up-experiment"></a>Configurare l'esperimento

Il codice seguente imposta un nuovo esperimento e denomina la directory `root_run`di esecuzione. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Qui viene caricato il set di dati del diabete, un set di dati di piccole dimensioni integrato con Scikit-learn, che viene suddiviso in set di training e di testing.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Eseguire metriche di esperimento e log

Per questo codice, viene eseguito il training di un modello di regressione lineare e la metrica della chiave del log `alpha`, il coefficiente alfa, e `mse`l'errore quadratico medio,, nella cronologia di esecuzione.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Esporta esecuzioni in TensorBoard

Con il metodo [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) dell'SDK, è possibile esportare la cronologia di esecuzione dell'esperimento di Azure Machine Learning nei log di tensorboard, in modo che sia possibile visualizzarli tramite tensorboard.  

Nel codice seguente viene creata la cartella `logdir` nella directory di lavoro corrente. In questa cartella si esporterà la cronologia di esecuzione dell'esperimento e `root_run` i log da e quindi si contrassegnerà l'esecuzione come completata. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 È anche possibile esportare una particolare esecuzione in TensorBoard specificando il nome dell'esecuzione`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Avviare e arrestare TensorBoard
Una volta esportata la cronologia di esecuzione di questo esperimento, è possibile avviare TensorBoard con il metodo [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Al termine, assicurarsi di chiamare il metodo [Stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) dell'oggetto TensorBoard. In caso contrario, TensorBoard continuerà a essere eseguito fino a quando non si arresta il kernel del notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passaggi successivi

In questa procedura, sono stati creati due esperimenti e si è appreso come avviare TensorBoard con le cronologie di esecuzione per identificare le aree per la potenziale ottimizzazione e la ripetizione del training. 

* Se si è soddisfatti del modello, andare all'articolo [come distribuire un modello](how-to-deploy-and-where.md) . 
* Altre informazioni sull' [ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md). 
