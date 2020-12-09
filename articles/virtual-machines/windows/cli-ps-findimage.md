---
title: Trova e usa immagini e piani di Azure Marketplace
description: Usare Azure PowerShell per trovare e usare le informazioni su server di pubblicazione, offerta, SKU, versione e piano per le immagini di macchine virtuali del Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906268"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Trovare e usare immagini di VM di Azure Marketplace con Azure PowerShell     

Questo articolo descrive come usare Azure PowerShell per trovare immagini di VM in Microsoft Azure Marketplace. È quindi possibile specificare un'immagine del Marketplace e pianificare le informazioni quando si crea una macchina virtuale.

È anche possibile sfogliare le immagini e le offerte disponibili usando la vetrina di [Azure Marketplace](https://azuremarketplace.microsoft.com/), il [portale di Azure](https://portal.azure.com) o l'[interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Creare una macchina virtuale da un disco rigido virtuale con informazioni sul piano

Se si dispone di un disco rigido virtuale esistente creato con un'immagine di Azure Marketplace, potrebbe essere necessario fornire le informazioni sul piano di acquisto quando si crea una nuova macchina virtuale da tale disco rigido virtuale.

Se è ancora presente la macchina virtuale originale o un'altra macchina virtuale creata dalla stessa immagine, è possibile ottenere il nome del piano, il server di pubblicazione e le informazioni sul prodotto utilizzando Get-AzVM. Questo esempio Mostra come ottenere una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e quindi visualizzare le informazioni sul piano di acquisto.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Se non sono state rilevate informazioni sul piano prima dell'eliminazione della macchina virtuale originale, è possibile archiviare una [richiesta di supporto](https://ms.portal.azure.com/#create/Microsoft.Support). Saranno necessari il nome della macchina virtuale, l'ID sottoscrizione e il timestamp dell'operazione di eliminazione.

Per creare una VM usando un disco rigido virtuale, fare riferimento a questo articolo [creare una macchina virtuale da un disco rigido virtuale specializzato](create-vm-specialized.md) e aggiungere una riga per aggiungere le informazioni sul piano alla configurazione della macchina virtuale usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) simile al seguente:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Creare una nuova macchina virtuale da un'immagine del Marketplace

Se si dispone già delle informazioni sull'immagine che si vuole usare, è possibile passare le informazioni nel cmdlet [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) per aggiungere informazioni sull'immagine alla configurazione della macchina virtuale. Vedere le sezioni successive per la ricerca e l'elenco delle immagini disponibili nel Marketplace.

Per alcune immagini a pagamento è anche necessario fornire le informazioni sul piano di acquisto usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Si passerà quindi la configurazione della macchina virtuale insieme agli altri oggetti di configurazione al `New-AzVM` cmdlet. Per un esempio dettagliato dell'uso di una configurazione di macchina virtuale con PowerShell, vedere questo [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Se viene visualizzato un messaggio relativo all'accettazione delle condizioni dell'immagine, vedere la sezione [accettare i termini](#accept-the-terms) più avanti in questo articolo.

## <a name="list-images"></a>Elencare le immagini

Un altro modo per trovare un'immagine in una posizione consiste nell'eseguire in sequenza i cmdlet [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) e [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku):

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.

Quindi, per uno SKU selezionato, eseguire [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) per ottenere un elenco delle versioni da distribuire.

1. Elencare gli editori:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Specificare il nome dell'editore scelto ed elencare le offerte:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Specificare il nome dell'offerta scelta ed elencare gli SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Specificare il nome dello SKU scelto e ottenere la versione dell'immagine:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Dall'output del comando `Get-AzVMImage` è possibile selezionare un'immagine di versione per distribuire una nuova macchina virtuale.

L'esempio seguente illustra la sequenza completa di comandi e i relativi output:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Output parziale:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Per l'editore *MicrosoftWindowsServer*:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Output:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Per l'offerta *WindowsServer*:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Output parziale:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Per lo SKU *2019-Datacenter*, quindi:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Ora è possibile combinare l'editore, l'offerta, lo SKU e la versione selezionati in un URN (valori separati da :). Passare questo URN con il parametro `--image` quando si crea una macchina virtuale con il cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). È possibile sostituire il numero di versione nell'URN con "latest" per ottenere la versione più recente dell'immagine.

Se si distribuisce una macchina virtuale con un modello di Resource Manager, impostare i parametri dell'immagine singolarmente nelle proprietà `imageReference`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visualizzare le proprietà del piano

Per visualizzare informazioni sul piano di acquisto di un'immagine, eseguire il cmdlet `Get-AzVMImage`. Se la proprietà `PurchasePlan` nell'output non è `null`, l'immagine presenta condizioni che è necessario accettare prima della distribuzione a livello di codice.  

Ad esempio, l'immagine di *Windows Server 2016 Datacenter* non ha condizioni aggiuntive, quindi l'informazione relativa a `PurchasePlan` è `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Output:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Nell'esempio seguente viene illustrato un comando simile per l'immagine *Data Science Virtual Machine-Windows 2016* , che presenta le `PurchasePlan` proprietà seguenti: `name` , `product` e `publisher` . Alcune immagini hanno anche una proprietà `promotion code`. Per distribuire questa immagine, vedere le sezioni seguenti per accettare le condizioni e abilitare la distribuzione a livello di codice.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Output:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Accettare le condizioni

Per visualizzare le condizioni di licenza, usare il cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passare i parametri del piano di acquisto. L'output include un collegamento alle condizioni di licenza per l'immagine del Marketplace e mostra se le condizioni sono già state accettate in precedenza. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Output:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Usare il cmdlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) per accettare o rifiutare le condizioni. È sufficiente accettare le condizioni per l'immagine una sola volta per ogni sottoscrizione. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Output:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Passaggi successivi

Per creare rapidamente una macchina virtuale con il cmdlet `New-AzVM` usando le informazioni di base sull'immagine, vedere [Creare una macchina virtuale Windows con PowerShell](quick-create-powershell.md).

Per altre informazioni sull'uso delle immagini di Azure Marketplace per creare immagini personalizzate in una raccolta di immagini condivise, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](../marketplace-images.md).
