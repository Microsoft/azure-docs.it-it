---
title: Bilanciare il carico di più siti Web - Azure PowerShell - Azure Load Balancer
description: Questo esempio di script di Azure PowerShell illustra come eseguire il bilanciamento del carico di più siti Web verso la stessa macchina virtuale
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a260887b169c8feecd304996ea57d1aec46aedc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696558"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Esempio di script di Azure PowerShell - Eseguire il bilanciamento del carico di più siti Web

Questo esempio di script di Azure PowerShell crea una rete virtuale con due macchine virtuali che fanno parte di un set di disponibilità. Il bilanciamento del carico indirizza il traffico di due diversi indirizzi IP verso le due macchine virtuali. Dopo l'esecuzione dello script, è possibile distribuire il software del server Web alle macchine virtuali e ospitare più siti Web, ciascuno con il suo indirizzo IP.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Crea un set di disponibilità di Azure per fornire disponibilità elevata. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Crea una configurazione IP front-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Crea una configurazione del pool di indirizzi back-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Consente di creare un probe di bilanciamento del carico di rete. Il probe di bilanciamento del carico di rete viene usato per monitorare ogni macchina virtuale nel set di bilanciamento del carico di rete. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Consente di creare una regola di bilanciamento del carico di rete. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al bilanciamento del carico di rete, viene indirizzato sulla porta 80 di una delle macchine virtuali presenti nel set di bilanciamento del carico di rete. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Crea un servizio di bilanciamento del carico. |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Definisce le funzionalità avanzate per un'interfaccia di rete virtuale. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crea un'interfaccia di rete. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).