---
title: 'Gateway VPN di Azure: creare un gateway basato su Route: PowerShell'
description: Creare rapidamente un gateway VPN basato su route usando PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 8a4bb9d2ac7b8124fa9b1e00f3ecceda4f4a4cdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152959"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Creare un gateway VPN basato su route usando PowerShell

Questo articolo illustra come creare rapidamente un gateway VPN di Azure basato su route usando PowerShell. Il gateway VPN viene usato quando si crea una connessione VPN alla rete locale. È anche possibile usare il gateway VPN per connettere reti virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

Seguendo i passaggi in questo articolo si creeranno una rete virtuale, una subnet, una subnet del gateway e un gateway VPN (gateway di rete virtuale) basato su route. Dopo aver creato il gateway, è possibile configurare le connessioni. Questi passaggi richiedono una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse. Se si esegue PowerShell in locale, aprire la console di PowerShell con privilegi elevati e connettersi ad Azure con `Connect-AzAccount` il comando.

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Creare una rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L'esempio seguente crea una rete virtuale denominata **VNet1** nella posizione **EastUS**:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Creare una configurazione della subnet usando il cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig).

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Impostare la configurazione della subnet per la rete virtuale usando il cmdlet [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork).


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Aggiungere una subnet del gateway

La subnet del gateway contiene gli indirizzi IP riservati usati dai servizi del gateway di rete virtuale. Per aggiungere una subnet del gateway, usare gli esempi seguenti:

Impostare una variabile per la rete virtuale.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Creare la subnet del gateway usando il cmdlet [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig).

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Impostare la configurazione della subnet per la rete virtuale usando il cmdlet [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork).

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Richiedere un indirizzo IP pubblico

Un gateway VPN deve avere un indirizzo IP pubblico allocato in modo dinamico. Si tratta dell'indirizzo IP specificato durante la creazione di una connessione in un gateway VPN. Usare l'esempio seguente per richiedere un indirizzo IP pubblico:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="create-the-gateway-ip-address-configuration"></a><a name="GatewayIPConfig"></a>Creare la configurazione dell'indirizzo IP del gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Creare il gateway VPN

La creazione di un gateway VPN può richiedere almeno 45 minuti. Al termine della creazione del gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale. Creare un gateway VPN usando il cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Visualizzare il gateway VPN

È possibile visualizzare il gateway VPN usando il cmdlet [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway).

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

L'output sarà simile a questo esempio:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="view-the-public-ip-address"></a><a name="viewgwpip"></a>Visualizzare l'indirizzo IP pubblico

Per visualizzare l'indirizzo IP pubblico del gateway VPN, usare il cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress).

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

Nella risposta di esempio il valore IpAddress è l'indirizzo IP pubblico.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando le risorse create non sono più necessarie, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per eliminare il gruppo di risorse. Questa operazione eliminerà il gruppo di risorse e tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale.

> [!div class="nextstepaction"]
> [Creare una connessione da sito a sito](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Creare una connessione da punto a sito](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Creare una connessione a un'altra rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md)
