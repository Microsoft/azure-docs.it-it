---
title: Abilitare la registrazione in Azure Machine Learning
description: Informazioni su come abilitare la registrazione in Azure Machine Learning usando il pacchetto di registrazione Python predefinito, oltre a usare funzionalità specifiche dell'SDK.
ms.author: larryfr
author: BlackMist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: d4fb08a618c6dfb35f3f8d154af6820c92d62781
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320137"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Abilitare la registrazione in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Il SDK Python di Azure Machine Learning consente di abilitare la registrazione usando sia il pacchetto di registrazione di Python predefinito, che la funzionalità specifica di SDK sia per la registrazione locale che per la registrazione all'area di lavoro nel portale. I log di forniscono agli sviluppatori informazioni in tempo reale sullo stato dell'applicazione e possono facilitare la diagnosi di errori o avvisi. Questo articolo illustra diverse modalità di abilitazione della registrazione nelle aree seguenti:

> [!div class="checklist"]
> * Training dei modelli e destinazioni di calcolo
> * Creazione di immagini
> * Modelli distribuiti
> * Impostazioni `logging` di Python

[Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md). Usare la [Guida](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per altre informazioni sull'SDK.

## <a name="training-models-and-compute-target-logging"></a>Training dei modelli e registrazione delle destinazioni di calcolo

Esistono diversi modi per abilitare la registrazione durante il processo di training del modello e gli esempi mostrati illustreranno gli schemi progettuali comuni. È possibile registrare facilmente dati relativi all'esecuzione nell'area di lavoro nel cloud usando la funzione `start_logging` nella classe `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Vedere la documentazione di riferimento per la classe [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) per altre funzioni di registrazione.

Per abilitare la registrazione locale dello stato dell'applicazione durante il corso del training, usare il parametro `show_output`. L'abilitazione della registrazione dettagliata consente di visualizzare i dettagli dal processo di training, nonché informazioni su tutte le risorse remote o le destinazioni di calcolo. Usare il codice seguente per abilitare la registrazione dopo l'invio di un esperimento.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

È anche possibile usare lo stesso parametro nella funzione `wait_for_completion` all'esecuzione risultante.

```python
run.wait_for_completion(show_output=True)
```

L'SDK supporta anche l'uso del pacchetto di registrazione di python predefinito in determinati scenari per il training. L'esempio seguente abilita il livello di registrazione di `INFO` in un oggetto `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

È anche possibile usare il parametro `show_output` durante la creazione di una destinazione di calcolo permanente. Specificare il parametro nella funzione `wait_for_completion` per abilitare la registrazione durante la creazione della destinazione di calcolo.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Registrazione per i modelli distribuiti

Per recuperare i log da un servizio Web distribuito in precedenza, caricare il servizio e usare la funzione `get_logs()`. Il log può contenere informazioni dettagliate sugli errori che si sono verificati durante la distribuzione.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

È anche possibile registrare analisi personalizzati dello stack per il servizio Web abilitando Application Insights, che consente di monitorare tempi di richiesta/risposta, frequenze di errori ed eccezioni. Chiamare la funzione `update()` in un servizio Web esistente per abilitare Application Insights.

```python
service.update(enable_app_insights=True)
```

Per altre informazioni, vedere [monitorare e raccogliere i dati dagli endpoint del servizio Web ml](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Impostazioni native della registrazione di Python

Determinati log nell'SDK possono contenere un errore che indica di impostare il livello di registrazione su DEBUG. Per impostare il livello di registrazione, aggiungere il codice seguente allo script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e raccogliere dati da endpoint servizio Web di ML](how-to-enable-app-insights.md)
