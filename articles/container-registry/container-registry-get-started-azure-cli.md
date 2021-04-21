---
title: 'Avvio rapido: Creare un registro - Interfaccia della riga di comando di Azure'
description: Informazioni su come apprendere rapidamente a creare un registro Docker privato con l'interfaccia della riga di comando di Azure.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: 5c313ab43fd3dc18acf8261730686a4d6657291d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783792"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Avvio rapido: Creare un registro contenitori privato usando l'interfaccia della riga di comando di Azure

Registro Azure Container è un servizio gestito di registri contenitori Docker usato per l'archiviazione di immagini di un contenitore Docker privato. Questa guida descrive la creazione di un'istanza di Registro Azure Container con l'interfaccia della riga di comando di Azure. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

Questa guida di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure (consigliata la versione 2.0.55 o successive). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

È anche necessario avere Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Poiché Azure Cloud Shell non include tutti i componenti di Docker necessari, ovvero il daemon `dockerd`, non è possibile usare Cloud Shell lo per questa guida rapida.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create][az-group-create]. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro contenitori

In questa guida di avvio rapido viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [Livelli di servizio del registro contenitori][container-registry-skus].

Creare un'istanza di Registro Azure Container usando il comando [azure acr create][az-acr-create]. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nell'esempio seguente viene usato il nome *myContainerRegistry007*. Aggiornarlo a un valore univoco.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

Quando viene creato il registro, l'output è simile al seguente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Prendere nota del valore di `loginServer` nell'output, che corrisponde al nome del registro completo (tutto minuscolo). Nella parte restante di questa guida di avvio rapido, `<registry-name>` è un segnaposto per il nome del registro contenitori e `<login-server>` è un segnaposto per il nome del server di accesso del registro.

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere al registro. A tale scopo usare il comando [az acr login][az-acr-login]. Quando si accede con l'interfaccia della riga di comando di Azure, specificare solo il nome del registro. Non usare il nome del server di accesso, che include un suffisso di dominio come `azurecr.io`. 

```azurecli
az acr login --name <registry-name>
```

Esempio:

```azurecli
az acr login --name mycontainerregistry
```

Il comando restituisce un messaggio `Login Succeeded` al termine dell'esecuzione.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Elencare le immagini del contenitore

L'esempio seguente elenca i repository presenti nel registro:

```azurecli
az acr repository list --name <registry-name> --output table
```

Output:

```
Result
----------------
hello-world
```

L'esempio seguente elenca i tag nel repository **hello-world**.

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

Output:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, il registro contenitori e le immagini del contenitore in esso archiviate non sono più necessari, è possibile usare il comando [az group delete][az-group-delete] per rimuoverli.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Registro Azure Container con l'interfaccia della riga di comando di Azure, è stato eseguito il push di un'immagine del contenitore nel registro e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Esercitazioni su Attività del Registro Azure Container][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
