---
title: Bilanciare il carico del traffico verso le macchine virtuali per la disponibilità elevata - Interfaccia della riga di comando di Azure - Azure Load Balancer
description: Questo esempio di script dell'interfaccia della riga di comando di Azure illustra come bilanciare il carico del traffico verso le macchine virtuali per la disponibilità elevata
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7041bb568aed7ec0619e57887db64f8041168ee6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696609"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Esempio di script dell'interfaccia della riga di comando di Azure: bilanciare il carico del traffico verso le macchine virtuali per la disponibilità elevata

Questo script di esempio dell'interfaccia della riga di comando di Azure crea tutti gli elementi necessari per eseguire più macchine virtuali Ubuntu configurate in una configurazione a disponibilità elevata e con bilanciamento del carico. Dopo aver eseguito lo script, si disporrà di tre macchine virtuali, aggiunte a un set di disponibilità di Azure e accessibili tramite Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Consente di creare una rete virtuale e una subnet di Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Consente di creare un indirizzo IP pubblico con un indirizzo IP statico e un nome DNS associato. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Crea un servizio di bilanciamento del carico di Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Crea un probe di bilanciamento del carico. Il probe di bilanciamento del carico viene usato per monitorare tutte le macchine virtuali nel set di bilanciamento del carico. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Crea una regola di bilanciamento del carico. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al bilanciamento del carico, viene indirizzato sulla porta 80 di una delle macchine virtuali presenti nel set di bilanciamento del carico. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Crea una regola Network Address Translation (NAT) di bilanciamento del carico.  Le regole NAT mappano una porta del bilanciamento del carico su una porta in una macchina virtuale. In questo esempio viene creata una regola NAT per il traffico SSH in ogni macchina virtuale disponibile nel set di bilanciamento del carico.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Consente di creare un gruppo di sicurezza di rete, ovvero un confine di sicurezza tra Internet e la macchina virtuale. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Consente di creare una regola NSG per consentire il traffico in ingresso. In questo esempio, la porta 22 è aperta al traffico SSH. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Consente di creare una scheda di rete virtuale e la collega alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Consente di creare un set di disponibilità. I set di disponibilità garantiscono il tempo di attività dell'applicazione suddividendo le macchine virtuali in risorse fisiche in modo tale che, in caso di errore, non venga interessato l'intero set. |
| [az vm create](/cli/azure/vm#az-vm-create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per la rete di Azure sono disponibili nella [documentazione con la panoramica delle reti di Azure](../cli-samples.md).