---
title: Esercitazione per C# - Ottimizzare l'indicizzazione con l'API push
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare i dati in modo efficiente usando l'API push di Ricerca cognitiva di Azure. Questa esercitazione e il codice di esempio usano C#.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094193"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Esercitazione: Ottimizzare l'indicizzazione con l'API push

Ricerca cognitiva di Azure supporta [due approcci di base](search-what-is-data-import.md) per l'importazione di dati in un indice di ricerca: eseguire il *push* dei dati nell'indice a livello di codice oppure puntare a un [indicizzatore di Ricerca cognitiva di Azure](search-indexer-overview.md) in un'origine dati supportata per eseguire il *pull* nei dati.

Questa esercitazione illustra come indicizzare i dati in modo efficiente con il [modello push](search-what-is-data-import.md#pushing-data-to-an-index) eseguendo l'invio in batch delle richieste e usando una strategia di ripetizione dei tentativi con backoff esponenziale. È possibile [scaricare ed eseguire l'applicazione di esempio](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Questo articolo illustra gli aspetti principali dell'applicazione e i fattori da considerare quando si indicizzano i dati.

In questa esercitazione si usa C# e [.NET SDK](/dotnet/api/overview/azure/search) per le attività seguenti:

> [!div class="checklist"]
> * Creare un indice
> * Testare diverse dimensioni di batch per determinare quelle più efficienti
> * Indicizzare i batch in modo asincrono
> * Usare più thread per aumentare la velocità di indicizzazione
> * Usare una strategia di ripetizione di tentativi di backoff esponenziale per eseguire nuovi tentativi con i documenti che presentano errori

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione sono necessari i servizi e gli strumenti seguenti.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

+ [Creare un servizio di Ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente.

<a name="get-service-info"></a>

## <a name="download-files"></a>Scaricare i file

Il codice sorgente di questa esercitazione si trova nella cartella [optimzize-data-indexing/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) del repository GitHub [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="key-considerations"></a>Considerazioni sulle chiavi

Quando si esegue il push dei dati in un indice, esistono diverse considerazioni chiave che influiscono sulla velocità di indicizzazione. Per altre informazioni su questi fattori, vedere l'articolo [Indicizzare set di dati di grandi dimensioni](search-howto-large-index.md).

Di seguito sono illustrati i sei fattori chiave da tenere in considerazione.

+ **Livello di servizio e numero di partizioni/repliche**: sia l'aggiunta di partizioni che l'aumento del livello aumenteranno la velocità di indicizzazione.
+ **Schema dell'indice**: sia l'aggiunta di campi che l'aggiunta di altre proprietà ai campi (ad esempio, *searchable*, *facetable* o *filterable*) riducono la velocità di indicizzazione.
+ **Dimensioni dei batch**: le dimensioni ottimali dei batch variano in base allo schema dell'indice e al set di dati.
+ **Numero di thread/ruoli di lavoro**: un singolo thread non sfrutterà appieno la velocità di indicizzazione.
+ **Strategia di ripetizione dei tentativi**: per ottimizzare l'indicizzazione è consigliabile usare una strategia di ripetizione dei tentativi con backoff esponenziale.
+ **Velocità di trasferimento dei dati di rete**: la velocità di trasferimento dei dati può essere un fattore limitante. Indicizzare i dati all'interno dell'ambiente Azure per aumentare la velocità di trasferimento dei dati.


## <a name="1---create-azure-cognitive-search-service"></a>1 - Creare un servizio di Ricerca cognitiva di Azure

Per completare questa esercitazione sarà necessario un servizio di Ricerca cognitiva di Azure, che è possibile [creare nel portale](search-create-service-portal.md). È consigliabile usare lo stesso livello che si prevede di usare nell'ambiente di produzione, per poter testare e ottimizzare la velocità di indicizzazione in modo accurato.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

Le chiamate API richiedono l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   ![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-rest/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

## <a name="2---set-up-your-environment"></a>2 - Configurare l'ambiente

1. Avviare Visual Studio e aprire **OptimizeDataIndexing.sln**.
1. In Esplora soluzioni aprire il file **appsettings.json** per aggiungervi le informazioni sulla connessione.

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - Esplorare il codice

Dopo l'aggiornamento di *appsettings.json*, il programma di esempio in **OptimizeDataIndexing.sln** sarà pronto per la compilazione e l'esecuzione.

Il codice deriva dalla [guida di avvio rapido per C#](search-get-started-dotnet.md). In tale articolo sono disponibili informazioni più dettagliate sulle nozioni di base per l'uso di .NET SDK.

Questa semplice app console in C#/.NET esegue le attività seguenti:

+ Crea un nuovo indice basato sulla struttura dei dati della classe Hotel in C# (che fa anche riferimento alla classe Address)
+ Testa diverse dimensioni di batch per determinare quelle più efficienti
+ Indicizza i dati in modo asincrono
    + Usando più thread per aumentare la velocità di indicizzazione
    + Usando una strategia di ripetizione dei tentativi con backoff esponenziale per gli elementi con errori

 Prima di eseguire il programma, esaminare il codice e le definizioni di indice per questo esempio. Il codice rilevante si trova in diversi file:

  + **Hotel.cs** e **Address.cs** contengono lo schema che definisce l'indice
  + **DataGenerator.cs** contiene una classe semplice per facilitare la creazione di grandi quantità di dati degli alberghi
  + **ExponentialBackoff.cs** contiene il codice per ottimizzare il processo di indicizzazione, come descritto di seguito
  + **Program.cs** contiene funzioni per creare ed eliminare l'indice di Ricerca cognitiva di Azure, indicizza i batch di dati e testa diverse dimensioni di batch.

### <a name="creating-the-index"></a>Creazione dell'indice

Questo programma di esempio usa .NET SDK per definire e creare un indice di Ricerca cognitiva di Azure. Sfrutta la classe `FieldBuilder` per generare una struttura di indice da una classe di modello di dati C#.

Il modello di dati è definito dalla classe Hotel, che contiene anche riferimenti alla classe Address. FieldBuilder esegue il drill-down attraverso più definizioni di classi per generare una struttura dei dati complessa per l'indice. I tag di metadati vengono usati per definire gli attributi di ogni campo, ad esempio se è ordinabile o ricercabile.

I frammenti di codice seguenti del file **Hotel.cs** illustrano come sia possibile specificare un singolo campo e un riferimento a un'altra classe di modello di dati.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

Nel file **Program.cs** l'indice è definito con un nome e una raccolta di campi generata dal metodo `FieldBuilder.Build(typeof(Hotel))` e viene quindi creato come segue:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Generazione dei dati

Nel file **DataGenerator.cs** viene implementata una classe semplice per generare i dati per i test. L'unico scopo di questa classe è facilitare la creazione di un numero elevato di documenti con ID univoco per l'indicizzazione.

Per ottenere un elenco di 100.000 hotel con ID univoci, eseguire le righe di codice seguenti:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

A scopo di test, in questo esempio sono disponibili due tipi di albergo: **small** e **large**.

Lo schema dell'indice può avere un impatto significativo sulla velocità di indicizzazione. A causa di tale impatto, al termine dell'esercitazione è opportuno convertire questa classe in modo da generare dati corrispondenti allo schema dell'indice desiderato.

## <a name="4---test-batch-sizes"></a>4 - Testare le dimensioni dei batch

Per caricare uno o più documenti in un indice, Ricerca cognitiva di Azure supporta le API seguenti:

+ [Aggiungere, aggiornare o eliminare documenti (API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) o [classe IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

L'indicizzazione dei documenti in batch migliorerà significativamente le prestazioni di indicizzazione. I batch possono contenere fino a 1000 documenti oppure fino a circa 16 MB per batch.

Determinare le dimensioni ottimali dei batch per i dati è fondamentale per ottimizzare la velocità di indicizzazione. I due fattori principali che influiscono sulle dimensioni ottimali dei batch sono i seguenti:

+ Schema dell'indice
+ Dimensioni dei dati

Dato che le dimensioni ottimali dei batch dipendono dall'indice e dai dati, l'approccio migliore consiste nel testare dimensioni di batch diverse per determinare quali garantiscono la velocità di indicizzazione più elevata per lo scenario specifico.

La funzione seguente illustra un approccio semplice per testare le dimensioni dei batch.

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Dato che non tutti i documenti sono delle stesse dimensioni (nonostante in questo esempio lo siano), si stimano le dimensioni dei dati inviati al servizio di ricerca. A tale scopo si usa la funzione seguente, che prima converte l'oggetto in JSON e quindi ne determina le dimensioni in byte. Questa tecnica consente di determinare le dimensioni di batch più efficienti in termini di velocità di indicizzazione in MB/s.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

La funzione richiede `SearchClient` nonché il numero di tentativi da testare per ogni dimensione di batch. Dato che i tempi di indicizzazione per ogni batch potrebbero presentare una certa variabilità, per impostazione predefinita ogni batch viene eseguito tre volte per rendere i risultati statisticamente più significativi.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

Quando si esegue la funzione, nella console verrà visualizzato un output simile al seguente:

   ![Output della funzione di test delle dimensioni dei batch](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Output della funzione di test delle dimensioni dei batch")

Identificare le dimensioni di batch più efficienti e quindi usare tali dimensioni nel passaggio successivo dell'esercitazione. Tra le diverse dimensioni di batch si potrebbe riscontrare una stabilizzazione dei MB/s.

## <a name="5---index-data"></a>5 - Indicizzare dati

Ora che sono state identificate le dimensioni di batch che si intende usare, il passaggio successivo consiste nell'iniziare a indicizzare i dati. Per indicizzare i dati in modo efficiente, questo esempio:

+ Usa più thread/ruoli di lavoro.
+ Implementa una strategia di ripetizione dei tentativi con backoff esponenziale.

### <a name="use-multiple-threadsworkers"></a>Usare più thread/ruoli di lavoro

Per sfruttare appieno la velocità di indicizzazione di Ricerca cognitiva di Azure, sarà probabilmente necessario usare più thread per inviare simultaneamente richieste di indicizzazione in batch al servizio.  

Sul numero ottimale di thread influiscono diverse considerazioni chiave indicate sopra. È possibile modificare questo esempio ed eseguire test con diversi conteggi dei thread per determinare il conteggio ottimale per lo scenario specifico. Con l'esecuzione simultanea di diversi thread, comunque, dovrebbe essere possibile sfruttare la maggior parte dei vantaggi in termini di efficienza.

Quando si aumentano le richieste che raggiungono il servizio di ricerca, potrebbero essere visualizzati [codici di stato HTTP](/rest/api/searchservice/http-status-codes) che indicano che la richiesta non è stata interamente completata. Durante l'indicizzazione, i due codici di stato HTTP comuni sono i seguenti:

+ **503 - Servizio non disponibile**. Questo errore indica che il sistema è in sovraccarico e al momento la richiesta non può essere elaborata.
+ **207 - Multi-Status**. Questo errore indica che alcuni documenti hanno avuto esito positivo, ma almeno uno ha avuto esito negativo.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementare una strategia di ripetizione dei tentativi con backoff esponenziale

Se si verifica un errore, le richieste dovranno essere ripetute usando una [strategia di ripetizione dei tentativi con backoff esponenziale](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

.NET SDK di Ricerca cognitiva di Azure ripete automaticamente le richieste con codice 503 e le altre richieste non riuscite, ma per ripetere le richieste con codice 207 è necessario implementare una logica personalizzata. È anche possibile usare strumenti open source come [Polly](https://github.com/App-vNext/Polly) per implementare una strategia di ripetizione dei tentativi.

In questo esempio si implementa una strategia di ripetizione dei tentativi con backoff esponenziale. A tale scopo, si definiscono prima di tutto alcune variabili, tra cui `maxRetryAttempts` e il valore iniziale di `delay` per una richiesta non riuscita:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

I risultati dell'operazione di indicizzazione sono archiviati nella variabile `IndexDocumentResult result`. Questa variabile è importante perché consente di verificare se i documenti nel batch presentano errori, come illustrato di seguito. In caso di errore parziale, viene creato un nuovo batch in base all'ID dei documenti con l'errore.

È anche necessario rilevare le eccezioni `RequestFailedException`, perché indicano che la richiesta non è completamente riuscita e dovrà essere ritentata.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

A questo punto si esegue il wrapping del codice di backoff esponenziale in una funzione per facilitarne la chiamata.

Viene quindi creata un'altra funzione per gestire i thread attivi. Per semplicità, questa funzione non è inclusa, ma è disponibile in [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). La funzione può essere chiamata con il comando seguente, dove `hotels` rappresenta i dati da caricare, `1000` la dimensione dei batch e `8` il numero di thread simultanei:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

Quando si esegue la funzione, verrà visualizzato un output simile al seguente:

![Output della funzione di indicizzazione dei dati](media/tutorial-optimize-data-indexing/index-data-start.png "Output della funzione di indicizzazione dei dati")

Quando un batch di documenti ha esito negativo, viene visualizzato un errore che indica l'errore e che verrà eseguito un nuovo tentativo:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

Al termine dell'esecuzione della funzione, è possibile verificare che tutti i documenti siano stati aggiunti all'indice.

## <a name="6---explore-index"></a>6 - Esplorare l'indice

Dopo l'esecuzione del programma è possibile esplorare l'indice di ricerca popolato a livello di codice oppure usando [**Esplora ricerche**](search-explorer.md) nel portale.

### <a name="programatically"></a>A livello di codice

Per verificare il numero di documenti in un indice sono disponibili due opzioni principali: l'[API di conteggio dei documenti](/rest/api/searchservice/count-documents) e l'[API di recupero delle statistiche dell'indice](/rest/api/searchservice/get-index-statistics). In entrambi i casi, l'aggiornamento potrebbe richiedere più tempo, pertanto non allarmarsi se il numero di documenti restituito è inizialmente inferiore a quello previsto.

#### <a name="count-documents"></a>Contare i documenti

L'operazione di conteggio dei documenti recupera un conteggio del numero di documenti in un indice di ricerca:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Ottenere le statistiche di un indice

L'operazione di recupero delle statistiche dell'indice restituisce il numero di documenti per l'indice corrente, nonché l'utilizzo dello spazio di archiviazione. L'aggiornamento delle statistiche dell'indice richiederà più tempo rispetto all'aggiornamento del numero di documenti.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure aprire la pagina **Panoramica** del servizio di ricerca e trovare l'indice **optimize-indexing** nell'elenco **Indici**.

  ![Elenco di indici di Ricerca cognitiva di Azure](media/tutorial-optimize-data-indexing/portal-output.png "Elenco di indici di Ricerca cognitiva di Azure")

I valori di *Conteggio documenti* e *Dimensioni archiviazione* sono basati sull'[API di recupero delle statistiche dell'indice](/rest/api/searchservice/get-index-statistics) e il loro aggiornamento potrebbe richiedere alcuni minuti.

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Il codice di esempio per questa esercitazione verifica la presenza di indici esistenti e li elimina affinché sia possibile eseguire nuovamente il codice.

Per eliminare gli indici è anche possibile usare il portale.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con il concetto dell'inserimento efficiente dei dati, si esamineranno l'architettura di query di Lucene e il funzionamento della ricerca full-text in Ricerca cognitiva di Azure.

> [!div class="nextstepaction"]
> [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)