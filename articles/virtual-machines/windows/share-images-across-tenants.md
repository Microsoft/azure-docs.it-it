---
title: Condividere immagini della raccolta tra i tenant in Azure
description: Informazioni su come condividere immagini di VM tra tenant di Azure usando le raccolte di immagini condivise e PowerShell.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/15/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: e78cb29551bf7eb3f713edb755464eb7696106e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556129"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-powershell"></a>Condividere immagini di VM della raccolta tra i tenant di Azure con PowerShell

Le raccolte immagini condivise consentono di condividere immagini con il controllo degli accessi in base al ruolo È possibile usare il controllo degli accessi in base al ruolo di Azure per condividere immagini all'interno del tenant e anche a utenti esterni al tenant. Per ulteriori informazioni su questa semplice opzione di condivisione, vedere la pagina relativa alla [condivisione della raccolta](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Non è possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant di Azure. Per creare una macchina virtuale da un'immagine condivisa tra i tenant, è necessario usare l'interfaccia della riga di comando di [Azure](../linux/share-images-across-tenants.md) o PowerShell.

## <a name="create-a-vm-using-powershell"></a>Creare una macchina virtuale con PowerShell

Accedere a entrambi i tenant usando l'ID applicazione, il segreto e l'ID tenant. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Creare la macchina virtuale nel gruppo di risorse che dispone dell'autorizzazione per la registrazione dell'app. Sostituire le informazioni in questo esempio con le proprie.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare risorse della raccolta immagini condivise usando il [portale di Azure](shared-images-portal.md).
