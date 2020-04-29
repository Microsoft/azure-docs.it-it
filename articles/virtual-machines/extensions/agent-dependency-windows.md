---
title: Estensione macchina virtuale dipendenza monitoraggio di Azure per Windows
description: Distribuire l'agente di dipendenza di monitoraggio di Azure nella macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250673"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Estensione macchina virtuale dipendenza monitoraggio di Azure per Windows

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione macchina virtuale dell'agente di dipendenza VM di Azure per Windows è pubblicata e supportata da Microsoft. L'estensione installa Dependency Agent nelle macchine virtuali di Azure. Questo documento descrive in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale dell'agente di dipendenza VM di Azure per Windows.

## <a name="operating-system"></a>Sistema operativo

L'estensione Azure VM Dependency Agent per Windows può essere eseguita nei sistemi operativi supportati elencati nella sezione [sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dell'articolo sulla distribuzione di monitoraggio di Azure per le macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema dell'estensione dell'agente di dipendenza delle macchine virtuali di Azure in una macchina virtuale Windows di Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valori delle proprietà

| Name | Valore/esempio |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft. Azure. Monitoring. DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Distribuzione del modello

È possibile distribuire le estensioni di VM di Azure con i modelli Azure Resource Manager. È possibile usare lo schema JSON descritto in dettaglio nella sezione precedente di un modello di Azure Resource Manager per eseguire l'estensione dell'agente di dipendenza delle macchine virtuali di Azure durante una distribuzione del modello di Azure Resource Manager.

Il codice JSON per un'estensione della macchina virtuale può essere annidato all'interno della risorsa della macchina virtuale. In alternativa, è possibile posizionarlo alla radice o al livello superiore di un modello di Gestione risorse JSON. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere [impostare il nome e il tipo per le risorse figlio](../../azure-resource-manager/templates/child-resource-name-type.md).

Nell'esempio seguente si presuppone che l'estensione dell'agente di dipendenza sia annidata all'interno della risorsa della macchina virtuale. Quando si nidifica la risorsa di estensione, il codice JSON viene inserito `"resources": []` nell'oggetto della macchina virtuale.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando si inserisce l'estensione JSON alla radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre. Il tipo riflette la configurazione nidificata.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Distribuzione PowerShell

È possibile utilizzare il `Set-AzVMExtension` comando per distribuire l'estensione macchina virtuale di Dependency Agent a una macchina virtuale esistente. Prima di eseguire il comando, è necessario archiviare le configurazioni pubbliche e private in una tabella hash di PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure e usando il modulo di Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando il modulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, è possibile contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni su come usare il supporto di Azure, leggere le [domande frequenti sul supporto Microsoft Azure](https://azure.microsoft.com/support/faq/).
