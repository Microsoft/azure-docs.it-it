---
title: Script di PowerShell per creare un contenitore Azure Cosmos DB con una chiave di partizione di grandi dimensioni
description: Esempio di script di Azure PowerShell - Creare un contenitore con una chiave di partizione di grandi dimensioni in un account Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: e13db803a491f5baa33c17ab650995346cd48022
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282639"
---
# <a name="create-a-container-with-a-large-partition-key-in-an-azure-cosmos-db-account-using-powershell"></a>Creare un contenitore con una chiave di partizione di grandi dimensioni in un account Azure Cosmos DB usando PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-large-partition-key.ps1 "Create a container with a large partition key in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un account Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Crea un database SQL di Cosmos DB. |
| [New-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Ottiene un contenitore SQL di Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).
