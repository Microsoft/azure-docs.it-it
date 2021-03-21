---
title: Distribuire l'applicazione IPv6 dual stack-Load Balancer Basic-CLI
titlesuffix: Azure Virtual Network
description: Informazioni su come distribuire un'applicazione dual stack (IPv4 + IPv6) con Load Balancer di base usando l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: e9bb0f499e8df712107a6fcdbff14ee367a02bf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934156"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Distribuire un'applicazione IPv6 dual stack usando l'interfaccia della riga di comando di base Load Balancer

Questo articolo illustra come distribuire un'applicazione dual stack (IPv4 + IPv6) con Load Balancer di base usando l'interfaccia della riga di comando di Azure che include una rete virtuale a doppio stack con una subnet dello stack doppio, una Load Balancer di base con due configurazioni front-end Dual (IPv4 + IPv6), macchine virtuali con NIC con una doppia configurazione IP, regole del gruppo di sicurezza di rete doppie

Per distribuire un'applicazione dual stack (IPV4 + IPv6) con Load Balancer Standard, vedere [distribuire un'applicazione IPv6 dual stack con Load Balancer standard usando l'interfaccia della riga di comando di Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare la rete virtuale a doppio stack, è necessario creare un gruppo di risorse con [AZ Group create](/cli/azure/group). Nell'esempio seguente viene creato un gruppo di risorse denominato *DsResourceGroup01* nella posizione *eastus* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Creare indirizzi IP pubblici IPv4 e IPv6 per il servizio di bilanciamento del carico
Per accedere agli endpoint IPv4 e IPv6 su Internet, sono necessari indirizzi IP pubblici IPv4 e IPv6 per il servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). L'esempio seguente crea un indirizzo IP pubblico IPv4 e IPv6 denominato *dsPublicIP_v4* e *dsPublicIP_v6* nel gruppo di risorse *DsResourceGroup01* :

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Creare indirizzi IP pubblici per le macchine virtuali

Per accedere in modalità remota alle VM su Internet, sono necessari indirizzi IP pubblici IPv4 per le macchine virtuali. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione vengono configurati due IP front-end (IPv4 e IPv6) e il pool di indirizzi back-end per il servizio di bilanciamento del carico e quindi viene creato un Load Balancer di base.

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare il Load Balancer di base con [AZ Network lb create](/cli/azure/network/lb) denominato **dsLB** che include un pool front-end denominato **dsLbFrontEnd_v4**, un pool back-end denominato **dsLbBackEndPool_v4** associato all'indirizzo IP pubblico IPv4 **dsPublicIP_v4** creato nel passaggio precedente. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Crea front-end IPv6

Creare un indirizzo IP front-end IPV6 con [AZ Network lb frontend-IP create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create). L'esempio seguente crea una configurazione IP front-end denominata *dsLbFrontEnd_v6* e connette l'indirizzo del *dsPublicIP_v6* :

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurare il pool di indirizzi back-end IPv6

Creare un pool di indirizzi back-end IPv6 con [AZ Network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create). Nell'esempio seguente viene creato un pool di indirizzi back-end denominato *dsLbBackEndPool_v6*  per includere macchine virtuali con configurazioni NIC IPv6:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Creare un probe di integrità
Creare un probe di integrità con il comando [az network lb probe create](/cli/azure/network/lb/probe) per monitorare l'integrità delle macchine virtuali. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. 

Creare una regola di bilanciamento del carico con [AZ Network lb Rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create). L'esempio seguente crea regole di bilanciamento del carico denominate *dsLBrule_v4* e *dsLBrule_v6* e bilancia il traffico sulla porta *TCP* *80* alle configurazioni IP front-end IPv4 e IPv6:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Creare risorse di rete
Prima di distribuire alcune macchine virtuali, è necessario creare risorse di rete di supporto: set di disponibilità, gruppo di sicurezza di rete, rete virtuale e NIC virtuali. 
### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per migliorare la disponibilità dell'app, inserire le VM in un set di disponibilità.

Creare un set di disponibilità con [AZ VM Availability-set create](/cli/azure/vm/availability-set). Nell'esempio seguente viene creato un set di disponibilità denominato *dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che gestiranno le comunicazioni in ingresso e in uscita nella VNET.

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [AZ Network NSG create](/cli/azure/network/nsg#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Creare una regola del gruppo di sicurezza di rete per le connessioni in ingresso e in uscita

Creare una regola del gruppo di sicurezza di rete per consentire le connessioni RDP attraverso la porta 3389, la connessione Internet tramite la porta 80 e per le connessioni in uscita con [AZ Network NSG Rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Nell'esempio seguente viene creata una rete virtuale denominata *dsVNET* con subnet *dsSubNET_v4* e *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Creare NIC

Creare schede di interfaccia di rete virtuali per ogni macchina virtuale con [AZ Network NIC create](/cli/azure/network/nic#az-network-nic-create). Nell'esempio seguente viene creata una scheda di interfaccia di rete virtuale per ogni VM. Ogni scheda di interfaccia di rete ha due configurazioni IP (1 configurazione IPv4, 1 configurazione IPv6). Si crea la configurazione di IPV6 con [AZ Network NIC IP-config create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create).

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare le VM con [az vm create](/cli/azure/vm#az-vm-create). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. 

Creare *dsVM0* macchina virtuale come segue:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Creare *dsVM1* macchina virtuale come segue:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizza rete virtuale dual stack IPv6 in portale di Azure
È possibile visualizzare la rete virtuale dual stack IPv6 in portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale immettere *dsVnet*.
2. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *dsVnet*. La rete virtuale dual stack Mostra le due schede NIC con le configurazioni IPv4 e IPv6 presenti nella subnet dello stack doppio denominata *dsSubnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un Load Balancer di base con una doppia configurazione IP front-end (IPv4 e IPv6). Sono state anche create due macchine virtuali che includono nic con doppie configurazioni IP (IPV4 + IPv6) che sono state aggiunte al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto di IPv6 in reti virtuali di Azure, vedere [che cos'è IPv6 per rete virtuale di Azure?](ipv6-overview.md)
