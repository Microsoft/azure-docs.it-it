---
title: CI/CD per contenitori Linux personalizzati
description: Informazioni su come configurare la distribuzione continua in un contenitore Linux personalizzato nel servizio app Azure. La distribuzione continua è supportata per Docker Hub e ACR.
keywords: servizio app di azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b73c7e62c6b621d3ac86cfb99b10b567460534f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961857"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Distribuzione continua con l'app Web per contenitori

In questa esercitazione verrà configurata la distribuzione continua per un'immagine personalizzata del contenitore da repository gestite del [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Abilitare la distribuzione continua con il Registro Azure Container

![Screenshot del webhook di Registro Azure Container](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'opzione **Servizio app** a sinistra nella pagina.
3. Selezionare il nome dell'app per cui si desidera configurare la distribuzione continua.
4. Nella pagina **Impostazioni contenitore** selezionare **Contenitore singolo**
5. Selezionare **Registro Azure Container**
6. Selezionare **Distribuzione continua > On**
7. Selezionare **Salva** per abilitare la distribuzione continua.

## <a name="use-the-acr-webhook"></a>Usare il webhook di Registro Azure Container

Quando la distribuzione continua viene abilitata, è possibile visualizzare il webhook appena creato nella pagina dei webhook di Registro Azure Container.

![Screenshot del webhook di Registro Azure Container](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Nel Registro contenitori fare clic su Webhook per visualizzare i webhook correnti.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Abilitare la distribuzione continua con l'hub Docker (facoltativo)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'opzione **Servizio app** a sinistra nella pagina.
3. Selezionare il nome dell'app per cui si desidera configurare la distribuzione continua.
4. Nella pagina **Impostazioni contenitore** selezionare **Contenitore singolo**
5. Selezionare **Hub Docker**
6. Selezionare **Distribuzione continua > On**
7. Selezionare **Salva** per abilitare la distribuzione continua.

![Schermata dell'impostazione app](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Copiare l'URL webhook. Per aggiungere un webhook per l'hub Docker, vedere <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhook per l'hub Docker</a>.

## <a name="automate-with-cli"></a>Automatizzare con l'interfaccia della riga di comando

Per configurare CI/CD usando l'interfaccia della riga di comando di Azure, eseguire il comando [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) per generare l'URL del webhook. L'URL può essere usato per configurare la Container Registry di Azure o DockerHub.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Passaggi successivi

* [Registro Azure Container](https://azure.microsoft.com/services/container-registry/)
* [Creare un'app Web .NET Core nel Servizio app in Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Creare un'app Web Ruby nel servizio app in Linux](quickstart-ruby.md)
* [Avvio rapido: Eseguire un contenitore personalizzato nel servizio app](quickstart-custom-container.md?pivots=container-linux)
* [Domande frequenti sul servizio app in Linux](faq-app-service-linux.md)
* [Configurare contenitori Linux personalizzati](configure-custom-container.md)