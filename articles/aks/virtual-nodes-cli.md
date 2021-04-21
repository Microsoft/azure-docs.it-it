---
title: Creare nodi virtuali usando l'interfaccia della riga di comando di Azure
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un cluster del servizio Azure Kubernetes che usa nodi virtuali per eseguire i pod.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769284"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Creare e configurare un cluster del servizio Azure Kubernetes per l'uso di nodi virtuali tramite l'interfaccia della riga di comando di Azure

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare e configurare le risorse di rete virtuale e il cluster del servizio AzureKs e quindi abilitare i nodi virtuali.


## <a name="before-you-begin"></a>Prima di iniziare

I nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in Istanze di Azure Container e nel cluster del servizio Azure Kubernetes. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster del servizio AKS creati usando *la* rete avanzata (Azure CNI). Per impostazione predefinita, i cluster del servizio Ku-kubenet vengono creati con *la rete* di base (kubenet). Questo articolo illustra come creare una rete virtuale e le subnet e quindi distribuire un cluster del servizio Azure Kubernetes che usa reti avanzate.

> [!IMPORTANT]
> Prima di usare i nodi virtuali con il servizio AKS, esaminare sia le limitazioni dei nodi virtuali del servizio [AKS][virtual-nodes-aks] che le limitazioni di rete [virtuale di ACI.][virtual-nodes-networking-aci] Queste limitazioni influiscono sulla posizione, sulla configurazione di rete e su altri dettagli di configurazione del cluster del servizio Web Disack e dei nodi virtuali.

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

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, è necessaria l'interfaccia della riga di comando di Azure versione 2.0.49 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse con il comando [az group create][az-group-create]. Nell'esempio seguente viene creato un gruppo di risorse *denominato myResourceGroup* nella *località westus.*

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale con il comando [az network vnet create][az-network-vnet-create]. L'esempio seguente crea il nome di rete virtuale *myVnet* con il prefisso di indirizzo *10.0.0.0/8* e una subnet denominata *myAKSSubnet*. Per impostazione predefinita, il prefisso di indirizzo di questa subnet è *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Creare ora una subnet aggiuntiva per i nodi virtuali usando il comando [az network vnet subnet create][az-network-vnet-subnet-create]. L'esempio seguente crea una subnet denominata *myVirtualNodeSubnet* con il prefisso di indirizzo *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Creare un'entità servizio o usare un'identità gestita

Per consentire a un cluster del servizio AzureKs di interagire con altre risorse di Azure, viene usata un'identità del cluster. Questa identità del cluster può essere creata automaticamente dall'interfaccia della riga di comando di Azure o dal portale oppure è possibile crearne una e assegnare autorizzazioni aggiuntive. Per impostazione predefinita, questa identità del cluster è un'identità gestita. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md). È anche possibile usare un'entità servizio come identità del cluster. La procedura seguente illustra come creare e assegnare manualmente l'entità servizio al cluster.

Creare un'entità servizio usando il comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Il parametro `--skip-assignment` limita l'assegnazione di autorizzazioni aggiuntive.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output è simile all'esempio seguente:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Prendere nota di *appId* e *password*. Questi valori vengono usati nei passaggi successivi.

## <a name="assign-permissions-to-the-virtual-network"></a>Assegnare le autorizzazioni alla rete virtuale

Per consentire al cluster di usare e gestire la rete virtuale, è necessario concedere all'entità servizio del servizio Azure Kubernetes i diritti necessari per usare le risorse di rete.

Ottenere innanzitutto l'ID risorsa della rete virtuale usando [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Per concedere l'accesso corretto al cluster del servizio Azure Kubernetes per l'uso della rete virtuale, creare un'assegnazione di ruolo usando il comando [az role assignment create][az-role-assignment-create]. Sostituire `<appId`> e `<vnetId>` con i valori raccolti nei due passaggi precedenti.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Distribuire un cluster del servizio Azure Kubernetes nella subnet del servizio creata nel passaggio precedente. Ottenere l'ID della subnet usando il comando [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Usare il comando [az aks create][az-aks-create] per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Sostituire con l'ID ottenuto nel passaggio precedente e quindi con i valori `<subnetId>` `<appId>` raccolti nella sezione `<password>` precedente.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni in formato JSON sul cluster.

## <a name="enable-virtual-nodes-addon"></a>Abilitare il componente aggiuntivo nodi virtuali

Per abilitare i nodi virtuali, usare il comando [az aks enable-addons][az-aks-enable-addons]. L'esempio seguente usa la subnet denominata *myVirtualNodeSubnet* creata nel passaggio precedente:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Con questo passaggio si scaricano le credenziali e si configura l'interfaccia della riga di comando di Kubernetes per il loro uso.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente illustra il singolo nodo di macchina virtuale creato e quindi il nodo virtuale per Linux, *virtual-node-aci-linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuire un'app di esempio

Creare un file denominato `virtual-node.yaml` e copiarlo nel codice YAML seguente. Per pianificare il contenitore nel nodo, vengono definiti gli elementi [nodeSelector][node-selector] e [toleration][toleration].

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
      - key: azure.com/aci
        effect: NoSchedule
```

Eseguire l'applicazione con il comando [kubectl apply][kubectl-apply].

```console
kubectl apply -f virtual-node.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod e il nodo pianificato. Si noti che il pod `aci-helloworld` è stato pianificato nel nodo `virtual-node-aci-linux`.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Al pod viene assegnato un indirizzo IP interno della subnet di rete virtuale di Azure delegata per l'uso con nodi virtuali.

> [!NOTE]
> Se si usano immagini archiviate nel Registro Azure Container, [configurare e usare un segreto Kubernetes][acr-aks-secrets]. Una limitazione corrente dei nodi virtuali è costituita dall'impossibilità di usare l'autenticazione dell'entità servizio di Azure AD. Se non si usa un segreto, i pod pianificati nei nodi virtuali non si avviano e segnalano l'errore `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testare il pod del nodo virtuale

Per testare il pod in esecuzione nel nodo virtuale, passare all'applicazione demo con un client Web. Poiché al pod è assegnato un indirizzo IP interno, è possibile eseguire rapidamente il test di questa connettività da un altro pod nel cluster del servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

## <a name="remove-virtual-nodes"></a>Rimuovere i nodi virtuali

Se si vuole interrompere l'uso di nodi virtuali, è possibile disabilitarli usando il comando [az aks disable-addons][az aks disable-addons]. 

Se necessario, passare a [https://shell.azure.com](https://shell.azure.com) per aprire Azure Cloud Shell nel browser.

Eliminare prima di tutto `aci-helloworld` il pod in esecuzione nel nodo virtuale:

```console
kubectl delete -f virtual-node.yaml
```

Il comando di esempio seguente disabilita i nodi virtuali Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

A questo punto, rimuovere le risorse di rete virtuale e il gruppo di risorse:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato pianificato un pod nel nodo virtuale ed è stato assegnato un indirizzo IP interno privato. In alternativa, è possibile creare una distribuzione di servizio e instradare il traffico al pod attraverso un bilanciamento del carico o un controller in ingresso. Per altre informazioni, vedere[Creare un controller di ingresso di base nel servizio Azure Kubernetes][aks-basic-ingress].

I nodi virtuali sono spesso un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Usare il ridimensionamento automatico orizzontale dei pod di Kubernetes][aks-hpa]
- [Usare ridimensionamento automatico del cluster di Kubernetes][aks-cluster-autoscaler]
- [Vedere l'esempio di scalabilità automatica per i nodi virtuali][virtual-node-autoscale]
- [Leggere altre informazioni sulla libreria open source Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md