---
title: Script di PowerShell per creare un database e un contenitore per l'API SQL di Azure Cosmos DB
description: Script di Azure PowerShell - Creare un database e un contenitore per l'API SQL di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 2a098d3eff7bc4a8a78a880386145457b80b42d4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675564"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Creare un database e un contenitore per Azure Cosmos DB - API SQL
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Per questo esempio è necessario Azure PowerShell AZ 5.4.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per determinare le versioni installate.
Se è necessario installarlo, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Eseguire [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) per accedere ad Azure.

## <a name="sample-script"></a>Script di esempio

Questo script crea un account Cosmos per l'API SQL (Core) in due aree con coerenza a livello di sessione, un database e un contenitore con una chiave di partizione, criteri di indicizzazione personalizzati, criteri di chiave univoca, durata TTL, unità elaborate dedicate e criteri di risoluzione dei conflitti basati sulla priorità dell'ultima scrittura con un percorso di risoluzione dei conflitti personalizzato che verrà usato quando `multipleWriteLocations=true`.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un account Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Crea un database SQL di Cosmos DB. |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Crea un oggetto PSSqlUniqueKey usato come parametro per New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Crea un oggetto PSSqlUniqueKeyPolicy usato come parametro per New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Crea un oggetto PSCompositePath usato come parametro per New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Crea un oggetto PSIndexes usato come parametro per New-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Crea un oggetto PSIncludedPath usato come parametro per New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Crea un oggetto PSSqlIndexingPolicy usato come parametro per New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Crea un oggetto PSSqlConflictResolutionPolicy usato come parametro per New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Crea un nuovo contenitore SQL di Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).