---
title: Come configurare funzionalità multimaster in Azure Cosmos DB
description: Informazioni su come configurare la funzionalità multimaster per le applicazioni usando SDK diversi in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873642"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurare funzionalità multimaster nelle applicazioni che usano Azure Cosmos DB

Dopo aver creato un account con più aree di scrittura abilitate, è necessario apportare due modifiche a ConnectionPolicy nell'applicazione per far sì che DocumentClient abiliti le funzionalità multimaster e multihoming in Azure Cosmos DB. In ConnectionPolicy, impostare UseMultipleWriteLocations su true e passare il nome dell'area in cui viene distribuita l'applicazione su SetCurrentLocation. Questo popolerà la proprietà PreferredLocations in base alla prossimità geografica della posizione passata. Se viene aggiunta una nuova area all'account in un secondo momento, l'applicazione non deve essere aggiornata o ridistribuita, ma rileverà automaticamente l'area più vicina ed eseguirà l'homing automatico su di essa in caso di eventi a livello di area.

> [!Note]
> Gli account Cosmos inizialmente configurati con una sola area per le operazioni di scrittura possono essere configurati con più aree per le operazioni di scrittura (multimaster) senza tempi di inattività. Per altre informazioni, consultare [Configurare più aree di scrittura](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Per abilitare le funzionalità multimaster nell'applicazione, impostare `UseMultipleWriteLocations` su `true`. Inoltre, impostare `SetCurrentLocation` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK V3

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `ApplicationRegion` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Facoltativamente, è possibile usare `CosmosClientBuilder` e `WithApplicationRegion` per ottenere lo stesso risultato:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Per abilitare le funzionalità multimaster nelle applicazioni, impostare `policy.setUsingMultipleWriteLocations(true)` e `policy.setPreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript e TypeScript SDK

Per abilitare le funzionalità multimaster nell'applicazione, impostare `connectionPolicy.UseMultipleWriteLocations` su `true`. Inoltre, impostare `connectionPolicy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Per abilitare le funzionalità multimaster nell'applicazione, impostare `connection_policy.UseMultipleWriteLocations` su `true`. Inoltre, impostare `connection_policy.PreferredLocations` sull'area in cui viene distribuita l'applicazione e in cui viene replicato Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti:

* [Usare token di sessione per gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipi di conflitto e criteri di risoluzione in Azure Cosmos DB](conflict-resolution-policies.md)
* [Disponibilità elevata in Azure Cosmos DB](high-availability.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)
* [Coerenza, disponibilità e compromessi delle prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Distribuzione globale: dietro le quinte](global-dist-under-the-hood.md)
