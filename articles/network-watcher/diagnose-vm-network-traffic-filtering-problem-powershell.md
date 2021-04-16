---
title: 'Guida introduttiva: Diagnosticare un problema di filtro del traffico di rete di una VM - Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Informazioni su come usare Azure PowerShell per diagnosticare un problema di filtro del traffico di rete di una macchina virtuale usando la funzionalità di verifica del flusso IP di Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
ms.author: damendo
editor: ''
ms.date: 01/07/2021
ms.assetid: ''
ms.topic: quickstart
ms.service: network-watcher
ms.workload: infrastructure
ms.tgt_pltfrm: network-watcher
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: fafe090d72d53b33ddb3e9863b7e62deba1055c5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535766"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Guida introduttiva: Diagnosticare un problema di filtro del traffico di rete di una macchina virtuale - Azure PowerShell

In questa guida introduttiva si distribuisce una macchina virtuale e quindi si controllano le comunicazioni verso un indirizzo IP e un URL e da un indirizzo IP. Viene determinata la causa di un errore di comunicazione e si apprende come è possibile risolverlo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa guida di avvio rapido è necessario il modulo `Az` di Azure PowerShell. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.



## <a name="create-a-vm"></a>Creare una macchina virtuale

Prima di poter creare una macchina virtuale, è necessario creare un gruppo di risorse per contenerla. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Creare la VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la VM.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

La creazione della VM richiede alcuni minuti. Non continuare a seguire i passaggi rimanenti fino a quando non viene creata la macchina virtuale e PowerShell non restituisce l'output.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Per testare la comunicazione di rete con Network Watcher è necessario innanzitutto abilitare un network watcher nell'area in cui si trova la macchina virtuale che si intende testare, quindi usare la funzionalità di verifica del flusso IP di Network Watcher per testare la comunicazione.

### <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si ha già un'istanza di Network Watcher abilitata nell'area Stati Uniti orientali, usare [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) per recuperare Network Watcher. L'esempio seguente recupera un'istanza di Network Watcher esistente denominata *NetworkWatcher_eastus* che si trova nel gruppo di risorse *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se non si ha già un'istanza di Network Watcher abilitata nell'area Stati Uniti orientali, usare [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) per creare un'istanza di Network Watcher in tale area:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>Usare la verifica del flusso IP

Quando si crea una macchina virtuale, per impostazione predefinita Azure consente e nega il traffico di rete da e verso la macchina virtuale. È possibile eseguire l'override in un secondo momento delle impostazioni predefinite di Azure, consentendo o negando altri tipi di traffico. Per verificare se il traffico verso destinazioni diverse e da un indirizzo IP di origine è consentito o negato, usare il comando [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow).

Testare le comunicazioni in uscita dalla macchina virtuale a uno degli indirizzi IP di www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Dopo alcuni secondi, il risultato restituito informa l'utente che l'accesso è consentito dalla regola di sicurezza denominata **AllowInternetOutbound**.

Testare le comunicazioni in uscita dalla macchina virtuale verso 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Il risultato restituito informa l'utente che l'accesso è negato dalla regola di sicurezza denominata **DefaultOutboundDenyAll**.

Testare le comunicazioni in ingresso verso la macchina virtuale provenienti da 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Il risultato restituito informa l'utente che l'accesso è negato dalla regola di sicurezza denominata **DefaultInboundDenyAll**. Conoscendo le regole di sicurezza che consentono o negano il traffico da o verso una macchina virtuale, è possibile determinare come risolvere i problemi.

## <a name="view-details-of-a-security-rule"></a>Visualizzare i dettagli di una regola di sicurezza

