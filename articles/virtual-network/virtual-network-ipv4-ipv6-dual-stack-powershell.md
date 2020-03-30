---
title: Distribuire l'applicazione dual stack IPv6 - Basic Load Balancer - PowerShellDeploy IPv6 dual stack application - Basic Load Balancer - PowerShell
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire un'applicazione dual stack IPv6 nella rete virtuale di Azure usando Azure Powershell.This article shows how deploy an IPv6 dual stack application in Azure virtual network using Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: 003d677dcdead5792f932ecfe6350df63184cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75368331"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell-preview"></a>Distribuire un'applicazione dual stack IPv6 usando Basic Load Balancer - PowerShell (anteprima)Deploy an IPv6 dual stack application using Basic Load Balancer - PowerShell (Preview)

In questo articolo viene illustrato come distribuire un'applicazione dual stack (IPv4 e IPv6) con il bilanciamento del carico di base usando Azure PowerShell che include una rete virtuale a doppio stack e una subnet, un servizio di bilanciamento del carico di base con configurazioni front-end dual (IPv4 e IPv6), macchine virtuali con schede di interfaccia di rete hanno una configurazione IP doppia, un gruppo di sicurezza di rete e indirizzi IP pubblici.

Per distribuire un'applicazione dual stack (IPV4 e IPv6) usando Load Balancer Standard, vedere [Distribuire un'applicazione Dual Stack IPv6 con Load Balancer Standard usando Azure PowerShell.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)

> [!Important]
> Il supporto IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede la versione 6.9.0 o successiva del modulo di Azure PowerShell.If you choose to install and use PowerShell locally, this article requires the Azure PowerShell module version 6.9.0 or later. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="prerequisites"></a>Prerequisiti
Prima di distribuire un'applicazione dual stack in Azure, è necessario configurare la sottoscrizione per questa funzionalità di anteprima usando Azure PowerShell seguente:Before you deploy a dual stack application in Azure, you must configure your subscription for this preview feature using the following Azure PowerShell:

Registrarsi come segue:

