---
title: Accesso al feed di modifiche in Azure Cosmos DB
description: Questo articolo descrive diverse opzioni disponibili per leggere e accedere al feed di modifiche in Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688124"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lettura del feed di modifiche in Azure Cosmos DB

È possibile usare il feed di modifiche di Azure Cosmos DB con una delle opzioni seguenti:

* Uso di Funzioni di Azure
* Uso della libreria del processore dei feed di modifiche
* Uso di Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>Uso di Funzioni di Azure

Funzioni di Azure è l'opzione più semplice e consigliata. Quando si crea un trigger di funzioni di Azure per Cosmos DB, è possibile selezionare il contenitore per la connessione e la funzione di Azure viene attivata ogni volta che viene apportata una modifica al contenitore. I trigger possono essere creati usando il portale di Funzioni di Azure, il portale di Azure Cosmos DB o a livello di codice con gli SDK. Visual Studio e VS Code forniscono il supporto per scrivere le funzioni di Azure ed è anche possibile usare l'interfaccia della riga di comando di Funzioni di Azure per lo sviluppo multipiattaforma. È possibile scrivere il codice ed eseguire il debug dal desktop e quindi distribuire la funzione con un solo clic. Per altre informazioni, vedere gli articoli [Elaborazione di database serverless con Funzioni di Azure](serverless-computing-database.md) e [Using change feed with Azure Functions](change-feed-functions.md) (Uso del feed di modifiche con Funzioni di Azure).

## <a name="using-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed di modifiche

La libreria del processore dei feed delle modifiche rende tutto più semplice, pur offrendo un controllo completo del feed di modifiche. La libreria segue il criterio osservatore in cui la funzione di elaborazione viene chiamata dalla libreria. Se il feed di modifiche è caratterizzato da una velocità effettiva elevata, è possibile creare più istanze client per leggere il feed di modifiche. Poiché si usa la libreria del processore dei feed delle modifiche, il carico verrà diviso automaticamente tra i diversi client senza dover implementare questa logica. Tutte le complessità vengono gestite dalla libreria. Se si vuole avere un servizio di bilanciamento del carico proprio, è possibile implementare `IPartitionLoadBalancingStrategy` per elaborare il feed di modifiche con una strategia di partizionamento personalizzata. Per altre informazioni, vedere [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Uso di Azure Cosmos DB SQL API SDK

Con l'SDK, si ottiene un controllo di basso livello del feed delle modifiche. È possibile gestire il checkpoint, accedere a una particolare chiave di partizione logica e così via. Se si dispone di più lettori, è possibile `ChangeFeedOptions` usare per distribuire il carico di lettura a thread diversi o a client diversi.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed delle modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed delle modifiche viene rilevata come flusso di modifica nell'API MongoDB ed esegue una query con predicato in API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere i [flussi di modifiche nell'api Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

Apache Cassandra nativo fornisce Change Data Capture (CDC), un meccanismo per contrassegnare le tabelle specifiche per l'archiviazione e rifiutare le Scritture nelle tabelle quando viene raggiunta una dimensione configurabile su disco per il log CDC. La funzionalità del feed delle modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query sulle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [feed delle modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso della libreria del processore del feed delle modifiche](change-feed-processor.md)
