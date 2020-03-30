---
title: Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB
description: Usare le metriche in Azure Cosmos DB per eseguire il debug di problemi comuni e monitorare il database.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065923"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB

Azure Cosmos DB offre metriche per velocità effettiva, archiviazione, coerenza, disponibilità e latenza. Il portale di Azure offre una visualizzazione aggregata di queste metriche. È anche possibile visualizzare le metriche di Azure Cosmos DB nell'API di Monitoraggio di Azure. Per altre informazioni su come visualizzare le metriche dal monitoraggio di Azure, vedere l'articolo [Ottenere metriche da Monitoraggio di Azure.To](cosmos-db-azure-monitor-metrics.md) learn about how to view metrics from Azure monitor, see the Get metrics from Azure Monitor article. 

Questo articolo illustra dettagliatamente i casi d'uso comuni e come usare le metriche di Azure Cosmos DB per analizzare ed eseguire il debug di questi problemi. Le metriche vengono raccolte ogni cinque minuti e vengono conservate per sette giorni.

## <a name="view-metrics-from-azure-portal"></a>Visualizzare le metriche dal portale di AzureView metrics from Azure portal

1. Accedere al [portale di Azure](https://portal.azure.com/)

1. Aprire il riquadro **Metriche.** Per impostazione predefinita, il riquadro delle metriche mostra le metriche di archiviazione, indice e unità richiesta per tutti i database nell'account Cosmos di Azure.By default, the metrics pane shows the storage, index, request units metrics for all the databases in your Azure Cosmos account. È possibile filtrare queste metriche per ogni database, contenitore o area. È inoltre possibile filtrare le metriche in base a una granularità temporale specifica. Ulteriori dettagli sulle metriche di velocità effettiva, archiviazione, disponibilità, latenza e coerenza vengono forniti in schede separate. 

   ![Metriche delle prestazioni di Cosmos DB nel portale di AzureCosmos DB performance metrics in Azure portal](./media/use-metrics/performance-metrics.png)

Nel riquadro **Metriche** sono disponibili le metriche seguenti:The following metrics are available from the Metrics pane: 

* **Metriche della velocità effettiva:** questa metrica indica il numero di richieste utilizzate o non riuscite (codice di risposta 429) perché la velocità effettiva o la capacità di archiviazione di cui è stato eseguito il provisioning per il contenitore è stata superata.

* **Metriche di archiviazione:** questa metrica mostra le dimensioni dei dati e l'utilizzo dell'indice.

* **Metriche di disponibilità:** questa metrica mostra la percentuale di richieste riuscite sul totale delle richieste all'ora. La percentuale di successo è definita dai contratti di gruppo di Azure Cosmos DB.

* **Metriche di latenza:** questa metrica mostra la latenza di lettura e scrittura osservata da Azure Cosmos DB nell'area in cui opera l'account. È possibile visualizzare la latenza tra le aree per un account con replica geografica. Questa metrica non rappresenta la latenza delle richieste end-to-end.

* **Metriche di coerenza:** questa metrica mostra come sia possibile la coerenza per il modello di coerenza scelto. Per gli account con più aree, questa metrica mostra anche la latenza di replica tra le aree selezionate.

* **Metriche di sistema:** questa metrica indica quante richieste di metadati vengono servite dalla partizione master. Aiuta anche a identificare le richieste limitate.

Le sezioni seguenti illustrano scenari comuni in cui è possibile usare le metriche di Azure Cosmos DB.The following sections explain common scenarios where you can use Azure Cosmos DB metrics. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Scoprire il numero di richieste che riesce o causa errori

Per iniziare, accedere al [portale di Azure](https://portal.azure.com) e passare al pannello **Metriche**. Nel pannello, trovare il numero di richieste superato la capacità per grafico di 1 minuto. Questo grafico mostra un minuto delle richieste totali per minuto segmentate in base al codice di stato. Per altre informazioni sui codici di stato HTTP, vedere Codici di [stato HTTP per Azure Cosmos DB.](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

Il codice di stato di errore più comune è 429 (limitazione della velocità/limitazione). Questo errore indica che le richieste ad Azure Cosmos DB sono maggiori rispetto alle UR di cui è stato effettuato provisioning. La soluzione più comune per questo problema consiste nell'[aumentare il numero di UR](./set-throughput.md) per la raccolta specificata.

![Numero di richieste al minuto](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determinare la distribuzione della velocità effettiva tra le partizioni

Avere una buona cardinalità delle chiavi di partizione è essenziale per qualsiasi applicazione scalabile. Per determinare la distribuzione della velocità effettiva di un contenitore partizionato suddiviso per partizione, passare al **pannello Metriche** nel [portale di Azure](https://portal.azure.com). Nella scheda **Velocità effettiva** la scomposizione di archiviazione viene mostrata nel grafico **Numero massimo di unità richiesta al secondo utilizzate da ogni partizione fisica**. Il grafico seguente illustra un esempio di distribuzione non efficace dei dati come evidenziato dalla deviazione della partizione all'estrema sinistra.

![Partizione singola che mostra un uso intenso alle 15:05](media/use-metrics/metrics-17.png)

Una distribuzione non uniforme della velocità effettiva può generare partizioni *critiche*, che possono ridurre le richieste e potrebbero richiedere una nuova ripartizione. Per altre informazioni sul partizionamento in Azure Cosmos DB, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determinare la distribuzione dell'archiviazione tra le partizioni

Avere una buona cardinalità della partizione è essenziale per qualsiasi applicazione scalabile. Per determinare la distribuzione dell'archiviazione di un contenitore partizionato suddiviso per partizione, passare al pannello Metriche nel [portale di Azure](https://portal.azure.com). Nella scheda Archiviazione la scomposizione dell'archiviazione viene mostrata nel grafico della risorsa di archiviazione dati + indice utilizzata dalle chiavi di partizione principali. Il grafico seguente illustra una distribuzione non efficace della risorsa di archiviazione dati come evidenziato dalla deviazione della partizione all'estrema sinistra.

![Esempio di distribuzione dei dati ridotta](media/use-metrics/metrics-07.png)

È possibile capire quale chiave di partizione riduce la distribuzione facendo clic sulla partizione nel grafico.

![Chiave di partizione che riduce la distribuzione](media/use-metrics/metrics-05.png)

Dopo aver identificato la chiave di partizione che riduce la distribuzione, è possibile che sia necessario ripartizionare il contenitore con una chiave di partizione più distribuita. Per altre informazioni sul partizionamento in Azure Cosmos DB, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Confrontare le dimensioni dei dati e le dimensioni dell'indice

In Azure Cosmos DB lo spazio di archiviazione totale usato risulta dalla combinazione delle dimensioni dei dati e delle dimensioni dell'indice. In genere, le dimensioni dell'indice sono una frazione delle dimensioni dei dati. Nel pannello Metriche nel [portale di Azure](https://portal.azure.com) la scheda Archiviazione mostra la suddivisione dell'uso dello spazio di archiviazione in base ai dati e all'indice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Se si desidera risparmiare spazio degli indici, è possibile modificare i [criteri di indicizzazione](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Eseguire il degug per individuare il motivo per cui l'esecuzione delle query risulta rallentata

Negli SDK dell'API SQL Azure Cosmos DB presenta le statistiche di esecuzione delle query.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* offre informazioni dettagliate sulla durata dell'esecuzione di ogni componente della query. La causa radice più comune per le query con esecuzione prolungata è rappresentata dalle analisi, che indicano che la query non è riuscita a sfruttare gli indici. Questo problema può essere risolto con una condizione di filtro migliore.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come monitorare ed eseguire il debug dei problemi con le metriche disponibili nel portale di Azure. È possibile leggere gli articoli seguenti per scoprire di più su come migliorare le prestazioni del database:

* Per altre informazioni su come visualizzare le metriche dal monitoraggio di Azure, vedere l'articolo [Ottenere metriche da Monitoraggio di Azure.To](cosmos-db-azure-monitor-metrics.md) learn about how to view metrics from Azure monitor, see the Get metrics from Azure Monitor article. 
* [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md)
* [Suggerimenti sulle prestazioni per Azure Cosmos DB](performance-tips.md)
