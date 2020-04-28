---
title: Libreria del processore del feed delle modifiche in Azure Cosmos DB
description: Informazioni su come usare la libreria del processore dei feed delle modifiche Azure Cosmos DB per leggere il feed delle modifiche, i componenti del processore del feed delle modifiche
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273315"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processore dei feed di modifiche in Azure Cosmos DB 

Il processore del feed delle modifiche fa parte del [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Semplifica il processo di lettura del feed delle modifiche e la distribuzione efficace dell'elaborazione degli eventi tra più consumer.

Il vantaggio principale della libreria del processore dei feed delle modifiche è il comportamento a tolleranza di errore che garantisce un recapito "at-least-once" di tutti gli eventi nel feed delle modifiche.

## <a name="components-of-the-change-feed-processor"></a>Componenti del processore del feed delle modifiche

Sono disponibili quattro componenti principali per l'implementazione del processore del feed delle modifiche: 

1. **Contenitore monitorato:** il contenitore monitorato include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e aggiornamenti del contenitore monitorati vengono riflessi nel feed delle modifiche del contenitore.

1. **Il contenitore lease:** Il contenitore lease funge da archiviazione dello stato e coordina l'elaborazione del feed delle modifiche tra più thread di lavoro. Il contenitore lease può essere archiviato nello stesso account del contenitore monitorato o in un account separato. 

1. **Host:** Un host è un'istanza dell'applicazione che utilizza il processore del feed delle modifiche per restare in ascolto delle modifiche. Più istanze con la stessa configurazione di lease possono essere eseguite in parallelo, ma ogni istanza deve avere un **nome di istanza**diverso. 

1. **Il delegato:** Il delegato è il codice che definisce le operazioni che lo sviluppatore desidera eseguire con ogni batch di modifiche lette dal processore del feed delle modifiche. 

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. Il contenitore monitorato archivia i documenti e USA ' City ' come chiave di partizione. Si noterà che i valori della chiave di partizione sono distribuiti in intervalli che contengono elementi. Sono disponibili due istanze dell'host e il processore del feed delle modifiche assegna diversi intervalli di valori di chiave di partizione a ogni istanza per ottimizzare la distribuzione di calcolo. Ogni intervallo viene letto in parallelo e lo stato di avanzamento viene mantenuto separatamente dagli altri intervalli nel contenitore lease.

![Esempio di processore dei feed di modifiche](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementazione del processore del feed delle modifiche

Il punto di ingresso è sempre il contenitore monitorato, da `Container` un'istanza chiamata `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Dove il primo parametro è un nome distinto che descrive l'obiettivo del processore e il secondo nome è l'implementazione del delegato che gestirà le modifiche. 

Un esempio di un delegato è:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Infine, definire un nome per l'istanza del processore `WithInstanceName` con e che è il contenitore per il quale mantenere lo `WithLeaseContainer`stato di lease.

Se `Build` si chiama `StartAsync`, viene restituita l'istanza del processore che è possibile avviare chiamando.

## <a name="processing-life-cycle"></a>Ciclo di vita dell'elaborazione

Il normale ciclo di vita di un'istanza dell'host è:

1. Leggere il feed delle modifiche.
1. Se non sono state apportate modifiche, sospendere per un periodo di tempo predefinito ( `WithPollInterval` personalizzabile con nel generatore) e passare a #1.
1. Se sono presenti modifiche, inviarle al **delegato**.
1. Al termine dell'elaborazione **delle modifiche da**parte del delegato, aggiornare l'archivio dei lease con l'ultimo momento elaborato e passare a #1.

## <a name="error-handling"></a>Gestione degli errori

Il processore del feed delle modifiche è resiliente agli errori del codice utente. Ciò significa che se l'implementazione del delegato presenta un'eccezione non gestita (passaggio #4), il thread che elabora tale batch di modifiche verrà arrestato e verrà creato un nuovo thread. Il nuovo thread verificherà quale sia l'ultimo punto nel tempo dell'archivio di lease per l'intervallo di valori della chiave di partizione e il riavvio da tale intervallo, inviando efficacemente lo stesso batch di modifiche al delegato. Questo comportamento continuerà fino a quando il delegato non elabora correttamente le modifiche ed è il motivo per cui il processore del feed delle modifiche ha una garanzia di "almeno una volta", perché se il codice del delegato genera, il batch verrà ritentato.

## <a name="dynamic-scaling"></a>Scalabilità dinamica

Come indicato durante l'introduzione, il processore del feed delle modifiche può distribuire automaticamente il calcolo tra più istanze. È possibile distribuire più istanze dell'applicazione usando il processore del feed di modifiche e sfruttarne i vantaggi. gli unici requisiti principali sono:

1. Tutte le istanze devono avere la stessa configurazione del contenitore di lease.
1. Tutte le istanze devono avere lo stesso nome del flusso di lavoro.
1. Ogni istanza deve avere un nome di istanza diverso (`WithInstanceName`).

Se si applicano queste tre condizioni, il processore del feed delle modifiche utilizzerà un algoritmo di distribuzione uguale, distribuirà tutti i lease nel contenitore di lease tra tutte le istanze in esecuzione e il calcolo parallelizzare. Un lease può essere di proprietà di un'istanza in un determinato momento, quindi il numero massimo di istanze equivale al numero di lease.

È possibile aumentare e ridurre il numero di istanze e il processore del feed delle modifiche regola dinamicamente il carico ridistribuendo di conseguenza.

Inoltre, il processore del feed delle modifiche può adattarsi dinamicamente alla scalabilità dei contenitori a causa dell'aumento della velocità effettiva o dell'archiviazione. Quando il contenitore si espande, il processore del feed delle modifiche gestisce in modo trasparente questi scenari aumentando dinamicamente i lease e distribuendo i nuovi lease tra le istanze esistenti.

## <a name="change-feed-and-provisioned-throughput"></a>Feed di modifiche e velocità effettiva di cui viene effettuato il provisioning

Vengono addebitati i costi per le UR utilizzate, in quanto lo spostamento dei dati da e verso i contenitori Cosmos comporta sempre l'utilizzo di UR. Vengono addebitati i costi per le UR utilizzate dal contenitore di lease.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Come eseguire la migrazione dalla libreria del processore dei feed delle modifiche](how-to-migrate-from-change-feed-library.md)
* [Uso dello strumento di stima dei feed di modifiche](how-to-use-change-feed-estimator.md)
* [Ora di avvio del processore dei feed di modifiche](how-to-configure-change-feed-start-time.md)
