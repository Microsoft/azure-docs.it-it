---
title: Creare un'immagine gestita in AzureCreate a managed image in Azure
description: Creare un'immagine gestita di un disco rigido virtuale o una macchina virtuale generalizzati in Azure. È possibile usare le immagini per creare più macchine virtuali che usino i dischi gestiti.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084465"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Creare un'immagine gestita di una macchina virtuale generalizzata in Azure

È possibile creare una risorsa di tipo immagine gestita da una macchina virtuale (VM) generalizzata che è stata archiviata come disco gestito o come disco non gestito in un account di archiviazione. L'immagine è quindi utilizzabile per creare più macchine virtuali. Per informazioni sulla fatturazione delle immagini gestite, vedere [Prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove tutte le informazioni sull'account personale e sulla sicurezza e quindi prepara la macchina da usare come immagine. Per informazioni su Sysprep, vedere [Sysprep overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Panoramica di Sysprep).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere Supporto di [Sysprep per i ruoli del server](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e Scenari non [supportati](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Dopo aver eseguito Sysprep in una VM, quest'ultima viene considerata *generalizzata* e non può essere riavviata. Il processo di generalizzazione di una macchina virtuale è irreversibile. Per mantenere in funzionamento la VM originale, è consigliabile creare una [copia della VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizzarla. 
>
> Se si prevede di eseguire Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Per generalizzare la VM Windows, seguire questa procedura:

1. Accedere alla VM Windows.
   
2. Aprire una finestra del Prompt dei comandi come amministratore. Impostare la directory su %windir%\system32\sysprep e quindi eseguire `sysprep.exe`.
   
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e quindi selezionare la casella di controllo **Generalizza**.
   
4. In **Opzioni di arresto** selezionare **Arresta il sistema**.
   
5. Selezionare **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Al termine, Sysprep arresta la VM. Non riavviare la VM.

> [!TIP]
> **Facoltativo** Usa [Gestione e](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) distribuzione per ottimizzare l'immagine e ridurre il primo avvio della macchina virtuale.
>
> Per ottimizzare l'immagine, montare il disco rigido virtuale facendo doppio clic `/optimize-image` su di esso in Esplora risorse, quindi eseguire Gestione e distribuzione immagini distribuzione con il parametro.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Dove D: è il percorso del disco rigido virtuale montato.
>
> L'esecuzione `DISM /optimize-image` dovrebbe essere l'ultima modifica apportata al disco rigido virtuale. Se si apportano modifiche al disco rigido virtuale prima `DISM /optimize-image` della distribuzione, sarà necessario eseguire di nuovo.

## <a name="create-a-managed-image-in-the-portal"></a>Creare un'immagine gestita nel portale 

1. Passare al portale di [Azure](https://portal.azure.com) per gestire l'immagine della macchina virtuale. Cercare e selezionare **Macchine virtuali**.

2. Selezionare la macchina virtuale dall'elenco.

3. Nella pagina **Macchina virtuale** della VM, nel menu superiore, selezionare **Acquisisci**.

   Viene visualizzata la pagina **Crea immagine**.

4. Per il campo **Nome** accettare il nome già popolato oppure immettere un nome che si vuole usare per l'immagine.

5. Per **Gruppo**di risorse selezionare **Crea nuovo** e immettere un nome oppure selezionare un gruppo di risorse da usare dall'elenco a discesa.

6. Per eliminare la VM di origine dopo che l'immagine è stata creata, selezionare **Elimina automaticamente questa macchina virtuale dopo aver creato l'immagine**.

7. Per poter usare l'immagine in qualsiasi [zona di disponibilità](../../availability-zones/az-overview.md), selezionare **Sì** per **Resilienza della zona**.

8. Selezionare **Crea** per creare l'immagine.

Dopo averla creata, l'immagine sarà disponibile come risorsa di tipo **Immagine** nell'elenco delle risorse del gruppo.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Creare un'immagine di una macchina virtuale tramite PowerShell

 

La creazione di un'immagine direttamente nella VM garantisce che l'immagine includa tutti i dischi associati alla VM, compresi il disco del sistema operativo e gli eventuali dischi dati. Questo esempio illustra come creare un'immagine gestita da una VM che usa dischi gestiti.

Prima di iniziare, assicurarsi di disporre della versione più recente del modulo di Azure PowerShell. Per trovare la versione, eseguire `Get-Module -ListAvailable Az` in PowerShell. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell on Windows with PowerShellGet](/powershell/azure/install-az-ps) (Installare Azure PowerShell in Windows con PowerShellGet). Se si esegue PowerShell in locale, eseguire `Connect-AzAccount` per creare una connessione con Azure.


> [!NOTE]
> Se si vuole archiviare l'immagine in una risorsa di archiviazione con ridondanza della zona, è necessario crearla in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-ZoneResilient` nella configurazione dell'immagine (comando `New-AzImageConfig`).

Per creare un'immagine della VM, seguire questa procedura:

1. Creare alcune variabili.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assicurarsi che la macchina virtuale sia stata deallocata.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Trovare la macchina virtuale. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Creare un'immagine da un disco gestito usando PowerShell

Se si vuole creare un'immagine del solo disco del sistema operativo, specificare l'ID del disco gestito come disco del sistema operativo:

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Ottenere la VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Ottenere l'ID del disco gestito.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Creare un'immagine da uno snapshot usando PowerShell

Per creare un'immagine gestita da uno snapshot di una VM generalizzata, seguire questa procedura:

    
1. Creare alcune variabili. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Trovare lo snapshot.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Creare la configurazione dell'immagine.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Creare l'immagine.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Creare un'immagine da una macchina virtuale che usa un account di archiviazioneCreate an image from a VM that uses a storage account

Per creare un'immagine gestita da una macchina virtuale che non usa dischi gestiti, è necessario l'URI del disco rigido virtuale del sistema operativo nell'account di archiviazione nel formato seguente: https://*mystorageaccount*.blob.core.windows.net//*vhdcontainer vhdfilename.vhd*.*vhdcontainer* In questo esempio il disco rigido virtuale si trova in *mystorageaccount* in un contenitore denominato *vhdcontainer* e il nome file del disco rigido virtuale è *vhdfilename.vhd*.


1.  Creare alcune variabili.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Arrestare o deallocare la VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Contrassegnare la macchina virtuale come generalizzata.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Creare un'immagine tramite il disco rigido virtuale del sistema operativo generalizzato.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passaggi successivi
- [Creare una macchina virtuale da un'immagine gestita.](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

