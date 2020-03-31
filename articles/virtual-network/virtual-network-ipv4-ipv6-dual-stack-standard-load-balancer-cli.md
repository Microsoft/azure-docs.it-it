---
title: Distribuire l'applicazione dual stack IPv6 - Load Balancer Standard - CLIDeploy IPv6 dual stack application - Standard Load Balancer - CLI
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire un'applicazione dual stack IPv6 nella rete virtuale di Azure usando l'interfaccia della riga di comando di Azure.This article shows how deploy an IPv6 dual stack application in Azure virtual network using Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: fa895a294e26b6c74ab72afa3136feac2b2ec986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240239"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Distribuire un'applicazione dual stack IPv6 nella rete virtuale di Azure - CLI (anteprima)Deploy an IPv6 dual stack application in Azure virtual network - CLI (Preview)

In questo articolo viene illustrato come distribuire un'applicazione dual stack (IPv4 e IPv6) usando Il bilanciamento del carico standard in Azure che include una rete virtuale dual stack con una subnet dual stack, un servizio di bilanciamento del carico standard con configurazioni front-end dual (IPv4 e IPv6), macchine virtuali con Schede di rete con una configurazione dual IP, regole del gruppo di sicurezza di rete duale e indirizzi IP pubblici doppi.

> [!Important]
> Lo stack doppio IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se invece si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, questa guida introduttiva richiede l'uso della versione 2.0.49 dell'interfaccia della riga di comando di Azure.If you decide to install and use Azure CLI locally instead, this quickstart requires you to use Azure CLI version 2.0.49 or later. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti
Per usare la funzionalità di rete virtuale IPv6 per Azure, è necessario configurare la sottoscrizione usando l'interfaccia della riga di comando di Azure come segue:To use the IPv6 for Azure virtual network feature, you must configure your subscription using Azure CLI as follows:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato della registrazione eseguendo il comando dell'interfaccia della riga di comando di Azure seguente:You can check your registration status by running the following Azure CLI command:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Al termine della registrazione eseguire questo comando:

```azurecli
az provider register --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare la rete virtuale dual stack, è necessario creare un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato DsResourceGroup01 nella posizione *eastus:The following* example creates a resource group named *DsResourceGroup01* in the eastus location:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Creare indirizzi IP pubblici IPv4 e IPv6 per il servizio di bilanciamento del caricoCreate IPv4 and IPv6 public IP addresses for load balancer
Per accedere agli endpoint IPv4 e IPv6 su Internet, sono necessari indirizzi IP pubblici IPv4 e IPv6 per il servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). Nell'esempio seguente vengono creati gli indirizzi IP pubblici IPv4 e IPv6 denominati *dsPublicIP_v4* e *dsPublicIP_v6* nel gruppo di risorse *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Creare indirizzi IP pubblici per le macchine virtualiCreate public IP addresses for VMs

Per accedere in remoto alle macchine virtuali su Internet, sono necessari indirizzi IP pubblici IPv4 per le macchine virtuali. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

In questa sezione si configura l'IP front-end doppio (IPv4 e IPv6) e il pool di indirizzi back-end per il servizio di bilanciamento del carico e quindi si crea un servizio di bilanciamento del carico standard.

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare il servizio di bilanciamento del carico Standard con [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) named **dsLB** che include un pool front-end denominato **dsLbFrontEnd_v4**, un pool back-end denominato **dsLbBackEndPool_v4** associato all'indirizzo IP pubblico IPv4 **dsPublicIP_v4** creato nel passaggio precedente. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Creare front-end IPv6

Creare un IP front-end IPV6 con [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Nell'esempio seguente viene creata una configurazione IP front-end denominata *dsLbFrontEnd_v6* e viene collegato l'indirizzo *dsPublicIP_v6:*

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurare il pool di indirizzi back-end IPv6

Creare un pool di indirizzi back-end IPv6 con [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). L'esempio seguente crea un pool di indirizzi back-end denominato dsLbBackEndPool_v6 per includere macchine virtuali con configurazioni NIC IPv6:The following example creates back-end address pool named *dsLbBackEndPool_v6* to include VMs with IPv6 NIC configurations:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Creare un probe di integrità
Creare un probe di integrità con [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) per monitorare l'integrità delle macchine virtuali. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. 

Creare una regola di bilanciamento del carico con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). L'esempio seguente crea regole di bilanciamento del carico denominate dsLBrule_v4 e dsLBrule_v6 e bilancia il traffico sulla porta TCP 80 alle configurazioni IP front-end IPv4 e IPv6:The following example creates load balancer rules named *dsLBrule_v4* and *dsLBrule_v6* and dsLBrule_v6 and balances traffic on *TCP* port *80* to the IPv4 and IPv6 frontend IP configurations:

```azurecli
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
Prima di distribuire alcune macchine virtuali, è necessario creare risorse di rete di supporto: set di disponibilità, gruppo di sicurezza di rete, rete virtuale e schede di interfaccia di rete virtuali. 
### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per migliorare la disponibilità dell'app, inserire le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Nell'esempio seguente viene creato un set di disponibilità denominato *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che regoleranno le comunicazioni in ingresso e in uscita nella rete virtuale.

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Creare una regola del gruppo di sicurezza di rete per le connessioni in ingresso e in uscitaCreate a network security group rule for inbound and outbound connections

Creare una regola del gruppo di sicurezza di rete per consentire le connessioni RDP tramite la porta 3389, la connessione Internet tramite la porta 80 e per le connessioni in uscita con [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
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

Creare una rete virtuale con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Nell'esempio seguente viene creata una rete virtuale denominata *dsVNET* con subnet *dsSubNET_v4* e *dsSubNET_v6:*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Creare NIC

Creare schede di interfaccia di rete virtuali per ogni macchina virtuale con [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). L'esempio seguente crea una scheda di interfaccia di rete virtuale per ogni macchina virtuale. Ogni scheda di interfaccia di rete ha due configurazioni IP (1 configurazione IPv4, 1 configurazione IPv6). Creare la configurazione IPV6 con [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
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

Creare le VM con [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. 

Creare la macchina virtuale dsVM0 come segue:Create virtual machine *dsVM0* as follows:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Creare la macchina virtuale dsVM1 come segue:Create virtual machine *dsVM1* as follows:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizzare la rete virtuale dual stack IPv6 nel portale di AzureView IPv6 dual stack virtual network in Azure portal
È possibile visualizzare la rete virtuale dual stack IPv6 nel portale di Azure come segue:You can view the IPv6 dual stack virtual network in Azure portal as follows:
1. Nella barra di ricerca del portale immettere *dsVnet*.
2. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *dsVnet*. La rete virtuale dual stack mostra le due schede di interfaccia di rete con configurazioni IPv4 e IPv6 situate nella subnet dello stack doppio denominata *dsSubnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> La rete virtuale IPv6 per Azure è disponibile nel portale di Azure in sola lettura per questa versione di anteprima.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un servizio di bilanciamento del carico Standard con una configurazione IP front-end doppia (IPv4 e IPv6). Sono state inoltre create due macchine virtuali che includevano schede di interfaccia di rete con configurazioni dual IP (IPV4 e IPv6) che sono state aggiunte al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto IPv6 nelle reti virtuali di Azure, vedere [Che cos'è IPv6 per](ipv6-overview.md) la rete virtuale di Azure?
