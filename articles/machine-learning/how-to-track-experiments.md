---
title: Registrare gli esperimenti e le metriche di ML
titleSuffix: Azure Machine Learning
description: Abilitare la registrazione sulle esecuzioni di training ML per monitorare le metriche di esecuzione in tempo reale e per facilitare la diagnosi di errori e avvisi.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9e5f64d9ef61a272da488ad70e690db4c07ddccc
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625078"
---
# <a name="enable-logging-in-ml-training-runs"></a>Abilitare la registrazione in esecuzioni di training ML


Azure Machine Learning Python SDK consente di registrare le informazioni in tempo reale usando sia il pacchetto di registrazione Python predefinito che la funzionalità specifica dell'SDK. È possibile registrare in locale e inviare i log all'area di lavoro nel portale.

I log consentono di diagnosticare gli errori e gli avvisi o di tenete traccia di metriche delle prestazioni come parametri e prestazioni modello. Questo articolo illustra come abilitare la registrazione negli scenari seguenti:

> [!div class="checklist"]
> * Sessioni di training interattive
> * Invio dei processi di training tramite ScriptRunConfig
> * Impostazioni di `logging` native di Python
> * Registrazione da origini aggiuntive


> [!TIP]
> Questo articolo illustra come monitorare il processo di training dei modelli. Per informazioni sul monitoraggio dell'utilizzo delle risorse e di eventi di Azure Machine Learning, come quote, esecuzioni di training o distribuzioni di modelli completate, vedere [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipi di dati

È possibile registrare più tipi di dati, tra cui valori scalari, elenchi, tabelle, immagini, directory e altro ancora. Per altre informazioni e per esempi di codice Python per tipi di dati diversi, vedere la [pagina di informazioni di riferimento sulla classe Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessione di registrazione interattiva

Le sessioni di registrazione interattive vengono in genere usate negli ambienti notebook. Il metodo [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) avvia una sessione di registrazione interattiva. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento. Il metodo [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) termina le sessioni e contrassegna l'esecuzione come completata.

## <a name="scriptrun-logs"></a>Log ScriptRun

Questa sezione illustra come aggiungere codice di registrazione all'interno delle esecuzioni create se la configurazione è stata eseguita con ScriptRunConfig. È possibile usare la classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) per incapsulare gli script e gli ambienti per esecuzioni ripetibili. È anche possibile usare questa opzione per mostrare un widget visuale di Jupyter Notebook per il monitoraggio.

Questo esempio esegue uno spostamento di parametri su valori alfa e acquisisce i risultati con il metodo [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Creare uno script di training che include la logica di registrazione, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Inviare lo script ```train.py``` per l'esecuzione in un ambiente gestito dall'utente. L'intera cartella di script viene inviata per il training.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)]


   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? nome = esecuzione)]

    Il parametro `show_output` attiva la registrazione dettagliata, che consente di visualizzare i dettagli dal processo di training oltre a informazioni su tutte le risorse remote o le destinazioni di calcolo. Usare il codice seguente per attivare la registrazione dettagliata quando si invia l'esperimento.

```python
run = exp.submit(src, show_output=True)
```

È anche possibile usare lo stesso parametro nella funzione `wait_for_completion` all'esecuzione risultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Registrazione nativa di Python

Alcuni log nell'SDK possono contenere un errore che indica di impostare il livello di registrazione su DEBUG. Per impostare il livello di registrazione, aggiungere il codice seguente allo script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Origini di registrazione aggiuntive

Azure Machine Learning può anche registrare le informazioni provenienti da altre origini durante il training, ad esempio le esecuzioni di Machine Learning automatizzato o i contenitori Docker che eseguono i processi. Questi log non sono documentati, ma se si verificano problemi e si contatta il supporto tecnico Microsoft, potrebbero risultare utili per la risoluzione.

Per informazioni sulla registrazione di metriche nella finestra di progettazione di Azure Machine Learning, vedere [Come registrare le metriche nella finestra di progettazione](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare Azure Machine Learning, vedere questi articoli:

* Informazioni su come [registrare le metriche nella finestra di progettazione di Azure Machine Learning](how-to-track-designer-experiments.md).

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).