Per comprendere perché le regole indicate in [Testare la comunicazione di rete](#test-network-communication) consentono o impediscono la comunicazione, esaminare le regole di sicurezza valide per l'interfaccia di rete con [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

L'output restituito include il testo seguente per la regola **AllowInternetOutbound** che ha consentito l'accesso in uscita a www.bing.com in [Usare la verifica del flusso IP](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Nell'output il valore di **DestinationAddressPrefix** è **Internet**. Tuttavia non è chiaro come 13.107.21.200, l'indirizzo testato in [Usare la verifica del flusso IP](#use-ip-flow-verify), sia correlato a **Internet**. In **ExpandedDestinationAddressPrefix** sono elencati numerosi prefissi degli indirizzi. Uno dei prefissi nell'elenco è **12.0.0.0/6**, che comprende l'intervallo di indirizzi IP 12.0.0.1-15.255.255.254. Poiché 13.107.21.200 è compreso in tale intervallo di indirizzi, la regola **AllowInternetOutBound** consente il traffico in uscita. Inoltre, nell'output restituito da `Get-AzEffectiveNetworkSecurityGroup` non sono elencate regole con **priorità** più alta (numero inferiore) che eseguono l'override di questa regola. Per impedire le comunicazioni in uscita verso 13.107.21.200, è possibile aggiungere una regola di sicurezza con una priorità più alta, che neghi l'accesso in uscita dalla porta 80 verso l'indirizzo IP.

Quando si esegue il comando `Test-AzNetworkWatcherIPFlow` per testare la comunicazione in uscita verso 172.131.0.100 in [Usare la verifica del flusso IP](#use-ip-flow-verify), l'output informa l'utente che la regola **DefaultOutboundDenyAll** ha negato la comunicazione. La regola **DefaultOutboundDenyAll** equivale alla regola **DenyAllOutBound** indicata nell'output seguente del comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

La regola indica **0.0.0.0/0** come valore di **DestinationAddressPrefix**. La regola nega la comunicazione in uscita verso 172.131.0.100, perché l'indirizzo non rientra nel valore di **DestinationAddressPrefix** di nessuna delle altre regole in uscita nell'output derivante dal comando `Get-AzEffectiveNetworkSecurityGroup`. Per consentire la comunicazione in uscita, è possibile aggiungere una regola di sicurezza con una priorità più alta, che consente il traffico in uscita sulla porta 80 verso 172.131.0.100.

Quando si esegue il comando `Test-AzNetworkWatcherIPFlow` in [Usare la verifica del flusso IP](#use-ip-flow-verify) per testare le comunicazioni in ingresso da 172.131.0.100, l'output informa l'utente che la regola **DefaultInboundDenyAll** ha negato la comunicazione. La regola **DefaultInboundDenyAll** equivale alla regola **DenyAllInBound** indicata nell'output seguente del comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

La regola **DenyAllInBound** viene applicata perché, come illustrato nell'output, non è presente un'altra regola con priorità più alta nell'output del comando `Get-AzEffectiveNetworkSecurityGroup` che consente l'ingresso della porta 80 nella macchina virtuale da 172.131.0.100. Per consentire la comunicazione in ingresso, è possibile aggiungere una regola di sicurezza con una priorità più alta, che consente il traffico in ingresso sulla porta 80 da 172.131.0.100.

I controlli eseguiti in questa guida introduttiva hanno consentito di testare la configurazione di Azure. Se i controlli hanno restituito i risultati previsti ma sussistono ancora problemi di rete, verificare che non sia presente un firewall tra la macchina virtuale e l'endpoint con cui si sta comunicando e che il sistema operativo nella macchina virtuale non disponga di un firewall che stia consentendo o negando la comunicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuoverli:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare una macchina virtuale e diagnosticare problemi dei filtri del traffico di rete in ingresso e in uscita. Si è appreso che le regole del gruppo di sicurezza di rete consentono o negano il traffico da e verso una macchina virtuale. Altre informazioni sulle [regole di sicurezza](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e su come [creare le regole di sicurezza](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Anche se i filtri del traffico di rete non presentano problemi, la comunicazione verso una macchina virtuale può non riuscire a causa della configurazione del routing. Per informazioni su come diagnosticare problemi di routing della rete delle macchine virtuali, vedere [Diagnosticare i problemi di routing delle macchine virtuali](diagnose-vm-network-routing-problem-powershell.md) o, per diagnosticare con uno strumento i problemi inerenti il routing in uscita, la latenza e i filtri del traffico, vedere [Risolvere i problemi relativi alle connessioni](network-watcher-connectivity-powershell.md).
