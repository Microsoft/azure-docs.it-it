---
title: Connettersi al servizio Azure Kubernetes-database di Azure per MySQL
description: Informazioni sulla connessione del servizio Azure Kubernetes con il Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 712bf702ba355ec3b2ca6184c33da8489f8a178e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519865"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Connessione del servizio Azure Kubernetes e del Database di Azure per MySQL

Il servizio Azure Kubernetes fornisce un cluster Kubernetes gestito che è possibile usare in Azure. Di seguito sono riportate alcune opzioni da prendere in considerazione quando si usano insieme il servizio Azure Kubernetes e il Database di Azure per MySQL per creare un'applicazione.


## <a name="accelerated-networking"></a>Rete accelerata
Usare le macchine virtuali sottostanti abilitate alla rete accelerata nel cluster del servizio Azure Kubernetes. Quando la funzionalità di rete accelerata è abilitata in una macchina virtuale, si riduce la latenza, l'instabilità e l'utilizzo della CPU nella macchina virtuale. Sono disponibili altre informazioni su come funziona la rete accelerata, sulle versioni del sistema operativo supportate e sulle istanze di macchine virtuali supportate per [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Da novembre 2018 il servizio Azure Kubernetes supporta la funzionalità di rete accelerata nelle istanze delle macchine virtuali supportate. La rete accelerata è abilitata per impostazione predefinita nei nuovi cluster del servizio Azure Kubernetes che usano queste macchine virtuali.

È possibile verificare se il cluster del servizio Azure Kubernetes ha la funzionalità di rete accelerata:
1. Accedere al portale di Azure e selezionare il cluster del servizio Azure Kubernetes.
2. Selezionare la scheda Proprietà.
3. Copiare il nome del **gruppo di risorse dell'infrastruttura**.
4. Usare la barra di ricerca nel portale per individuare e aprire il gruppo di risorse dell'infrastruttura.
5. Selezionare una macchina virtuale in questo gruppo di risorse.
6. Passare alla scheda**Rete** della macchina virtuale.
7. Verificare che la **rete accelerata** sia abilitata.

In alternativa, eseguire i due comandi seguenti tramite l'interfaccia della riga di comando di Azure:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Come output verrà restituito il gruppo di risorse generato dal servizio Azure Kubernetes contenente l'interfaccia di rete. Prendere nota del nome "nodeResourceGroup" e usarlo nel comando successivo. **EnableAcceleratedNetworking** sarà true o false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="next-steps"></a>Passaggi successivi
- [Creare un cluster del servizio Azure Kubernetes](../aks/kubernetes-walkthrough.md)
- Informazioni su come [installare WordPress da un grafico Helm usando OSBA e il Database di Azure per MySQL](../aks/integrate-azure.md)
