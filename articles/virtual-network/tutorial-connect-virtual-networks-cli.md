---
title: Connettere reti virtuali con il peering VNet-interfaccia della riga di comando di Azure
description: In questo articolo viene illustrato come connettere reti virtuali con il peering di rete virtuale usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94741774"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Connettere reti virtuali con il peering di rete virtuale usando l'interfaccia della riga di comando di Azure

È possibile connettere due reti virtuali tra loro con il peering reti virtuali. Dopo che è stato eseguito il peering, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che sarebbero disponibili se si trovassero nella stessa rete virtuale. In questo articolo vengono illustrate le operazioni seguenti:

* Creare due reti virtuali
* Connettere due reti virtuali con un peering di reti virtuali
* Distribuire una macchina virtuale in ogni rete virtuale
* Stabilire la comunicazione tra le macchine virtuali

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-virtual-networks"></a>Creare reti virtuali

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork1* con prefisso di indirizzo *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Creare una rete virtuale denominata *myVirtualNetwork2* con il prefisso dell'indirizzo *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Eseguire il peering delle reti virtuali

I peering vengono stabiliti tra gli ID delle reti virtuali, quindi occorre prima di tutto ottenere l'ID di ogni rete virtuale con [az network vnet show](/cli/azure/network/vnet) e memorizzarlo in una variabile.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Creare un peering da *myVirtualNetwork1* a *myVirtualNetwork2* con [az network vnet peering create](/cli/azure/network/vnet/peering). Se non si specifica il parametro `--allow-vnet-access`, il peering viene stabilito, ma non è possibile alcuna comunicazione attraverso di esso.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Nell'output restituito dopo l'esecuzione del comando precedente si noterà che **peeringState** è stato *avviato*. Il peering rimane nello stato *Initiated* finché non si crea il peering da *myVirtualNetwork2* a *myVirtualNetwork1*. Creare un peering da *myVirtualNetwork2* a *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Nell'output restituito dopo l'esecuzione del comando precedente, si noterà che **peeringState** è *connesso*. Azure avrà modificato anche lo stato del peering *myVirtualNetwork1-myVirtualNetwork2* in *Connected*. Verificare che lo stato del peering di *myVirtualNetwork1-myVirtualNetwork2* sia cambiato in *Connected* con [az network vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Le risorse in una rete virtuale non possono comunicare con le risorse nell'altra rete virtuale fino a quando non viene *connesso* il **peeringState** per i peering in entrambe le reti virtuali. 

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale in ogni rete virtuale per poter stabilire la comunicazione tra di esse in un passaggio successivo.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con il comando [az vm create](/cli/azure/vm). L'esempio seguente crea una macchina virtuale denominata *myVm1* nella rete virtuale *myVirtualNetwork1*. Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'opzione `--no-wait` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

Creare una macchina virtuale nella rete virtuale *myVirtualNetwork2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Dopo aver creato la macchina virtuale, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Prendere nota di **publicIpAddress**. Questo indirizzo viene usato per accedere alla VM da Internet in un passaggio successivo.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

Usare il comando seguente per creare una sessione SSH con la VM *myVm2*. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della macchina virtuale. Nell'esempio precedente l'indirizzo IP pubblico è *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Effettuare il ping della macchina virtuale in *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Si riceveranno quattro risposte. 

Chiudere la sessione SSH alla macchina virtuale *myVm2*. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure/group) per rimuoverli.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come connettere due reti nella stessa area di Azure con il peering di rete virtuale. È anche possibile creare un peering di reti virtuali in [aree supportate](virtual-network-manage-peering.md#cross-region) diverse e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#cli) e creare [progettazioni di rete di tipo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) con peering. Per altre informazioni sul peering di rete virtuale, vedere [Peering di rete virtuale](virtual-network-peering-overview.md) e [Gestire un peering di rete virtuale](virtual-network-manage-peering.md).

È possibile [connettere il proprio computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite una VPN e interagire con le risorse in una rete virtuale o in reti virtuali associate tramite peering. Vedere gli [esempi di script](cli-samples.md) riutilizzabili per completare molte delle attività illustrate negli articoli relativi alle reti virtuali.
