---
title: (DEPRECATO) Esercitazione sul servizio Azure Container - Preparare il Registro Azure Container
description: Esercitazione sul servizio Azure Container - Preparare il Registro Azure Container
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 087530fd3834c4ec4620c087134bee0ed26bb6c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399788"
---
# <a name="deprecated-deploy-and-use-azure-container-registry"></a>(DEPRECATO) Distribuire e usare il Registro Azure Container

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Esercitazione: Distribuire e usare il Registro Azure Container](../../aks/tutorial-kubernetes-prepare-acr.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Registro Azure Container è un registro privato basato su Azure per le immagini del contenitore Docker. Questa esercitazione, parte due di sette, illustra la distribuzione di un'istanza di Registro Azure Container e il push di un'immagine del contenitore al suo interno. I passaggi completati comprendono:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro Azure Container
> * Assegnazione di tag a un'immagine del contenitore per Registro Azure Container
> * Caricamento dell'immagine in Registro Azure Container

Nelle esercitazioni successive, questa istanza di Registro Azure Container viene integrata con un cluster Kubernetes del servizio Azure Container. 

## <a name="before-you-begin"></a>Prima di iniziare

Nell'[esercitazione precedente](./container-service-tutorial-kubernetes-prepare-app.md) è stata creta un'immagine del contenitore per una semplice applicazione Azure Voting. Se l'immagine dell'app Azure Vote non è stata creata, tornare all'[Esercitazione 1 - Creare immagini del contenitore](./container-service-tutorial-kubernetes-prepare-app.md).

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Distribuire Registro Azure Container

Prima di distribuire un'istanza di Registro Azure Container, è necessario che esista un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). In questo esempio viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Creare un'istanza di Registro Azure Container con il comando [az acr create](/cli/azure/acr#az-acr-create). Il nome di un registro contenitori **deve essere univoco**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Nella parte restante di questa esercitazione si usa `<acrname>` come segnaposto per il nome del registro contenitori.

## <a name="container-registry-login"></a>Accesso al registro contenitori

Usare il comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) per accedere all'istanza di Registro Azure Container. È necessario specificare il nome univoco assegnato al registro contenitori al momento della creazione.

```azurecli
az acr login --name <acrName>
```

Al termine, il comando restituisce un messaggio di accesso riuscito.

## <a name="tag-container-images"></a>Assegnare tag alle immagini del contenitore

Per visualizzare un elenco di immagini correnti, usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Output:

```output
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Ogni immagine del contenitore deve essere contrassegnata con il nome del server di accesso del registro. Questo tag viene usato per il routing quando si esegue il push delle immagini del contenitore nel registro delle immagini.

Per ottenere il nome loginServer, eseguire questo comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Applicare ora il tag loginServer del registro contenitori all'immagine `azure-vote-front`. Aggiungere anche `:v1` alla fine del nome dell'immagine. Questo tag indica la versione dell'immagine.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Dopo l'assegnazione del tag, eseguire [immagini Docker](https://docs.docker.com/engine/reference/commandline/images/) per verificare l'operazione.

```bash
docker images
```

Output:

```output
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Eseguire il push delle immagini nel registro

Eseguire il push dell'immagine `azure-vote-front` nel registro. 

Nell'esempio seguente sostituire il nome del loginServer di Registro Azure Container con il loginServer dell'ambiente in uso.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Il completamento dell'operazione richiede alcuni minuti.

## <a name="list-images-in-registry"></a>Elencare le immagini nel registro

Per restituire un elenco di immagini di cui è stato eseguito il push in Registro Azure Container, usare il comando [az acr repository list](/cli/azure/acr/repository#az-acr-repository-list). Aggiornare il comando con il nome dell'istanza di Registro Azure Container.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```output
Result
----------------
azure-vote-front
```

Per visualizzare i tag per un'immagine specifica, usare il comando [az acr repository show-tags](/cli/azure/acr/repository).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```output
Result
--------
v1
```

Al termine dell'esercitazione, l'immagine del contenitore sarà stata archiviata in un'istanza privata di Registro Azure Container. Questa immagine verrà distribuita da Registro Azure Container a un cluster Kubernetes nelle esercitazioni successive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata preparata un'istanza di Registro Azure Container per l'uso in un cluster Kubernetes del servizio Azure Container. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Distribuzione di un'istanza di Registro Azure Container
> * Assegnazione di tag a un'immagine del contenitore per Registro Azure Container
> * Caricamento dell'immagine in Registro Azure Container

Passare all'esercitazione successiva per informazioni sulla distribuzione di un cluster Kubernetes in Azure.

> [!div class="nextstepaction"]
> [Distribuire un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md).
