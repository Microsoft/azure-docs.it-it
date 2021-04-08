---
title: Eseguire la migrazione dalla libreria del processore dei feed delle modifiche al Azure Cosmos DB .NET V3 SDK
description: Informazioni su come eseguire la migrazione dell'applicazione usando la libreria del processore dei feed delle modifiche in Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341264"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Eseguire la migrazione dalla libreria del processore dei feed delle modifiche al Azure Cosmos DB .NET V3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive i passaggi necessari per eseguire la migrazione del codice di un'applicazione esistente che usa la [libreria del processore dei feed delle modifiche](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) alla funzionalità del feed delle [modifiche](change-feed.md) nella versione più recente di .NET SDK (detta anche .NET V3 SDK).

## <a name="required-code-changes"></a>Modifiche al codice richieste

.NET V3 SDK presenta diverse modifiche di rilievo. di seguito sono riportati i passaggi principali per eseguire la migrazione dell'applicazione:

1. Convertire le `DocumentCollectionInfo` istanze in `Container` riferimenti per i contenitori monitorati e lease.
1. Le personalizzazioni che usano `WithProcessorOptions` devono essere aggiornate per usare `WithLeaseConfiguration` e `WithPollInterval` per gli intervalli, `WithStartTime` [per l'ora di inizio](./change-feed-processor.md#starting-time)e `WithMaxItems` per definire il numero massimo di elementi.
1. Impostare `processorName` on `GetChangeFeedProcessorBuilder` in modo che corrisponda al valore configurato in `ChangeFeedProcessorOptions.LeasePrefix` oppure utilizzare `string.Empty` in caso contrario.
1. Poiché le modifiche non vengono più recapitate come `IReadOnlyList<Document>` , invece, si tratta di un `IReadOnlyCollection<T>` `T` tipo in cui è necessario definire, non esiste più una classe di elementi di base.
1. Per gestire le modifiche, non è più necessaria un'implementazione, bensì è necessario [definire un delegato](change-feed-processor.md#implementing-the-change-feed-processor). Il delegato può essere una funzione statica o, se è necessario mantenere lo stato tra le esecuzioni, è possibile creare una classe personalizzata e passare un metodo di istanza come delegato.

Se, ad esempio, il codice originale per compilare il processore del feed delle modifiche è simile al seguente:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Il codice migrato sarà simile al seguente:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E il delegato, può essere un metodo statico:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Stato e contenitore lease

Analogamente alla libreria del processore dei feed delle modifiche, la funzionalità del feed delle modifiche in .NET V3 SDK usa un [contenitore di lease](change-feed-processor.md#components-of-the-change-feed-processor) per archiviare lo stato. Tuttavia, gli schemi sono diversi.

Il processore di feed di modifiche di SDK V3 rileverà qualsiasi stato della libreria precedente ed eseguirà la migrazione automatica al nuovo schema al momento della prima esecuzione del codice dell'applicazione migrata. 

È possibile arrestare tranquillamente l'applicazione usando il codice precedente, eseguire la migrazione del codice alla nuova versione, avviare l'applicazione migrata e tutte le modifiche apportate durante l'arresto dell'applicazione verranno selezionate ed elaborate dalla nuova versione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del processore di feed di modifiche](change-feed-processor.md)
* [Uso dello strumento di stima di feed di modifiche](how-to-use-change-feed-estimator.md)
* [Ora di avvio del processore di feed di modifiche](./change-feed-processor.md#starting-time)