---
title: Monitorare i dati del database Cosmos di Azure usando le impostazioni di Diagnostica di AzureMonitor Azure Cosmos DB data by using Azure Diagnostic settings
description: Informazioni su come usare le impostazioni di Diagnostica di Azure per monitorare le prestazioni e la disponibilità dei dati archiviati in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252062"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorare i dati del database Cosmos di Azure usando le impostazioni di diagnostica in AzureMonitor Azure Cosmos DB data by using diagnostic settings in Azure

Le impostazioni di diagnostica in Azure vengono usate per raccogliere i log delle risorse. I log delle risorse di Azure vengono generati da una risorsa e forniscono dati frequenti e dettagliati sul funzionamento di tale risorsa. Questi registri vengono acquisiti per ogni richiesta e vengono anche definiti "registri piano dati". Alcuni esempi di operazioni del piano dati includono delete, insert e readFeed. Il contenuto di questi log varia in base al tipo di risorsa.

Le metriche della piattaforma e i log attività vengono raccolti automaticamente, mentre è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o inoltrarli all'esterno di Monitoraggio di Azure.Platform metrics and the Activity logs are collected automatically, whereas you must create a diagnostic setting to collect resource logs or forward them outside of Azure Monitor. È possibile attivare l'impostazione di diagnostica per gli account di Azure Cosmos usando la procedura seguente:You can turn on diagnostic setting for Azure Cosmos accounts by using the following steps:

1. Accedere al portale di [Azure](https://portal.azure.com).

1. Accedere all'account Azure Cosmos. Aprire il riquadro **Impostazioni di diagnostica** e quindi selezionare l'opzione Aggiungi **impostazione diagnostica.**

1. Nel riquadro **Impostazioni di diagnostica** compilare il modulo con i dettagli seguenti: 

    * **Nome**: immettere un nome per i log da creare.

    * È possibile archiviare i log da archiviare in **un account di archiviazione,** eseguire lo streaming in un hub eventi o **inviare a Log AnalyticsYou** can store the logs to Archive to a storage **account, Stream to an event hub** or Send to Log Analytics

1. Quando si crea un'impostazione di diagnostica, si specifica la categoria di log da raccogliere. Le categorie di log supportate da Azure Cosmos DB sono elencate di seguito insieme al log di esempio raccolto da essi:The categories of logs supported by Azure Cosmos DB are listed below with sample log collected by them:

 * **DataPlaneRequests:** selezionare questa opzione per registrare le richieste back-end a tutte le API, che includono gli account SQL, Graph, MongoDB, Cassandra e Table API in Azure Cosmos DB. Le proprietà chiave `Requestcharge`da `statusCode` `clientIPaddress`notare `partitionID`sono: , , , e .

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests:** selezionare questa opzione per registrare le richieste avviate dall'utente dal front-end per soddisfare le richieste all'API di Azure Cosmos DB per MongoDB, questo tipo di log non è disponibile per altri account API. Le richieste MongoDB verranno visualizzate in MongoRequests e DataPlaneRequests. Le proprietà chiave `Requestcharge`da `opCode`notare sono: , .

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selezionare questa opzione per registrare il testo della query eseguito. Questo tipo di log è disponibile solo per gli account API SQL.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selezionare questa opzione per registrare le statistiche delle chiavi di partizione. Questo è attualmente rappresentato con la dimensione di archiviazione (KB) delle chiavi di partizione. Vedere la sezione [Risoluzione dei problemi tramite le query](#diagnostic-queries) di diagnostica di Azure di questo articolo. Ad esempio query che utilizzano "PartitionKeyStatistics". Il log viene generato rispetto alle prime tre chiavi di partizione che occupano la maggior parte dell'archiviazione dei dati. Questo log contiene dati quali l'ID sottoscrizione, il nome dell'area, il nome del database, il nome della raccolta, la chiave di partizione e la dimensione di archiviazione in KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption:** questo log segnala l'utilizzo aggregato di RU al secondo delle chiavi di partizione. Attualmente, il database Cosmos di Azure segnala le chiavi di partizione solo per gli account API SQL e per le operazioni di lettura/scrittura e stored procedure in punti. altre API e tipi di operazione non sono supportati. Per altre API, la colonna chiave di partizione nella tabella del log di diagnostica sarà vuota. Questo log contiene dati quali l'ID sottoscrizione, il nome dell'area, il nome del database, il nome della raccolta, la chiave di partizione, il tipo di operazione e l'addebito della richiesta. Vedere la sezione [Risoluzione dei problemi tramite le query](#diagnostic-queries) di diagnostica di Azure di questo articolo. Ad esempio query che utilizzano "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Questo registro contiene dettagli sulle operazioni del piano di controllo come la creazione di un account, l'aggiunta o la rimozione di un'area, l'aggiornamento delle impostazioni di replica dell'account e così via. Questo tipo di log è disponibile per tutti i tipi di API che includono SQL (Core), MongoDB, Gremlin, Cassandra, API tabella.

* **Richieste:** selezionare questa opzione per raccogliere i dati delle metriche dal database Cosmos di Azure alle destinazioni nell'impostazione di diagnostica. Si tratta degli stessi dati raccolti automaticamente nelle metriche di Azure.This is the same data collected automatically in Azure Metrics. Raccogliere i dati delle metriche con i log delle risorse per analizzare entrambi i tipi di dati insieme e per inviare dati di metrica all'esterno di Monitoraggio di Azure.Collect metric data with resource logs to analyze both kinds of data together and to send metric data outside of Azure Monitor.

Per informazioni dettagliate su come creare un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell, vedere [Creare un'impostazione di diagnostica per raccogliere i log e le metriche](../azure-monitor/platform/diagnostic-settings.md) della piattaforma nell'articolo di Azure.For detailed information about how to create a diagnostic setting by using the Azure portal, CLI, or PowerShell, see Create diagnostic setting to collect platform logs and metrics in Azure article.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Risolvere i problemi relativi alle query di diagnosticaTroubleshoot issues with diagnostics queries

1. Come ottenere gli addebiti di richiesta per le query costose?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Come trovare quali operazioni stanno prendendo la maggior parte di RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Come ottenere la distribuzione per diverse operazioni?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual è la velocità effettiva massima utilizzata da una partizione?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Come ottenere le informazioni sulle chiavi di partizione RU/s consumo al secondo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Come ottenere l'addebito della richiesta per una chiave di partizione specificaHow to get the request charge for a specific partition key

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Come ottenere le chiavi di partizione superiore con la maggior parte RU/s utilizzati in un periodo specifico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Come ottenere i log per le chiavi di partizione la cui dimensione di archiviazione è maggiore di 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Come ottenere statistiche chiave di partizione per valutare l'asimmetria tra le prime tre partizioni per l'account del database?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)
