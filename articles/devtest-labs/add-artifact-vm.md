---
title: Aggiungere un elemento a una macchina virtuale in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere un elemento a una macchina virtuale in un Lab in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483891"
---
# <a name="add-an-artifact-to-a-vm"></a>Aggiungere un artefatto a una VM
Durante la creazione di una macchina virtuale, è possibile aggiungervi elementi esistenti. Questi elementi possono provenire dal [repository git DevTest Labs pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) o dal repository git. Questo articolo illustra come aggiungere elementi nel portale di Azure e usando Azure PowerShell. 

Gli *elementi* di Azure DevTest Labs consentono di specificare le *azioni* eseguite quando viene eseguito il provisioning della macchina virtuale, ad esempio, l'esecuzione di script Windows PowerShell, comandi Bash e installazione del software. Gli elementi *parametri* consentono di personalizzare l'elemento per un determinato scenario.

Per informazioni su come creare elementi personalizzati, vedere l'articolo [creare artefatti personalizzati](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Usare il portale di Azure 
1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di lab selezionare il lab contenente la VM che si vuole usare.  
1. Selezionare **Macchine virtuali**.
1. Selezionare la VM desiderata.
1. Selezionare **Gestisci gli artefatti**. 
1. Selezionare **Apply artifacts** (Applica elementi).
1. In **Applica artefatti** selezionare l'artefatto da aggiungere alla macchina virtuale.
1. Nel riquadro **Aggiungi artefatto** immettere i valori dei parametri obbligatori e gli eventuali parametri facoltativi necessari.  
1. Selezionare **Aggiungi** per aggiungere l'artefatto e tornare al riquadro **Applica artefatti**.
1. Continuare ad aggiungere gli elementi necessari per la macchina virtuale.
1. Dopo aver aggiunto gli elementi, è possibile [modificare l'ordine in cui vengono eseguiti](#change-the-order-in-which-artifacts-are-run). È anche possibile tornare indietro per [visualizzare o modificare un elemento](#view-or-modify-an-artifact).
1. Una volta aggiunti tutti gli elementi, selezionare **Applica**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Modificare l'ordine di esecuzione degli elementi
Per impostazione predefinita, le azioni degli elementi vengono eseguite nell'ordine in cui vengono aggiunte alla macchina virtuale. La procedura seguente descrive come modificare l'ordine in cui vengono eseguiti gli elementi.

1. Nella parte superiore del riquadro **Applica artefatti** selezionare il collegamento che indica il numero di artefatti aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel riquadro **Artefatti selezionati** trascinare e rilasciare gli artefatti nell'ordine desiderato. se si verificano problemi di trascinamento dell'artefatto, assicurarsi di trascinare dal lato sinistro dell'artefatto. 
1. Selezionare **OK** al termine dell'operazione.  

### <a name="view-or-modify-an-artifact"></a>visualizzare o modificare un elemento
I passaggi seguenti illustrano come visualizzare o modificare i parametri di un elemento:

1. Nella parte superiore del riquadro **Applica artefatti** selezionare il collegamento che indica il numero di artefatti aggiunti alla macchina virtuale.
   
    ![Numero di elementi aggiunti alla macchina virtuale](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Nel riquadro **Artefatti selezionati** selezionare l'artefatto che si vuole visualizzare o modificare.  
1. Nel riquadro **Aggiungi artefatto** apportare le modifiche necessarie e selezionare **OK** per chiudere il riquadro **Aggiungi artefatto**.
1. Selezionare **OK** per chiudere il riquadro **Artefatti selezionati**.

## <a name="use-powershell"></a>Usare PowerShell
Lo script seguente applica l'elemento specificato alla macchina virtuale specificata. Il comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) è quello che esegue l'operazione.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sugli artefatti:

- [Specificare gli artefatti obbligatori per il Lab](devtest-lab-mandatory-artifacts.md)
- [Creare elementi personalizzati](devtest-lab-artifact-author.md)
- [Aggiungere un repository di artefatti a un lab](devtest-lab-artifact-author.md)
- [Diagnosticare errori degli elementi](devtest-lab-troubleshoot-artifact-failure.md)
