---
title: Eliminare il gruppo di risorse e le risorse
description: Viene descritto come eliminare i gruppi di risorse e le risorse. Descrive il modo in cui Azure Resource Manager Ordina l'eliminazione delle risorse quando si elimina un gruppo di risorse. Descrive i codici di risposta e il modo in cui Resource Manager li gestisce in modo da determinare se l'eliminazione è stata completata.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 244d59ffc096b5e219e27fd376b07baecde3670e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587662"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager l'eliminazione di risorse e gruppi di risorse

Questo articolo illustra come eliminare i gruppi di risorse e le risorse. Descrive il modo in cui Azure Resource Manager Ordina l'eliminazione delle risorse quando si elimina un gruppo di risorse.

## <a name="how-order-of-deletion-is-determined"></a>Come viene determinato l'ordine di eliminazione

Quando si elimina un gruppo di risorse, Resource Manager determina l'ordine di eliminazione delle risorse. L'ordine è il seguente:

1. Vengono eliminate tutte le risorse figlio (annidate).

2. Successivamente vengono eliminate le risorse che gestiscono altre risorse. Per una risorsa può essere impostata la proprietà `managedBy` per indicare che è gestita da una risorsa diversa. Quando questa proprietà è impostata, la risorsa che gestisce l'altra risorsa viene eliminata prima delle altre risorse.

3. Le risorse rimanenti vengono eliminate dopo le due categorie precedenti.

Dopo aver determinato l'ordine, Resource Manager genera un'operazione DELETE per ogni risorsa. Attende il completamento di eventuali dipendenze prima di procedere.

Per le operazioni sincrone, i codici di risposta con esito positivo previsti sono:

* 200
* 204
* 404

Per le operazioni asincrone, il codice di risposta con esito positivo previsto è 202. Resource Manager monitora l'intestazione del percorso o l'intestazione dell'operazione azure-async per determinare lo stato dell'operazione di eliminazione asincrona.
  
### <a name="deletion-errors"></a>Errori di eliminazione

Quando un'operazione di eliminazione restituisce un errore, Resource Manager ritenta la chiamata DELETE. La ripetizione dei tentativi avviene per i codici di stato 5xx, 429 e 408. Per impostazione predefinita, l'intervallo di tempo per la ripetizione dei tentativi è di 15 minuti.

## <a name="after-deletion"></a>Dopo l'eliminazione

Resource Manager invia una chiamata GET per ogni risorsa che ha tentato di eliminare. La risposta della chiamata GET è previsto sia 404. Quando Resource Manager riceve un codice 404, considera l'eliminazione completata correttamente. Resource Manager rimuove la risorsa dalla propria cache.

Tuttavia, se la chiamata GET sulla risorsa restituisce 200 o 201, Resource Manager ricrea la risorsa.

Se l'operazione GET restituisce un errore, Resource Manager esegue un nuovo tentativo GET per i codici di errore seguenti:

* Minore di 100
* 408
* 429
* Maggiore di 500

Per altri codici di errore, Resource Manager considera l'eliminazione della risorsa non riuscita.

> [!IMPORTANT]
> L'eliminazione del gruppo di risorse è irreversibile.

## <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Usare uno dei metodi seguenti per eliminare il gruppo di risorse.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale](https://portal.azure.com) selezionare il gruppo di risorse che si intende eliminare.

1. Selezionare **Elimina gruppo di risorse**.

   ![Eliminare un gruppo di risorse](./media/delete-resource-group/delete-group.png)

1. Per confermare l'eliminazione, digitare il nome del gruppo di risorse

---

## <a name="delete-resource"></a>Eliminare una risorsa

Usare uno dei metodi seguenti per eliminare una risorsa.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale](https://portal.azure.com)selezionare la risorsa che si vuole eliminare.

1. Selezionare **Elimina**. Lo screenshot seguente mostra le opzioni di gestione per una macchina virtuale.

   ![Eliminare una risorsa](./media/delete-resource-group/delete-resource.png)

1. Quando richiesto, confermare l'eliminazione.

---

## <a name="required-access"></a>Accesso obbligatorio

Per eliminare un gruppo di risorse, è necessario accedere all'azione Delete per la risorsa **Microsoft. resources/subscriptions/resourceGroups** . È anche necessario eliminare per tutte le risorse nel gruppo di risorse.

Per un elenco di operazioni, vedere [operazioni del provider di risorse di Azure](../../role-based-access-control/resource-provider-operations.md). Per un elenco dei ruoli predefiniti, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Se si dispone dell'accesso richiesto, ma la richiesta di eliminazione ha esito negativo, è possibile che si verifichi un [blocco](lock-resources.md) sul gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui concetti correlati a Resource Manager, vedere [Panoramica di Azure Resource Manager](overview.md).
* Per i comandi di eliminazione, vedere [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Interfaccia della riga di comando di Azure](/cli/azure/group#az-group-delete) e [API REST](/rest/api/resources/resourcegroups/delete).
