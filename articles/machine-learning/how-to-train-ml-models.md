---
title: Eseguire il training di modelli di Machine Learning con oggetti Estimator
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training a nodo singolo e distribuito di modelli tradizionali di apprendimento automatico e apprendimento avanzato usando Azure Machine Learning classe Estimator
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: a9a3c10687b92d946fccb282f2eda4af637cf2ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686763"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Eseguire il training di modelli con Azure Machine Learning usando l'oggetto Estimator
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Con Azure Machine Learning, è possibile inviare facilmente lo script di training a [diverse destinazioni di calcolo](how-to-set-up-training-targets.md#compute-targets-for-training), usando un [oggetto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e un [oggetto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Questo modello offre una notevole della flessibilità e il massimo controllo.

Per semplificare il training dei modelli di Deep Learning, Python SDK di Azure Machine Learning fornisce un'astrazione alternativa di più alto livello, la classe Estimator, che consente agli utenti di creare con facilità configurazioni di esecuzione. È possibile creare e usare uno strumento di [stima](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di training usando qualsiasi framework di apprendimento scelto (ad esempio Scikit-learn) in qualsiasi destinazione di calcolo scelta, sia che si tratti del computer locale, di una singola macchina virtuale in Azure o di un cluster GPU in Azure. Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche i rispettivi estimatori [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) per semplificare l'uso di questi Framework.

## <a name="train-with-an-estimator"></a>Eseguire il training con un Estimator

Dopo aver creato l'[area di lavoro](concept-workspace.md) e configurato l'[ambiente di sviluppo](how-to-configure-environment.md), il training di un modello in Azure Machine Learning prevede i passaggi seguenti:  
1. Creare una [destinazione di calcolo remota](how-to-set-up-training-targets.md) (è anche possibile usare il computer locale come destinazione di calcolo)
2. Caricare i [dati di training](how-to-access-data.md) nell'archivio dati (facoltativo)
3. Creare [lo script di training](tutorial-train-models-with-aml.md#create-a-training-script)
4. Crea un oggetto `Estimator`
5. Inviare l'oggetto Estimator a un oggetto Experiment nell'area di lavoro

Questo articolo è incentrato sui passaggi 4-5. Per i passaggi da 1 a 3, fare riferimento all'[esercitazione Eseguire il training di un modello](tutorial-train-models-with-aml.md) per un esempio.

### <a name="single-node-training"></a>Training su nodo singolo

Usare un `Estimator` per un training su nodo singolo su risorse di calcolo remote in Azure per un modello scikit-learn. È necessario aver già creato l' [compute target](how-to-set-up-training-targets.md#amlcompute) oggetto `compute_target` di destinazione di calcolo e l'oggetto [FileDataset](how-to-create-register-datasets.md) `ds`filedataset.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Questo frammento di codice specifica i parametri seguenti per il costruttore di `Estimator`.

Parametro | Descrizione
--|--
`source_directory`| Directory locale contenente tutto il codice necessario per il processo di training. Questa cartella viene copiata dal computer locale al calcolo remoto.
`script_params`| Dizionario che specifica gli argomenti della riga di comando da passare allo script `entry_script`di training, sotto forma `<command-line argument, value>` di coppie. Per specificare un flag dettagliato in `script_params`, utilizzare. `<command-line argument, "">`
`compute_target`| Destinazione di calcolo remota in cui verrà eseguito lo script di training, in questo caso un cluster Azure Machine Learning calcolo ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). Si noti che anche se il cluster AmlCompute è la destinazione di uso comune, è anche possibile scegliere altri tipi di destinazione di calcolo, ad esempio macchine virtuali di Azure o persino computer locale.
`entry_script`| Percorso file (relativo a `source_directory`) dello script di training da eseguire nelle risorse di calcolo remote. Questo file e tutti i file aggiuntivi da cui dipende devono trovarsi in questa cartella.
`conda_packages`| Elenco dei pacchetti Python da installare tramite Conda, necessari per lo script di training.  

Il costruttore dispone di un altro `pip_packages` parametro denominato usato per tutti i pacchetti PIP necessari.

Ora che è stato creato l'oggetto `Estimator`, inviare il processo di training per l'esecuzione sulle risorse di calcolo remote tramite una chiamata alla funzione `submit` sull'oggetto [Experiment](concept-azure-machine-learning-architecture.md#experiments)`experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Cartelle speciali** Due cartelle *outputs* e *logs*, hanno un trattamento speciale in Azure Machine Learning. Durante il training, se si scrivono file in cartelle denominate *outputs* e *logs* relative alla directory radice (rispettivamente `./outputs` e `./logs`), i file verranno caricati automaticamente nella cronologia di esecuzione e vi si potrà accedere al termine dell'esecuzione.
>
> Per creare artefatti creati durante il training (ad esempio file modello, checkpoint, file di dati o immagini tracciate), scriverli nella cartella `./outputs`.
>
> Analogamente, è possibile scrivere tutti i log del training eseguito nella cartella `./logs`. Per usare l'[integrazione TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) di Azure Machine Learning, assicurarsi di scrivere i log di TensorBoard in questa cartella. Mentre è in corso l'esecuzione, sarà possibile avviare TensorBoard e riprodurre in streaming questi log.  In seguito sarà anche possibile ripristinare i log di una qualsiasi delle esecuzioni precedenti.
>
> Ad esempio, per scaricare un file scritto nella cartella *outputs* del computer locale dopo l'esecuzione del training remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Training distribuito e immagini Docker personalizzate

Esistono due scenari di training aggiuntivi che è possibile eseguire con `Estimator`:
* Uso di un'immagine Docker personalizzata
* Training distribuito in un cluster con più nodi

Il codice seguente mostra come eseguire il training distribuito per un modello Keras. Inoltre, invece di usare le immagini di Azure Machine Learning predefinite, viene specificata un'immagine Docker personalizzata dall'hub Docker `continuumio/miniconda` per il training.

È necessario avere già creato l'oggetto [destinazione di calcolo](how-to-set-up-training-targets.md#amlcompute)`compute_target`. È possibile creare l'oggetto Estimator come segue:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Il codice precedente espone i nuovi parametri seguenti al costruttore di `Estimator`:

Parametro | Descrizione | Predefinito
--|--|--
`custom_docker_image`| Nome dell'immagine da usare. Specificare solo immagini disponibili in repository Docker pubblici (in questo caso Hub Docker). Per usare un'immagine da un repository Docker privato, usare invece il parametro `environment_definition` del costruttore. [Vedere l'esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Numero di nodi da usare per il processo di training. | `1`
`process_count_per_node`| Numero di processi (o "ruoli di lavoro") da eseguire in ogni nodo. In questo caso si usano i `2` GPU disponibili in ogni nodo.| `1`
`distributed_training`| Oggetto [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) per l'avvio della formazione distribuita tramite il back-end MPI.  | `None`


Infine, inviare il processo di training:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrazione di un modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).

L'esecuzione del codice seguente registrerà il modello nell'area di lavoro e la renderà disponibile per il riferimento in base al nome nei contesti di calcolo remoti o negli script di distribuzione. Per [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) ulteriori informazioni e parametri aggiuntivi, vedere la documentazione di riferimento.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Rilevamento e integrazione di GitHub

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per ulteriori informazioni, vedere [integrazione di Git per Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Esempi
Per un notebook che mostra le nozioni di base di un modello di Estimator, vedere:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Per un notebook che addestra un modello Scikit-learn usando Estimator, vedere:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Per i notebook sui modelli di training usando gli estimatori specifici del Framework per l'apprendimento avanzato, vedere:

* [How-to-use-azureml/ml-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Eseguire il training di modelli di PyTorch](how-to-train-pytorch.md)
* [Eseguire il training di modelli di TensorFlow](how-to-train-tensorflow.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Creazione e gestione di ambienti per il training e la distribuzione](how-to-use-environments.md)