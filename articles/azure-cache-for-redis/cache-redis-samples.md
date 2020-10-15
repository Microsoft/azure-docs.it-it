---
title: Esempi di Cache Redis di Azure
description: 'Informazioni su come usare Azure Cache for Redis con questi esempi di codice: connessione a una cache, lettura e scrittura di dati in una cache, provider di Azure Cache for Redis ASP.NET.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212294"
---
# <a name="azure-cache-for-redis-samples"></a>Esempi di Cache Redis di Azure
Questo argomento fornisce un elenco di esempi di Cache Redis di Azure relativi a scenari come la connessione a una cache, la lettura e la scrittura di dati nella cache e l'uso di provider di Cache Redis ASP.NET. Alcuni esempi sono progetti scaricabili e alcuni forniscono informazioni dettagliate e includono frammenti di codice, ma non un collegamento a un progetto scaricabile.

## <a name="hello-world-samples"></a>Esempi Hello World
Gli esempi in questa sezione illustrano le nozioni di base della connessione a un'istanza di Cache Redis di Azure, nonché della lettura e della scrittura di dati nella cache con numerosi linguaggi e client Redis.

L'esempio [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra come eseguire varie operazioni nella cache usando il client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

In questo esempio viene illustrato come:

* Utilizzare varie opzioni di connessione
* Leggere e scrivere gli oggetti da e nella cache utilizzando operazioni sincrone e asincrone
* Utilizzare i comandi MGET/MSET di Redis per restituire i valori delle chiavi specificate
* Eseguire operazioni Redis transazionali
* Utilizzare gli elenchi e i set ordinati di Redis
* Archiviare oggetti .NET mediante i serializzatori JsonConvert
* Utilizzare i set di Redis per implementare l'assegnazione di tag
* Lavorare con Cluster Redis

Per altre informazioni, vedere la documentazione di [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) in GitHub. Per altri scenari di utilizzo, vedere gli unit test [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests).

In [Come usare Cache Redis di Azure con Python](cache-python-get-started.md) viene illustrato come usare Cache Redis di Azure con Python e il client [redis-py](https://github.com/andymccurdy/redis-py).

In [Gestire gli oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) viene illustrato come serializzare gli oggetti .NET per poterli scrivere e leggere in un'istanza di Cache Redis di Azure. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Usare Cache Redis come backplane con scalabilità orizzontale per ASP.NET SignalR
Nell'esempio sull'[utilizzo di Cache Redis come backplane con scalabilità orizzontale ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) viene illustrato come è possibile usare Cache Redis di Azure come backplane SignalR. Per altre informazioni sul backplane, vedere l’argomento relativo alla [scalabilità orizzontale di SignalR con Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Esempio di query cliente Cache Redis di Azure
Questo esempio mette a confronto le prestazioni di accesso ai dati da una cache e di accesso ai dati da un archivio di salvataggio permanente. L'esempio contiene due progetti.

* [Demo su come Cache Redis di Azure può migliorare le prestazioni grazie alla memorizzazione dei dati nella cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Effettuare il seeding del database e della cache per la demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Memorizzazione nella cache dell'output e dello stato sessione ASP.NET
Nell'[esempio relativo all'uso di Cache Redis di Azure per archiviare SessionState e OutputCache ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) viene illustrato come usare Cache Redis di Azure per archiviare la cache di output e la sessione ASP.NET usando i provider SessionState e OutputCache per Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gestire Cache Redis di Azure con MAML
Nell'esempio relativo alla [Gestione di Cache Redis di Azure con le librerie di gestione](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) viene illustrato come usare le librerie di gestione di Azure per gestire (creare/aggiornare/eliminare) la cache. 

## <a name="custom-monitoring-sample"></a>Esempio di monitoraggio personalizzato
L'esempio relativo all'[accesso ai dati di monitoraggio di Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) illustra come accedere ai dati di monitoraggio per Cache Redis di Azure all'esterno del portale di Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Clone in stile Twitter scritto utilizzando PHP e Redis
L’esempio [Retwis](https://github.com/SyntaxC4-MSFT/retwis) rappresenta Hello World di Redis. Si tratta di un clone social network in stile Twitter scritto utilizzando Redis e PHP mediante il client [Predis](https://github.com/nrk/predis) . Il codice sorgente è progettato per essere molto semplice e allo stesso tempo per mostrare diverse strutture di dati di Redis.

## <a name="bandwidth-monitor"></a>Monitor della larghezza di banda
L’esempio relativo al [monitor della larghezza di banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) consente di monitorare la larghezza di banda utilizzata nel client. Per misurare la larghezza di banda, eseguire l'esempio nel computer client della cache, effettuare chiamate alla cache e osservare la larghezza di banda segnalata nell'esempio del monitor della larghezza di banda.
