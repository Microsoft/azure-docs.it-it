---
title: Convertire i dischi non gestiti di una VM Windows in dischi gestiti
description: Come convertire i dischi non gestiti di una VM Windows in dischi gestiti usando PowerShell nel modello di distribuzione Resource Manager
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: cf601c7f7429d4676ec480ddc016158150da8ce5
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815200"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertire i dischi non gestiti di una VM Windows in dischi gestiti

Se si hanno macchine virtuali (VM) Windows che usano dischi non gestiti, è possibile convertire le VM all'uso di dischi gestiti mediante il servizio [Azure Managed Disks](managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

 

## <a name="before-you-begin"></a>Prima di iniziare


* Vedere [Pianificare la migrazione a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Vedere le [domande frequenti sulla migrazione a Managed Disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* I dischi rigidi virtuali originali e l'account di archiviazione usato dalla macchina virtuale prima della conversione non verranno eliminati e i costi correlati continueranno a essere addebitati. Per evitare addebiti per questi elementi, eliminare i BLOB VHD originali dopo aver verificato che la conversione sia stata completata. Se è necessario trovare questi dischi non collegati per eliminarli, vedere l'articolo [trovare ed eliminare dischi gestiti e non gestiti di Azure non collegati](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le macchine virtuali si trovano in un set di disponibilità, vedere la sezione successiva. 

1. Deallocare la macchina virtuale mediante il cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Convertire la macchina virtuale in dischi gestiti mediante il cmdlet [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk). Il processo seguente converte la macchina virtuale precedente, incluso il disco del sistema operativo e i dischi dati, e quindi avvia la macchina virtuale:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

1. Convertire il set di disponibilità mediante il cmdlet [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset). L'esempio seguente aggiorna il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se l'area in cui si trova il set di disponibilità ha solo 2 domini di errore gestiti, ma il numero di domini di errore non gestiti è 3, questo comando visualizza un errore di questo tipo: "Il conteggio del dominio di errore specificato 3 deve essere compreso nell'intervallo 1-2". Per correggere l'errore, impostare il dominio di errore su 2 e impostare `Sku` su `Aligned` come segue:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Deallocare e convertire le VM nel set di disponibilità. Lo script seguente dealloca ogni macchina virtuale mediante il cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm), la converte usando [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) e la riavvia automaticamente con un processo separato da quello di conversione:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la conversione o se una VM si trova in uno stato di errore a causa di problemi in una conversione precedente, eseguire di nuovo il cmdlet `ConvertTo-AzVMManagedDisk`. In genere è sufficiente riprovare per sbloccare la situazione.
Prima di eseguire la conversione, assicurarsi che lo stato di tutte le estensioni di macchina virtuale corrisponda a 'Provisioning completato'. In caso contrario, la conversione avrà esito negativo con codice di errore 409.

## <a name="convert-using-the-azure-portal"></a>Eseguire la conversione usando il portale di Azure

È anche possibile convertire i dischi non gestiti in dischi gestiti usando il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco di macchine virtuali nel portale.
3. Nel pannello della macchina virtuale selezionare **Dischi** dal menu.
4. Nella parte superiore del pannello **Dischi** selezionare **Eseguire la migrazione a Managed Disks**.
5. Se la macchina virtuale è in un set di disponibilità, nel pannello **Eseguire la migrazione a Managed Disks** apparirà un avviso che indica che è necessario prima convertire il set di disponibilità. L'avviso deve avere un collegamento su cui si può fare clic per convertire il set di disponibilità. Dopo avere convertito il set di disponibilità o se la macchina virtuale non è in un set di disponibilità, fare clic su **Esegui la migrazione** per avviare il processo di migrazione dei dischi ai dischi gestiti.

La macchina virtuale verrà arrestata e riavviata al termine della migrazione.

## <a name="next-steps"></a>Passaggi successivi

[Convertire i dischi gestiti standard in Premium](convert-disk-storage.md)

Eseguire una copia di sola lettura di una VM usando [snapshot](snapshot-copy-managed-disk.md).
