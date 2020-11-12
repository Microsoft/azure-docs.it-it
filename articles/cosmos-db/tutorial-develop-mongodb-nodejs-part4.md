---
title: Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB (parte 1)
description: Parte 4 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 4ca7286676c441b2fa96883e0c187497f59d6222
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082628"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Creare un account Cosmos
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Questa esercitazione in più parti illustra come creare una nuova app scritta in Node.js con Express e Angular e quindi connetterla all'[account Cosmos configurato con l'API di Cosmos DB per MongoDB](mongodb-introduction.md).

La Parte 4 dell'esercitazione è basata sulla [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un gruppo di risorse di Azure con l'interfaccia della riga di comando di Azure
> * Creare un account Cosmos usando l'interfaccia della riga di comando di Azure

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa parte dell'esercitazione, assicurarsi di avere completato le procedure illustrate nella [Parte 3](tutorial-develop-mongodb-nodejs-part3.md) dell'esercitazione. 

In questa sezione dell'esercitazione è possibile usare Azure Cloud Shell (nel browser Internet) o l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) installata in locale.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare un account Azure Cosmos DB con il comando [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Per `<cosmosdb-name>` usare il nome account Azure Cosmos DB univoco, che deve essere univoco tra tutti i nomi account Azure Cosmos DB in Azure.
* L'impostazione `--kind MongoDB` consente ad Azure Cosmos DB di avere connessioni client MongoDB.

Il completamento del comando potrebbe richiedere un minuto o due. Al termine, la finestra del terminale visualizza le informazioni sul nuovo database. 

Dopo la creazione dell'account Azure Cosmos DB:
1. Aprire una nuova finestra del browser e passare a [https://portal.azure.com](https://portal.azure.com)
1. Fare clic sul logo di Azure Cosmos DB :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png"::: nella barra a sinistra per visualizzare tutte le istanze di Azure Cosmos DB disponibili.
1. Fare clic sull'account Azure Cosmos DB appena creato, selezionare la scheda **Panoramica** e scorrere verso il basso per visualizzare la mappa con la posizione del database. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="Screenshot che mostra la panoramica di un account Azure Cosmos DB.":::

4. Scorrere verso il basso sulla barra di spostamento a sinistra e fare clic sulla scheda **Replica i dati a livello globale** per visualizzare una mappa con le diverse aree in cui è possibile eseguire la replica. È ad esempio possibile fare clic su Australia sud-orientale o su Australia orientale e replicare i dati in Australia. Per altre informazioni sulla replica globale, vedere [Come distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md). Per il momento sarà sufficiente una sola istanza che sarà possibile replicare, se necessario.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="Screenshot che mostra un account Azure Cosmos DB con l'opzione Replica i dati a livello globale selezionata.":::

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato creato un gruppo di risorse di Azure con l'interfaccia della riga di comando di Azure
> * È stato creato un account Azure Cosmos DB mediante l'interfaccia della riga di comando di Azure

È possibile passare alla parte successiva dell'esercitazione per connettere Azure Cosmos DB all'app usando Mongoose.

> [!div class="nextstepaction"]
> [Usare Mongoose per connettersi ad Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)