---
title: Creare una macchina virtuale da un disco rigido virtuale generalizzato caricatoCreate a VM from an uploaded generalized VHD
description: Caricare un disco rigido virtuale generalizzato in Azure e usarlo per creare nuove macchine virtuali nel modello di distribuzione Azure Resource Manager.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464946"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Caricare un disco rigido virtuale generalizzato e usarlo per creare nuove macchine virtuali in Azure

Questo articolo illustra come usare PowerShell per caricare un disco rigido virtuale di una macchina virtuale generalizzata in Azure, creare un'immagine dal disco rigido virtuale e quindi una nuova macchina virtuale dall'immagine. È possibile caricare un disco rigido virtuale esportato da uno strumento di virtualizzazione locale o da un altro cloud. Usando [Managed Disks](managed-disks-overview.md) per la nuova macchina virtuale è possibile semplificarne la gestione e ottenere una maggiore disponibilità posizionando la macchina virtuale in un set di disponibilità. 

Per uno script di esempio, vedere [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Prima di iniziare

- Prima di caricare dischi rigidi virtuali in Azure, è necessario seguire la procedura in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Rivedere l'articolo [Plan for the migration to Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Piano per la migrazione a Managed Disks) prima di avviare la migrazione a [Managed Disks](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizzare la macchina virtuale di origine con Sysprep

Se non è già stato fatto, è necessario Sysprep la macchina virtuale prima di caricare il disco rigido virtuale in Azure.If you haven't already, you need to Sysprep the VM before uploading the VHD to Azure. Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per i dettagli su Sysprep, vedere [Sysprep Overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Panoramica di Sysprep).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se si prevede di eseguire Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la macchina virtuale](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su %windir%\system32\sysprep e quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto** selezionare **Arresta il sistema**.
5. Selezionare **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. Non riavviare la VM.


## <a name="upload-the-vhd"></a>Caricare il disco rigido virtuale 

È ora possibile caricare un disco rigido virtuale direttamente in un disco gestito. Per istruzioni, vedere [Caricare un disco rigido virtuale in Azure usando Azure PowerShell.For instructions, see Upload a VHD to Azure using Azure PowerShell.](disks-upload-vhd-to-managed-disk-powershell.md)



Una volta caricato il disco rigido virtuale sul disco gestito, è necessario utilizzare [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) per ottenere il disco gestito.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Creare l'immagine
Creare un'immagine gestita dal disco gestito del sistema operativo generalizzato. Sostituire i valori seguenti con le informazioni personali.

In primo luogo, impostare alcune variabili:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Creare l'immagine utilizzando il disco gestito.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Creare l'immagine.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Creare la VM

Ora che si dispone di un'immagine, è possibile creare una o più nuove VM dall'immagine. Questo esempio crea una macchina virtuale denominata *myVM* dall'immagine *myImage* in *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Passaggi successivi

Accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

