---
title: 'Esercitazione sulla regressione: Funzionalità automatiche di Machine Learning'
titleSuffix: Azure Machine Learning
description: Creare un esperimento di Machine Learning automatizzato che genera un modello di regressione in base ai dati di training e alle impostazioni di configurazione fornite.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python, automl
ms.openlocfilehash: f4d26ba6bec8f3e63ba89ed9abae789704a0828b
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184102"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Esercitazione: Usare il servizio Machine Learning automatizzato per stimare le tariffe dei taxi


In questa esercitazione si userà il machine learning automatizzato in Azure Machine Learning per creare un modello di regressione per prevedere i prezzi delle corse in taxi di New York City. Questo processo accetta impostazioni di configurazione e dati di training ed esegue automaticamente iterazioni di combinazioni di metodi di normalizzazione/standardizzazione delle caratteristiche, impostazioni di iperparametri e modelli diversi per ottenere il modello ottimale.

![Diagramma di flusso](./media/tutorial-auto-train-models/flow2.png)

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Scaricare i dati, trasformarli e pulirli con i set di dati aperti di Azure
> * Eseguire il training di un modello di regressione di Machine Learning automatizzato
> * Calcolare l'accuratezza del modello

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento](https://aka.ms/AMLFree) di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'[esercitazione relativa all'installazione](tutorial-1st-experiment-sdk-setup.md) se non si ha già un'area di lavoro di Azure Machine Learning o una macchina virtuale per notebook.
* Al termine dell'esercitazione sull'installazione, aprire il notebook *tutorials/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb* usando lo stesso server notebook.

Questa esercitazione è disponibile anche in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se si vuole eseguirla nel proprio [ambiente locale](how-to-configure-environment.md#local). Per ottenere i pacchetti richiesti: 
* [Installare il client `automl` completo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).
* Eseguire `pip install azureml-opendatasets azureml-widgets` per ottenere i pacchetti necessari.

## <a name="download-and-prepare-data"></a>Scaricare e preparare i dati

Importare i pacchetti necessari. Nel pacchetto Set di dati Open, ogni origine dati è rappresentata da una classe (ad esempio, `NycTlcGreen`) che consente di filtrare facilmente i parametri di data prima del download.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Per iniziare, creare un frame di dati che conterrà i dati dei taxi. Quando si lavora in un ambiente non Spark, Set di dati Open consente di scaricare solo un mese di dati alla volta con determinate classi per evitare `MemoryError` in caso di set di dati di grandi dimensioni.

Per scaricare i dati relativi ai taxi, recuperare in modo iterativo un mese alla volta e campionare in modo casuale 2.000 record da ogni mese prima dell'aggiunta a `green_taxi_df` per evitare di aumentare eccessivamente le dimensioni del frame di dati. Visualizzare quindi in anteprima i dati.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType |fareAmount |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|nessuno|nessuno|-73,88|40,84|-73,94|...|2|15,00|0.50|0.50|0,3|0,00|0,00|nan|16,30|1,00
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|nessuno|nessuno|-73,96|40,81|-73,96|...|2|4.50|1,00|0.50|0,3|0,00|0,00|nan|6,30|1,00
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|nessuno|nessuno|-73,92|40,76|-73,91|...|2|4,00|0,00|0.50|0,3|0,00|0,00|nan|4,80|1,00
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|nessuno|nessuno|-73,81|40,70|-73,82|...|2|12,50|0.50|0.50|0,3|0,00|0,00|nan|13,80|1,00
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|nessuno|nessuno|-73,92|40,76|-73,92|...|2|4,00|0.50|0.50|0|0,00|0,00|nan|5,00|1,00
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1,10|nessuno|nessuno|-73,96|40,72|-73,95|...|2|6,50|0.50|0.50|0,3|0,00|0,00|nan|7,80|1,00
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|nessuno|nessuno|-73,88|40,76|-73,87|...|2|6,00|0,00|0.50|0,3|0,00|0,00|nan|6,80|1,00
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|nessuno|nessuno|-73,96|40,72|-73,91|...|2|12,50|0.50|0.50|0,3|0,00|0,00|nan|13,80|1,00
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|nessuno|nessuno|-73,94|40,71|-73,95|...|1|7,00|0,00|0.50|0,3|1,75|0,00|nan|9,55|1,00
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0.65|nessuno|nessuno|-73,94|40,71|-73,94|...|2|5,00|0.50|0.50|0,3|0,00|0,00|nan|6,30|1,00

Ora che sono stati caricati i dati iniziali, definire una funzione per creare varie caratteristiche basate su data e ora dal campo datetime di inizio corsa. Verranno così creati nuovi campi per il numero del mese, il giorno del mese, il giorno della settimana e l'ora del giorno e il modello potrà tenere in considerazione la stagionalità su base temporale. Usare la funzione `apply()` sul frame di dati per applicare in modo iterativo la funzione `build_time_features()` a ogni riga dei dati relativi ai taxi.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType|fareAmount  |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|----
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|nessuno|nessuno|-73,88|40,84|-73,94|...|2|15,00|0.50|0.50|0,3|0,00|0,00|nan|16,30|1,00|1|11|6|5
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|nessuno|nessuno|-73,96|40,81|-73,96|...|2|4.50|1,00|0.50|0,3|0,00|0,00|nan|6,30|1,00|1|20|1|16
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|nessuno|nessuno|-73,92|40,76|-73,91|...|2|4,00|0,00|0.50|0,3|0,00|0,00|nan|4,80|1,00|1|1|3|5
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|nessuno|nessuno|-73,81|40,70|-73,82|...|2|12,50|0.50|0.50|0,3|0,00|0,00|nan|13,80|1,00|1|17|5|2
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|nessuno|nessuno|-73,92|40,76|-73,92|...|2|4,00|0.50|0.50|0|0,00|0,00|nan|5,00|1,00|1|1|3|5
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1,10|nessuno|nessuno|-73,96|40,72|-73,95|...|2|6,50|0.50|0.50|0,3|0,00|0,00|nan|7,80|1,00|1|4|6|19
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|nessuno|nessuno|-73,88|40,76|-73,87|...|2|6,00|0,00|0.50|0,3|0,00|0,00|nan|6,80|1,00|1|3|5|12
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|nessuno|nessuno|-73,96|40,72|-73,91|...|2|12,50|0.50|0.50|0,3|0,00|0,00|nan|13,80|1,00|1|9|4|23
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|nessuno|nessuno|-73,94|40,71|-73,95|...|1|7,00|0,00|0.50|0,3|1,75|0,00|nan|9,55|1,00|1|11|6|17
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0.65|nessuno|nessuno|-73,94|40,71|-73,94|...|2|5,00|0.50|0.50|0,3|0,00|0,00|nan|6,30|1,00|1|22|3|23

Rimuovere alcune colonne che non saranno necessarie per il training o la creazione di caratteristiche aggiuntive.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Pulire i dati

Eseguire la funzione `describe()` sul nuovo frame di dati per visualizzare statistiche di riepilogo per ogni campo.

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num   day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|---
|count|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00
|mean|1,78|1,37|2.87|-73,83|40,69|-73,84|40,70|14,75|6,50|15,13|3,27|13,52
|std|0,41|1.04|2,93|2.76|1,52|2.61|1,44|12,08|3.45|8,45|1,95|6,83
|Min|1,00|0,00|0,00|-74,66|0,00|-74,66|0,00|-300,00|1,00|1,00|0,00|0,00
|25%|2,00|1,00|1,06|-73,96|40,70|-73,97|40,70|7,80|3,75|8,00|2,00|9,00
|50%|2,00|1,00|1,90|-73,94|40,75|-73,94|40,75|11,30|6,50|15,00|3,00|15,00
|75%|2,00|1,00|3.60|-73,92|40,80|-73,91|40,79|17,80|9,25|22,00|5,00|19,00
|max|2,00|9,00|97,57|0,00|41,93|0,00|41,94|450,00|12,00|30,00|6,00|23,00


Nelle statistiche di riepilogo si rileva che sono presenti diversi campi contenenti outlier o valori che ridurranno l'accuratezza del modello. Per prima cosa, filtrare i campi di latitudine e longitudine in modo che rientrino nei limiti della zona di Manhattan. In questo modo si filtrano le corse dei taxi più lunghe o le corse che sono outlier rispetto alla relazione con altre funzionalità.

Filtrare inoltre il campo `tripDistance` in modo che sia maggiore di zero ma minore di 31 miglia (la distanza dell'emisenoverso tra le due coppie latitudine/longitudine). In questo modo si eliminano le corse outlier lunghe con costi di viaggio incoerenti.

Il campo `totalAmount`, infine, contiene valori negativi per le tariffe dei taxi, privi di senso nel contesto del modello, e il campo `passengerCount` contiene dati non validi il cui valore minimo è zero.

Escludere queste anomalie usando funzioni di query e quindi rimuovere le ultime colonne non necessarie per il training.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Chiamare di nuovo `describe()` sui dati per verificare che la pulizia abbia funzionato come previsto. È ora disponibile un set preparato e pulito di dati relativi a taxi, festività e meteo da usare per il training del modello di Machine Learning.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Configurare l'area di lavoro

Creare un oggetto area di lavoro dall'area di lavoro esistente. Un'[area di lavoro](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud per monitorare le esecuzioni del modello e tenerne traccia. `Workspace.from_config()` legge il file **config.json** e carica i dettagli di autenticazione in un oggetto denominato `ws`. `ws` viene usato in tutta la parte restante del codice in questa esercitazione.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Dividere i dati in set di training e di test

Dividere i dati in set di training e di test usando la funzione `train_test_split` nella libreria `scikit-learn`. Questa funzione separa i dati nel set di dati x (**caratteristiche**) per il training del modello e nel set di dati y (**valori da stimare**) per il test.

Il parametro `test_size` determina la percentuale di dati da allocare al test. Il parametro `random_state` imposta un valore di inizializzazione sul generatore di casuale, in modo che le divisioni training-test siano deterministiche.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Lo scopo di questo passaggio consiste nell'usare punti dati per testare il modello finito che non sono stati usati per il training del modello, per misurare l'accuratezza.

In altre parole, un modello con training ottimale deve poter eseguire in modo accurato le stime dai dati che non ha già visualizzato. Sono stati preparati i dati per il training automatico di un modello di Machine Learning.

## <a name="automatically-train-a-model"></a>Eseguire il training automatico di un modello

Per eseguire il training automatico di un modello, eseguire queste operazioni:
1. Definire le impostazioni per l'esecuzione dell'esperimento. Allegare i dati di training alla configurazione e modificare le impostazioni che controllano il processo di training.
1. Inviare l'esperimento per l'ottimizzazione del modello. Dopo aver inviato l'esperimento, il processo esegue l'iterazione attraverso diversi algoritmi di machine learning e impostazioni degli iperparametri, rispettando i vincoli definiti dall'utente. Sceglie il modello più appropriato ottimizzando una metrica di accuratezza.

### <a name="define-training-settings"></a>Definire le impostazioni di training

Definire le impostazioni del modello e dei parametri dell'esperimento per il training. Visualizzare l'elenco completo delle [impostazioni](how-to-configure-auto-train.md). L'invio dell'esperimento con queste impostazioni predefinite richiederà circa 5-20 minuti. Se si vuole abbreviare il tempo di esecuzione, ridurre il parametro `experiment_timeout_hours`.

|Proprietà| Valore in questa esercitazione |Descrizione|
|----|----|---|
|**iteration_timeout_minutes**|10|Limite di tempo in minuti per ogni iterazione. Aumentare questo valore per i set di dati più grandi in cui ogni iterazione richiede più tempo.|
|**experiment_timeout_hours**|0,3|Quantità massima di tempo, in ore, che tutte le iterazioni combinate possono impiegare prima che l'esperimento venga terminato.|
|**enable_early_stopping**|True|Flag che abilita la terminazione anticipata se il punteggio non sta migliorando a breve termine.|
|**primary_metric**| spearman_correlation | Metrica che si vuole ottimizzare. Verrà scelto il modello più appropriato in base a questa metrica.|
|**featurization**| auto | Usando **auto**, l'esperimento può pre-elaborare i dati di input (gestendo i dati mancanti, convertendo il testo in valori numerici e così via)|
|**verbosity**| logging.INFO | Controlla il livello di dettaglio della registrazione.|
|**n_cross_validations**|5|Numero di divisioni di convalida incrociata da eseguire quando i dati di convalida non sono specificati.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Usare le impostazioni di training definite come parametro `**kwargs` a un oggetto `AutoMLConfig`. In aggiunta, specificare i dati di training e il tipo di modello, ovvero `regression` in questo caso.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="train-the-automatic-regression-model"></a>Eseguire il training del modello di regressione automatica

Creare un oggetto esperimento nell'area di lavoro. Un esperimento funge da contenitore per le singole esecuzioni. Passare l'oggetto `automl_config` definito all'esperimento e impostare l'output su `True` per visualizzare lo stato di avanzamento durante l'esecuzione.

Dopo aver iniziato l'esperimento, l'output visualizzato si aggiorna in tempo reale durante l'esecuzione. Per ogni iterazione, vengono visualizzati il tipo di modello, la durata dell'esecuzione e l'accuratezza del training. Il campo `BEST` traccia il miglior punteggio di training in esecuzione in base del tipo di metrica.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "Tutorial-NYCTaxi")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Esplorare i risultati

Esplorare i risultati del training automatico usando un [widget di Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Il widget consente di visualizzare un grafico e una tabella di tutte le iterazioni di esecuzione singole, insieme alle metriche e ai metadati di accuratezza del training. Inoltre, è possibile filtrare in base a metriche di accuratezza diverse rispetto alla metrica primaria con il selettore a discesa.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Dettagli sull'esecuzione del widget Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Tracciato del widget Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Recuperare il modello migliore

Selezionare il modello migliore dalle iterazioni. La funzione `get_output` restituisce l'esecuzione migliore e il modello più adatto per l'ultima chiamata alla funzione di fit. Usando gli overload in `get_output`, è possibile recuperare l'esecuzione migliore e il modello più adatto per qualsiasi metrica registrata o per un'iterazione specifica.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testare l'accuratezza del modello migliore

Usare il modello migliore per eseguire stime sul set di dati di test per stimare le tariffe dei taxi. La funzione `predict` usa il modello migliore ed esegue una stima dei valori di y (**costo della corsa**) in base al set di dati `x_test`. Stampare i primi 10 valori dei costi stimati da `y_predict`.

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Calcolare il `root mean squared error` dei risultati. Convertire il frame di dati `y_test` in un elenco da confrontare con i valori previsti. La funzione `mean_squared_error` accetta due matrici di valori e calcola l'errore quadratico medio tra di essi. La radice quadrata del risultato restituisce un errore nelle stesse unità di misura della variabile y, **cost**. Indica approssimativamente quanto le stime dei prezzi delle corse in taxi si discostano dai prezzi effettivi.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Eseguire il codice seguente per calcolare l'errore percentuale assoluto medio usando i set di dati completi `y_actual` e `y_predict`. Questa metrica calcola una differenza assoluta tra ogni valore stimato ed effettivo e somma tutte le differenze. Esprime quindi la somma come percentuale del totale dei valori effettivi.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


Dalle due metriche di accuratezza della stima finali, si nota che il modello esegue una stima discreta dei prezzi delle corse in taxi in base alle funzionalità del set di dati, in genere con un margine di $4.00 in eccesso o in difetto e un errore di circa il 15%.

Il processo di sviluppo del modello di Machine Learning tradizionale richiede risorse ingenti, notevoli conoscenze settoriali e tempi estremamente lunghi per poter eseguire un elevato numero di modelli e confrontarne i risultati. L'uso dell'apprendimento automatico automatizzato è un ottimo modo per testare rapidamente molti modelli diversi per il proprio scenario.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende eseguire altre esercitazioni su Azure Machine Learning, non completare questa sezione.

### <a name="stop-the-compute-instance"></a>Arrestare l'istanza di calcolo

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminarle per evitare addebiti.

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.
1. Nell'elenco selezionare il gruppo di risorse creato.
1. Selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sulla modalità automatizzata di apprendimento automatico sono state eseguite queste attività:

> [!div class="checklist"]
> * Configurare un'area di lavoro e preparare i dati per un esperimento.
> * Eseguire il training usando un modello di regressione automatizzato in locale con parametri personalizzati.
> * Esplorare ed esaminare i risultati del training.

[Distribuire il modello](tutorial-deploy-models-with-aml.md) con Azure Machine Learning.
