---
title: Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati
description: Come trovare ed eliminare dischi gestiti e non gestiti di Azure (dischi rigidi virtuali/BLOB di pagine) scollegati tramite Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2973d2589be05426a13d16870d0b0fe1a5be9213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023059"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati

Quando si elimina una macchina virtuale (VM) in Azure, per impostazione predefinita, nessun disco collegato alla macchina virtuale viene eliminato. Questa funzionalità consente di prevenire la perdita di dati a causa dell'eliminazione accidentale di macchine virtuali. Dopo l'eliminazione di una macchina virtuale, si continuerà a pagare per i dischi scollegati. Questo articolo illustra come trovare ed eliminare tutti i dischi scollegati e ridurre i costi non necessari.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dischi gestiti: trovare ed eliminare dischi scollegati

Lo script seguente cerca i [dischi gestiti](managed-disks-overview.md) non collegati esaminando il valore della proprietà **ManagedBy**. Quando un disco gestito è collegato a una macchina virtuale, la proprietà **ManagedBy** contiene l'ID risorsa della macchina virtuale. Quando un disco gestito è scollegato, la proprietà **ManagedBy** è null. Lo script esamina tutti i dischi gestiti in una sottoscrizione di Azure. Quando lo script individua un disco gestito con la proprietà **ManagedBy** impostata su null, lo script determina che il disco è scollegato.

>[!IMPORTANT]
>In primo luogo, eseguire lo script impostando la variabile **deleteUnattachedDisks** su 0. Questa azione consente di individuare e visualizzare tutti i dischi gestiti scollegati.
>
>Dopo aver verificato tutti i dischi scollegati, eseguire nuovamente lo script e impostare la variabile **deleteUnattachedDisks** su 1. Questa azione consente di eliminare tutti i dischi gestiti scollegati.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dischi non gestiti: trovare ed eliminare dischi scollegati

I dischi non gestiti sono file disco rigido virtuale che sono archiviati come [BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) negli [account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Lo script seguente cerca i dischi non gestiti scollegati (BLOB di pagine) esaminando il valore della proprietà **LeaseStatus**. Quando un disco non gestito è collegato a una macchina virtuale, la proprietà **LeaseStatus** è impostata su **Bloccato**. Quando un disco non gestito è scollegato, la proprietà **LeaseStatus** è impostata su **Sbloccato**. Lo script esamina tutti i dischi non gestiti in tutti gli account di archiviazione di Azure in una sottoscrizione di Azure. Quando lo script individua un disco non gestito con la proprietà **LeaseStatus** impostata su **Sbloccato**, lo script determina che il disco è scollegato.

>[!IMPORTANT]
>In primo luogo, eseguire lo script impostando la variabile **deleteUnattachedVHDs** su 0. Questa azione consente di individuare e visualizzare tutti i dischi rigidi virtuali non gestiti scollegati.
>
>Dopo aver verificato tutti i dischi scollegati, eseguire nuovamente lo script e impostare la variabile **deleteUnattachedVHDs** su 1. Questa azione consente di eliminare tutti i dischi rigidi virtuali non gestiti scollegati.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Eliminare l'account di archiviazione](../../storage/common/storage-create-storage-account.md) e [Identificare i dischi orfani con PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
