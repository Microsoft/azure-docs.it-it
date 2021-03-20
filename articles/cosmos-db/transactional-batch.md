---
title: Operazioni batch transazionali in Azure Cosmos DB con .NET SDK
description: Informazioni su come usare TransactionalBatch in Azure Cosmos DB .NET SDK per eseguire un gruppo di operazioni Point che hanno esito positivo o negativo.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347558"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Operazioni batch transazionali in Azure Cosmos DB con .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Batch transazionale descrive un gruppo di operazioni punto che devono avere esito positivo o negativo insieme alla stessa chiave di partizione in un contenitore. In .NET SDK, la `TransactionalBatch` classe viene usata per definire questo batch di operazioni. Se tutte le operazioni vengono eseguite correttamente nell'ordine in cui sono descritte nell'operazione batch transazionale, verrà eseguito il commit della transazione. Tuttavia, se un'operazione ha esito negativo, viene eseguito il rollback dell'intera transazione.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Informazioni sulle transazioni in Azure Cosmos DB

Una transazione in un database tipico può essere definita come una sequenza di operazioni eseguite come una singola unità logica di lavoro. Ogni transazione fornisce garanzie di proprietà ACID (atomicità, coerenza, isolamento e durabilità).

* L' **atomicità** garantisce che tutte le operazioni eseguite all'interno di una transazione vengano considerate come una singola unità e ne viene eseguito il commit o nessuna di esse.
* La **coerenza** garantisce che i dati siano sempre in uno stato valido tra le transazioni.
* L' **isolamento** garantisce che le due transazioni non interferiscano tra loro: molti sistemi commerciali forniscono più livelli di isolamento che possono essere usati in base alle esigenze dell'applicazione.
* La **durabilità** garantisce che tutte le modifiche di cui è stato eseguito il commit in un database saranno sempre presenti.
Azure Cosmos DB supporta [le transazioni complete acid compatibili con l'isolamento dello snapshot](database-transactions-optimistic-concurrency.md) per le operazioni all'interno della stessa [chiave di partizione logica](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Operazioni batch transazionali e stored procedure

Azure Cosmos DB attualmente supporta stored procedure, che forniscono anche l'ambito transazionale sulle operazioni. Tuttavia, le operazioni batch transazionali offrono i vantaggi seguenti:

* **Opzione lingua** : il batch transazionale è supportato nell'SDK e nel linguaggio che si utilizza già, mentre le stored procedure devono essere scritte in JavaScript.
* **Controllo** delle versioni del codice: il controllo delle versioni del codice dell'applicazione e il caricamento nella pipeline ci/CD è molto più naturale che orchestrare l'aggiornamento di un stored procedure e assicurarsi che il rollover avvenga al momento giusto. Consente inoltre di eseguire più facilmente il rollback delle modifiche.
* **Prestazioni** : latenza ridotta per operazioni equivalenti fino al 30% rispetto all'esecuzione del stored procedure.
* **Serializzazione del contenuto** : ogni operazione all'interno di un batch transazionale può sfruttare le opzioni di serializzazione personalizzate per il payload.

## <a name="how-to-create-a-transactional-batch-operation"></a>Come creare un'operazione batch transazionale

Quando si crea un'operazione batch transazionale, si inizia da un'istanza del contenitore e si chiama `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Sarà quindi necessario chiamare il `ExecuteAsync` batch:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Una volta ricevuta la risposta, verificare se l'operazione ha esito positivo o meno ed estrarre i risultati:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Se si verifica un errore, l'operazione non riuscita avrà un codice di stato dell'errore corrispondente. Tutte le altre operazioni avranno un codice di stato 424 (dipendenza non riuscita). Nell'esempio seguente l'operazione ha esito negativo perché tenta di creare un elemento già esistente (409 HttpStatusCode. Conflict). Il codice di stato consente di identificare la cause di un errore di transazione.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Modalità di esecuzione delle operazioni batch transazionali

Quando `ExecuteAsync` viene chiamato il metodo, tutte le operazioni nell' `TransactionalBatch` oggetto vengono raggruppate, serializzate in un singolo payload e inviate come singola richiesta al servizio Azure Cosmos DB.

Il servizio riceve la richiesta ed esegue tutte le operazioni all'interno di un ambito transazionale e restituisce una risposta usando lo stesso protocollo di serializzazione. Questa risposta è un esito positivo o negativo e fornisce le singole risposte dell'operazione per ogni operazione.

L'SDK espone la risposta per verificare il risultato e, facoltativamente, estrarre ogni risultato dell'operazione interna.

## <a name="limitations"></a>Limitazioni

Attualmente esistono due limiti noti:

* Il limite delle dimensioni della richiesta Azure Cosmos DB vincola le dimensioni del `TransactionalBatch` payload a non superare i 2 MB e il tempo di esecuzione massimo è 5 secondi.
* Esiste un limite corrente di 100 operazioni per `TransactionalBatch` per garantire che le prestazioni siano quelle previste e nei contratti di contratto.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [operazioni che è possibile eseguire con TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Visitare gli [esempi](sql-api-dotnet-v3sdk-samples.md) per altre modalità di utilizzo di Cosmos DB .NET SDK
