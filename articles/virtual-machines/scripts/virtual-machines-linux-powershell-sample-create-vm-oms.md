---
title: Esempio di script di Azure PowerShell per Linux - Log di Monitoraggio di Azure
description: Esempio di script di Azure PowerShell per Linux - Log di Monitoraggio di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 354f7fad2d7c0b4de273f9fe3420addb464472b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336072"
---
# <a name="create-an-azure-monitor-vm-with-powershell"></a>Creare una macchina virtuale di Monitoraggio di Azure con PowerShell

Questo script consente di creare una macchina virtuale di Azure, installare l'agente Log Analytics e registrare il sistema in un'area di lavoro Log Analytics. Dopo l'esecuzione dello script, la macchina virtuale sarà visibile nella console.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.ps1 "Create VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Crea una configurazione di regola del gruppo di sicurezza di rete. Questa configurazione viene usata per creare una regola NSG quando viene creato il gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene informazioni sulla subnet. Queste informazioni vengono usate durante la creazione di un'interfaccia di rete. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crea un'interfaccia di rete. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | Aggiungere un'estensione di VM alla macchina virtuale. In questo caso, l'estensione dell'agente Log Analytics viene usata per installare l'agente Log Analytics e registrare la VM in un'area di lavoro Log Analytics. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
