---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico pubblico - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida introduttiva mostra come creare un servizio di bilanciamento del carico pubblico con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1956d51cbcf962f7a73e970ca2cec858d9ec6579
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056522"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Avvio rapido: Creare un servizio pubblico di bilanciamento del carico per le macchine virtuali tramite l'interfaccia della riga di comando di Azure

Iniziare a usare Azure Load Balancer con l'interfaccia della riga di comando di Azure per creare un servizio di bilanciamento del carico pubblico e tre macchine virtuali.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az-group-create) un gruppo di risorse:

* Con nome **CreatePubLBQS-rg**. 
* Nella posizione **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Risorse di Load Balancer Standard create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---standard"></a>Configurare la rete virtuale - Standard

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CCreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Creare una subnet bastion

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.1.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Creare un host bastion

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreatePubLBQS-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Creare i server back-end - Standard

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) creare tre interfacce di rete:

* Denominate **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](/cli/azure/vm#az-vm-create) creare le macchine virtuali:

### <a name="vm1"></a>VM1
* Denominata **myVM1**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Denominata **myVM3**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM3**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

## <a name="create-a-public-ip-address---standard"></a>Creare un indirizzo IP pubblico - Standard

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. 

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myPublicIP**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Per creare un indirizzo IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Con [az network lb create](/cli/azure/network/lb#az-network-lb-create) creare un servizio di bilanciamento del carico pubblico:

* Denominato **myLoadBalancer**.
* Un pool front-end denominato **myFrontEnd**.
* Un pool back-end denominato **myBackEndPool**.
* Con associazione all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Con [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) creare un probe di integrità:

* Che esegua il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Con [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) creare una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Timeout di inattività di **15 minuti**.
* Abilitare la reimpostazione TCP.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Creare la configurazione delle regole in uscita
Le regole in uscita del servizio di bilanciamento del carico configurano la conversione degli indirizzi di rete di origine in uscita per le macchine virtuali nel pool back-end. 

Per altre informazioni sulle connessioni in uscita, vedere [Connessioni in uscita in Azure](load-balancer-outbound-connections.md).

Per la configurazione in uscita è possibile usare un indirizzo o un prefisso IP pubblico.

### <a name="public-ip"></a>IP pubblico

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un singolo indirizzo IP per la connettività in uscita.  

* Denominato **myPublicIPOutbound**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Per creare un indirizzo IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Prefisso IP pubblico

Usare [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) per creare un prefisso IP pubblico per la connettività in uscita.

* Denominato **myPublicIPPrefixOutbound**.
* In **CreatePubLBQS-rg**.
* Lunghezza del prefisso pari a **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Per creare un prefisso IP pubblico ridondante di zona nella Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Per altre informazioni sul ridimensionamento di NAT in uscita e della connettività in uscita, vedere [Scalabilità di NAT in uscita con più indirizzi IP](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Creare la configurazione IP front-end in uscita

Creare una nuova configurazione IP front-end con [az network lb frontend-ip create ](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create):

Selezionare i comandi per l'indirizzo IP pubblico o per il prefisso IP pubblico in base alla decisione presa nel passaggio precedente.

#### <a name="public-ip"></a>IP pubblico

* Denominato **myFrontEndOutbound**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione all'indirizzo IP pubblico **myPublicIPOutbound**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefisso di indirizzo IP pubblico

* Denominato **myFrontEndOutbound**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al prefisso IP pubblico **myPublicIPPrefixOutbound**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Creare un pool in uscita

Con [az network lb address-pool create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) creare un nuovo pool in uscita:

* Denominato **myBackEndPoolOutbound**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Creare una regola in uscita

Con [az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create) creare una nuova regola in uscita per il pool back-end in uscita:

* Denominata **myOutboundRule**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.
* Con associazione al front-end **myFrontEndOutbound**.
* Protocollo **All**.
* Timeout di inattività pari a **15**.
* **10000** porte in uscita.
* Con associazione al pool back-end **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Aggiungere le macchine virtuali al pool in uscita

Con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) aggiungere le macchine virtuali al pool in uscita:


* Nel pool di indirizzi back-end **myBackEndPoolOutbound**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Risorse di Load Balancer Basic create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---basic"></a>Configurare la rete virtuale - Basic

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Creare una subnet bastion

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.1.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Creare un host bastion

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreatePubLBQS-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Creare i server back-end - Basic

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Un set di disponibilità per le macchine virtuali.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) creare tre interfacce di rete:


* Denominate **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Creare il set di disponibilità per le macchine virtuali

Con [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) creare il set di disponibilità:

* Denominato **myAvSet**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](/cli/azure/vm#az-vm-create) creare le macchine virtuali:

### <a name="vm1"></a>VM1
* Denominata **myVM1**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Denominata **myVM2**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Denominata **myVM3**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM3**.
* Immagine della macchina virtuale **win2019datacenter**.
* Nella **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

## <a name="create-a-public-ip-address---basic"></a>Creare un indirizzo IP pubblico - Basic

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. 

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myPublicIP**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Con [az network lb create](/cli/azure/network/lb#az-network-lb-create) creare un servizio di bilanciamento del carico pubblico:

* Denominato **myLoadBalancer**.
* Un pool front-end denominato **myFrontEnd**.
* Un pool back-end denominato **myBackEndPool**.
* Con associazione all'indirizzo IP pubblico **myPublicIP** creato nel passaggio precedente. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Con [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) creare un probe di integrità:

* Che esegua il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Con [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) creare una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Timeout di inattività di **15 minuti**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreatePubLBQS-rg**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Installare IIS

Usare [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) per installare IIS nelle macchine virtuali e impostare il sito Web predefinito sul nome computer.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare il comando [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testare il servizio di bilanciamento del carico" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido

* È stato creato un servizio di bilanciamento del carico standard o pubblico.
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a:
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)