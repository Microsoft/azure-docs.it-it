---
title: Ottenere le prestazioni di query SQL & le metriche di esecuzione
description: Informazioni su come recuperare le metriche di esecuzione delle query SQL e profilare le prestazioni delle query SQL delle richieste Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097397"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Ottenere le metriche di esecuzione delle query SQL e analizzare le prestazioni delle query con .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo illustra come profilare le prestazioni delle query SQL in Azure Cosmos DB. Questa profilatura può essere eseguita usando `QueryMetrics` recuperato da .NET SDK ed è descritta in dettaglio qui. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) è un oggetto fortemente tipizzato con informazioni sull'esecuzione di query back-end. Queste metriche sono documentate più dettagliatamente nell'articolo [ottimizzare le prestazioni delle query](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>Impostare il parametro FeedOptions

Tutti gli overload di [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) accettano un parametro facoltativo [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) . Questa opzione consente di ottimizzare e parametrizzare l'esecuzione delle query. 

Per raccogliere le metriche di esecuzione delle query SQL, è necessario impostare il parametro [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) in [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) su `true` . Se si imposta `PopulateQueryMetrics` su true, l'oggetto conterrà `FeedResponse` l'oggetto pertinente `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Ottenere le metriche di query con AsDocumentQuery ()
Nell'esempio di codice seguente viene illustrato come recuperare le metriche quando si usa il metodo [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Aggregazione di QueryMetrics

Nella sezione precedente si noti che erano presenti più chiamate al metodo [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) . Ogni chiamata ha restituito un `FeedResponse` oggetto con un dizionario di `QueryMetrics` ; uno per ogni continuazione della query. Nell'esempio seguente viene illustrato come aggregare questi elementi `QueryMetrics` utilizzando LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Raggruppamento di metriche di query in base all'ID partizione

È possibile raggruppare `QueryMetrics` in base all'ID della partizione. Il raggruppamento in base all'ID di partizione consente di verificare se una partizione specifica causa problemi di prestazioni rispetto ad altri. Nell'esempio seguente viene illustrato come eseguire il raggruppamento `QueryMetrics` con LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ su DocumentQuery

È anche possibile ottenere `FeedResponse` da una query LINQ usando il `AsDocumentQuery()` Metodo:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Query dispendiose

È possibile acquisire le unità richiesta utilizzate da ogni query per analizzare le query o le query dispendiose che utilizzano una velocità effettiva elevata. È possibile ottenere l'addebito per le richieste usando la proprietà [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) in `FeedResponse`. Per altre informazioni su come ottenere l'addebito per le richieste usando il portale di Azure e diversi SDK, vedere l'articolo [trovare l'addebito delle unità richiesta](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Ottenere il tempo di esecuzione della query

Quando si calcola il tempo necessario per eseguire una query sul lato client, assicurarsi di includere solo il tempo per chiamare il `ExecuteNextAsync` metodo e non altre parti della codebase. Queste chiamate consentono di calcolare il tempo impiegato per l'esecuzione della query, come illustrato nell'esempio seguente:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Query di analisi (in genere lenta e costosa)

Una query di analisi fa riferimento a una query che non è stata gestita dall'indice, a causa della quale, molti documenti vengono caricati prima di restituire il set di risultati.

Di seguito è riportato un esempio di query di analisi:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Il filtro della query utilizza la funzione di sistema UPPER, che non viene servita dall'indice. L'esecuzione di questa query su una raccolta di grandi dimensioni ha prodotto le metriche di query seguenti per la prima continuazione:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Prendere nota dei valori seguenti nell'output della metrica della query:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Questa query ha caricato 60.951 documenti, che hanno totalizzato 399.998.938 byte. Il caricamento di molti byte comporta un costo elevato o un addebito per le unità richiesta. L'esecuzione della query richiede molto tempo, che è evidente con la proprietà tempo totale impiegato:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Il che significa che la query ha richiesto 4,5 secondi per l'esecuzione (ed era solo una continuazione).

Per ottimizzare questa query di esempio, evitare l'uso di UPPER nel filtro. Al contrario, quando i documenti vengono creati o aggiornati, i `c.description` valori devono essere inseriti in tutti i caratteri maiuscoli. La query diventa quindi: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

È ora possibile servire questa query dall'indice.

Per altre informazioni sull'ottimizzazione delle prestazioni delle query, vedere l'articolo Ottimizzazione [delle prestazioni delle query](./sql-api-query-metrics.md) .

## <a name="references"></a><a id="References"></a>Riferimenti

- [Specifica SQL di Azure Cosmos DB](./sql-query-getting-started.md)
- [SQL 2011 ANSI](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare le prestazioni delle query](sql-api-query-metrics.md)
- [Panoramica sull'indicizzazione](index-overview.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)