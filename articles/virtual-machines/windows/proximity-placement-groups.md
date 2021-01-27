---
title: 'PowerShell: usare i gruppi di posizionamento di prossimità'
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità con Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4de71be8c88264d2cfb513a7f0214515058b5185
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878325"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando PowerShell


Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](../co-location.md#proximity-placement-groups).

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.


## <a name="create-a-proximity-placement-group"></a>Creare un gruppo di selezione host di prossimità
Creare un gruppo di posizionamento di prossimità usando il cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup). 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Elencare i gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento di prossimità usando il cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale nel gruppo di posizionamento di prossimità usando `-ProximityPlacementGroup $ppg.Id` per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si usa [New-AzVM](/powershell/module/az.compute/new-azvm) per creare la macchina virtuale.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

È possibile visualizzare la macchina virtuale nel gruppo di posizionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Spostare una macchina virtuale esistente in un gruppo di posizionamento vicino

È anche possibile aggiungere una macchina virtuale esistente a un gruppo di posizionamento di prossimità. Prima di tutto è necessario stop\deallocate la macchina virtuale, quindi aggiornare la macchina virtuale e riavviare.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Spostare una macchina virtuale esistente da un gruppo di posizionamento vicino

Per rimuovere una macchina virtuale da un gruppo di posizionamento vicino, è necessario prima di tutto stop\deallocate la macchina virtuale, quindi aggiornare la macchina virtuale e riavviare.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso `-ProximityPlacementGroup` parametro con il cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) per creare un set di disponibilità e tutte le macchine virtuali create nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

Per aggiungere o rimuovere un set di disponibilità esistente in un gruppo di posizionamento di prossimità, è necessario prima arrestare tutte le VM nel set di disponibilità. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Spostare un set di disponibilità esistente in un gruppo di posizionamento vicino

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Spostare un set di disponibilità esistente fuori da un gruppo di posizionamento vicino

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso `-ProximityPlacementGroup` parametro con [New-AzVmss](/powershell/module/az.compute/new-azvmss) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.


Per aggiungere o rimuovere un set di scalabilità esistente in un gruppo di posizionamento vicino, è necessario prima arrestare il set di scalabilità. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Spostare un set di scalabilità esistente in un gruppo di posizionamento vicino

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Spostare un set di scalabilità esistente fuori da un gruppo di posizionamento vicino

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Per creare gruppo di posizionamento di prossimità, è anche possibile usare l'[interfaccia della riga di comando di Azure](../linux/proximity-placement-groups.md).