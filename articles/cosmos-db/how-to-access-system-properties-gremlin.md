---
title: Accedere alle proprietà di sistema dei documenti tramite Graph di Azure Cosmos DB
description: Informazioni su come leggere e scrivere le proprietà di sistema dei documenti tramite l'API Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: 61814082ebe9828a08da1e8786890b500c239082
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081842"
---
# <a name="system-document-properties"></a>Proprietà di sistema dei documenti
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB dispone di [proprietà di sistema](/rest/api/cosmos-db/databases) , ad esempio,, ```_ts``` ```_self``` ```_attachments``` , ```_rid``` e ```_etag``` in ogni documento. Inoltre il motore di Gremlin aggiunge le proprietà ```inVPartition``` e ```outVPartition``` sui bordi. Per impostazione predefinita, queste proprietà sono disponibili per l'attraversamento. È tuttavia possibile includere proprietà specifiche, o tutte le proprietà, nell'attraversamento Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tag

Questa proprietà è usata per il controllo della concorrenza ottimistica. Se l'applicazione deve suddividere l'operazione in più attraversamenti distinti, può usare una proprietà eTag per evitare la perdita di dati nelle operazioni di scrittura simultanee.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Durata (TTL)

Se nella raccolta è abilitata la scadenza dei documenti e nei documenti è impostata la proprietà ```ttl```, questa proprietà sarà disponibile nell'attraversamento Gremlin come una normale proprietà vertice o bordo. Non occorre ```ProjectionStrategy``` per abilitare l'esposizione della proprietà relativa alla durata (TTL).

Il vertice creato con l'attraversamento riportato di seguito verrà eliminato automaticamente tra **123 secondi**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Passaggi successivi
* [Concorrenza ottimistica di Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Durata (TTL)](time-to-live.md) in Azure Cosmos DB
