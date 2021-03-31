---
title: Usare lo strumento di stima dei feed di modifiche - Azure Cosmos DB
description: Informazioni su come usare lo strumento di stima dei feed di modifiche per analizzare lo stato di avanzamento del processore di feed di modifiche
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339838"
---
# <a name="use-the-change-feed-estimator"></a>Usare lo strumento di stima dei feed di modifiche
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive come monitorare lo stato di avanzamento delle istanze del [processore di feed di modifiche](./change-feed-processor.md) durante la lettura del feed di modifiche.

## <a name="why-is-monitoring-progress-important"></a>Perché è importante monitorare lo stato di avanzamento?

Il processore di feed di modifiche funge da puntatore che avanza nel [feed di modifiche](./change-feed.md) e distribuisce le modifiche a un'implementazione delegata. 

La distribuzione del processore di feed di modifiche può elaborare le modifiche a una determinata frequenza in base alle risorse disponibili, ad esempio CPU, memoria, rete e così via.

Se questa frequenza è più bassa di quella con cui si verificano le modifiche nel contenitore Azure Cosmos, il processore inizierà a ritardare.

L'identificazione di questo scenario consente di capire se è necessario ridimensionare la distribuzione del processore di feed di modifiche.

## <a name="implement-the-change-feed-estimator"></a>Implementare lo strumento di stima dei feed di modifiche

Analogamente al [processore di feed di modifiche](./change-feed-processor.md), lo strumento di stima dei feed di modifiche funziona come modello push. Lo strumento misura la differenza tra l'ultimo elemento elaborato (definito dallo stato del contenitore di lease) e la modifica più recente nel contenitore ed effettua il push di questo valore a un delegato. L'intervallo in cui viene eseguita la misurazione può anche essere personalizzato con un valore predefinito di 5 secondi.

Ad esempio, se il processore di feed di modifiche è definito come segue:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Il modo corretto per inizializzare uno strumento di stima per misurare il processore consiste nell'uso di `GetChangeFeedEstimatorBuilder`, come indicato di seguito:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

In cui sia il processore che lo strumento di stima condividono lo stesso `leaseContainer` e lo stesso nome.

Gli altri due parametri corrispondono al delegato, che riceverà un numero che rappresenta la **quantità di modifiche in sospeso per la lettura** da parte del processore e l'intervallo di tempo in cui si vuole che venga eseguita questa misurazione.

Di seguito è riportato un esempio di un delegato che riceve la stima:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

È possibile inviare questa stima alla soluzione di monitoraggio e usarla per comprendere lo stato di avanzamento nel corso del tempo.

> [!NOTE]
> Non è necessario distribuire lo strumento di stima dei feed di modifiche come parte del processore di feed di modifiche né come parte dello stesso progetto. Può essere indipendente ed essere eseguito in un'istanza completamente diversa. È sufficiente usare lo stesso nome e la stessa configurazione di lease.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del processore di feed di modifiche](change-feed-processor.md)
* [Ora di avvio del processore di feed di modifiche](./change-feed-processor.md#starting-time)