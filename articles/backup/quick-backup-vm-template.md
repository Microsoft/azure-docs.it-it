---
title: 'Avvio rapido: Eseguire backup delle macchine virtuali con il modello di Azure Resource Manager'
description: Informazioni su come eseguire il backup delle macchine virtuali con un modello di Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458846"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Eseguire il backup di una macchina virtuale in Azure usando un modello di Azure Resource Manager

[Backup di Azure](backup-overview.md) esegue il backup di computer e app locali, oltre che delle VM di Azure. Questo articolo descrive come eseguire il backup di una macchina virtuale di Azure con un modello di Azure Resource Manager e Azure PowerShell. Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Resource Manager per creare un insieme di credenziali di Servizi di ripristino. Per altre informazioni sullo sviluppo di modelli di Resource Manager, vedere la [documentazione di Resource Manager](/azure/azure-resource-manager/) e le [informazioni di riferimento sui modelli](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In alternativa, è possibile eseguire il backup di una VM con [Azure PowerShell](./quick-backup-vm-powershell.md), l'[interfaccia della riga di comando di Azure](quick-backup-vm-cli.md) oppure nel [portale di Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Creare una macchina virtuale e un insieme di credenziali di Servizi di ripristino

Un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md) è un contenitore logico in cui vengono archiviati i dati di backup per le risorse protette, ad esempio le VM di Azure. Quando viene eseguito, il processo di backup crea un punto di ripristino all'interno dell'insieme di credenziali di Servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Questo modello consente di distribuire una semplice macchina virtuale Windows e un insieme di credenziali di Servizi di ripristino configurato con i criteri di protezione predefiniti.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

Nel modello sono definite le risorse seguenti:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, selezionare **Prova** per aprire Azure Cloud Shell e quindi incollare lo script PowerShell seguente nella finestra della shell. Per incollare il codice, fare clic con il pulsante destro del mouse nella finestra della shell e quindi selezionare **Incolla**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

In questo argomento di avvio rapido, per la distribuzione del modello di Resource Manager viene usato Azure PowerShell. Per distribuire i modelli è anche possibile usare il [portale di Azure](../azure-resource-manager/templates/deploy-portal.md), l'[interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md) e l'[API Rest](../azure-resource-manager/templates/deploy-rest.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

### <a name="start-a-backup-job"></a>Avviare un processo di backup

Il modello crea una macchina virtuale e abilita il backup nella macchina virtuale. Dopo aver distribuito il modello, è necessario avviare un processo di backup. Per altre informazioni, vedere [Avviare un processo di backup](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorare il processo di backup

Per monitorare il processo di backup, vedere [Monitorare il processo di backup](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Pulire la distribuzione

Se non è più necessario eseguire il backup della VM, è possibile eseguire la pulizia.

- Se si vuole provare a ripristinare la macchina virtuale, ignorare la pulizia.
- Se è stata usata una VM esistente, si può ignorare il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) finale e mantenere così il gruppo di risorse e la VM.

Disabilitare la protezione, rimuovere i punti di ripristino e l'insieme di credenziali. Quindi, eliminare il gruppo di risorse e le risorse VM associate, come segue:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un insieme di credenziali di Servizi di ripristino, è stata abilitata la protezione per una VM ed è stato creato il punto di ripristino iniziale.

- [Informazioni su come](tutorial-backup-vm-at-scale.md) eseguire il backup delle VM nel portale di Azure.
- [Informazioni su come](tutorial-restore-disk.md) ripristinare rapidamente una VM
- [Informazioni su come](../azure-resource-manager/templates/template-tutorial-create-first-template.md) creare modelli di Resource Manager.
