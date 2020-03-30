---
title: Creare un gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure
description: Distribuire gateway VPN ed ExpressRoute in zone di disponibilità
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150171"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Creare un gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure

È possibile distribuire gateway VPN ed ExpressRoute in zone di disponibilità di Azure. In questo modo, i gateway di rete virtuale ottengono maggiore disponibilità, scalabilità e resilienza. La distribuzione di gateway in zone di disponibilità di Azure separa fisicamente e logicamente i gateway all'interno di un'area e consente, al contempo, di proteggere la connettività di rete locale ad Azure da errori a livello di zona. Per informazioni, vedere  [Informazioni sui gateway di rete virtuale con ridondanza della zona](about-zone-redundant-vnet-gateways.md) e [Informazioni sulle zone di disponibilità di Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Dichiarare le variabili

Dichiarare le variabili da usare. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati, se necessario. Se si chiude la sessione di PowerShell o Cloud Shell in qualsiasi momento durante l'esercizio, copiare e incollare nuovamente i valori per dichiarare nuovamente le variabili. Quando si specifica un percorso, verificare che l'area specificata sia supportata. Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Creare la rete virtuale

Creare un gruppo di risorse.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Creare una rete virtuale.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Aggiungere la subnet del gateway

La subnet del gateway contiene gli indirizzi IP riservati usati dai servizi del gateway di rete virtuale. Per aggiungere e impostare una subnet del gateway, usare gli esempi seguenti:

Aggiungere la subnet del gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Impostare la configurazione della subnet del gateway per la rete virtuale.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Richiedere un indirizzo IP pubblico
 
In questo passaggio scegliere le istruzioni che si applicano al gateway da creare. La selezione delle zone per la distribuzione dei gateway dipende dalle zone specificate per l'indirizzo IP pubblico.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Per i gateway con ridondanza della zona

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Standard** e non specificare alcuna zona. In questo caso, l'indirizzo IP pubblico Standard creato sarà un IP pubblico con ridondanza della zona.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Per i gateway a livello di zona

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Standard**. Specificare la zona (1, 2 o 3). Tutte le istanze del gateway verranno distribuite in questa zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Per i gateway a livello di area

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Basic**. In questo caso, il gateway viene distribuito come gateway a livello di area e non dispone di alcuna ridondanza della zona integrata nel gateway. Le istanze del gateway vengono create rispettivamente in tutte le zone disponibili.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Creare la configurazione IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Creare il gateway

Creare il gateway di rete virtuale.

### <a name="for-expressroute"></a>Per ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Per Gateway VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Domande frequenti

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Cosa cambia quando si distribuiscono queste nuove SKU?

Per l'utente, è possibile distribuire i gateway con ridondanza della zona. Pertanto, tutte le istanze dei gateway verranno distribuite nelle zone di disponibilità di Azure e ogni zona di disponibilità avrà un dominio di aggiornamento e di errore differente. In questo modo, i gateway saranno più affidabili, disponibili e resilienti agli errori di zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usare il portale di Azure?

Sì, è possibile usare il portale di Azure per distribuire le nuove SKU. Tuttavia, queste nuove SKU saranno visibili solo nelle aree di Azure con zone di disponibilità di Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>In quali aree è possibile usare le nuove SKU?

Vedere [zone di disponibilità](../availability-zones/az-overview.md#services-support-by-region) per l'elenco più recente delle aree disponibili.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>È possibile modificare, eseguire la migrazione o aggiornare i gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona?

La migrazione dei gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona attualmente non è supportata. È tuttavia possibile eliminare il gateway esistente e ricrearne uno con ridondanza della zona o a livello di zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>È possibile distribuire i gateway VPN ed ExpressRoute nella stessa rete virtuale?

La coesistenza di gateway VPN ed ExpressRoute nella stessa rete virtuale è supportata. Tuttavia è consigliabile riservare un intervallo di indirizzi IP /27 per la subnet del gateway.
