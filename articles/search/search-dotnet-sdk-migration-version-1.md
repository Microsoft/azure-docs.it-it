---
title: Eseguire l'aggiornamento a Ricerca di Azure .NET SDK versione 1.1Upgrade to Azure Search .NET SDK version 1.1
titleSuffix: Azure Cognitive Search
description: Eseguire la migrazione di codice a .NET SDK Ricerca di Azure versione 1.1 da versioni API precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792389"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Eseguire l'aggiornamento a Ricerca di Azure .NET SDK versione 1.1Upgrade to Azure Search .NET SDK version 1.1

Se si usa la versione 1.0.2 di anteprima o precedente di [.NET SDK Ricerca di Azure](https://aka.ms/search-sdk), questo articolo include informazioni utili per aggiornare l'applicazione per l'uso della versione 1.1.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Quando si esegue l'aggiornamento alla versione 1.1 o se è già in uso una versione tra 1.1 e 2.0 inclusiva di anteprima, è consigliabile aggiornare alla versione 3. Per informazioni, vedere [Aggiornamento a .NET SDK Ricerca di Azure versione 3](search-dotnet-sdk-migration.md).
>

Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto.

Se in precedenza si usava la versione 1.0.0 di anteprima, la versione 1.0.1 di anteprima o la versione 1.0.2 di anteprima, la compilazione dovrebbe avere esito positivo e tutto dovrebbe essere pronto per iniziare.

Se in precedenza si usava versione 0.13.0 di anteprima o una versione precedente, la compilazione dovrebbe generare errori simili ai seguenti:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Il passaggio successivo consiste nel correggere gli errori di compilazione uno alla volta. La maggior parte richiederà di modificare alcuni nomi di classe e di metodo rinominati nell'SDK. [Elenco di modifiche di rilievo nella versione 1.1](#ListOfChangesV1) contiene l'elenco delle modifiche dei nomi.

Se si usano classi personalizzate per modellare i documenti e tali classi hanno proprietà di tipi primitivi non nullable, ad esempio, `int` o `bool` in C#, nella versione 1.1 dell'SDK è presente una correzione di bug che è opportuno conoscere. Per altri dettagli, vedere [Correzioni di bug nella versione 1.1](#BugFixesV1) .

Infine, dopo avere corretto gli errori di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Elenco di modifiche di rilievo nella versione 1.1
L'ordinamento dell'elenco seguente segue la probabilità che la modifica abbia effetto sul codice dell'applicazione.

### <a name="indexbatch-and-indexaction-changes"></a>Modifiche a IndexBatch e IndexAction
`IndexBatch.Create` è stato rinominato `IndexBatch.New` e non include più un argomento `params`. È possibile usare `IndexBatch.New` per i batch che combinano tipi diversi di azioni (unioni, eliminazioni e così via). Sono anche presenti nuovi metodi statici per creare batch in cui tutte le azioni sono le stesse: `Delete`, `Merge`, `MergeOrUpload` e `Upload`.

`IndexAction` non include più costruttori pubblici e le proprietà ora sono non modificabili. È consigliabile usare i nuovi metodi statici per creare azioni con scopi diversi: `Delete`, `Merge`, `MergeOrUpload` e `Upload`. `IndexAction.Create` è stato rimosso. Se si è usato l'overload che accetta un solo documento, verificare di usare invece `Upload` .

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se si vuole, è possibile semplificarlo ulteriormente in questo modo:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>Modifiche a IndexBatchException
La proprietà `IndexBatchException.IndexResponse` è stata rinominata `IndexingResults` e il tipo ora è `IList<IndexingResult>`.

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Modifiche ai metodi delle operazioni
Ogni operazione in Azure Search .NET SDK è esposta come set di overload di un metodo per i chiamanti sincroni e asincroni. Le firme e il factoring di questi overload del metodo sono stati modificati nella versione 1.1.

Ad esempio, l'operazione "Get Index Statistics" nelle versioni precedenti dell'SDK espone queste firme:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Le firme dei metodi per la stessa operazione nella versione 1.1 sono simili a questa:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partire dalla versione 1.1, Azure Search .NET SDK organizza i metodi delle operazioni in modo diverso:

* I parametri facoltativi ora vengono modellati come parametri predefiniti invece che come overload dei metodi aggiuntivi. Questo riduce, in alcuni casi anche considerevolmente, il numero di overload dei metodi.
* I metodi di estensione ora nascondono al chiamante numerosi dettagli estranei di HTTP. Ad esempio, le versioni precedenti dell'SDK restituiscono un oggetto risposta con un codice di stato HTTP, che spesso non è necessario controllare perché i metodi delle operazioni generano `CloudException` per tutti i codici di stato indicanti un errore. I nuovi metodi di estensione restituiscono solo oggetti modello, evitando il fastidio di doverne annullare il wrapping nel codice.
* Al contrario, le interfacce principali ora espongono metodi che offrono un maggiore controllo a livello HTTP, se necessario. Ora è possibile passare le intestazioni HTTP personalizzate da includere nelle richieste e il nuovo tipo restituito `AzureOperationResponse<T>` consente l'accesso diretto a `HttpRequestMessage` e a `HttpResponseMessage` per l'operazione. `AzureOperationResponse` è definito nello spazio dei nomi `Microsoft.Rest.Azure` e sostituisce `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>Modifiche a ScoringParameters
Una nuova classe denominata `ScoringParameter` è stata aggiunta alla versione più recente dell'SDK per semplificare la specifica di parametri per i profili di punteggio in una query di ricerca. In precedenza la proprietà `ScoringProfiles` della classe `SearchParameters` veniva tipizzata come `IList<string>`. Ora viene tipizzata come `IList<ScoringParameter>`.

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

È possibile sostituirlo con questo per correggere gli errori di compilazione: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Modifiche alle classi di modelli
In seguito alle modifiche apportate alle firme descritte in [Modifiche ai metodi delle operazioni](#OperationMethodChanges), molte classi nello spazio dei nomi `Microsoft.Azure.Search.Models` sono state rinominate o rimosse. Ad esempio:

* `IndexDefinitionResponse` è stata sostituita da `AzureOperationResponse<Index>`
* `DocumentSearchResponse` è stata rinominata `DocumentSearchResult`
* `IndexResult` è stata rinominata `IndexingResult`
* `Documents.Count()` ora restituisce `long` con il conteggio dei documenti invece di `DocumentCountResponse`
* `IndexGetStatisticsResponse` è stata rinominata `IndexGetStatisticsResult`
* `IndexListResponse` è stata rinominata `IndexListResult`

Per riepilogare, le classi derivate da `OperationResponse`che servivano solo a eseguire il wrapping di un oggetto modello sono state rimosse. I suffissi delle altre classi sono passati da `Response` a `Result`.

#### <a name="example"></a>Esempio
Se il codice è simile a questo:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Classi di risposte e IEnumerable
Un'altra modifica che può avere effetto sul codice è che le classi di risposte contenenti raccolte non implementano più `IEnumerable<T>`. Invece è possibile accedere direttamente alla proprietà della raccolta. Ad esempio, se il codice è simile a questo:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Caso speciale per le applicazioni Web
Se è presente un'applicazione Web che serializza direttamente `DocumentSearchResponse` per inviare i risultati della ricerca al browser, sarà necessario modificare il codice o i risultati non verranno serializzati correttamente. Ad esempio, se il codice è simile a questo:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

È possibile modificarla ottenendo la proprietà `.Results` della risposta della ricerca per correggere il rendering dei risultati della ricerca:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Sarà necessario cercare manualmente tali casi nel codice. **Il compilatore non genererà avvisi** perché `JsonResult.Data` è di tipo `object`.

### <a name="cloudexception-changes"></a>Modifiche a CloudException
La classe `CloudException` è stata spostata dallo spazio dei nomi `Hyak.Common` allo spazio dei nomi `Microsoft.Rest.Azure`. Inoltre la proprietà `Error` è stata rinominata `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Modifiche a SearchServiceClient e SearchIndexClient
Il tipo della proprietà `Credentials` è passato da `SearchCredentials` alla relativa classe base, `ServiceClientCredentials`. Se è necessario accedere a `SearchCredentials` di un oggetto `SearchIndexClient` o `SearchServiceClient`, usare la nuova proprietà `SearchCredentials`.

Nelle versioni precedenti dell'SDK `SearchServiceClient` e `SearchIndexClient` hanno costruttori che accettano un parametro `HttpClient`. Questi sono stati sostituiti con costruttori che accettano `HttpClientHandler` e una matrice di oggetti `DelegatingHandler`. In questo modo è più semplice installare gestori personalizzati per pre-elaborare le richieste HTTP, se necessario.

Infine sono stati modificati i costruttori che accettano `Uri` e `SearchCredentials`. Ad esempio, se il codice è simile a questo:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Si noti anche che il tipo del parametro delle credenziali è diventato `ServiceClientCredentials`. È improbabile che ciò abbia effetto sul codice perché `SearchCredentials` deriva da `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Passaggio di un ID richiesta
Nelle versioni precedenti dell'SDK è possibile impostare un ID richiesta su `SearchServiceClient` o su `SearchIndexClient` per includerlo in ogni richiesta per l'API REST. Ciò è utile per la risoluzione dei problemi relativi al servizio di ricerca se è necessario contattare il supporto. È però più utile impostare un ID richiesta univoco per ogni operazione invece di usare lo stesso ID per tutte le operazioni. Per questo motivo, i metodi `SetClientRequestId` di `SearchServiceClient` e `SearchIndexClient` sono stati rimossi. È invece possibile passare un ID richiesta a ogni metodo di un'operazione tramite il parametro `SearchRequestOptions` facoltativo.

> [!NOTE]
> In una delle versioni future dell'SDK verrà aggiunto un nuovo meccanismo per impostare un ID richiesta a livello globale negli oggetti client, con un approccio coerente con quello usato da altri Azure SDK.
> 
> 

### <a name="example"></a>Esempio
Se il codice è simile a questo:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

È possibile sostituirlo con questo per correggere gli errori di compilazione:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Modifiche ai nomi delle interfacce
I nomi delle interfacce dei gruppi di operazioni sono stati tutti modificati in modo che fossero coerenti con i corrispondenti nomi di proprietà:

* Il tipo di `ISearchServiceClient.Indexes` è stato rinominato da `IIndexOperations` a `IIndexesOperations`.
* Il tipo di `ISearchServiceClient.Indexers` è stato rinominato da `IIndexerOperations` a `IIndexersOperations`.
* Il tipo di `ISearchServiceClient.DataSources` è stato rinominato da `IDataSourceOperations` a `IDataSourcesOperations`.
* Il tipo di `ISearchIndexClient.Documents` è stato rinominato da `IDocumentOperations` a `IDocumentsOperations`.

Questa modifica potrebbe avere effetto sul codice solo se si creano interfacce fittizie a scopo di test.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Correzioni di bug nella versione 1.1
Nelle versioni precedenti di Azure Search .NET SDK esiste un bug relativo alla serializzazione delle classi di modelli personalizzate. Il bug può verificarsi se si crea una classe di modelli personalizzata con una proprietà di un tipo di valore non nullable.

### <a name="steps-to-reproduce"></a>Passaggi per riprodurre il bug
Creare una classe di modelli personalizzata con una proprietà di tipo di valore non nullable. Ad esempio, aggiungere una proprietà pubblica `UnitCount` di tipo `int` invece che `int?`.

Se si indicizza un documento con il valore predefinito di tale tipo (ad esempio, 0 per `int`), il campo sarà null in Ricerca di Azure. Se in seguito si cerca tale documento, la chiamata a `Search` genererà `JsonSerializationException` perché non riesce a convertire `null` in `int`.

Anche i filtri potrebbero non funzionare normalmente perché nell'indice è stato scritto null invece del valore previsto.

### <a name="fix-details"></a>Dettagli della correzione
Questo problema è stato risolto nella versione 1.1 dell'SDK. Se è presente una classe di modelli come questa:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e si imposta `IntValue` su 0, tale valore ora viene serializzato correttamente come 0 durante il transito e archiviato come 0 nell'indice. Anche il round trip funziona come previsto.

Questo approccio presenta un potenziale problema che è opportuno tenere presente: se si usa un tipo di modello con una proprietà che non ammette i valori null, è necessario **garantire** che nessun documento nell'indice contenga un valore null per il campo corrispondente. Né l'SDK né l'API REST per Ricerca di Azure consentiranno di applicare questo valore.

Non è solo un problema ipotetico: si pensi a uno scenario in cui si aggiunge un nuovo campo a un indice esistente di tipo `Edm.Int32`. Dopo l'aggiornamento della definizione dell'indice, tutti i documenti avranno un valore Null per il nuovo campo (perché tutti i tipi sono nullable in Ricerca di Azure). Se quindi si usa una classe di modelli con una proprietà `int` che non ammette i valori Null per tale campo, verrà restituita un'eccezione `JsonSerializationException`, come questa, quando si cercherà di recuperare i documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è ancora consigliabile usare tipi nullable nelle classi di modelli.

Per altri dettagli sul bug e sulla correzione, vedere [questo problema in GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

