---
title: Script di PowerShell per modificare la priorità di failover per un account Azure Cosmos con una singola area di scrittura
description: Esempi di script di Azure PowerShell - Cambiare la priorità di failover o attivare un failover per un account Azure Cosmos con una singola area di scrittura
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 07561de8236a06a3ac34aa8867a07890aaf0656f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570605"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>Cambiare la priorità di failover o attivare un failover di un account Azure Cosmos con una singola area di scrittura tramite PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

> [!NOTE]
> Qualsiasi modifica apportata a un'area con `failoverPriority=0` attiva un failover manuale e può essere eseguita solo in un account configurato per il failover manuale. Le modifiche apportate a tutte le altre aree cambiano semplicemente la priorità di failover per un account Cosmos.
> [!NOTE]
> Questo esempio dimostra l'uso di un account dell'API SQL (Core). Per usare questo esempio per altre API, copiare le proprietà correlate e applicarle allo script specifico dell'API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Elenca gli account Cosmos DB o ottiene un account Cosmos DB specificato. |
| [Update-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aggiorna l'ordine delle priorità di failover delle aree di un account Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Cosmos DB sono disponibili in [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Script di PowerShell per Azure Cosmos DB).
