---
title: Uso della configurazione dello stato desiderato con i set di scalabilità di macchine virtuali
description: Usare i set di scalabilità di macchine virtuali con l'estensione di configurazione dello stato desiderato di Azure per configurare le macchine virtuali.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278080"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure
I [set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md) possono essere usati con il gestore dell'estensione [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Configurazione dello stato desiderato (DSC) di Azure). Il set di scalabilità di macchine virtuali consente di distribuire e gestire un numero elevato di macchine virtuali e di aumentare o ridurre la dimensione in risposta al carico. DSC viene usato per la configurazione delle VM mano a mano che sono in linea, in modo che eseguano il software di produzione.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Differenze tra la distribuzione di macchine virtuali e i set di scalabilità di macchine virtuali
La struttura del modello sottostante di un set di scalabilità di macchine virtuali è leggermente diversa da quella di una singola VM. In particolare, una singola VM distribuisce le estensioni nel nodo "virtualMachines". È presente una voce del tipo "extensions" nella quale DSC viene aggiunto al modello:

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Il nodo di un set di scalabilità di macchine virtuali ha una sezione "properties" con gli attributi "VirtualMachineProfile", "extensionProfile". DSC viene aggiunto sotto "extensions":

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamento di un set di scalabilità di macchine virtuali
Il comportamento di un set di scalabilità di macchine virtuali è identico a quello di una singola macchina virtuale. Quando viene creata una nuova VM, il provisioning viene automaticamente eseguito con l'estensione DSC. Se l'estensione richiede una versione più recente del file WMF, la VM verrà riavviata prima di passare online. Una volta online, il file con estensione zip di configurazione DSC viene scaricato e ne viene eseguito il provisioning nella VM. Altre informazioni sono reperibili nella [panoramica sull'estensione DSC di Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
Esaminare il [modello di Azure Resource Manager per l'estensione DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Informazioni su come l' [estensione DSC gestisce in modo sicuro le credenziali](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per altre informazioni sul gestore dell'estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](/powershell/scripting/dsc/overview/overview). 

