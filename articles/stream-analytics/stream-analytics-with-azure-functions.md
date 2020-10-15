---
title: 'Esercitazione: Eseguire Funzioni di Azure in processi di Analisi di flusso di Azure'
description: Questa esercitazione descrive come configurare Funzioni di Azure come sink di output per i processi di Analisi di flusso.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 70ea5ec9ee91fdba8023b9c6af1ce65b691a17fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006891"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Esercitazione: Eseguire Funzioni di Azure da processi di Analisi di flusso di Azure 

È possibile eseguire Funzioni di Azure da Analisi di flusso di Azure configurando Funzioni come uno dei sink di output per il processo di Analisi di flusso. Funzioni offre un'esperienza di calcolo on demand guidata dagli eventi che consente di implementare il codice attivato da eventi generati nei servizi di Azure o in servizi di terze parti. La possibilità offerta da Funzioni di rispondere ai trigger la rende l'output naturale per i processi di Analisi di flusso.

Analisi di flusso richiama Funzioni tramite trigger HTTP. L'adattatore di output di Funzioni consente agli utenti di connettere Funzioni ad Analisi di flusso, in modo che gli eventi possano essere attivati in base alle query di Analisi di flusso. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare ed eseguire un processo di Analisi di flusso
> * Creare un'istanza di Azure Cache per Redis
> * Creare una funzione di Azure
> * Controllare i risultati in Azure Cache per Redis

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurare un processo di Analisi di flusso per eseguire una funzione 

Questa sezione illustra come configurare un processo di Analisi di flusso per eseguire una funzione che scriva dati in Azure Cache per Redis. Il processo di Analisi di flusso legge gli eventi da Hub eventi di Azure ed esegue una query che richiama la funzione. Quest'ultima legge i dati dal processo di Analisi di flusso e li scrive in Azure Cache per Redis.

