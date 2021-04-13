---
title: 'Azure Cosmos DB Java SDK v4 per API SQL: note sulla versione e risorse'
description: Informazioni complete su Azure Cosmos DB Java SDK v4 per SDK e API SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d31f3d1c510ffe6c3f0a739a4e41313c8c6e7cf0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364822"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 per API Core (SQL): note sulla versione e risorse
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connettore OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 per API Core (SQL) combina un'API asincrona e un'API sincrona in un unico artefatto Maven. V4 SDK offre prestazioni migliorate, nuove funzionalità API e supporto asincrono basato su Project Reactor e sulla [libreria Netty](https://netty.io/). Si prevede quindi che Azure Cosmos DB Java SDK v4 offra prestazioni più elevate rispetto ad [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Queste note sulla versione sono destinate solo ad Azure Cosmos DB Java SDK v4. Se si usa una versione precedente, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento alla versione 4.
>
> Per iniziare rapidamente, eseguire questi tre passaggi.
> 1. Installare la [prima versione supportata di Java Runtime, JDK 8](/java/azure/jdk/) per poter usare l'SDK.
> 2. Consultare la [Guida di avvio rapido per Azure Cosmos DB Java SDK v4](./create-sql-api-java.md), che consente di accedere all'artefatto Maven e prende in esame le richieste di base di Azure Cosmos DB.
> 3. Leggere i [suggerimenti sulle prestazioni](performance-tips-java-sdk-v4-sql.md) e le guide alla [risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Azure Cosmos DB Java SDK v4 per ottimizzare l'SDK per l'applicazione.
>
> I [workshop e i laboratori su Azure Cosmos DB](https://aka.ms/cosmosworkshop) costituiscono un'altra risorsa molto utile per imparare a usare Azure Cosmos DB Java SDK v4.
>

## <a name="helpful-content"></a>Contenuto utile

| Content | Collegamento |
|---|---|
|**Download dell'SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentazione sull'API** | [Documentazione di riferimento API Java](/java/api/overview/azure/cosmosdb/client) |
|**Contribuire all'SDK** | [Repository centrale di Azure SDK per Java in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Operazioni preliminari** | [Avvio rapido: Compilare un'app Java per gestire i dati dell'API SQL di Azure Cosmos DB](./create-sql-api-java.md) <br> [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: esempi di Java dell'API SQL](sql-api-java-sdk-samples.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App console con feed di modifiche**| [Feed di modifiche - Esempio java SDK v4](create-sql-api-java-changefeed.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Esempio di App Web**| [Creare un'app Web con Java SDK v4](sql-api-java-application.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risolvere i problemi di Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Eseguire la migrazione a v4 da un SDK precedente** | [Eseguire la migrazione a Java v4 SDK](migrate-java-v4-sdk.md) |
| **Runtime minimo supportato**|[JDK 8](/java/azure/jdk/) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).