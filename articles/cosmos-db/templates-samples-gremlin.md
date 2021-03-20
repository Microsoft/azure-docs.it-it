---
title: Modelli di Resource Manager per l'API Gremlin di Azure Cosmos DB
description: Usare i modelli di Azure Resource Manager per creare e configurare l'API Gremlin di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 2fee06a2ceb9b8062b5150e5716f1ee9abf15cff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340628"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gestire le risorse dell'API Gremlin di Azure Cosmos DB con modelli di Azure Resource Manager
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Questo articolo illustra come usare i modelli di Azure Resource Manager per distribuire e gestire account, database e grafi di Azure Cosmos DB.

Questo articolo contiene esempi solo per gli account dell'API Gremlin. Per trovare esempi per account di altri tipi di API, vedere gli articoli relativi all'uso dei modelli di Azure Resource Manager con l'API di Azure Cosmos DB per [Cassandra](templates-samples-cassandra.md), [SQL](templates-samples-sql.md), [MongoDB](templates-samples-mongodb.md) e [Tabella](templates-samples-table.md).

> [!IMPORTANT]
>
> * I nomi degli account sono limitati a 44 caratteri, tutti in minuscolo.
> * Per modificare i valori di velocità effettiva, ridistribuire il modello con UR/s aggiornati.
> * Quando si aggiungono o si rimuovono percorsi in un account Azure Cosmos, non è possibile modificare contemporaneamente altre proprietà. Queste operazioni devono essere eseguite separatamente.

Per creare una delle risorse di Azure Cosmos DB seguenti, copiare il modello di esempio seguente in un nuovo file JSON. Facoltativamente, è possibile creare un file JSON dei parametri da usare quando si distribuiscono più istanze della stessa risorsa con nomi e valori diversi. Per distribuire modelli di Azure Resource Manager, è possibile scegliere tra varie soluzioni, tra cui [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), [interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Account Azure Cosmos DB per Gremlin con velocità effettiva con provisioning a scalabilità automatica

Questo modello creerà un account di Azure Cosmos per l'API Gremlin con un database e un grafo con velocità effettiva a scalabilità automatica. Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Account Azure Cosmos DB per Gremlin con velocità effettiva con provisioning standard

Questo modello creerà un account di Azure Cosmos per l'API Gremlin con un database e un grafo con velocità effettiva standard (manuale). Questo modello è disponibile anche per la distribuzione con un clic dalla raccolta Modelli di avvio rapido di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

Altre risorse:

* [Documentazione di Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema del provider di risorse Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelli di avvio rapido di Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)