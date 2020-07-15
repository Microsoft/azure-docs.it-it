---
title: 'Esercitazione: Rilevamento anomalie nei dati di streaming con Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Rilevamento anomalie e Azure Databricks per monitorare le anomalie nei dati.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: b8263e0445f7997469ba9165decbaccfa9ed2d6e
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027857"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Esercitazione: Rilevamento anomalie nei dati di streaming con Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) è un servizio di analisi veloce, semplice e collaborativo basato su Apache Spark. L'API Rilevamento anomalie, inclusa in Servizi cognitivi di Azure, offre un modo per monitorare i dati delle serie temporali. Usare questa esercitazione per eseguire il rilevamento anomalie in un flusso di dati in tempo quasi reale con Azure Databricks. Sarà possibile inserire i dati di twitter usando Hub eventi di Azure e importarli in Azure Databricks tramite il connettore di Hub eventi per Spark. Successivamente, si userà l'API per rilevare le anomalie nei dati trasmessi. 

L'illustrazione seguente mostra il flusso dell'applicazione:

![Azure Databricks con Hub eventi e Servizi cognitivi](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks con Hub eventi e Servizi cognitivi")

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks
> * Creare un cluster Spark in Azure Databricks
> * Creare un'app Twitter per accedere a dati in streaming
> * Creare notebook in Azure Databricks
> * Collegare librerie per Hub eventi e API Twitter
> * Creare una risorsa Rilevamento anomalie e recuperare la chiave di accesso
> * Inviare tweet a Hub eventi
> * Leggere tweet da Hub eventi
> * Eseguire il rilevamento anomalie sui tweet

> [!Note]
> * Questa esercitazione introduce un approccio all'implementazione dell'[architettura della soluzione](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) consigliata per l'API Rilevamento anomalie.
> * Non è possibile completare questa esercitazione con un sottoscrizione del livello gratuito (`F0`) dell'API Rilevamento anomalie o di Azure Databricks. 

Creare una [sottoscrizione di Azure](https://azure.microsoft.com/free/), se non se ne possiede una.

## <a name="prerequisites"></a>Prerequisiti

- Uno [spazio dei nomi di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) e un hub eventi.

- [Stringa di connessione](../../../event-hubs/event-hubs-get-connection-string.md) per l'accesso allo spazio dei nomi di Hub eventi. La stringa di connessione deve avere un formato simile al seguente:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Nome dei criteri di accesso condiviso e chiave dei criteri per Hub eventi.

Vedere l'[avvio rapido](../../../event-hubs/event-hubs-create.md) di Hub eventi di Azure per informazioni sulla creazione di uno spazio dei nomi e di un hub eventi.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il [portale di Azure](https://portal.azure.com/).

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks nel portale di Azure")

3. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks:


    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../../azure-resource-manager/management/overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti orientali 2** o una delle altre aree disponibili. Per le aree disponibili, vedere [Servizi di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).        |
    |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. NON scegliere **Versione di valutazione**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selezionare **Crea**.

4. La creazione dell'area di lavoro richiede alcuni minuti, 

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi selezionare **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **New Cluster** (Nuovo cluster).

    ![Databricks in Azure](../media/tutorials/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New Cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare un cluster di Databricks Spark in Azure](../media/tutorials/create-databricks-spark-cluster.png "Creare un cluster di Databricks Spark in Azure")

    Accettare tutti gli altri valori predefiniti tranne i seguenti:

   * Immettere un nome per il cluster.
   * Per questo articolo creare un cluster con il runtime **5.2**. NON selezionare il runtime **5.3**.
   * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di inattività). Se il cluster non viene usato, specificare una durata in minuti per terminarlo.

     Selezionare **Crea cluster**. 
4. La creazione del cluster richiede alcuni minuti. Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per ricevere un flusso di tweet, è necessario creare un'applicazione in Twitter. Seguire le istruzioni per creare un'applicazione Twitter e registrare i valori necessari per completare questa esercitazione.

1. In un Web browser passare alla [pagina di gestione delle applicazioni Twitter](https://apps.twitter.com/) e selezionare **Create New App** (Crea nuova app).

    ![Creare un'applicazione Twitter](../media/tutorials/databricks-create-twitter-app.png "Creare un'applicazione Twitter")

2. Nella pagina **Create an application** (Crea applicazione) specificare i dettagli per la nuova app e quindi selezionare **Create your Twitter application** (Crea applicazione Twitter).

    ![Dettagli dell'applicazione Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Dettagli dell'applicazione Twitter")

3. Nella pagina dell'applicazione selezionare **Keys and Access Tokens** (Chiavi e token di accesso) e copiare i valori di **Consumer Key** (Chiave utente) e **Consumer Secret** (Segreto utente). Selezionare anche **Create my access token** (Crea il mio token di accesso) per generare i token di accesso. Copiare i valori di **Access Token** (Token di accesso) e **Access Token Secret** (Segreto del token di accesso).

    ![Dettagli dell'applicazione Twitter](../media/tutorials/twitter-app-key-secret.png "Dettagli dell'applicazione Twitter")

Salvare i valori recuperati per l'applicazione Twitter. Sarà necessario usare questi valori più avanti nell'esercitazione.

## <a name="attach-libraries-to-spark-cluster"></a>Collegare librerie a un cluster Spark

In questa esercitazione verranno usate le API Twitter per inviare tweet a Hub eventi. Verrà anche usato il [connettore di Hub eventi per Apache Spark](https://github.com/Azure/azure-event-hubs-spark) per leggere e scrivere dati in Hub eventi di Azure. Per usare queste API nell'ambito del cluster, aggiungerle come librerie ad Azure Databricks e quindi associarle al cluster Spark. Le istruzioni seguenti illustrano come aggiungere le librerie alla cartella **Shared** (Condivisa) dell'area di lavoro.

1. Nell'area di lavoro di Azure Databricks selezionare **Workspace** (Area di lavoro) e quindi fare clic con il pulsante destro del mouse su **Shared** (Condivisa). Nel menu di scelta rapida scegliere **Create (Crea)**  > **Library (Raccolta)** .

   ![Finestra di dialogo Aggiungi libreria](../media/tutorials/databricks-add-library-option.png "Finestra di dialogo Aggiungi libreria")

2. Nella pagina New Library (Nuova libreria) selezionare **Maven**  in **Source** (Origine). Per **Coordinates** (Coordinate) immettere la coordinata del pacchetto da aggiungere. Le coordinate di Maven per le librerie usate in questa esercitazione sono le seguenti:

   * Connettore di Hub eventi per Spark - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API Twitter - `org.twitter4j:twitter4j-core:4.0.7`

     ![Specifica delle coordinate di Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Specifica delle coordinate di Maven")

3. Selezionare **Crea**.

4. Passare alla cartella in cui è stata aggiunta la libreria e selezionare il nome della libreria.

    ![Selezionare la libreria da aggiungere](../media/tutorials/select-library.png "Selezionare la libreria da aggiungere")

5. Se nella pagina della libreria non è presente alcun cluster, selezionare **Clusters** (Cluster) ed eseguire il cluster creato. Attendere fino quando non viene visualizzato 'Running' (In esecuzione) come stato e quindi tornare alla pagina della libreria.
Nella pagina della libreria selezionare il cluster in cui si vuole usare la libreria e quindi selezionare **Install** (Installa). Dopo aver associato la libreria al cluster, lo stato diventa immediatamente **Installed** (Installato).

    ![Installare la libreria nel cluster](../media/tutorials/databricks-library-attached.png "Installare la libreria nel cluster")

6. Ripetere questi passaggi per il pacchetto Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Ottenere una chiave di accesso per Servizi cognitivi

In questa esercitazione si useranno le [API Rilevamento anomalie di Servizi cognitivi di Azure](../overview.md) per eseguire il rilevamento anomalie su un flusso di tweet quasi in tempo reale. Prima di usare le API, è necessario creare una risorsa Rilevamento anomalie in Azure e recuperare una chiave di accesso per usare le API Rilevamento anomalie.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **+ Crea una risorsa**.

3. In Azure Marketplace selezionare **Intelligenza artificiale e Machine Learning** > **Visualizza tutto** > **Servizi cognitivi - Altro** > **Rilevamento anomalie**. In alternativa è possibile usare [questo collegamento](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) per passare direttamente alla finestra di dialogo **Crea**.

    ![Creare una risorsa di Rilevamento anomalie](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Creare una risorsa di Rilevamento anomalie")

4. Nella finestra di dialogo **Crea** specificare i valori seguenti:

    |valore |Descrizione  |
    |---------|---------|
    |Nome     | Nome per la risorsa Rilevamento anomalie.        |
    |Subscription     | Sottoscrizione di Azure a cui verrà associata la risorsa.        |
    |Location     | Località di Azure.        |
    |Piano tariffario     | Piano tariffario per il servizio. Per altre informazioni sui prezzi di Rilevamento anomalie, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Resource group     | Specificare se si vuole creare un nuovo gruppo di risorse o selezionarne uno esistente.        |


     Selezionare **Crea**.

5. Dopo aver creato la risorsa, nella scheda **Panoramica** copiare e salvare l'URL **Endpoint** come illustrato nello screenshot. Selezionare quindi **Mostra chiavi di accesso**.

    ![Mostra chiavi di accesso](../media/tutorials/cognitive-services-get-access-keys.png "Mostra chiavi di accesso")

6. In **Chiavi** selezionare l'icona di copia per la chiave da usare. Salvare la chiave di accesso.

    ![Copia delle chiavi di accesso](../media/tutorials/cognitive-services-copy-access-keys.png "Copia delle chiavi di accesso")

## <a name="create-notebooks-in-databricks"></a>Creare notebook in Databricks

In questa sezione vengono creati due notebook nell'area di lavoro di Databricks con i nomi seguenti:

- **SendTweetsToEventHub**: notebook producer usato per ottenere tweet da Twitter e trasmetterli a Hub eventi.
- **AnalyzeTweetsFromEventHub**: notebook consumer usato per leggere tweet da Hub eventi ed eseguire il rilevamento anomalie.

1. Nell'area di lavoro di Azure Databricks selezionare **Area di lavoro** nel riquadro sinistro. Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create** (Crea) e quindi **Notebook**.

    ![Creare un notebook in Databricks](../media/tutorials/databricks-create-notebook.png "Creare un notebook in Databricks")

2. Nella finestra di dialogo **Create Notebook** (Crea notebook) immettere **SendTweetsToEventHub** come nome, selezionare **Scala** come linguaggio e selezionare il cluster Spark creato in precedenza.

    ![Creare un notebook in Databricks](../media/tutorials/databricks-notebook-details.png "Creare un notebook in Databricks")

    Selezionare **Crea**.

3. Ripetere la procedura per creare il notebook **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Inviare tweet a Hub eventi

Nel notebook **SendTweetsToEventHub** incollare il codice seguente e sostituire il segnaposto con i valori per lo spazio dei nomi di Hub eventi e l'applicazione Twitter creata in precedenza. Questo notebook estrae l'ora di creazione e il numero di "Mi piace" dai tweet con la parola chiave "Azure" e li trasmette come eventi in Hub eventi in tempo reale.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente. Ogni evento nell'output è una combinazione di timestamp e numero di "Mi piace" inserita in Hub eventi.

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>Leggere tweet da Hub eventi

Nel notebook **AnalyzeTweetsFromEventHub** incollare il codice seguente e sostituire il segnaposto con i valori della risorsa Rilevamento anomalie creata in precedenza. Questo notebook legge i tweet trasmessi in precedenza a Hub eventi tramite il notebook **SendTweetsToEventHub**.

In primo luogo, scrivere un client per chiamare Rilevamento anomalie. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente.

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

Preparare quindi una funzione di aggregazione per un utilizzo futuro.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente.

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

Caricare quindi i dati dall'hub eventi per il rilevamento anomalie. Sostituire il segnaposto con i valori dell'istanza di Hub eventi di Azure creata in precedenza.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

L'output sarà ora simile all'immagine seguente. Si noti che la data nella tabella potrebbe essere diversa da quella di questa esercitazione perché i dati sono in tempo reale.
![Caricare i dati dall'hub eventi](../media/tutorials/load-data-from-eventhub.png "Caricare i dati dall'hub eventi")

È stato eseguito lo streaming di dati near real time da Hub eventi di Azure ad Azure Databricks usando il connettore di Hub eventi per Apache Spark. Per altre informazioni su come usare il connettore di Hub eventi per Spark, vedere la [documentazione del connettore](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Eseguire il rilevamento anomalie sui tweet

In questa sezione si esegue il rilevamento anomalie sui tweet ricevuti usando l'API Rilevamento anomalie. Per questa sezione, aggiungere i frammenti di codice allo stesso notebook **AnalyzeTweetsFromEventHub**.

Per eseguire il rilevamento anomalie, è necessario prima di tutto aggregare il conteggio delle metriche per ora.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
L'output sarà ora simile al seguente.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Ottenere quindi il risultato dell'output aggregato in Delta. Poiché il rilevamento anomalie richiede una finestra della cronologia più lunga, viene usato Delta per mantenere i dati della cronologia per il punto che si vuole rilevare. Sostituire "[Placeholder: table name]" con un nome di tabella Delta completo da creare (ad esempio, "tweets"). Sostituire "[Placeholder: folder name for checkpoints]" con un valore stringa che è univoco ogni volta che si esegue questo codice (ad esempio, "etl-from-eventhub-20190605").
Per altre informazioni su Delta Lake in Azure Databricks, vedere la [Guida di Delta Lake](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Sostituire "[Placeholder: table name]" con lo stesso nome di tabella Delta selezionato in precedenza.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
L'output sarà simile al seguente: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

I dati delle serie temporali aggregati vengono ora inseriti in modo continuo nel Delta. È quindi possibile pianificare un processo orario per rilevare le anomalie del punto più recente. Sostituire "[Placeholder: table name]" con lo stesso nome di tabella Delta selezionato in precedenza.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Il risultato sarà simile al seguente: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

L'operazione è terminata. Usando Azure Databricks sono stati trasmessi dati a Hub eventi di Azure, i dati trasmessi sono stati usati con il connettore di Hub eventi ed è stato quindi eseguito il rilevamento anomalie sui dati in streaming quasi in tempo reale.
Sebbene in questa esercitazione la granularità è su base oraria, è sempre possibile modificare la granularità in base alle proprie esigenze. 

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver concluso l'esecuzione per l'esercitazione è possibile terminare il cluster. A questo scopo, nell'area di lavoro di Azure Databricks selezionare **Clusters** (Cluster) nel riquadro sinistro. Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e selezionare l'icona **Terminate** (Termina), quindi selezionare **Confirm** (Conferma).

![Arrestare un cluster di Databricks](../media/tutorials/terminate-databricks-cluster.png "Arrestare un cluster di Databricks")

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di inattività) durante la creazione del cluster. In tal caso, il cluster verrà automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Azure Databricks per trasmettere dati a Hub eventi di Azure e quindi leggere i dati trasmessi da Hub eventi in tempo reale. Passare all'esercitazione successiva per informazioni su come chiamare l'API Rilevamento anomalie e visualizzare le anomalie con Power BI Desktop. 

> [!div class="nextstepaction"]
>[Rilevamento anomalie in batch con Power BI Desktop](batch-anomaly-detection-powerbi.md)
