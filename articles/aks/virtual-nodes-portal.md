---
title: Creare nodi virtuali usando il portale nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il portale di Azure per creare un cluster del servizio Azure Kubernetes che usa nodi virtuali per eseguire i pod.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c1ecaa88dd5329d86818565983a6ba891a6d8424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577827"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Creare e configurare un cluster del servizio Azure Kubernetes per l'uso di nodi virtuali nel portale di Azure

Questo articolo illustra come usare la portale di Azure per creare e configurare le risorse di rete virtuale e un cluster AKS con i nodi virtuali abilitati.

> [!NOTE]
> [Questo articolo](virtual-nodes.md) fornisce una panoramica della disponibilità e delle limitazioni dell'area usando i nodi virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

I nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in Istanze di Azure Container e nel cluster del servizio Azure Kubernetes. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster AKS creati usando *Advanced* Networking (Azure CNI). Per impostazione predefinita, i cluster AKS vengono creati con la rete di *base* (kubenet). Questo articolo illustra come creare una rete virtuale e le subnet e quindi distribuire un cluster del servizio Azure Kubernetes che usa reti avanzate.

Se ACI non è stato usato in precedenza, registrare il provider di servizi con la sottoscrizione. È possibile controllare lo stato della registrazione del provider di Istanze di Azure Container usando il comando [az provider list][az-provider-list], come spiegato nell'esempio seguente:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Il provider *Microsoft.ContainerInstance* deve essere contrassegnato come *Registrato*, come spiegato nell'output di esempio seguente:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Se il provider viene visualizzato come *NotRegistered*, registrare il provider usando [az provider register][az-provider-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Nell'angolo superiore sinistro del portale di Azure, selezionare **Crea una risorsa** > **Servizio Kubernetes**.

Nella pagina **Informazioni di base** configurare le opzioni seguenti:

- *DETTAGLI DEL PROGETTO*: Selezionare una sottoscrizione di Azure, quindi selezionare o creare un gruppo di risorse di Azure, ad esempio *myResourceGroup*. Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*.
- *Dettagli cluster*: selezionare un'area e la versione Kubernetes per il cluster AKS.
- *POOL DI NODI PRIMARIO*: Selezionare le dimensioni di macchina virtuale per i nodi del servizio Azure Kubernetes. Le dimensioni della macchina virtuale **non possono** essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
     - Selezionare il numero di nodi da distribuire nel cluster. Per questo articolo, impostare **Conteggio dei nodi** su *1*. Il numero di nodi **può** essere modificato dopo che il cluster è stato distribuito.

Fare clic su **Avanti: pool di nodi**.

Nella pagina **pool di nodi** selezionare *Abilita nodi virtuali*.

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="In un browser, Mostra la creazione di un cluster con nodi virtuali abilitati nella portale di Azure. L'opzione ' Abilita nodi virtuali ' è evidenziata.":::

Per impostazione predefinita, viene creata un'identità del cluster. Questa identità del cluster viene usata per la comunicazione del cluster e l'integrazione con altri servizi di Azure. Per impostazione predefinita, questa identità del cluster è un'identità gestita. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md). È anche possibile usare un'entità servizio come identità del cluster.

Il cluster è configurato anche per le funzionalità di rete avanzate. I nodi virtuali sono configurati per l'uso della relativa subnet di rete virtuale di Azure. La subnet include autorizzazioni delegate per la connessione delle risorse di Azure nel cluster del servizio Azure Kubernetes. Se non è già presente una subnet delegata, il portale di Azure crea e configura la rete virtuale di Azure e la subnet per l'uso dei nodi virtuali.

Selezionare **Rivedi e crea**. Dopo aver completato la convalida, selezionare **Crea**.

Sono necessari alcuni minuti perché il cluster del servizio Azure Kubernetes venga creato e sia pronto per l'uso.

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Il client `kubectl` è preinstallato in Azure Cloud Shell.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Usare il comando [az aks get-credentials][az-aks-get-credentials] per configurare `kubectl` per la connessione al cluster Kubernetes. L'esempio seguente ottiene le credenziali per il nome cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente illustra il singolo nodo di macchina virtuale creato e quindi il nodo virtuale per Linux, *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuire un'app di esempio

In Azure Cloud Shell creare un file denominato `virtual-node.yaml` e copiarlo nel codice YAML seguente. Per pianificare il contenitore nel nodo, vengono definiti gli elementi [nodeSelector][node-selector] e [toleration][toleration]. Queste impostazioni consentono di pianificare il pod nel nodo virtuale e verificare che la funzionalità sia abilitata correttamente.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
```

Eseguire l'applicazione con il comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod e il nodo pianificato. Si noti che il pod `virtual-node-helloworld` è stato pianificato nel nodo `virtual-node-linux`.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Al pod viene assegnato un indirizzo IP interno della subnet di rete virtuale di Azure delegata per l'uso con nodi virtuali.

> [!NOTE]
> Se si usano immagini archiviate nel Registro Azure Container, [configurare e usare un segreto Kubernetes][acr-aks-secrets]. Una limitazione corrente dei nodi virtuali è costituita dall'impossibilità di usare l'autenticazione dell'entità servizio di Azure AD. Se non si usa un segreto, i pod pianificati nei nodi virtuali non si avviano e segnalano l'errore `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testare il pod del nodo virtuale

Per testare il pod in esecuzione nel nodo virtuale, passare all'applicazione demo con un client Web. Poiché al pod è assegnato un indirizzo IP interno, è possibile eseguire rapidamente il test di questa connettività da un altro pod nel cluster del servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installare `curl` nel pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Accedere all'indirizzo del pod usando `curl`, ad esempio *http://10.241.0.4* . Specificare l'indirizzo IP interno visualizzato nel comando `kubectl get pods` precedente:

```console
curl -L http://10.241.0.4
```

Viene visualizzata l'applicazione demo, come illustrato nell'output di esempio condensato seguente:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Chiudere la sessione del terminale nel pod del test con `exit`. Quando viene terminata la sessione, il pod viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato pianificato un pod nel nodo virtuale ed è stato assegnato un indirizzo IP interno privato. In alternativa, è possibile creare una distribuzione di servizio e instradare il traffico al pod attraverso un bilanciamento del carico o un controller in ingresso. Per altre informazioni, vedere[Creare un controller di ingresso di base nel servizio Azure Kubernetes][aks-basic-ingress].

I nodi virtuali sono un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Usare il ridimensionamento automatico orizzontale dei pod di Kubernetes][aks-hpa]
- [Usare ridimensionamento automatico del cluster di Kubernetes][aks-cluster-autoscaler]
- [Vedere l'esempio di scalabilità automatica per i nodi virtuali][virtual-node-autoscale]
- [Leggere altre informazioni sulla libreria open source Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
