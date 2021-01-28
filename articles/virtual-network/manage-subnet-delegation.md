---
title: Aggiungere o rimuovere una delega della subnet in una rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come aggiungere o rimuovere una subnet delegata per un servizio in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 2bb80ba421617d5fd1699826deda00e56f1e43af
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943667"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Aggiungere o rimuovere una delega della subnet

La delega di subnet offre esplicite autorizzazioni per creare le risorse specifiche del servizio nella subnet, tramite un identificatore univoco durante la distribuzione del servizio. Questo articolo descrive come aggiungere o rimuovere una subnet delegata per un servizio di Azure.

## <a name="portal"></a>Portale

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Questa sezione illustra come creare una rete virtuale e la subnet da delegare in seguito a un servizio di Azure.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa**  >    >  **rete rete virtuale**.
1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.0.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **eastus**.|
    | Subnet - Nome | Immettere la *subnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.0.0.0/24*. |
    |||
1. Lasciare l'impostazione predefinita REST e quindi selezionare **Crea**.

### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si vuole delegare a un servizio di Azure, è necessaria l'autorizzazione seguente: `Microsoft.Network/virtualNetworks/subnets/write` .

Il ruolo predefinito [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di Azure

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure.

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **subnet** in **Impostazioni** e quindi fare clic su subnet **.**
4. *Nella pagina subnet* , per l'elenco **delega Subnet** , selezionare tra i servizi elencati in **Delega subnet a un servizio** (ad esempio, **Microsoft. DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di Azure

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **subnet** in **Impostazioni** e quindi fare clic su subnet **.**
4. Nella *pagina subnet,* per l'elenco **delega Subnet** , selezionare **nessuno** dai servizi elencati in **Delega subnet a un servizio**. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nella posizione **eastus**:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** nel gruppo **myResourceGroup** con il comando [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si vuole delegare a un servizio di Azure, è necessaria l'autorizzazione seguente: `Microsoft.Network/virtualNetworks/subnets/write` .

Il ruolo predefinito [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di Azure

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure. 

Usare [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) per aggiornare la **subnet denominata Subnet** con una delega a un servizio di Azure.  In questo esempio **Microsoft. DBforPostgreSQL/serversv2** viene usato per la delega di esempio:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Per verificare che la delega sia stata applicata, usare [AZ Network VNET subnet Show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Verificare che il servizio sia delegato alla subnet sotto la proprietà **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di Azure

Usare [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) per rimuovere la delega dalla **subnet denominata Subnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Per verificare che la delega sia stata rimossa, usare [AZ Network VNET subnet Show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Verificare che il servizio sia stato rimosso dalla subnet sotto la proprietà **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
L'output del comando è una parentesi null:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Connettersi ad Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse con [New-AzResourceGroup](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) in **myResourceGroup** con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Lo spazio degli indirizzi IP per la rete virtuale è **10.0.0.0/16**. La subnet all'interno della rete virtuale è **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si vuole delegare a un servizio di Azure, è necessaria l'autorizzazione seguente: `Microsoft.Network/virtualNetworks/subnets/write` .

Il ruolo predefinito [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di Azure

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure. 

Usare [Add-AzDelegation](/powershell/module/az.network/add-azdelegation) per aggiornare la subnet denominata " **Subnet** " con una delega denominata **dedelegation** a un servizio di Azure.  In questo esempio **Microsoft. DBforPostgreSQL/serversv2** viene usato per la delega di esempio:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Usare [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) per verificare la delega:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di Azure

Usare [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) per rimuovere la delega dalla **subnet denominata Subnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Usare [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) per verificare che la delega sia stata rimossa:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [gestire le subnet in Azure](virtual-network-manage-subnet.md).