```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato della registrazione eseguendo il comando di Azure PowerShell seguente: controllare la registrazione come segue:You can check your registration status by running the following Azure PowerShell command: Check on the registration as follows:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Al termine della registrazione eseguire questo comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare la rete virtuale dual stack, è necessario creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato myRGDualStack nella posizione *est-us:The following* example creates a resource group named *myRGDualStack* in the east us location:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Creare indirizzi IP pubblici IPv4 e IPv6Create IPv4 and IPv6 public IP addresses
Per accedere alle macchine virtuali da Internet, sono necessari indirizzi IP pubblici IPv4 e IPv6 per il servizio di bilanciamento del carico. Creare indirizzi IP pubblici con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Nell'esempio seguente vengono creati gli indirizzi IP pubblici IPv4 e IPv6 denominati *dsPublicIP_v4* e *dsPublicIP_v6* nel gruppo di risorse *dsRG1:*

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Per accedere alle macchine virtuali tramite una connessione RDP, creare un indirizzo IP pubblico IPV4 per le macchine virtuali con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione si configura l'IP front-end doppio (IPv4 e IPv6) e il pool di indirizzi back-end per il servizio di bilanciamento del carico e quindi si crea un servizio di bilanciamento del carico di base.

### <a name="create-front-end-ip"></a>Creare l'IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Nell'esempio seguente vengono create configurazioni IP front-end IPv4 e IPv6 denominate *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6 :*

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackaddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti. L'esempio seguente crea pool di indirizzi back-end denominati dsLbBackEndPool_v4 e dsLbBackEndPool_v6 per includere macchine virtuali con configurazioni NIC IPV4 e IPv6:The following example creates back-end address pools named *dsLbBackEndPool_v4* and *dsLbBackEndPool_v6* to include VMs with both IPV4 and IPv6 NIC configurations:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Creare un probe di integrità
Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità per monitorare l'integrità delle macchine virtuali.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Per assicurarsi che solo le macchine virtuali integre ricevano il traffico, è possibile definire facoltativamente un probe di integrità. Il servizio di bilanciamento del carico di base usa un probe IPv4 per valutare l'integrità degli endpoint IPv4 e IPv6 nelle macchine virtuali. Il servizio di bilanciamento del carico standard include il supporto per probe di integrità IPv6 in modo esplicito.

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L'esempio seguente crea regole di bilanciamento del carico denominate dsLBrule_v4 e dsLBrule_v6 e bilancia il traffico sulla porta TCP 80 alle configurazioni IP front-end IPv4 e IPv6:The following example creates load balancer rules named *dsLBrule_v4* and *dsLBrule_v6* and dsLBrule_v6 and balances traffic on *TCP* port *80* to the IPv4 and IPv6 frontend IP configurations:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare l'istanza di Load Balancer Basic con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Nell'esempio seguente viene creato un servizio di bilanciamento del carico di base pubblico denominato myLoadBalancer usando le configurazioni IP front-end IPv4 e IPv6, i pool back-end e le regole di bilanciamento del carico creati nei passaggi precedenti:The following example creates a public Basic Load Balancer named *myLoadBalancer* using the IPv4 and IPv6 frontend IP configurations, back-end pools, and load-balancing rules that you created in the preceding steps:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Creare risorse di rete
Prima di distribuire alcune macchine virtuali e testare il bilanciamento, è necessario creare risorse di rete di supporto: set di disponibilità, gruppo di sicurezza di rete, rete virtuale e schede di interfaccia di rete virtuali. 
### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per aumentare la disponibilità elevata dell'app, posizionare le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). L'esempio seguente crea un set di disponibilità denominato *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che regoleranno le comunicazioni in ingresso e in uscita nella rete virtuale.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Creare una regola del gruppo di sicurezza di rete per la porta 3389

Per consentire le connessioni RDP tramite la porta 3389, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creare una regola del gruppo di sicurezza di rete per la porta 80

Creare una regola del gruppo di sicurezza di rete per consentire le connessioni Internet tramite la porta 80 con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Creare NIC

Creare schede di interfaccia di rete virtuali con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Nell'esempio seguente vengono create due schede di interfaccia di rete virtuali con configurazioni IPv4 e IPv6. Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi.

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

A questo punto è possibile creare le VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Determinare gli indirizzi IP degli endpoint IPv4 e IPv6
Ottenere tutti gli oggetti interfaccia di rete nel gruppo di `get-AzNetworkInterface`risorse per riepilogare gli indirizzi IP utilizzati in questa distribuzione con . Ottenere inoltre gli indirizzi front-end del servizio di bilanciamento del `get-AzpublicIpAddress`carico degli endpoint IPv4 e IPv6 con .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Nella figura seguente viene illustrato un output di esempio che elenca gli indirizzi IPv4 e IPv6 privati delle due macchine virtuali e gli indirizzi IP IPv4 e IPv6 front-end del servizio di bilanciamento del carico.

![Riepilogo IP della distribuzione dell'applicazione dual stack (IPv4/IPv6) in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizzare la rete virtuale dual stack IPv6 nel portale di AzureView IPv6 dual stack virtual network in Azure portal
È possibile visualizzare la rete virtuale dual stack IPv6 nel portale di Azure come segue:You can view the IPv6 dual stack virtual network in Azure portal as follows:
1. Nella barra di ricerca del portale immettere *dsVnet*.
2. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *dsVnet*. La rete virtuale dual stack mostra le due schede di interfaccia di rete con configurazioni IPv4 e IPv6 situate nella subnet dello stack doppio denominata *dsSubnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> La rete virtuale IPv6 per Azure è disponibile nel portale di Azure in sola lettura per questa versione di anteprima.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un servizio di bilanciamento del carico di base con una configurazione IP front-end doppia (IPv4 e IPv6). Sono state inoltre create due macchine virtuali che includevano schede di interfaccia di rete con configurazioni dual IP (IPV4 e IPv6) che sono state aggiunte al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto IPv6 nelle reti virtuali di Azure, vedere [Che cos'è IPv6 per](ipv6-overview.md) la rete virtuale di Azure?
