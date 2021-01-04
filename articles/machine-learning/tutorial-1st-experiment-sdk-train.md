---
title: 'Esercitazione: Eseguire il training del primo modello di Machine Learning - Python'
titleSuffix: Azure Machine Learning
description: La terza parte della serie introduttiva su Azure Machine Learning illustra come eseguire il training di un modello di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: df511e79b73256833ec54c5906bb6acbc852bc46
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739621"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Esercitazione: Eseguire il training del primo modello di Machine Learning (parte 3 di 4)

Questa esercitazione illustra come eseguire il training di un modello di Machine Learning in Azure Machine Learning.

Questa esercitazione è la *terza di una serie in quattro parti* in cui vengono illustrati i concetti fondamentali su Azure Machine Learning e vengono completate attività di Machine Learning basate su processi in Azure. Questa esercitazione si basa sull'attività completata nella [Parte 1: Configurare](tutorial-1st-experiment-sdk-setup-local.md) e [Parte 2: Eseguire "Hello world!"](tutorial-1st-experiment-hello-world.md) della serie.

In questa esercitazione si eseguirà il passaggio successivo inviando uno script che esegue il training di un modello di Machine Learning. Questo esempio illustra come Azure Machine Learning renda possibile un comportamento coerente tra il debug locale e le esecuzioni remote.

In questa esercitazione:

> [!div class="checklist"]
> * Creare uno script di training.
> * Usare Conda per definire un ambiente di Azure Machine Learning.
> * Creare uno script di controllo.
> * Comprendere le classi di Azure Machine Learning (`Environment`, `Run`, `Metrics`).
> * Inviare ed eseguire lo script di training.
> * Visualizzare l'output del codice nel cloud.
> * Registrare le metriche in Azure Machine Learning.
> * Visualizzare le metriche nel cloud.

## <a name="prerequisites"></a>Prerequisiti

* Completamento della [parte 2](tutorial-1st-experiment-hello-world.md) della serie.
* Conoscenze di base del linguaggio Python e dei flussi di lavoro di Machine Learning.
* Ambiente di sviluppo locale, come Visual Studio Code, Jupyter o PyCharm.
* Python (versione 3.5-3.7).

## <a name="create-training-scripts"></a>Creare gli script di training

Prima di tutto si definisce l'architettura della rete neurale in un file `model.py`. Tutto il codice di training, incluso `model.py`, verrà inserito nella sottodirectory `src`.

Il codice seguente è tratto da [questo esempio introduttivo](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) di PyTorch. Si noti che i concetti su Azure Machine Learning si applicano a qualsiasi codice di Machine Learning, non solo a PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Successivamente si definisce lo script di training. Questo script scarica il set di dati CIFAR10 usando le API `torchvision.dataset` di PyTorch, configura la rete definita in `model.py` e ne esegue il training per due periodi usando il metodo SGD standard e la perdita di entropia incrociata.

Creare uno script `train.py` nella sottodirectory `src`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

È stata creata la struttura di directory seguente:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

