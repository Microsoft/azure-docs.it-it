---
title: Procedura per l'esecuzione di query sui dati tabella in Azure Cosmos DB
description: Informazioni su come eseguire query sui dati archiviati nell'account dell'API Tabella di Azure Cosmos DB usando filtri OData e query LINQ
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e184d85e3daee41f530334aa0034fc98f40a8766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099222"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Esercitazione: Eseguire query in Azure Cosmos DB con l'API Tabella
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

L'[API di tabella](table-introduction.md) di Azure Cosmos DB supporta l'esecuzione di query OData e [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) sui dati chiave/valore (tabella).  

Questo articolo illustra le attività seguenti:

> [!div class="checklist"]
> * Esecuzione di query sui dati con l'API Table

Le query di questo articolo usano la tabella di esempio `People`:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

Vedere [Querying Tables and Entities](/rest/api/storageservices/fileservices/querying-tables-and-entities) (Esecuzione di query su tabelle ed entità) per informazioni dettagliate su come eseguire una query con l'API di tabella.

Per altre informazioni sulle funzionalità Premium offerte da Azure Cosmos DB, vedere [Introduzione all'API di tabella di Azure Cosmos DB](table-introduction.md) e [Sviluppare con l'API di tabella in .NET](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Prerequisiti

Per il funzionamento di queste query è necessario disporre di un account Azure Cosmos DB e nel contenitore devono essere presenti dati di entità. Questi requisiti non sono disponibili? Completare la [Guida introduttiva di 5 minuti](create-table-dotnet.md) o l'[esercitazione per sviluppatori](tutorial-develop-table-dotnet.md) per creare un account e popolare il database.

## <a name="query-on-partitionkey-and-rowkey"></a>Query su PartitionKey e RowKey

Poiché le proprietà PartitionKey e RowKey costituiscono la chiave primaria di un'entità, è possibile usare la sintassi speciale seguente per identificare l'entità:

**Query**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Risultati**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

In alternativa, è possibile specificare queste proprietà come parte dell'opzione `$filter`, come illustrato nella sezione seguente. Si noti che i nomi delle proprietà chiave e i valori costanti distinguono tra maiuscole e minuscole. Entrambe le proprietà PartitionKey e RowKey sono di tipo String.

## <a name="query-by-using-an-odata-filter"></a>Eseguire una query usando un filtro OData

Quando si crea una stringa di filtro, tenere presente queste regole:

* Usare gli operatori logici definiti dalla specifica del protocollo OData per confrontare una proprietà con un valore. Si noti che non è possibile confrontare una proprietà con un valore dinamico. Un lato dell'espressione deve essere una costante.
* Il nome della proprietà, l'operatore e il valore costante devono essere separati da spazi con codifica URL. Uno spazio con codifica URL è come `%20`.
* Viene effettuata la distinzione tra maiuscole e minuscole per tutte le parti della stringa di filtro.
* Il valore costante deve essere dello stesso tipo di dati della proprietà affinché il filtro restituisca risultati validi. Per altre informazioni sui tipi di proprietà supportati, vedere [Informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/understanding-the-table-service-data-model).

La query di esempio illustra come filtrare in base alla proprietà PartitionKey Email usando un `$filter` ODATA.

**Query**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Per altre informazioni su come creare espressioni filtro per vari tipi di dati sono disponibili in [Querying Tables and Entities](/rest/api/storageservices/querying-tables-and-entities) (Esecuzione di query su tabelle ed entità).

**Risultati**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

Le query sulle proprietà datetime non restituiscono dati quando vengono eseguite nell'API Tabella di Azure Cosmos DB. Mentre l'archiviazione tabelle di Azure archivia i valori di data con la granularità temporale dei tick, l'API Tabella in Azure Cosmos DB usa la proprietà `_ts`. La proprietà `_ts` si trova a un secondo livello di granularità, che non corrisponde a un filtro OData. Di conseguenza, le query sulle proprietà timestamp vengono bloccate da Azure Cosmos DB. Per ovviare al problema, è possibile definire una proprietà di tipo datetime o dati long personalizzata e impostare il valore della data dal client.

## <a name="query-by-using-linq"></a>Eseguire una query utilizzando LINQ 
È anche possibile eseguire query con LINQ, che consente di tradurle nelle corrispondenti espressioni di query OData. L'esempio seguente mostra come compilare le query usando .NET SDK:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato appreso come eseguire query usando l'API di tabella

È ora possibile passare all'esercitazione successiva per imparare a distribuire i dati a livello globale.

> [!div class="nextstepaction"]
> [Distribuire i dati a livello globale](tutorial-global-distribution-table.md)
