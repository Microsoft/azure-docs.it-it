---
title: Ottenere la chiave di accesso per una risorsa di griglia di eventi
description: Questo articolo descrive come ottenere la chiave di accesso per un argomento o un dominio di griglia di eventi
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632530"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Ottenere le chiavi di accesso per le risorse di griglia di eventi (argomenti o domini)
Le chiavi di accesso vengono usate per autenticare gli eventi di pubblicazione di un'applicazione nelle risorse di griglia di eventi di Azure (argomenti e domini). È consigliabile rigenerare regolarmente le chiavi e archiviarle in modo sicuro. Vengono fornite due chiavi di accesso in modo che sia possibile mantenere le connessioni utilizzando una chiave durante la rigenerazione dell'altra.

Questo articolo descrive come ottenere le chiavi di accesso per una risorsa di griglia di eventi (argomento o dominio) usando portale di Azure, PowerShell o l'interfaccia della riga di comando. 

## <a name="azure-portal"></a>Portale di Azure
Nella portale di Azure passare alla scheda **chiavi di accesso** dell' **argomento griglia di eventi** o della pagina dominio di griglia di **eventi** per l'argomento o il dominio.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Pagina chiavi di accesso":::

## <a name="azure-powershell"></a>Azure PowerShell
Usare il comando [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) per ottenere le chiavi di accesso per gli argomenti. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Usare il comando [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) per ottenere le chiavi di accesso per i domini. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare il comando [AZ eventgrid topic Key List](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) per ottenere le chiavi di accesso per gli argomenti. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Usare il comando [AZ eventgrid Domain Key List](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) per ottenere le chiavi di accesso per i domini. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [autenticare i client di pubblicazione](security-authenticate-publishing-clients.md). 
