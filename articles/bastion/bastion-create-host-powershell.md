---
title: Creare un host Bastion usando Azure PowerShell | Microsoft Docs
description: Questo articolo illustra come creare un host Bastion di Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8abd4c417181b46fbf4d5c139c157044b329ea2a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580154"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Creare un host di Azure Bastion tramite Azure PowerShell

Questo articolo illustra come creare un host di Azure Bastion usando PowerShell. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH trasparente è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Facoltativamente, è possibile creare un host Bastion di Azure usando il [portale di Azure](./tutorial-create-host-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 >[!NOTE]
 >L'uso di Azure Bastion con le zone DNS privato di Azure non è attualmente supportato. Prima di iniziare, assicurarsi che la rete virtuale in cui si intende distribuire la risorsa Bastion non sia collegata a una zona DNS privata.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host bastion

Questa sezione consente di creare una nuova risorsa di Azure Bastion usando Azure PowerShell.

1. Creare una rete virtuale e una subnet di Azure Bastion. È necessario creare la subnet di Azure Bastion usando il valore del nome **AzureBastionSubnet**. Questo valore indica ad Azure la subnet in cui devono essere distribuite le risorse Bastion. Questa subnet è diversa rispetto a una subnet del gateway. È necessario usare una subnet di almeno/27 o una subnet più grande (/27,/26 e così via). Creare **AzureBastionSubnet** senza alcuna tabella o delega di route. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo [lavorare con gruppi](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Creare un indirizzo IP pubblico per Azure Bastion. L'IP pubblico è l'indirizzo IP pubblico della risorsa Bastion su cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Creare una nuova risorsa di Azure Bastion nella AzureBastionSubnet della rete virtuale. Per la creazione e la distribuzione della risorsa Bastion sono necessari circa 5 minuti.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md) .
* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi di sicurezza di rete](bastion-nsg.md).
