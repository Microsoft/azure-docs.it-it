---
title: Come distribuire Windows 10 in Azure con Multitenant Hosting Rights
description: Informazioni su come ottimizzare i vantaggi di Windows Software Assurance per portare licenze locali in Azure con diritti di hosting multi-tenant.
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 5631cbdd0b1eae343899be2147720d980e605dbb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452723"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Come distribuire Windows 10 in Azure con Multitenant Hosting Rights 
Per i clienti con Windows 10 Enterprise E3/E5 per utente o Windows Virtual Desktop Access per utente (licenze di sottoscrizione utente o licenze di sottoscrizione utente per i componenti aggiuntivi) Multitenant Hosting Rights per Windows 10 consente di trasferire le licenze di Windows 10 nel cloud ed eseguire macchine virtuali di Windows 10 in Azure senza sostenere i costi di un'altra licenza. Per altre informazioni vedere la pagina relativa all'[hosting multi-tenant per Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Questo articolo illustra come implementare il vantaggio della licenza per le immagini di Windows 10 Pro Desktop in Azure Marketplace.
> - Per le immagini di Windows 7, 8.1, 10 Enterprise (x64) in Azure Marketplace per le sottoscrizioni MSDN, fare riferimento a [Use Windows client in Azure for dev/test scenarios](client-images.md) (Usare il client Windows in Azure per scenari di sviluppo/test).
> - Per i vantaggi della licenza per Windows Server, fare riferimento a [Azure Hybrid use benefits for Windows Server images](hybrid-use-benefit-licensing.md) (Vantaggi Azure Hybrid Use per le immagini di Windows Server).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuzione dell'immagine di Windows 10 da Azure Marketplace 
Per le distribuzioni di PowerShell, dell'interfaccia della riga di comando e del modello di Azure Resource Manager, è possibile trovare l'immagine di Windows 10 con il seguente editorename, offerta, SKU.

| OS  |      PublisherName      |  Offerta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="qualify-for-multi-tenant-hosting-rights"></a>Qualifica per i diritti di hosting multi-tenant 
Per qualificarsi per i diritti di hosting multi-tenant e per eseguire immagini Windows 10 negli utenti di Azure, è necessario disporre di una delle seguenti sottoscrizioni: 

-   Microsoft 365 E3/E5/F3/a3/a5
-   Windows 10 Enterprise E3/E5 
-   Windows 10 Education a3/a5
-   Windows VDA E3/E5


## <a name="uploading-windows-10-vhd-to-azure"></a>Caricamento di un disco rigido virtuale di Windows 10 in Azure
Se si carica un disco rigido virtuale generalizzato di Windows 10, tenere presente che per impostazione predefinita in Windows 10 non è abilitato un account predefinito Administrator. Per abilitare l'account predefinito Administrator, includere il comando seguente come parte dell'estensione Script personalizzato.

```powershell
Net user <username> /active:yes
```

Il frammento di codice di PowerShell seguente consente di contrassegnare tutti gli account amministratore come attivi, incluso l'amministratore predefinito. Questo esempio è utile se il nome utente dell'account predefinito Administrator è sconosciuto.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Per altre informazioni: 
* [How to upload VHD to Azure](upload-generalized-managed.md) (Come caricare un disco rigido virtuale in Azure)
* [How to prepare a Windows VHD to upload to Azure](prepare-for-upload-vhd-image.md) (Come preparare un disco rigido virtuale di Windows per il caricamento in Azure)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuzione di Windows 10 con Multitenant Hosting Rights
Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azure/). Dopo aver preparato il disco rigido virtuale, caricarlo nell'account di archiviazione di Azure usando il cmdlet `Add-AzVhd` come segue:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuzione con Distribuzione di modelli di Azure Resource Manager** All'interno dei modelli di Resource Manager è possibile specificare un parametro aggiuntivo per `licenseType`. Altre informazioni sulla creazione di [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Dopo aver caricato il disco rigido virtuale in Azure, modificare il modello di Resource Manager per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello come di consueto:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuzione con PowerShell** Quando si distribuisce la macchina virtuale Windows Server con PowerShell è presente un parametro aggiuntivo per `-LicenseType`. Dopo aver caricato il disco rigido virtuale in Azure, creare una nuova VM usando `New-AzVM` e specificare il tipo di licenza come segue:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale usi il vantaggio della licenza
Dopo avere distribuito la VM con il metodo di distribuzione tramite PowerShell o Resource Manager, verificare il tipo di licenza con `Get-AzVM` come indicato di seguito:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

L'output è simile all'esempio seguente per Windows 10 con il tipo di licenza corretto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

L'output differisce da quello della seguente VM distribuita senza vantaggio Azure Hybrid Use, quale ad esempio una VM distribuita direttamente dalla raccolta di Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Altre informazioni sull'aggiunta di Azure AD
>[!NOTE]
>Azure esegue il provisioning di tutte le macchine virtuali di Windows con l'account predefinito Administrator, che non può essere usato per l'aggiunta di AAD. Ad esempio, *Impostazioni > Account > Accedi all'azienda o all'istituto di istruzione > +Connetti* non funziona. È necessario creare e accedere come secondo account Administrator per aggiungere manualmente Azure AD. È anche possibile configurare Azure Active Directory con un pacchetto di provisioning. Per altre informazioni, usare il collegamento nella sezione *Passaggi successivi*.
>
>

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [configurazione di un disco rigido virtuale per Windows 10](/windows/deployment/vda-subscription-activation)
- Altre informazioni sull'[hosting multi-tenant per Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
