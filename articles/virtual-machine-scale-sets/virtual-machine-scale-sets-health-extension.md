---
title: Usare l'estensione integrità dell'applicazione con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come usare l'estensione Integrità applicazione per monitorare l'integrità delle applicazioni distribuite nei set di scalabilità di macchine virtuali.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: a38a715b45ab4d0810862ef4d016e4187ea507ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84783045"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Uso dell'estensione Integrità applicazione con i set di scalabilità di macchine virtuali di Azure
Il monitoraggio dell'integrità delle applicazioni è un elemento importante per la gestione e l'aggiornamento della distribuzione. I set di scalabilità di macchine virtuali di Azure supportano gli [aggiornamenti in sequenza](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model), inclusi gli [aggiornamenti automatici dell'immagine del sistema operativo](virtual-machine-scale-sets-automatic-upgrade.md), che si basano sul monitoraggio dell'integrità delle singole istanze per l'aggiornamento della distribuzione. È anche possibile usare l'estensione per l'integrità per monitorare lo stato di integrità dell'applicazione di ogni istanza nel set di scalabilità ed eseguire le riparazioni delle istanze usando le [riparazioni automatiche dell'istanza](virtual-machine-scale-sets-automatic-instance-repairs.md)

In questo articolo viene descritto come usare l'estensione Integrità applicazione per monitorare l'integrità delle applicazioni distribuite nei set di scalabilità di macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia familiarità con:
-   [Estensioni](../virtual-machines/extensions/overview.md) della macchina virtuale di Azure
-   [Modifica](virtual-machine-scale-sets-upgrade-scale-set.md) dei set di scalabilità di macchine virtuali

## <a name="when-to-use-the-application-health-extension"></a>Quando usare l'estensione Integrità applicazione
L'estensione Integrità applicazione viene distribuita in un'istanza di un set di scalabilità di macchine virtuali e segnala l'integrità della macchina virtuale dall'interno dell'istanza di set di scalabilità. È possibile configurare l'estensione per eseguire il probe su un endpoint dell'applicazione e aggiornare lo stato dell'applicazione in tale istanza. Questo stato dell'istanza viene verificato da Azure per determinare se un'istanza è idonea per le operazioni di aggiornamento.

Poiché l'estensione segnala l'integrità dall'interno di una macchina virtuale, può essere usata nelle situazioni in cui non è possibile usare probe esterni, ad esempio i probe di Integrità applicazione (che usano [probe](../load-balancer/load-balancer-custom-probe-overview.md) personalizzati di Azure Load Balancer).

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON riportato di seguito mostra lo schema dell'estensione Integrità applicazione. L'estensione richiede almeno una richiesta "TCP", "http" o "https" con una porta o un percorso di richiesta associato rispettivamente.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | Data |
| publisher | `Microsoft.ManagedServices` | string |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | INT |

### <a name="settings"></a>Impostazioni

| Nome | Valore/Esempio | Tipo di dati
| ---- | ---- | ----
| protocol | `http` o `https` o `tcp` | string |
| port | Facoltativo quando il protocollo è `http` o `https` , obbligatorio quando il protocollo è `tcp` | INT |
| requestPath | Obbligatoria quando il protocollo è `http` o `https` , non è consentito quando il protocollo è `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Distribuire l'estensione Integrità applicazione
Esistono diversi modi per distribuire l'estensione Integrità applicazione nei set di scalabilità, come descritto in dettaglio negli esempi seguenti.

### <a name="rest-api"></a>API REST

L'esempio seguente aggiunge l'estensione Integrità applicazione (denominata myHealthExtension) a extensionProfile nel modello di set di scalabilità di un set di scalabilità basato su Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Usare `PATCH` per modificare un'estensione già distribuita.

### <a name="azure-powershell"></a>Azure PowerShell

Usare il cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) per aggiungere l'estensione Integrità applicazione alla definizione del modello del set di scalabilità.

L'esempio seguente aggiunge l'estensione Integrità applicazione a `extensionProfile` nel modello di set di scalabilità di un set di scalabilità basato su Windows. L'esempio usa il nuovo modulo di Azure PowerShell.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) per aggiungere l'estensione Integrità applicazione alla definizione del modello del set di scalabilità.

Nell'esempio seguente viene aggiunta l'estensione dell'integrità dell'applicazione al modello del set di scalabilità di un set di scalabilità basato su Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
extension.jssul contenuto del file.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Risolvere problemi
L'output dell'esecuzione dell'estensione viene registrato nei file presenti nelle directory seguenti:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

I log inoltre acquisiscono periodicamente lo stato di integrità dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
