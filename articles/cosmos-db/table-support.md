---
title: Supporto dell'archiviazione tabelle di Azure in Azure Cosmos DB
description: Informazioni su come interagiscono l'API Tabella di Azure Cosmos DB e le tabelle di archiviazione di Azure tramite la condivisione dello stesso modello di dati della tabella e delle operazioni
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/21/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 28c58251d9a30b3bae9d958c32c4d6a71f86aaae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263212"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Sviluppo con l'API di tabella di Azure Cosmos DB e Archiviazione tabelle di Azure

L'API di tabella di Azure Cosmos DB e Archiviazione tabelle di Azure condividono lo stesso modello di dati di tabella ed espongono le stesse operazioni di creazione, eliminazione, aggiornamento ed esecuzione di query tramite i relativi SDK.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Sviluppo con l'API di tabella di Azure Cosmos DB

L'[API Table di Azure Cosmos DB](table-introduction.md) attualmente dispone di quattro SDK per lo sviluppo: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Questa libreria è destinata a .NET Standard e include le stesse classi e firme di metodi disponibili nella versione pubblica di [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ma permette anche di connettersi agli account Azure Cosmos DB tramite l'API Tabella. Per gli utenti della libreria .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) è consigliabile effettuare l'aggiornamento a [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) perché è in modalità manutenzione e sarà presto deprecata.

* [Python SDK](table-sdk-python.md): Il nuovo SDK Python di Azure Cosmos DB è l'unico SDK che supporta l'archiviazione tabelle di Azure in Python. Questo SDK si connette sia ad Archiviazione tabelle di Azure che all'API Table di Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Questo SDK di Archiviazione di Azure è in grado di connettersi agli account di Azure Cosmos DB tramite l'API Table.

* [Node.js SDK](table-sdk-nodejs.md): Questo SDK di Archiviazione di Azure è in grado di connettersi agli account di Azure Cosmos DB tramite l'API Table.


Per altre informazioni sull'uso dell'API Tabella, vedere l'articolo [Domande frequenti: Sviluppare con l'API Tabella](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Sviluppo con Archiviazione tabelle di Azure

Archiviazione tabelle di Azure include questi SDK disponibili per lo sviluppo:

- Le librerie [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft.Azure.Storage.File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) e [Microsoft.Azure.Storage.Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) consentono di lavorare con il servizio di archiviazione delle tabelle di Azure. Se si usa l’API Tabella in Azure Cosmos DB, è possibile usare invece la libreria [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/).
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Azure Cosmos DB Table SDK per Python supporta il servizio Archiviazione tabelle (poiché Archiviazione tabelle di Azure e l'API Tabella di Cosmos DB condividono le stesse caratteristiche e funzionalità, e nel tentativo di valorizzare gli sforzi per lo sviluppo dell'SDK, è consigliabile usare questo SDK).
- [Azure Storage per Java](https://github.com/azure/azure-storage-java). Questo SDK di archiviazione di Azure fornisce una libreria client i n Java per l'utilizzo di Archiviazione tabelle di Azure.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Questo SDK fornisce un pacchetto Node.js e una libreria client JavaScript compatibile con browser per utilizzare il servizio di archiviazione tabelle.
- [Modulo di PowerShell AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Questo modulo di PowerShell include cmdlet che utilizzano le tabelle di archiviazione.
- [Libreria client di archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/). Questa libreria consente di creare applicazioni per Archiviazione di Azure.
- [Libreria client delle tabelle di Archiviazione di Azure per Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Questo progetto fornisce un pacchetto Ruby che rende più semplice accedere al servizio tabelle di Archiviazione di Azure.
- [Libreria client PHP delle tabelle di Archiviazione di Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Questo progetto fornisce una libreria client PHP che rende più semplice accedere al servizio tabelle di Archiviazione di Azure.


   





