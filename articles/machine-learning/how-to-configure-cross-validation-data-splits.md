---
title: Suddivisione dei dati e convalida incrociata nell'apprendimento automatico automatico
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare le suddivisioni del set di dati e la convalida incrociata per gli esperimenti di Machine Learning automatici
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501583"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurare la suddivisione dei dati e la convalida trasversale in Machine Learning automatizzato

In questo articolo vengono illustrate le diverse opzioni per la configurazione dei dati di training e la suddivisione dei dati di convalida con le impostazioni di convalida incrociata per gli esperimenti automatici di Machine Learning, Automated ML.

In Azure Machine Learning, quando si usa Machine Learning Machine Learning per compilare più modelli ML, ogni esecuzione figlio deve convalidare il modello correlato calcolando la metrica di qualità per tale modello, ad esempio l'accuratezza o l'AUC ponderata. Queste metriche vengono calcolate confrontando le stime effettuate con ogni modello con etichette reali dalle osservazioni precedenti nei dati di convalida. [Altre informazioni su come vengono calcolate le metriche in base al tipo di convalida](#metric-calculation-for-cross-validation-in-machine-learning). 

Gli esperimenti di Machine Learning automatici eseguono automaticamente la convalida del modello. Le sezioni seguenti descrivono come è possibile personalizzare ulteriormente le impostazioni di convalida con [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/). 

Per un'esperienza di basso livello o senza codice, vedere [creare esperimenti automatici di Machine Learning in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> Lo Studio supporta attualmente la suddivisione dei dati di training e convalida, nonché le opzioni di convalida incrociata, ma non supporta la specifica di singoli file di dati per il set di convalida. 

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario,

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Familiarità con la configurazione di un esperimento di Machine Learning automatizzato con il Azure Machine Learning SDK. Seguire l' [esercitazione](tutorial-auto-train-models.md) o le [procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione dell'esperimento automatizzato di machine learning.

* Conoscenza della suddivisione dei dati di Training/convalida e della convalida incrociata come concetti di machine learning. Per una spiegazione di alto livello,

    * [Informazioni sui dati di training, convalida e test in Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Comprendere la convalida incrociata in Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Suddivisione dei dati predefinita e convalida incrociata in Machine Learning

Usare l'oggetto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) per definire le impostazioni dell'esperimento e del training. Nel frammento di codice seguente si noti che sono definiti solo i parametri obbligatori, ovvero i parametri `n_cross_validation` per `validation_ data` o **non** sono inclusi.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Se non si specifica in modo esplicito un `validation_data` parametro o, Machine Learning `n_cross_validation` applica le tecniche predefinite in base al numero di righe fornite nel singolo set di dati `training_data` :

|&nbsp;Dimensioni dati di training &nbsp;| Tecnica di convalida |
|---|-----|
|**Maggiore &nbsp; di &nbsp; 20.000 &nbsp; righe**| Viene applicata la suddivisione dei dati di Training/convalida. Il valore predefinito prevede il 10% del set di dati di training iniziale come set di convalida. A sua volta, il set di convalida viene usato per il calcolo delle metriche.
|**Inferiore &nbsp; a &nbsp; 20.000 &nbsp; righe**| Viene applicato l'approccio per la convalida incrociata. Il numero predefinito di riduzioni dipende dal numero di righe. <br> **Se il set di dati è inferiore a 1.000 righe**, vengono utilizzate 10 riduzioni. <br> **Se le righe sono comprese tra 1.000 e 20.000**, vengono usate tre riduzioni.

## <a name="provide-validation-data"></a>Fornire dati di convalida

In questo caso, è possibile iniziare con un singolo file di dati e suddividerlo in set di dati di training e di dati di convalida oppure è possibile fornire un file di dati separato per il set di convalida. In entrambi i casi, il `validation_data` parametro nell' `AutoMLConfig` oggetto assegna i dati da utilizzare come set di convalida. Questo parametro accetta solo i set di dati sotto forma di un set di dati [Azure Machine Learning](how-to-create-register-datasets.md) o di un dataframe Pandas.   

> [!NOTE]
> Il `validation_size` parametro non è supportato in scenari di previsione.

Nell'esempio di codice seguente viene definita in modo esplicito la parte dei dati forniti in `dataset` da utilizzare per il training e la convalida.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Specificare le dimensioni del set di convalida

In questo caso, per l'esperimento viene fornito solo un singolo set di dati. Ovvero il `validation_data` parametro **non** viene specificato e il set di dati fornito viene assegnato al  `training_data` parametro.  

Nell' `AutoMLConfig` oggetto è possibile impostare il parametro in `validation_size` modo da mantenere una parte dei dati di training per la convalida. Ciò significa che il set di convalida verrà suddiviso in base a ML automatizzato dall'iniziale `training_data` fornito. Questo valore deve essere compreso tra 0,0 e 1,0 non inclusivo (ad esempio, 0,2 significa che il 20% dei dati viene mantenuto per i dati di convalida).

> [!NOTE]
> Il `validation_size` parametro non è supportato in scenari di previsione. 

Vedere l'esempio di codice seguente:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>Convalida incrociata in k parti

Per eseguire la convalida incrociata k-fold, includere il `n_cross_validations` parametro e impostarlo su un valore. Questo parametro consente di impostare il numero di convalide incrociate da eseguire, in base allo stesso numero di riduzioni.

> [!NOTE]
> Il `n_cross_validations` parametro non è supportato negli scenari di classificazione che usano reti neurali profonde.
 
Nel codice seguente sono definite cinque riduzioni per la convalida incrociata. Di conseguenza, cinque corsi di formazione diversi, ogni training che usa 4/5 dei dati e ogni convalida che usa 1/5 dei dati con una piegatura diversa per ogni volta.

Di conseguenza, le metriche vengono calcolate con la media delle cinque metriche di convalida.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Convalida incrociata Monte Carlo

Per eseguire la convalida incrociata Monte Carlo, includere i `validation_size` `n_cross_validations` parametri e nell' `AutoMLConfig` oggetto. 

Per la convalida incrociata Monte Carlo, la ML automatizzata riserva la parte dei dati di training specificata dal `validation_size` parametro per la convalida, quindi assegna il resto dei dati per il training. Questo processo viene quindi ripetuto in base al valore specificato nel `n_cross_validations` parametro, che genera ogni volta nuove divisioni di training e convalida, in modo casuale.

> [!NOTE]
> La convalida incrociata Monte Carlo non è supportata negli scenari di previsione.

Il codice seguente definisce, 7 riduzioni per la convalida incrociata e il 20% dei dati di training deve essere usato per la convalida. Di conseguenza, 7 corsi di formazione diversi, ogni training usa il 80% dei dati e ogni convalida usa il 20% dei dati con una sezione di dati di attesa diversa ogni volta.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Specifica riduzioni dati personalizzate per la convalida incrociata

È anche possibile fornire riduzioni di dati di convalida incrociata (CV). Questo scenario è considerato uno scenario più avanzato perché si specificano le colonne da dividere e utilizzare per la convalida.  Includere colonne personalizzate con suddivisione CV nei dati di training e specificare le colonne popolando i nomi di colonna nel `cv_split_column_names` parametro. Ogni colonna rappresenta una suddivisione della convalida incrociata e viene riempita con i valori integer 1 o 0, dove 1 indica che la riga deve essere utilizzata per il training e 0 indica che la riga deve essere utilizzata per la convalida.

Il frammento di codice seguente contiene dati di marketing Bank con due colonne di divisione CV ' CV1' è CV2'.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Per usare `cv_split_column_names` con `training_data` e `label_column_name` , aggiornare il Azure Machine Learning Python SDK versione 1.6.0 o successiva. Per le versioni precedenti dell'SDK, fare riferimento a utilizzando `cv_splits_indices` , ma si noti che viene utilizzato solo con l' `X` input del `y` set di dati e. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Calcolo della metrica per la convalida incrociata in Machine Learning

Quando si usa la convalida incrociata k-fold o Monte Carlo, le metriche vengono calcolate a ogni riduzioni di convalida e quindi aggregate. L'operazione di aggregazione è una media per le metriche scalari e una somma dei grafici. Le metriche calcolate durante la convalida incrociata sono basate su tutte le riduzioni e quindi su tutti gli esempi del set di training. [Altre informazioni sulle metriche in Machine Learning automatizzato](how-to-understand-automated-ml.md).

Quando viene utilizzato un set di convalida personalizzato o un set di convalida selezionato automaticamente, le metriche di valutazione del modello vengono calcolate solo dal set di convalida, non dai dati di training.

## <a name="next-steps"></a>Passaggi successivi

* [Prevenire i dati sbilanciati e l'overfitting](concept-manage-ml-pitfalls.md).
* [Esercitazione: usare Machine Learning automatico per stimare le tariffe taxi-sezione dati suddivisi](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Come eseguire [il training automatico di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md).
