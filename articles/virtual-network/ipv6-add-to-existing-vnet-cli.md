---
title: Aggiungere IPv6 a un'applicazione IPv4 in rete virtuale di Azure-interfaccia della riga di comando di Azure
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire indirizzi IPv6 in un'applicazione esistente in rete virtuale di Azure usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932599"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Aggiungere IPv6 a un'applicazione IPv4 in rete virtuale di Azure-interfaccia della riga di comando di Azure

Questo articolo illustra come aggiungere indirizzi IPv6 a un'applicazione che usa un indirizzo IP pubblico IPv4 in una rete virtuale di Azure per un Load Balancer Standard usando l'interfaccia della riga di comando di Azure. L'aggiornamento sul posto include una rete virtuale e una subnet, un Load Balancer Standard con configurazioni front-end IPv4 + IPV6, VM con NIC con configurazioni IPv4 + IPv6, gruppo di sicurezza di rete e indirizzi IP pubblici.

## <a name="prerequisites"></a>Prerequisiti

- Questo articolo presuppone che sia stata distribuita una Load Balancer Standard come descritto in [Guida introduttiva: creare un'interfaccia](../load-balancer/quickstart-load-balancer-standard-public-cli.md)della riga di comando Load Balancer standard-Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-ipv6-addresses"></a>Crea indirizzi IPv6

Creare un indirizzo IPv6 pubblico con il comando [AZ Network Public-IP create](/cli/azure/network/public-ip) per la Load Balancer standard. Nell'esempio seguente viene creato un indirizzo IP pubblico IPv6 denominato *PublicIP_v6* nel gruppo di risorse *myResourceGroupSLB* :

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configurare front-end di bilanciamento del carico IPv6

Configurare il servizio di bilanciamento del carico con il nuovo indirizzo IP IPv6 usando [AZ Network lb frontend-IP create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) come indicato di seguito:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configurare il pool back-end di bilanciamento del carico IPv6

Creare il pool back-end per NIC con indirizzi IPv6 usando [AZ Network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) come indicato di seguito:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configurare le regole del servizio di bilanciamento del carico IPv6

Creare regole di bilanciamento del carico IPv6 con [AZ Network lb Rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create).

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Aggiungi intervalli di indirizzi IPv6

Aggiungere gli intervalli di indirizzi IPv6 alla rete virtuale e alla subnet che ospita il servizio di bilanciamento del carico come segue:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Aggiungere la configurazione IPv6 alle schede NIC

Configurare le NIC VM con un indirizzo IPv6 usando [AZ Network NIC IP-config create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) come indicato di seguito:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizza rete virtuale dual stack IPv6 in portale di Azure

È possibile visualizzare la rete virtuale dual stack IPv6 in portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale immettere *myVnet*.
2. Selezionare **myVnet** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *myVNet*. La rete virtuale dual stack Mostra le tre schede NIC con le configurazioni IPv4 e IPv6 presenti nella subnet Dual *stack denominata subnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato aggiornato un Load Balancer Standard esistente con una configurazione IP front-end IPv4 a una configurazione dual stack (IPv4 e IPv6). Sono state anche aggiunte configurazioni IPv6 alle schede NIC delle macchine virtuali nel pool back-end. Per altre informazioni sul supporto di IPv6 in reti virtuali di Azure, vedere [che cos'è IPv6 per rete virtuale di Azure?](ipv6-overview.md)
