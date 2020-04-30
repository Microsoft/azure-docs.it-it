---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster di Azure Kubernetes Service (AKS) per ottenere le funzionalità più recenti e gli aggiornamenti della sicurezza.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 7e9a47b7bda4cdb0ff6f1983bc884f7441a26d9b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207973"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Nel corso del ciclo di vita di un cluster del servizio Azure Kubernetes, è spesso necessario eseguire l'aggiornamento alla versione più recente di Kubernetes. È infatti importante applicare gli ultimi aggiornamenti della sicurezza di Kubernetes o eseguire l'aggiornamento per ottenere le funzionalità più nuove. Questo articolo illustra come aggiornare i componenti Master o un singolo pool di nodi predefinito in un cluster AKS.

Per i cluster AKS che usano più pool di nodi, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!WARNING]
> Un aggiornamento del cluster AKS attiva un cordone e lo svuotamento dei nodi. Se è disponibile una quota di calcolo bassa, l'aggiornamento potrebbe non riuscire. Per altre informazioni, vedere [aumentare le quote](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
> Se si esegue la distribuzione del servizio di scalabilità automatica del cluster, disabilitarla (è possibile ridimensionarla a zero repliche) durante l'aggiornamento, in quanto esiste la possibilità che interferisca con il processo di aggiornamento. Il ridimensionamento automatico gestito lo gestisce automaticamente. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. L'esempio seguente verifica se sono disponibili aggiornamenti per il cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster del servizio Azure Kubernetes, le versioni secondarie di Kubernetes non possono essere ignorate. Ad esempio, gli aggiornamenti compresi tra *1.12. x* -> *1.13. x* o *1.13. x* -> *1.14.* x sono consentiti, tuttavia *1.12* -> . x*1.14. x* non lo è.
>
> Per eseguire l'aggiornamento, da *1.12. x* -> *1.14. x*, eseguire prima l'aggiornamento da *1.12.* -> *1.13. x*, quindi eseguire l'aggiornamento da *1.13. x* -> *1.14. x*.

L'output di esempio seguente mostra che il cluster può essere aggiornato alle versioni *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se non è disponibile alcun aggiornamento, si otterrà quanto segue:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, AKS aggiunge un nuovo nodo al cluster che esegue la versione specificata di Kubernetes, quindi controlla attentamente [e svuota][kubernetes-drain] uno dei nodi obsoleti per ridurre al minimo le problematiche di esecuzione delle applicazioni. Quando il nuovo nodo viene confermato come esecuzione di Pod applicazione, il nodo precedente viene eliminato. Questo processo si ripete fino a quando tutti i nodi del cluster non sono stati aggiornati.

L'esempio seguente aggiorna un cluster alla versione *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone. 

> [!NOTE]
> È previsto un tempo totale consentito per il completamento dell'aggiornamento di un cluster. Questo tempo viene calcolato prendendo il prodotto di `10 minutes * total number of nodes in the cluster`. Ad esempio, in un cluster a 20 nodi, le operazioni di aggiornamento devono avere esito positivo in 200 minuti oppure AKS avrà esito negativo per evitare uno stato del cluster irreversibile. Per eseguire il ripristino in caso di errore di aggiornamento, ripetere l'operazione di aggiornamento dopo che è stato raggiunto il timeout.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster ora esegue *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni sul servizio Azure Kubernetes][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
