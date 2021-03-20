---
title: 'Avvio rapido: Creare un registro con replica geografica - Modello di Azure Resource Manager'
description: Informazioni su come creare un registro contenitori di Azure con replica geografica usando un modello di Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91826975"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Avvio rapido: Creare un registro contenitori con replica geografica usando un modello di Resource Manager

Questo argomento di avvio rapido descrive come creare un'istanza di Registro Azure Container usando un modello di Azure Resource Manager. Il modello configura un registro [con replica geografica](container-registry-geo-replication.md) che sincronizza automaticamente il contenuto del registro in più aree di Azure. La replica geografica consente un accesso vicino alla rete per le immagini dalle distribuzioni a livello di area, offrendo al tempo stesso un'unica esperienza di gestione. È una funzionalità del livello [Premium](container-registry-skus.md) del servizio per registri.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Il modello configura un registro e una replica aggiuntiva a livello di area.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Nel modello sono definite le risorse seguenti:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : consente di creare un registro contenitori di Azure
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** : consente di creare una replica del registro contenitori

Altri esempi di modelli di Registro Azure Container sono disponibili nella [raccolta di modelli di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

 1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Selezionare o immettere i valori seguenti.

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    * **Area**: selezionare una località per il gruppo di risorse. Esempio: **Stati Uniti centrali**.
    * **Acr Name** (Nome di Registro Azure Container): accettare il nome generato per il contenitore oppure immettere un nome. Deve essere univoco a livello globale.
    * **Acr Admin User Enabled** (Utente amministratore di Registro Azure Container): accettare il valore predefinito.
    * **Posizione**: accettare la posizione generata per la replica principale del registro o immettere una posizione quale **Stati Uniti centrali**. 
    * **Acr Sku** (SKU del Registro Azure Container): accettare il valore predefinito.
    * **Acr Replica Location** (Posizione della replica di Registro Azure Container): immettere una posizione per la replica del registro usando il nome breve dell'area. Deve essere diversa dalla posizione del registro principale. Esempio: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Proprietà modello":::

1. Selezionare **Rivedi e crea**, quindi esaminare le condizioni. Se si accettano le condizioni, selezionare **Crea**.

1. Dopo la creazione del registro, si riceverà una notifica:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Notifica del portale":::

 Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Usare il portale di Azure o uno strumento quale l'interfaccia della riga di comando di Azure per esaminare le proprietà del registro contenitori.

1. Nel portare cercare Registri contenitori e selezionare il registro contenitori creato.

1. Nella pagina **Panoramica** annotare il **Server di accesso** del registro. Usare questo URI quando si usa Docker per assegnare tag ed eseguire il push di immagini nel registro. Per informazioni, vedere [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Panoramica del registro":::

1. Nella pagina **Repliche** confermare le posizioni della replica principale e della replica aggiunta tramite il modello. Se si vuole, aggiungere altre repliche in questa pagina.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Repliche del registro":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il registro e la replica del registro. A questo scopo, accedere al portale di Azure, selezionare il gruppo di risorse che contiene il registro e quindi selezionare **Elimina gruppo di risorse**.

Eliminare un gruppo di risorse

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Registro Azure Container con un modello di Resource Manager ed è stata configurata una replica del registro in un'altra posizione. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container](container-registry-tutorial-prepare-registry.md)

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere:

> [!div class="nextstepaction"]
> [Esercitazione: Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