> [!div class="nextstepaction"]
> [Gli script di training sono stati creati](?success=create-scripts#environment) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-python-environment"></a><a name="environment"></a> Creare un ambiente Python

A scopo dimostrativo, verrà usato un ambiente Conda. I passaggi per un ambiente virtuale pip sono quasi identici.

Creare un file denominato `pytorch-env.yml` nella directory nascosta `.azureml`:

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Questo ambiente ha tutte le dipendenze richieste dal modello e dallo script di training. Si noti che non esistono dipendenze da Azure Machine Learning SDK per Python.

> [!div class="nextstepaction"]
> [Il file di ambiente è stato creato](?success=create-env-file#test-local) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Test in locale

Usare il codice seguente per testare le esecuzioni di script in locale in questo ambiente:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Dopo aver eseguito questo script, i dati risulteranno scaricati in una directory denominata `tutorial/data`.

> [!div class="nextstepaction"]
> [Il file di ambiente è stato creato](?success=test-local#create-local) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> Creare lo script di controllo

La differenza tra lo script di controllo seguente e quello usato per inviare "Hello World!" è che vengono aggiunte due righe in più per impostare l'ambiente.

Creare un nuovo file di Python denominato `04-run-pytorch.py`nella directory `tutorial`:

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Informazioni sulle modifiche al codice

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning si basa sul concetto di [ambiente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) per rappresentare un ambiente Python riproducibile e dotato di versione per l'esecuzione di esperimenti. La creazione di un ambiente da un ambiente Conda o pip locale è semplice.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Aggiunge l'ambiente a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Lo script di controllo è stato creato](?success=control-script#submit) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Inviare l'esecuzione ad Azure Machine Learning

Se l'ambiente locale è stato cambiato, assicurarsi di tornare in un ambiente con Azure Machine Learning SDK per Python installato e in esecuzione.

Eseguire quindi:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Alla prima esecuzione di questo script, Azure Machine Learning creerà una nuova immagine Docker dall'ambiente PyTorch. L'intera esecuzione potrebbe richiedere da 5 a 10 minuti. 
>
> È possibile visualizzare i log di compilazione di Docker in Azure Machine Learning Studio. Seguire il collegamento a Studio, selezionare la scheda **Output e log** e quindi selezionare `20_image_build_log.txt`.
>
> Questa immagine verrà riutilizzata nelle esecuzioni future per renderle molto più veloci.

Una volta creata l'immagine, selezionare `70_driver_log.txt` per visualizzare l'output dello script di training.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Se viene visualizzato l'errore `Your total snapshot size exceeds the limit`, significa che la directory `data` si trova nella cartella `source_directory` usata in `ScriptRunConfig`.
>
> Spostare `data` all'esterno di `src`.

Gli ambienti possono essere registrati in un'area di lavoro con `env.register(ws)`. Possono quindi essere facilmente condivisi, riutilizzati e dotati di versione. Gli ambienti facilitano la riproduzione dei risultati precedenti e la collaborazione con il team.

Azure Machine Learning gestisce anche una raccolta di ambienti curati. Questi ambienti coprono scenari comuni di Machine Learning e sono supportati da immagini Docker memorizzate nella cache. Le immagini Docker memorizzate nella cache velocizzano la prima esecuzione remota.

L'uso di ambienti registrati consente quindi di risparmiare tempo. Per altre informazioni, vedere [Come usare gli ambienti](./how-to-use-environments.md).

> [!div class="nextstepaction"]
> [L'esecuzione è stata inviata](?success=test-w-environment#log) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Registrare le metriche di training

Dopo aver creato un training del modello in Azure Machine Learning, è possibile iniziare a tenere traccia di alcune metriche delle prestazioni.

Lo script di training corrente stampa le metriche nel terminale. Azure Machine Learning offre un meccanismo per registrare le metriche con più funzionalità. Aggiungendo alcune righe di codice, è possibile visualizzare le metriche in Studio e confrontarle in più esecuzioni.

### <a name="modify-trainpy-to-include-logging"></a>Modificare `train.py` per includere la registrazione

Modificare lo script `train.py` per includere altre due righe di codice:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Informazioni sulle due righe di codice aggiuntive

In `train.py` si accede all'oggetto run dall'_interno_ dello script di training usando il metodo `Run.get_context()` e lo si usa per registrare le metriche:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Le metriche in Azure Machine Learning sono:

- Organizzate per esperimento ed esecuzione, per cui è facile tenerne traccia e confrontarle.
- Dotate di interfaccia utente per poter visualizzare le prestazioni del training in Studio.
- Progettate per la scalabilità, per continuare a sfruttare questi vantaggi anche quando si eseguono centinaia di esperimenti.

> [!div class="nextstepaction"]
> [Lo script train.py è stato modificato](?success=modify-train#log) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Aggiornare il file dell'ambiente Conda

Lo script `train.py` ha appena acquisito una nuova dipendenza da `azureml.core`. Aggiornare `pytorch-env.yml` in modo da riflettere questa modifica:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Il file di ambiente è stato aggiornato](?success=update-environment#submit-again) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Inviare l'esecuzione ad Azure Machine Learning
Inviare ancora una volta questo script:

```bash
python 04-run-pytorch.py
```

Questa volta, quando si visita Studio, passare alla scheda **Metriche**, dove è ora possibile visualizzare gli aggiornamenti dinamici sulla perdita di training del modello.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Grafico della perdita di training nella scheda Metriche":::.

> [!div class="nextstepaction"]
> [L'esecuzione è stata reinviata](?success=resubmit-with-logging#next-steps) [Si è verificato un problema](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Passaggi successivi

In questa sessione è stato eseguito l'aggiornamento da uno script "Hello World!" di base a uno script di training più realistico che ha richiesto l'esecuzione di un ambiente Python specifico. Si è visto come trasferire un ambiente Conda locale nel cloud con gli ambienti Azure Machine Learning. Si è infine visto come con poche righe di codice sia possibile registrare le metriche in Azure Machine Learning.

Esistono altri modi per creare ambienti di Azure Machine Learning, ad esempio [da un file pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) o anche [da un ambiente Conda locale esistente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

Nella sessione successiva si vedrà come usare i dati in Azure Machine Learning caricando in Azure il set di dati CIFAR10.

> [!div class="nextstepaction"]
> [Esercitazione: Usare i dati personali](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Se si sceglie di terminare questa serie di esercitazioni senza procedere con il passaggio successivo, assicurarsi di [pulire le risorse](tutorial-1st-experiment-bring-data.md#clean-up-resources).
