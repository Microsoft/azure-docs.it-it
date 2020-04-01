---
title: "Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e Java"
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239060"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e Java

Usare questa guida di avvio rapido per iniziare a usare le due modalità di rilevamento dell'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione Java invia due richieste API contenenti i dati delle serie temporali in formato JSON e riceve le risposte.

| Richiesta API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare anomalie come batch                        | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per ogni punto dati nei dati di serie temporali e le posizioni delle anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Prerequisiti

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) versione successiva.
- Chiave di Rilevamento anomalie ed endpoint
- Importare queste librerie dal repository Maven
    - Pacchetto [JSON in Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacchetto [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Creare un nuovo progetto Java e importare le librerie seguenti.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento in batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento nel punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione denominata `sendRequest()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un oggetto `CloseableHttpClient` che può inviare richieste all'API. Inviare la richiesta a un oggetto richiesta `HttpPost` combinando l'endpoint e un URL di Rilevamento anomalie.

3. Usare la funzione `setHeader()` della richiesta per impostare l'intestazione `Content-Type` su `application/json` e aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

4. Usare la funzione `setEntity()` della richiesta per i dati da inviare.

5. Usare la funzione `execute()` del client per inviare la richiesta e salvarla in un oggetto `CloseableHttpResponse`.

6. Creare un oggetto `HttpEntity` in cui archiviare il contenuto della risposta. Ottenere il contenuto con `getEntity()`. Viene restituita la risposta se non è vuota.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare un metodo denominato `detectAnomaliesBatch()` per rilevare le anomalie in tutti i dati come batch. Chiamare il metodo `sendRequest()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati json. Ottenere il risultato e visualizzarlo nella console.

2. Se la risposta contiene il campo `code`, viene visualizzato il codice errore e il messaggio di errore.

3. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene un valore booleano che indica se il punto dati specificato è un'anomalia. Ottenere la matrice JSON e scorrerla, stampando l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

Creare un metodo denominato `detectAnomaliesLatest()` per rilevare lo stato delle anomalie del punto dati più recente nel set di dati. Chiamare il metodo `sendRequest()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati json. Ottenere il risultato e visualizzarlo nella console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Nel metodo main dell'applicazione leggere il file JSON che contiene i dati che verranno aggiunti alle richieste.

2. Chiamare le due funzioni di rilevamento anomalie create in precedenza.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