![Diagramma che mostra le relazioni tra i servizi di Azure](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Creare un processo di Analisi di flusso con Hub eventi come input

Seguire l'esercitazione [Rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md) per creare un hub eventi, avviare l'applicazione di generazione di eventi e creare un processo di Analisi di flusso Ignorare i passaggi per la creazione della query e dell'output, ma fare riferimento alle sezioni seguenti per la configurazione dell'output di Funzioni di Azure.

## <a name="create-an-azure-cache-for-redis-instance"></a>Creare un'istanza di Azure Cache per Redis

1. Creare una cache in Azure Cache per Redis seguendo la procedura descritta in [Creare una cache](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Dopo aver creato la cache, in **Impostazioni**selezionare **Chiavi di accesso**. Annotare la **Stringa di connessione primaria**.

   ![Screenshot della stringa di connessione di Azure Cache per Redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Creare una funzione in Funzioni di Azure che possa scrivere dati in Azure Cache per Redis

1. Vedere la sezione [Creare un'app per le funzioni](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) della documentazione relativa a Funzioni. Questa sezione illustra come creare un'app per le funzioni e una [funzione attivata da HTTP in Funzioni di Azure](../azure-functions/functions-create-first-azure-function.md#create-function) usando il linguaggio C#.  

2. Passare alla funzione **run.csx** e aggiornarla con il codice seguente. Sostituire **"\<your Azure Cache for Redis connection string goes here\>"** con la stringa di connessione primaria della cache di Azure per Redis recuperata nella sezione precedente. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Quando Analisi di flusso di Azure riceve dalla funzione l'eccezione "Entità richiesta HTTP troppo grande", riduce la dimensione dei batch che invia a Funzioni. Il codice seguente verifica che Analisi di flusso non invii batch troppo grandi. Assicurarsi che i valori relativi alle dimensioni massime e al numero massimo di batch usati nella funzione siano conformi ai valori inseriti nel portale di Analisi di flusso.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. In un editor di testo creare un file JSON denominato **project.json**. Incollare il codice seguente e salvarlo nel computer locale. Questo file contiene le dipendenze dei pacchetti NuGet richiesti dalla funzione C#.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Tornare al portale di Azure. Dalla scheda **Funzionalità della piattaforma** passare alla funzione. In **Strumenti di sviluppo**selezionare **Editor del servizio app**. 
 
   ![Schermata dell'editor del servizio app](./media/stream-analytics-with-azure-functions/image3.png)

5. Nell'editor del servizio app fare clic con il pulsante destro del mouse sulla directory radice e caricare il file **project.json**. Al termine del processo di caricamento, aggiornare la pagina. Verrà ora visualizzato un file generato automaticamente di nome **project.lock.json**. Il file generato automaticamente contiene riferimenti ai file con estensione dll specificati nel file project.json.  

   ![Schermata dell'editor del servizio app](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aggiornare il processo di Analisi di flusso con la funzione come output

1. Nel portale di Azure aprire il processo di Analisi di flusso.  

2. Passare alla funzione e selezionare **Panoramica** > **Output** > **Aggiungi**. Per aggiungere un nuovo output, selezionare **Funzione di Azure** per l'opzione di sink. L'adattatore di output di Funzioni ha le proprietà seguenti:  

   |**Nome proprietà**|**Descrizione**|
   |---|---|
   |Alias di output| Nome descrittivo usato nella query del processo per fare riferimento all'output. |
   |Opzione di importazione| È possibile usare la funzione dalla sottoscrizione corrente oppure specificare manualmente le impostazioni se la funzione si trova in un'altra sottoscrizione. |
   |App per le funzioni| Nome dell'app Funzioni. |
   |Funzione| Nome della funzione nell'app Funzioni (nome della funzione di run.csx).|
   |Dimensioni massime batch|Imposta le dimensioni massime, in byte, per ogni batch di output inviato alla funzione. Per impostazione predefinita, questo valore è impostato su 262.144 byte (256 KB).|
   |Numero massimo di batch|Specifica il numero massimo di eventi in ogni batch inviato alla funzione. Il valore predefinito è 100. Questa proprietà è facoltativa.|
   |Chiave|Consente di usare una funzione di un'altra sottoscrizione. Specificare il valore della chiave per accedere alla funzione. Questa proprietà è facoltativa.|

3. Specificare un nome per l'alias di output. In questa esercitazione viene usato il nome **saop1**, ma è possibile usare qualsiasi nome. Specificare gli altri dettagli.

4. Aprire il processo di Analisi di flusso e aggiornare la query nel modo seguente. Se per il sink di output non è stato specificato il nome **saop1**, ricordarsi di modificarlo nella query.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Avviare l'applicazione telcodatagen.exe eseguendo il comando seguente nella riga di comando. Il comando usa il formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`.  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Avviare il processo di Analisi di flusso.

## <a name="check-azure-cache-for-redis-for-results"></a>Controllare i risultati in Azure Cache per Redis

1. Accedere al portale di Azure e individuare Azure Cache per Redis. Selezionare **Console**.  

2. Usare [i comandi di Azure Cache per Redis](https://redis.io/commands) per verificare che i dati siano in Azure Cache per Redis. Il comando assume il formato Get {chiave}. Ad esempio:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Questo comando dovrebbe visualizzare il valore relativo alla chiave specificata:

   ![Screenshot dell’output di Azure Cache per Redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Gestione degli errori e tentativi

Se si verifica un errore durante l'invio di eventi a Funzioni di Azure, Analisi di flusso ritenta la maggior parte delle operazioni. Tutte le eccezioni HTTP vengono ritentate fino all'esito positivo, ad eccezione dell'errore HTTP 413 (entità troppo grande). Un errore di tipo entità troppo grande viene considerato un errore di dati soggetto al [criterio Riprova o Rimuovi](stream-analytics-output-error-policy.md).

> [!NOTE]
> Il timeout per le richieste HTTP da Analisi di flusso a Funzioni di Azure è impostato su 100 secondi. Se l'app Funzioni di Azure impiega più di 100 secondi per elaborare un batch, vengono restituiti errori in Analisi di flusso.

## <a name="known-issues"></a>Problemi noti

Nel portale di Azure, quando si tenta di reimpostare il valore di Dimensioni massime batch/Numero massimo di batch su un valore vuoto (impostazione predefinita), al momento del salvataggio il valore viene reimpostato sul valore immesso in precedenza. In questo caso, immettere manualmente i valori predefiniti per questi campi.

L'uso del [routing HTTP](https://docs.microsoft.com/sandbox/functions-recipes/routes?tabs=csharp) in Funzioni di Azure non è attualmente supportato da Analisi di flusso.

Il supporto per la connessione a Funzioni di Azure ospitato in una rete virtuale non è abilitato.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa guida introduttiva seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata.  
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un semplice processo di Analisi di flusso che esegue una funzione di Azure. Per altre informazioni sui processi di Analisi di flusso, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Eseguire funzioni JavaScript definite dall'utente in processi di Analisi di flusso](stream-analytics-javascript-user-defined-functions.md)
