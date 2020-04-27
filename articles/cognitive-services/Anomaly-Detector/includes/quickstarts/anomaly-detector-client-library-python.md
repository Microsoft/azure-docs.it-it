---
title: Avvio rapido per la libreria client di Rilevamento anomalie per Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759820"
---
Questo argomento costituisce un'introduzione alla libreria client di Rilevamento anomalie per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Rilevamento anomalie consente di trovare le anomalie nei dati delle serie temporali applicando automaticamente i modelli di mapping più appropriati, indipendentemente dal settore, dallo scenario o dal volume di dati.

Usare la libreria client di Rilevamento anomalie per Python per:

* Rilevare le anomalie nel set di dati delle serie temporali come richiesta batch
* Rilevare lo stato di anomalia del punto dati più recente nella serie temporale

[Documentazione di riferimento della libreria](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pacchetto (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Trovare il codice di esempio in GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Chiave di Rilevamento anomalie ed endpoint
* [Python 3.x](https://www.python.org/)
* [Llibreria di analisi dei dati Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Configurazione

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

 Creare un nuovo file Python e importare le librerie seguenti.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Creare le variabili per la chiave in forma di una variabile di ambiente, il percorso a un file di dati della serie temporale e la località di Azure della sottoscrizione. Ad esempio: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modello a oggetti

Il client di Rilevamento anomalie è un oggetto [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) che esegue l'autenticazione ad Azure tramite la chiave dell'utente. Il client fornisce due metodi per il rilevamento delle anomalie: su un intero set di dati tramite [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) e sul punto dati più recente tramite [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

I dati delle serie temporali vengono inviati come serie di oggetti [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) in un oggetto [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). L'oggetto `Request` contiene le proprietà per la descrizione dei dati (ad esempio, [Granularity](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)) e i parametri per il rilevamento delle anomalie. 

La risposta del servizio Rilevamento anomalie è un oggetto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) o [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python), a seconda del metodo usato. 

## <a name="code-examples"></a>Esempi di codice 

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per Python:

* [Autenticare il client](#authenticate-the-client)
* [Caricare il set di dati di una serie temporale da un file](#load-time-series-data-from-a-file)
* [Rilevare le anomalie nell'intero set di dati](#detect-anomalies-in-the-entire-data-set) 
* [Rilevare lo stato di anomalia del punto dati più recente](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere la variabile della località di Azure all'endpoint e autenticare il client con la chiave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Caricare i dati di una serie temporale da un file

Scaricare i dati di esempio per questo avvio rapido da [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Nel browser fare clic con il pulsante destro del mouse su **Raw** (Non elaborato).
2. Fare clic su **Salva link con nome**.
3. Salvare il file nella directory dell'applicazione come file con estensione csv.

I dati della serie temporale vengono formattati come file con estensione csv e vengono inviati all'API Rilevamento anomalie.

Caricare il file di dati con il metodo `read_csv()` della libreria Pandas e creare una variabile di elenco vuoto in cui archiviare la serie di dati. Scorrere il file e accodare i dati come oggetto [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Questo oggetto contiene il timestamp e il valore numerico delle righe del file di dati CSV. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Creare un oggetto [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) con la serie temporale e la [granularità](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (o periodicità) dei relativi punti dati. Ad esempio: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Rilevare le anomalie nell'intero set di dati 

Chiamare l'API per rilevare le anomalie nei dati dell'intera serie temporale usando il metodo [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) del client. Archiviare l'oggetto [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) restituito. Scorrere l'elenco `is_anomaly` della risposta e visualizzare l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

Chiamare l'API Rilevamento anomalie per determinare se il punto dati più recente è un'anomalia usando il metodo [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) del client, quindi archiviare l'oggetto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) restituito. Il valore `is_anomaly` della risposta è un valore booleano che specifica lo stato di anomalia del punto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` e il nome del file.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
