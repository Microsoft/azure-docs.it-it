---
title: Script dell'interfaccia della riga di comando di Azure per le operazioni di velocità effettiva (UR/s) per le risorse dell'API Tabella di Azure Cosmos DB
description: Script dell'interfaccia della riga di comando di Azure per le operazioni di velocità effettiva (UR/s) per le risorse dell'API Tabella di Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 12ee46316a3eadceedf6f1772ae41938d1cc903e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761925"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Operazioni di velocità effettiva (UR/s) con l'interfaccia della riga di comando di Azure per una tabella per l'API Tabella di Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.12.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

Questo script crea una tabella dell'API Tabella quindi aggiorna le unità elaborate. Lo script esegue quindi la migrazione dalla velocità effettiva standard alla velocità effettiva con scalabilità automatica e, al termine della migrazione, legge il valore della velocità effettiva con scalabilità automatica.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crea un account Azure Cosmos DB. |
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) | Crea una tabella dell'API Tabella di Azure Cosmos. |
| [az cosmosdb table throughput update](/cli/azure/cosmosdb/table/throughput#az_cosmosdb_table_throughput_update) | Aggiorna la velocità effettiva per una tabella dell'API Tabella di Azure Cosmos. |
| [az cosmosdb table throughput migrate](/cli/azure/cosmosdb/table/throughput#az_cosmosdb_table_throughput_migrate) | Esegue la migrazione della velocità effettiva per una tabella dell'API Tabella di Azure Cosmos. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure Cosmos DB, vedere la relativa [documentazione](/cli/azure/cosmosdb).

Tutti gli esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nel [repository GitHub dell'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
