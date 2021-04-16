---
title: 'Avvio rapido: Creare un servizio Frontdoor di Azure usando un modello di Azure Resource Manager (modello ARM)'
description: Questo argomento di avvio rapido illustra come usare un modello di Azure Resource Manager (modello ARM) per creare un servizio Frontdoor di Azure.
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539003"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Avvio rapido: Creare una risorsa Frontdoor usando un modello ARM

Questo argomento di avvio rapido illustra come usare un modello di Azure Resource Manager (modello ARM) per creare una risorsa Frontdoor in modo da configurare la disponibilità elevata per un endpoint Web.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* IP o FQDN di un sito Web o di un'applicazione Web.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

In questo argomento di avvio rapido si creerà una configurazione Frontdoor con un unico back-end e un unico percorso predefinito corrispondente a `/*`.

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Nel modello è definita una risorsa di Azure:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

    La distribuzione del modello crea una risorsa Frontdoor con un unico back-end. In questo esempio viene usato `microsoft.com` come **backendAddress**.

    Il nome del gruppo di risorse è il nome del progetto seguito da **rg**.

    > [!NOTE]
    > Per consentire la corretta distribuzione del modello, **frontDoorName** deve essere un nome univoco a livello globale. Se la distribuzione non riesce, ricominciare dal passaggio 1.

    La distribuzione del modello può richiedere alcuni minuti. Al termine, l'output sarà simile al seguente:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Modello di Resource Manager per Frontdoor: output della distribuzione con PowerShell":::

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è il nome del progetto seguito da **rg**.

1. Selezionare la risorsa Frontdoor creata in precedenza e fare clic sul collegamento dell'**host front-end**. Il collegamento aprirà un Web browser per reindirizzare l'utente al nome di dominio completo (FQDN) back-end definito durante la creazione.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Panoramica del portale Frontdoor":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il servizio Frontdoor non è più necessario, eliminare il gruppo di risorse. Oltre alla risorsa Frontdoor verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una risorsa Frontdoor.

Per informazioni su come aggiungere un dominio personalizzato alla risorsa Frontdoor, proseguire con le esercitazioni su Frontdoor.

> [!div class="nextstepaction"]
> [Esercitazioni su Frontdoor](front-door-custom-domain.md)
