---
title: Query per recuperare le sottoscrizioni di Griglia di eventi di Azure
description: Questo articolo descrive come elencare le sottoscrizioni di griglia di eventi nella sottoscrizione di Azure. Si forniscono parametri diversi in base al tipo di sottoscrizione.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76721541"
---
# <a name="query-event-grid-subscriptions"></a>Sottoscrizioni di Griglia di eventi di Azure 

Questo articolo descrive come elencare le sottoscrizioni di Griglia di eventi nella sottoscrizione di Azure. Quando si eseguono query per recuperare le sottoscrizioni esistenti di Griglia di eventi di Azure, è importante conoscere i diversi tipi di sottoscrizioni. È necessario specificare parametri diversi in base al tipo di sottoscrizione che si vuole ottenere.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Gruppi di risorse e sottoscrizioni di Azure

Le sottoscrizioni di Azure e i gruppi di risorse non sono risorse di Azure. Pertanto, le sottoscrizioni di Griglia di eventi per i gruppi di risorse o le sottoscrizioni di Azure non hanno le stesse proprietà delle sottoscrizioni di Griglia di eventi per risorse di Azure. Le sottoscrizioni di Griglia di eventi per i gruppi di risorse o le sottoscrizioni di Azure vengono considerate globali.

Per ottenere le sottoscrizioni di Griglia di eventi per una sottoscrizione di Azure e i relativi gruppi di risorse, non è necessario specificare alcun parametro. Assicurarsi di aver selezionato la sottoscrizione di Azure per cui eseguire una query. Gli esempi seguenti non recuperano le sottoscrizioni di Griglia di eventi per gli argomenti personalizzati o le risorse di Azure.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Per PowerShell, usare:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Per ottenere le sottoscrizioni di Griglia di eventi per una sottoscrizione di Azure, specificare il tipo di argomento **Microsoft.Resources.Subscriptions**.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Per ottenere le sottoscrizioni di Griglia di eventi per tutti i gruppi di risorse in una sottoscrizione di Azure, specificare il tipo di argomento **Microsoft.Resources.ResourceGroups**.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Per ottenere le sottoscrizioni di Griglia di eventi per un gruppo di risorse specificato, immettere il nome del gruppo di risorse come parametro.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Argomenti personalizzati e risorse di Azure

Gli argomenti personalizzati di Griglia di eventi sono risorse di Azure. Pertanto, è possibile procedere allo stesso modo per eseguire una query per recuperare le sottoscrizioni di Griglia di eventi per gli argomenti personalizzati e le altre risorse, ad esempio account di archiviazione BLOB. Per ottenere le sottoscrizioni di Griglia di eventi per gli argomenti personalizzati, è necessario specificare i parametri che identificano la risorsa o la posizione della risorsa. Non è possibile eseguire query su vasta scala per recuperare le sottoscrizioni di Griglia di eventi per le risorse in tutta la sottoscrizione di Azure.

Per ottenere le sottoscrizioni di Griglia di eventi per gli argomenti personalizzati e altre risorse in una posizione, specificare il nome della posizione.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Per ottenere le sottoscrizioni degli argomenti personalizzati per una posizione, specificare la posizione e il tipo di argomento **Microsoft.EventGrid.Topics**.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Per ottenere le sottoscrizioni degli account di archiviazione per una posizione, specificare la posizione e il tipo di argomento **Microsoft.Storage.StorageAccounts**.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Per ottenere le sottoscrizioni di Griglia di eventi per un argomento personalizzato, specificare il nome dell'argomento personalizzato e il nome del relativo gruppo di risorse.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Per PowerShell, usare:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Per ottenere le sottoscrizioni di Griglia di eventi per una determinata risorsa, specificare l'ID di risorsa.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Per PowerShell, usare:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
