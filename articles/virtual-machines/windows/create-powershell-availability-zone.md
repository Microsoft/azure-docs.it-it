---
title: Creare una VM con zone usando Azure PowerShell
description: Creare una macchina virtuale in una zona di disponibilità con Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 7167c5806fde883a84878d4947dba9fd8c095f69
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558832"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Creare una macchina virtuale in una zona di disponibilità usando Azure PowerShell

Questo articolo illustra come usare Azure PowerShell per creare una macchina virtuale di Azure che esegue Windows Server 2016 in una zona di disponibilità di Azure. Una [zona di disponibilità](../../availability-zones/az-overview.md) è una zona fisicamente separata in un'area di Azure. Usare le zone di disponibilità per proteggere app e dati da un poco probabile errore o perdita di un intero data center.

Per usare una zona di disponibilità, creare la macchina virtuale in un'[area di Azure supportata](../../availability-zones/az-region.md).

 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Verificare la disponibilità dello SKU di VM
La disponibilità delle dimensioni delle macchine virtuali, o SKU, può variare in base all'area e alla zona. Per semplificare la pianificazione dell'uso delle zone di disponibilità, è possibile elencare gli SKU di VM disponibili per area e zona di Azure. Questa possibilità assicura la scelta di dimensioni adeguate per le macchine virtuali e l'ottenimento della resilienza desiderata tra le aree. Per altre informazioni sui tipi e sulle dimensioni delle VM, vedere la [panoramica delle dimensioni delle VM](../sizes.md).

È possibile visualizzare gli SKU di VM disponibili con il comando [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). L'esempio seguente elenca gli SKU di VM disponibili nell'area *eastus2*:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

L'output è simile all'esempio sintetico seguente, che mostra le zone di disponibilità nelle quali è disponibile ogni dimensione di VM:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus2*. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Creare risorse di rete

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Creare una rete virtuale, una subnet e un indirizzo IP pubblico 
Queste risorse vengono usate per fornire la connettività di rete alla macchina virtuale e connetterla a Internet. Creare l'indirizzo IP in una zona di disponibilità, la *2* in questo esempio. In un passaggio successivo viene creata la VM nella stessa zona usata per creare l'indirizzo IP.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Creare un gruppo di sicurezza di rete e una regola del gruppo di sicurezza di rete 
Il gruppo di sicurezza di rete protegge la macchina virtuale usando le regole in entrata e in uscita. In questo caso viene creata una regola in entrata per la porta 3389 che consente connessioni desktop remoto in ingresso. È necessario anche creare una regola in ingresso per la porta 80, che consente il traffico Web in ingresso.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Creare una scheda di rete per la macchina virtuale 
Creare una scheda di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) per la macchina virtuale. La scheda di rete connette la macchina virtuale a una subnet, a un gruppo di sicurezza di rete e a un indirizzo IP pubblico.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Creare macchina virtuale

Creare una configurazione di macchina virtuale. Questa configurazione include le impostazioni utilizzate quando si distribuisce la macchina virtuale, ad esempio l'immagine della macchina virtuale, la dimensione e la configurazione di autenticazione. La dimensione *Standard_DS1_v2* usata in questo esempio è supportata nelle zone di disponibilità. Questa configurazione specifica anche la zona di disponibilità impostata durante la creazione dell'indirizzo IP. Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Creare la macchina virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Confermare la zona per il disco gestito

La risorsa indirizzo IP della macchina virtuale è stata creata nella stessa zona disponibilità della macchina virtuale. La risorsa disco gestito per la VM viene creata nella stessa zona di disponibilità. È possibile verificarlo con [Get-AzDisk](/powershell/module/az.compute/get-azdisk):

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

L'output indica che il disco gestito si trova nella stessa zona di disponibilità della VM:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare una macchina virtuale in una zona di disponibilità. Altre informazioni sulla [disponibilità](../availability.md) per le macchine virtuali di Azure.