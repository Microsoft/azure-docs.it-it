---
title: Usare Azure Image Builder con una raccolta immagini per le macchine virtuali Windows (anteprima)Use Azure Image Builder with an image gallery for Windows VMs (preview)
description: Creare versioni dell'immagine della raccolta condivisa di Azure usando Azure Image Builder e Azure PowerShell.Create Azure Shared Gallery image versions using Azure Image Builder and Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869512"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Anteprima: Creare un'immagine Windows e distribuirla in una raccolta immagini condivise 

Questo articolo illustra come usare Azure Image Builder e Azure PowerShell per creare una versione dell'immagine in una [raccolta di immagini condivise,](shared-image-galleries.md)quindi distribuire l'immagine a livello globale. È anche possibile usare l'interfaccia della riga di comando di [Azure.](../linux/image-builder-gallery.md)

Utilizzeremo un modello .json per configurare l'immagine. Il file .json che stiamo usando è qui: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Il download e la modifica di una versione locale del modello verranno scaricati e modificare, pertanto questo articolo viene scritto usando la sessione di PowerShell locale.

Per distribuire l'immagine in una raccolta immagini condivise, il `distribute` modello utilizza [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) come valore per la sezione del modello.

Azure Image Builder esegue automaticamente sysprep per generalizzare l'immagine, si tratta di un comando sysprep generico, che è possibile [sostituire](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) se necessario. 

Tenere presente quante volte si layer personalizzazioni. È possibile eseguire il comando Sysprep fino a 8 volte su una singola immagine Windows. Dopo aver eseguito Sysprep 8 volte, è necessario ricreare l'immagine Windows. Per ulteriori informazioni, vedere Limiti relativi al numero di volte in [cui è possibile eseguire Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure Image Builder è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrare le funzioni
Per usare Azure Image Builder durante l'anteprima, è necessario registrare la nuova funzionalità.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Controllare lo stato della registrazione della funzionalità.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Attendere `RegistrationState` fino `Registered` a quando è prima di passare al passaggio successivo.

Controllare le registrazioni del provider. Assicurarsi che `Registered`ogni restituisce .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se non restituiscono `Registered`, utilizzare quanto segue per registrare i provider:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Creare le variabili

Utilizzeremo alcune informazioni ripetutamente, quindi creeremo alcune variabili per memorizzare tali informazioni. Sostituire i valori per `username` le `vmpassword`variabili, like e , con le proprie informazioni.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Creare un gruppo di risorse e concedere a Azure Image Builder l'autorizzazione per creare risorse in tale gruppo di risorse.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Creare la Raccolta immagini condivise

Per utilizzare Creazione immagini con una raccolta di immagini condivisa, è necessario disporre di una raccolta di immagini e di una definizione di immagine esistenti. Image Builder non creerà automaticamente la raccolta di immagini e la definizione dell'immagine.

Se non si dispone già di una raccolta e di una definizione di immagine da utilizzare, iniziare creandole. Creare innanzitutto una raccolta immagini.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Scaricare e configurare il modello

Scaricare il modello .json e configurarlo con le variabili.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Creare la versione dell'immagine

Il modello deve essere inviato al servizio, verranno scaricati tutti gli elementi dipendenti, ad esempio gli script, e nel gruppo di risorse di staging, preceduto da *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Per compilare l'immagine è necessario richiamare 'Run' sul modello.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

La creazione dell'immagine e la replica in entrambe le aree possono richiedere un po' di tempo. Attendere il completamento di questa parte prima di passare alla creazione di una macchina virtuale.

Per informazioni sulle opzioni per automatizzare l'ottenimento dello stato di compilazione dell'immagine, vedere il [file Leggimi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) per questo modello in GitHub.For information on options for automating getting the image build status, see the Readme for this template on GitHub.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione dell'immagine creata da Azure Image Builder.Create a VM from the image version that was created by Azure Image Builder.

Ottenere la versione dell'immagine creata.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Creare la macchina virtuale nella seconda area che era l'immagine è stata replicata.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verificare la personalizzazione
Creare una connessione Desktop remoto alla macchina virtuale usando il nome utente e la password impostati al momento della creazione della macchina virtuale. All'interno della macchina virtuale aprire un prompt dei comandi e digitare:Inside the VM, open a cmd prompt and type:

```console
dir c:\
```

Verrà visualizzata una `buildActions` directory denominata creata durante la personalizzazione dell'immagine.


## <a name="clean-up-resources"></a>Pulire le risorse
Se si vuole ora provare a ripersonalizzare la versione dell'immagine per creare una nuova versione della stessa immagine, **ignorare questo passaggio** e passare a [Usare Azure Image Builder per creare un'altra versione dell'immagine.](image-builder-gallery-update-image-version.md)


In questo modo verrà eliminata l'immagine creata, insieme a tutti gli altri file di risorse. Assicurarsi di aver completato questa distribuzione prima di eliminare le risorse.

Eliminare prima il modello di gruppo di risorse, altrimenti il gruppo di risorse di gestione temporanea (*IT_*) utilizzato da AIB non verrà pulito.

Ottenere ResourceID del modello di immagine. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Elimina modello di immagine.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

eliminare il gruppo di risorse.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare la versione dell'immagine creata, vedere [Usare Azure Image Builder per creare un'altra versione dell'immagine.](image-builder-gallery-update-image-version.md)
