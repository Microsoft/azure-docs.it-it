---
title: Indirizzare il traffico di rete con Azure PowerShell | Microsoft Docs
description: Questo articolo descrive come instradare il traffico di rete con una tabella di route usando PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bd52e8865bb704497740851f6a0e3c886ed9d6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790208"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Indirizzare il traffico di rete con una tabella di route usando PowerShell

Per impostazione predefinita, Azure indirizza automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questo articolo vengono illustrate le operazioni seguenti:

* Creare una tabella di route
* Creare una route
* Creare una rete virtuale con più subnet
* Associare una route a una subnet
* Creare un'appliance virtuale di rete che indirizza il traffico
* Distribuire le macchine virtuali in subnet diverse
* Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-route-table"></a>Creare una tabella di route

Prima di poter creare una tabella di route, creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* per tutte le risorse create in questo articolo.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una tabella di route con [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). L'esempio seguente crea una tabella di route denominata *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Creare una route

Creare una route recuperando l'oggetto tabella di route con [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), creare una route con [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig), quindi scrivere la configurazione della route nella tabella di route con [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Prima di poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet. Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Creare tre subnet creando tre configurazioni di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). L'esempio seguente crea tre configurazioni subnet per le subnet *Public*, *Private* e *DMZ*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Scrivere le configurazioni di subnet nella rete virtuale con [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), che consente di creare le subnet nella rete virtuale:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Associare la tabella di route *myRouteTablePublic* alla subnet *public* con [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet nella rete virtuale con [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Un'appliance virtuale di rete è una macchina virtuale che svolge una funzione di rete, ad esempio il routing, la protezione tramite firewall o l'ottimizzazione della WAN.

Prima di creare una macchina virtuale occorre creare un'interfaccia di rete.

### <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

Prima di creare un'interfaccia di rete, è necessario recuperare l'ID della rete virtuale con [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), quindi l'ID subnet con [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) nella subnet *DMZ* con l'invio IP abilitato:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

Per creare una macchina virtuale e collegare un'interfaccia di rete esistente, è prima necessario creare una configurazione di macchina virtuale con [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). La configurazione include l'interfaccia di rete creata nel passaggio precedente. Quando richiesto, selezionare il nome utente e la password con cui si vuole accedere alla macchina virtuale.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Creare la macchina virtuale usando la configurazione della macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo.

Creare una macchina virtuale nella subnet *public* con [New-AzVM](/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVmPublic* nella subnet *Public* della rete virtuale *myVirtualNetwork*.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Creare una macchina virtuale nella subnet *Private*.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

La creazione della VM richiede alcuni minuti. Non proseguire con il passaggio successivo fino a quando non viene creata la macchina virtuale e Azure non restituisce l'output a PowerShell.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per restituire l'indirizzo IP pubblico della macchina virtuale *myVmPrivate* . L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVmPrivate*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Usare il comando seguente per creare una sessione Desktop remoto con la macchina virtuale *myVmPrivate* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Aprire il file RDP scaricato. Quando richiesto, selezionare **Connetti**.

Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale), quindi scegliere **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.

In un passaggio successivo, il `tracert.exe` comando viene usato per testare il routing. Tracert usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell nella VM *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Anche se in questo articolo viene usato il tracciamento delle route per testare il routing, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

In Enable IP forwarding (Abilitare l'inoltro IP) è stato abilitato l'inoltro IP all'interno di Azure per l'interfaccia di rete della macchina virtuale. All'interno della macchina virtuale è necessario che il sistema operativo o un'applicazione in esecuzione nella macchina virtuale sia anche in grado di inoltrare il traffico di rete. Abilitare l'inoltri IP all'interno del sistema operativo di *myVmNva*.

Da un prompt dei comandi nella macchina virtuale *myVmPrivate*, avviare una sessione Desktop remoto con *myVmNva*:

``` 
mstsc /v:myvmnva
```

Per abilitare l'inoltro IP nel sistema operativo, immettere il comando seguente in PowerShell dalla macchina virtuale *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Riavviare la macchina virtuale *myVmNva*. Con questa operazione verrà anche disconnessa la sessione Desktop remoto.

Mentre si è ancora connessi alla macchina virtuale *myVmPrivate*, creare una sessione Desktop remoto alla macchina virtuale *myVmPublic* dopo il riavvio della macchina virtuale *myVmNva*:

``` 
mstsc /v:myVmPublic
```

Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell nella VM *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Per testare il routing del traffico di rete alla macchina virtuale *myVmPrivate* dalla macchina virtuale *myVmPublic*, immettere il comando seguente da PowerShell nella macchina virtuale *myVmPublic*:

```
tracert myVmPrivate
```

La risposta restituita è simile all'esempio seguente:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Come si può notare, il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, ossia l'indirizzo IP privato della macchina virtuale *myVmPrivate*. A causa della route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public*, Azure ha indirizzato il traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPublic*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmPrivate*.

Per testare il routing del traffico di rete alla macchina virtuale *myVmPublic* dalla macchina virtuale *myVmPrivate*, immettere il comando seguente da un prompt dei comandi nella macchina virtuale *myVmPrivate*:

```
tracert myVmPublic
```

La risposta restituita è simile all'esempio seguente:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Come si può osservare, il traffico viene indirizzato direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, usare [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Distribuire una varietà di appliance virtuali di rete preconfigurate che svolgono funzioni di rete come la protezione tramite firewall e l'ottimizzazione della WAN da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Per altre informazioni sul routing, vedere [Panoramica del routing](virtual-networks-udr-overview.md) e [Gestire una tabella di route](manage-route-table.md).

Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, non è possibile distribuire le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Per informazioni su come fare, vedere [Limitare l'accesso alla rete alle risorse PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
