---
title: Creare un IP pubblico-Azure PowerShell
description: Informazioni su come creare un indirizzo IP pubblico usando Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: f61d45b6c46830064c3b58608e2eca0787d559c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675123"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Guida introduttiva: creare un indirizzo IP pubblico usando Azure PowerShell

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando Azure PowerShell. Per altre informazioni sulle risorse a cui questo può essere associato, sulla differenza tra lo SKU Basic e standard e altre informazioni correlate, vedere [indirizzi IP pubblici](./public-ip-addresses.md).  Per questo esempio, si concentrerà solo sugli indirizzi IPv4; per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per Azure VNet](./ipv6-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) denominato **myResourceGroup** nella posizione **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Crea IP pubblico

---
# <a name="standard-sku---using-zones"></a>[**SKU standard-uso di zone**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Il comando seguente funziona per AZ. Network Module Version 4.5.0 o versioni successive.  Per ulteriori informazioni sui moduli di PowerShell attualmente in uso, vedere la documentazione di [PowerShellGet](/powershell/module/powershellget/?view=powershell-7.1).

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP** in **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Per i moduli AZ. Network precedenti a 4.5.0, eseguire il comando precedente senza specificare un parametro di zona per creare un indirizzo IP con ridondanza della zona. 
>

Per creare un indirizzo IP pubblico di zona standard in Zona 2 denominato **myStandardZonalPublicIP** in **myResourceGroup**, usare il comando seguente:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Si noti che le opzioni precedenti per le zone sono selezioni valide solo nelle aree con [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU standard-nessuna zona**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Il comando seguente funziona per AZ. Network Module Version 4.5.0 o versioni successive.  Per ulteriori informazioni sui moduli di PowerShell attualmente in uso, vedere la documentazione di [PowerShellGet](/powershell/module/powershellget/?view=powershell-7.1).

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico standard come risorsa non di zona denominata **myStandardPublicIP** in **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Questa selezione è valida in tutte le aree ed è la selezione predefinita per gli indirizzi IP pubblici standard in aree senza [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico statico di base denominato **myBasicPublicIP** in **myResourceGroup**.  Gli indirizzi IP pubblici di base non hanno il concetto di zone di disponibilità.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Se è accettabile che l'indirizzo IP cambi nel tempo, è possibile selezionare l'assegnazione IP **dinamica** cambiando il AllocationMethod in ' Dynamic '.

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altri dettagli sulle singole variabili elencate in precedenza, vedere [gestire gli indirizzi IP pubblici](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Passaggi successivi
- Associare un [indirizzo IP pubblico a una macchina virtuale](./associate-public-ip-address-vm.md#azure-portal)
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).