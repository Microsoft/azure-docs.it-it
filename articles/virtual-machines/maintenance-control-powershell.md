---
title: Controllo di manutenzione per macchine virtuali di Azure con PowerShell
description: Informazioni su come controllare quando viene applicata la manutenzione alle VM di Azure usando il controllo di manutenzione e PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2868d559f0d848095fa7fec174e09e1b9376c4ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552457"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Controllare gli aggiornamenti con il controllo di manutenzione e Azure PowerShell

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti della piattaforma all'infrastruttura host delle VM isolate e degli host dedicati di Azure. In questo argomento vengono illustrate le opzioni di Azure PowerShell per il controllo di manutenzione. Per ulteriori informazioni sui vantaggi derivanti dall'utilizzo del controllo manutenzione, delle relative limitazioni e di altre opzioni di gestione, vedere [gestione degli aggiornamenti della piattaforma con il controllo di manutenzione](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>Abilitare il modulo PowerShell

Assicurarsi che `PowerShellGet` sia aggiornato.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Installare il `Az.Maintenance` modulo PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Se si sta installando localmente, assicurarsi di aprire il prompt di PowerShell come amministratore.

Potrebbe anche essere richiesto di confermare che si vuole eseguire l'installazione da un *repository non attendibile*. Digitare `Y` o selezionare **Sì per** installare il modulo.


## <a name="create-a-maintenance-configuration"></a>Creare una configurazione della manutenzione

Creare un gruppo di risorse come contenitore per la configurazione. In questo esempio viene creato un gruppo di risorse denominato *myMaintenanceRG* in *eastus*. Se si dispone già di un gruppo di risorse che si vuole usare, è possibile ignorare questa parte e sostituire il nome del gruppo di risorse con il resto degli esempi.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Usare [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) per creare una configurazione di manutenzione. In questo esempio viene creata una configurazione di manutenzione denominata *config* con ambito nell'host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

L'utilizzo di `-MaintenanceScope host` garantisce che la configurazione di manutenzione venga utilizzata per controllare gli aggiornamenti all'host.

Se si tenta di creare una configurazione con lo stesso nome, ma in un percorso diverso, si otterrà un errore. I nomi di configurazione devono essere univoci per il gruppo di risorse.

È possibile eseguire una query per le configurazioni di manutenzione disponibili usando [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Creare una configurazione di manutenzione con la finestra pianificata

È anche possibile dichiarare una finestra pianificata quando Azure applicherà gli aggiornamenti sulle risorse. Questo esempio crea una configurazione di manutenzione denominata config con una finestra pianificata di 5 ore il quarto lunedì di ogni mese. Una volta creata una finestra pianificata, non è più necessario applicare manualmente gli aggiornamenti.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> La **durata** della manutenzione deve essere maggiore di *2 ore* . È necessario impostare la **ricorrenza** di manutenzione almeno una volta in 35 giorni.

La **ricorrenza** di manutenzione può essere espressa come giornaliera, settimanale o mensile. Di seguito sono riportati alcuni esempi:
 - **Daily**-RecurEvery "Day" **o** "3days" 
 - **settimanale**-RecurEvery "3Weeks" **o** "settimana sabato, domenica" 
 - **Monthly**-RecurEvery "month day23, day24" **o** "month Last Sunday" **o** "month Fourth Monday"  
      

## <a name="assign-the-configuration"></a>Assegnare la configurazione

Usare [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) per assegnare la configurazione alla macchina virtuale isolata o all'host dedicato di Azure.

### <a name="isolated-vm"></a>VM isolata

Applicare la configurazione a una macchina virtuale usando l'ID della configurazione. Specificare `-ResourceType VirtualMachines` e fornire il nome della macchina virtuale per `-ResourceName` e il gruppo di risorse della VM per `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Host dedicato

Per applicare una configurazione a un host dedicato, è inoltre necessario includere `-ResourceType hosts` , `-ResourceParentName` con il nome del gruppo host, e `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Verificare la presenza di aggiornamenti in sospeso

Usare [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) per verificare se sono presenti aggiornamenti in sospeso. Usare `-subscription` per specificare la sottoscrizione di Azure della VM se è diversa da quella a cui si è connessi.

Se non sono presenti aggiornamenti da visualizzare, questo comando non restituisce alcun elemento. In caso contrario, verrà restituito un oggetto PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>VM isolata

Verificare la presenza di aggiornamenti in sospeso per una macchina virtuale isolata. In questo esempio l'output viene formattato come tabella per migliorare la leggibilità.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Host dedicato

Per verificare la presenza di aggiornamenti in sospeso per un host dedicato. In questo esempio l'output viene formattato come tabella per migliorare la leggibilità. Sostituire i valori per le risorse con quelli personalizzati.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Applicare gli aggiornamenti

Usare [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) per applicare gli aggiornamenti in sospeso.

### <a name="isolated-vm"></a>VM isolata

Creare una richiesta per applicare gli aggiornamenti a una macchina virtuale isolata.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Al termine dell'operazione, questo comando restituirà un `PSApplyUpdate` oggetto. È possibile usare l'attributo Name nel `Get-AzApplyUpdate` comando per verificare lo stato dell'aggiornamento. Vedere [controllare lo stato di aggiornamento](#check-update-status).

### <a name="dedicated-host"></a>Host dedicato

Applicare gli aggiornamenti a un host dedicato.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Controlla stato aggiornamento
Usare [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) per controllare lo stato di un aggiornamento. I comandi riportati di seguito mostrano lo stato dell'ultimo aggiornamento usando `default` per il `-ApplyUpdateName` parametro. Per ottenere lo stato di un aggiornamento specifico, è possibile sostituire il nome dell'aggiornamento (restituito dal comando [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) ).

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime sarà il momento in cui l'aggiornamento è stato completato, avviato dall'utente o dalla piattaforma nel caso in cui non sia stata utilizzata la finestra self-maintenance. Se non è mai stato applicato un aggiornamento tramite il controllo di manutenzione, verrà visualizzato il valore predefinito.

### <a name="isolated-vm"></a>VM isolata

Verificare la disponibilità di aggiornamenti per una macchina virtuale specifica.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Host dedicato

Verificare la disponibilità di aggiornamenti per un host dedicato.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Rimuovere una configurazione di manutenzione

Usare [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) per eliminare una configurazione di manutenzione.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [manutenzione e aggiornamenti](maintenance-and-updates.md).
