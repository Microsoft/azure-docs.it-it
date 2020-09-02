---
title: "Guida di avvio rapido: Creare un'app multicontenitore"
description: Informazioni su come distribuire la prima app multicontenitore nel servizio app di Azure.
keywords: servizio app di azure, app web, linux, docker, compose, multicontenitore, multi-contenitore, app web per contenitori, più contenitori, contenitore, wordpress, db azure per mysql, database di produzione con contenitori
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 562185697f8f80fc52b06be80a9f15271f16c0a3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961466"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Creare un'app multi-contenitore (anteprima) con una configurazione di Docker Compose

> [!NOTE]
> La funzionalità multi-contenitore è disponibile in anteprima.

[App Web per contenitori](overview.md#app-service-on-linux) offre un modo flessibile per usare le immagini Docker. Questa guida di avvio rapido illustra come distribuire un'app multi-contenitore (anteprima) in App Web per contenitori in [Cloud Shell](../cloud-shell/overview.md) con una configurazione di Docker Compose.

Questa guida di avvio rapido verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.32 o versioni successive). 

![App multi-contenitore di esempio in App Web per contenitori][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

Per questa guida di avvio rapido, usare il file Compose da [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Il file di configurazione è disponibile negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart. Quindi, passare alla directory `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti centro-meridionali*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente.

Al termine del comando, un output JSON visualizza le proprietà del gruppo di risorse.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Standard** (`--sku S1`) e in un contenitore Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Creare un'app Docker Compose

> [!NOTE]
> Al momento per Docker Compose in Servizi app di Azure è previsto un limite di 4.000 caratteri.

Nel terminale Cloud Shell creare un'[app Web](overview.md#app-service-on-linux) multi-contenitore nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app (i caratteri validi sono `a-z`, `0-9` e `-`).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`. Il caricamento dell'app può richiedere alcuni minuti. Se si riceve un errore, attendere qualche minuto e quindi aggiornare il browser.

![App multi-contenitore di esempio in App Web per contenitori][1]

La creazione di un'app multi-contenitore in App Web per contenitori è stata **completata**.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Configurare un contenitore personalizzato](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png