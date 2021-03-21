---
title: Distribuire il firewall di Azure con zone di disponibilità tramite PowerShell
description: Questo articolo illustra come distribuire un firewall di Azure con zone di disponibilità usando il Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656106"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuire un Firewall di Azure con zone di disponibilità tramite Azure PowerShell

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità.

Questa funzionalità consente gli scenari seguenti:

- È possibile aumentare la disponibilità al 99,99% di tempo di ingrandimento. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.
- È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

Per altre informazioni sui zone di disponibilità di Azure firewall, vedere informazioni su [Firewall](overview.md) di Azure

Nell'esempio di Azure PowerShell seguente viene illustrato come è possibile distribuire un firewall di Azure con zone di disponibilità.

## <a name="create-a-firewall-with-availability-zones"></a>Creare un firewall con zone di disponibilità

Questo esempio crea un firewall nelle zone 1, 2 e 3.

Quando viene creato l'indirizzo IP pubblico standard, non viene specificata alcuna zona specifica. Per impostazione predefinita, viene creato un indirizzo IP con ridondanza della zona. Gli indirizzi IP pubblici standard possono essere configurati in tutte le zone o in una singola zona.

È importante saperlo, perché non è possibile avere un firewall nella zona 1 e un indirizzo IP nella zona 2. È tuttavia possibile avere un firewall nella zona 1 e nell'indirizzo IP in tutte le zone oppure un firewall e un indirizzo IP nella stessa zona per finalità di prossimità.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: monitorare i log del Firewall di Azure](./firewall-diagnostics.md)