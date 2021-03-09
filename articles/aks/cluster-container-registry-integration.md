---
title: Integrare Container Registry di Azure con il servizio Azure Kubernetes
description: Informazioni su come integrare Azure Kubernetes Service (AKS) con Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 19ece696dabc81e643e8a904d506d22e40eaa099
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499153"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Eseguire l'autenticazione con Registro Azure Container dal servizio Azure Kubernetes

Quando si usa Registro Azure Container con il servizio Azure Kubernetes, è necessario definire un meccanismo di autenticazione. Questa operazione viene implementata come parte dell'interfaccia della riga di comando e del portale concedendo le autorizzazioni necessarie al record di verifica. Questo articolo fornisce esempi per la configurazione dell'autenticazione tra questi due servizi di Azure. 

Con l'interfaccia della riga di comando di Azure è possibile configurare l'integrazione da AKS a ACR con pochi semplici comandi. Questa integrazione consente di assegnare il ruolo AcrPull all'identità gestita associata al cluster AKS.

> [!NOTE]
> Questo articolo descrive l'autenticazione automatica tra AKS e ACR. Se è necessario eseguire il pull di un'immagine da un registro esterno privato, usare un [segreto di pull dell'immagine][Image Pull Secret].

## <a name="before-you-begin"></a>Prima di iniziare

Gli esempi presentano i requisiti seguenti:

* **Proprietario** o ruolo di **amministratore dell'account di Azure** nella sottoscrizione di **Azure**
* Versione 2.7.0 o successiva dell'interfaccia della riga di comando di Azure

Per evitare di dover usare un **proprietario** o un ruolo di **amministratore dell'account di Azure** , è possibile configurare manualmente un'identità gestita o usare un'identità gestita esistente per autenticare ACR da AKS. Per altre informazioni, vedere [usare un'identità gestita di Azure per l'autenticazione in un registro contenitori di Azure](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Creare un nuovo cluster AKS con l'integrazione con ACR

È possibile configurare l'integrazione di AKS e ACR durante la creazione iniziale del cluster AKS.  Per consentire a un cluster AKS di interagire con ACR, viene usata un' **identità gestita** Azure Active Directory. Il comando dell'interfaccia della riga di comando seguente consente di autorizzare un registro di sistema esistente nella sottoscrizione e di configurare il ruolo **ACRPull** appropriato per l'identità gestita. Specificare i valori validi per i parametri indicati di seguito.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

In alternativa, è possibile specificare il nome dell'ACR usando un ID di risorsa ACR con il formato seguente:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Se si usa un record di registro di sistema che si trova in una sottoscrizione diversa dal cluster AKS, usare l'ID di risorsa ACR durante il collegamento o lo scollegamento da un cluster AKS.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Il completamento di questo passaggio può richiedere alcuni minuti.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurare l'integrazione di ACR per i cluster AKS esistenti

Integrare un ACR esistente con i cluster AKS esistenti fornendo valori validi per **ACR-Name** o **ACR-Resource-ID** come indicato di seguito.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

o

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

È anche possibile rimuovere l'integrazione tra un ACR e un cluster AKS con i seguenti

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

oppure

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Uso di ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importare un'immagine nell'ACR

Importare un'immagine dall'hub Docker nell'ACR eseguendo questa procedura:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuire l'immagine di esempio da ACR ad AKS

Assicurarsi di disporre delle credenziali AKS appropriate

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Creare un file denominato **ACR-nginx. YAML** contenente gli elementi seguenti. Sostituire il nome della risorsa del registro di sistema per **ACR-Name**. Esempio: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Eseguire quindi questa distribuzione nel cluster AKS:

```console
kubectl apply -f acr-nginx.yaml
```

È possibile monitorare la distribuzione eseguendo:

```console
kubectl get pods
```

Sono presenti due pod in esecuzione.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Risoluzione dei problemi
* Eseguire il comando [AZ AKS check-ACR](/cli/azure/aks#az_aks_check_acr) per verificare che il registro di sistema sia accessibile dal cluster AKS.
* Altre informazioni sulla [diagnostica ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Altre informazioni su [ACR Health](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/