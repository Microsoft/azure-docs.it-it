---
title: Usare la sequenziazione delle estensioni con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come eseguire la sequenziazione del provisioning delle estensioni durante la distribuzione di più estensioni in set di scalabilità di macchine virtuali.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83121032"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Eseguire la sequenziazione del provisioning delle estensioni in set scalabilità di macchine virtuali
Le estensioni di macchine virtuali di Azure offrono funzionalità come la configurazione e la gestione post-distribuzione, il monitoraggio, la sicurezza e altro ancora. In genere, le distribuzioni di produzione usano una combinazione di più estensioni configurate per le istanze di macchine virtuali per ottenere risultati desiderati.

Quando si usano più estensioni in una macchina virtuale, è importante assicurarsi che le estensioni che richiedono le stesse risorse del sistema operativo non tentino di acquisire queste risorse nello stesso momento. Alcune estensioni, inoltre, dipendono da altre estensioni per fornire le configurazioni richieste, come le impostazioni di ambiente e i segreti. Senza l'ordinamento e la sequenza corretti, le distribuzioni di estensioni dipendenti possono avere esito negativo.

Questo articolo illustra in dettaglio come è possibile eseguire la sequenziazione delle estensioni da configurare per le istanze di macchine virtuali in set di scalabilità di macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia familiarità con gli argomento seguenti:
-   [Estensioni](../virtual-machines/extensions/overview.md) della macchina virtuale di Azure
-   [Modifica](virtual-machine-scale-sets-upgrade-scale-set.md) dei set di scalabilità di macchine virtuali

## <a name="when-to-use-extension-sequencing"></a>Quando usare la sequenziazione delle estensioni
La sequenziazione delle estensioni non è obbligatoria per i set di scalabilità e, se non diversamente specificato, è possono eseguire il provisioning delle estensioni in un'istanza del set di scalabilità in qualsiasi ordine.

Ad esempio, se nel modello del set di scalabilità sono specificate le due estensioni EstensioneA ed EstensioneB, può essere usata una delle sequenze di provisioning seguenti:
-   EstensioneA -> EstensioneB
-   EstensioneB -> EstensioneA

Se l'applicazione richiede che il provisioning dell'estensione A venga sempre eseguito prima dell'estensione B, è consigliabile usare la sequenziazione delle estensioni come descritto in questo articolo. Con la sequenziazione delle estensioni verrà usata una sola sequenza:
-   EstensioneA - > EstensioneB

Qualsiasi estensione non specificata in una sequenza di provisioning definita può essere sottoposta a provisioning in qualsiasi momento, sia prima, che dopo o durante una sequenza definita. La sequenziazione delle estensioni specifica solo che il provisioning di un'estensione specifica verrà eseguito dopo un'altra estensione specifica e non ha effetti sul provisioning di qualsiasi altra estensione definita nel modello.

Ad esempio, se nel modello del set di scalabilità sono specificate tre estensioni (EstensioneA, EstensioneB ed EstensioneC) e il provisioning dell'estensione C è impostato per essere eseguito dopo quello dell'estensione A, può essere usata una delle sequenze di provisioning seguenti:
-   EstensioneA -> EstensioneC -> EstensioneB
-   EstensioneB -> EstensioneA -> EstensioneC
-   EstensioneA -> EstensioneB -> EstensioneC

Se è necessario assicurarsi che non venga eseguito il provisioning di altre estensioni durante l'esecuzione della sequenza definita, è consigliabile impostare la sequenziazione di tutte le estensioni nel modello del set di scalabilità. Nell'esempio precedente è possibile impostare il provisioning dell'estensione B dopo quello dell'estensione C in modo che possa essere usata solo una sequenza:
-   EstensioneA -> EstensioneC -> EstensioneB


## <a name="how-to-use-extension-sequencing"></a>Come usare la sequenziazione delle estensioni
Per la sequenziazione del provisioning, è necessario aggiornare la definizione delle estensioni nel modello del set di scalabilità per includere la proprietà "provisionAfterExtensions", che accetta una matrice di nomi di estensione. Le estensioni indicate nel valore della matrice della proprietà devono essere definite in modo completo nel modello del set di scalabilità.

### <a name="template-deployment"></a>Distribuzione di modelli
L'esempio seguente definisce un modello in cui il set di scalabilità ha tre estensioni (EstensioneA, EstensioneB ed EstensioneC) e il provisioning delle estensioni viene eseguito nell'ordine:
-   EstensioneA -> EstensioneB -> EstensioneC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Dato che la proprietà "provisionAfterExtensions" accetta una matrice di nomi di estensione, l'esempio precedente può essere modificato in modo che il provisioning di EstensioneC venga eseguito dopo EstensioneA ed EstensioneB, ma senza richiedere l'ordinamento per EstensioneA ed EstensioneB. Il modello seguente può essere usato per realizzare questo scenario:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>API REST
L'esempio seguente aggiunge una nuova estensione denominata EstensioneC a un modello del set di scalabilità. L'estensione C ha dipendenze dalle estensioni A e B, già definite nel modello del set di scalabilità.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Se l'estensione C è stata definita in precedenza nel modello del set di scalabilità e ora si vogliono aggiungere le relative dipendenze, è possibile eseguire un'operazione `PATCH` per modificare le proprietà dell'estensione già distribuita.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Le modifiche alle istanze del set di scalabilità esistenti vengono applicate in occasione dell'[aggiornamento](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) successivo.

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) per aggiungere l'estensione Integrità applicazione alla definizione del modello del set di scalabilità. Per la sequenziazione delle estensioni è richiesto l'uso di Az PowerShell 1.2.0 o versione successiva.

L'esempio seguente aggiunge l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md) a `extensionProfile` in un modello di un set di scalabilità basato su Windows. Il provisioning dell'estensione Integrità applicazione verrà eseguito dopo il provisioning dell'[estensione Script personalizzato](../virtual-machines/extensions/custom-script-windows.md), già definita nel set di scalabilità.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) per aggiungere l'estensione Integrità applicazione alla definizione del modello del set di scalabilità. Per la sequenziazione delle estensioni è richiesto l'uso dell'interfaccia della riga di comando di Azure versione 2.0.55 o successiva.

L'esempio seguente aggiunge l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md)al modello di un set di scalabilità basato su Windows. Il provisioning dell'estensione Integrità applicazione verrà eseguito dopo il provisioning dell'[estensione Script personalizzato](../virtual-machines/extensions/custom-script-windows.md), già definita nel set di scalabilità.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Risolvere problemi

### <a name="not-able-to-add-extension-with-dependencies"></a>Non è possibile aggiungere un'estensione con dipendenze?
1. Verificare che le estensioni specificate in provisionAfterExtensions siano definite nel modello del set di scalabilità.
2. Assicurarsi che non siano state introdotte dipendenze circolari. La sequenza seguente, ad esempio, non è consentita: Extensiona-> ExtensionB-> ExtensionC-> Extensiona
3. Assicurarsi che tutte le estensioni con dipendenze abbiano una proprietà "settings" in "properties". Ad esempio, se è necessario eseguire il provisioning di EstensioneB dopo EstensioneA, per EstensioneA deve essere disponibile il campo "settings" in "properties" di EstensioneA. È possibile specificare una proprietà "settings" vuota se l'estensione non richiede impostazioni obbligatorie.

### <a name="not-able-to-remove-extensions"></a>Non è possibile rimuovere le estensioni?
Assicurarsi che le estensioni da rimuovere non siano elencate in provisionAfterExtensions per altre estensioni.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
