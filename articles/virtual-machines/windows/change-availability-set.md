---
title: Modificare un set di disponibilità di macchine virtuali
description: Informazioni su come modificare il set di disponibilità per la macchina virtuale usando Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 65bf2dea6f3bc2e33ec10dc75b1678466401c10b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184475"
---
# <a name="change-the-availability-set-for-a-vm"></a>Modificare il set di disponibilità per una macchina virtuale
I passaggi seguenti descrivono come modificare il set di disponibilità di una VM tramite Azure PowerShell. Una VM può essere aggiunta a un set di disponibilità solo in fase di creazione. Per modificare il set di disponibilità, è necessario eliminare e quindi ricreare la macchina virtuale. 

Questo articolo si applica alle macchine virtuali Linux e Windows.

Questo articolo è stato testato l'ultima volta il 12/02/2019 tramite [Azure Cloud Shell](https://shell.azure.com/powershell) e il [modulo PowerShell di Azure](/powershell/azure/install-az-ps) versione 1.2.0.

Questo esempio non verifica se la macchina virtuale è collegata a un servizio di bilanciamento del carico. Se la macchina virtuale è collegata a un servizio di bilanciamento del carico, sarà necessario aggiornare lo script per gestire tale caso. 


## <a name="change-the-availability-set"></a>Modificare il set di disponibilità 

Lo script seguente offre un esempio di raccolta delle informazioni necessarie ed eliminazione e ricreazione della VM originale in un nuovo set di disponibilità.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Passaggi successivi

Aumentare lo spazio di archiviazione della VM aggiungendo un ulteriore [disco dati](attach-managed-disk-portal.md).
