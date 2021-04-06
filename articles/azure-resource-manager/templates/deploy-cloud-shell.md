---
title: Distribuire modelli con Cloud Shell
description: Usare Azure Resource Manager e Azure Cloud Shell per distribuire le risorse in Azure. Le risorse sono definite in un modello di Azure Resource Manager (modello ARM).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028608"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Distribuire modelli ARM da Azure Cloud Shell

È possibile usare [Azure cloud Shell](../../cloud-shell/overview.md) per distribuire un modello di Azure Resource Manager (modello ARM). È possibile distribuire un modello ARM archiviato in remoto oppure un modello ARM archiviato nell'account di archiviazione locale per Cloud Shell.

È possibile eseguire la distribuzione in qualsiasi ambito. Questo articolo illustra la distribuzione in un gruppo di risorse.

## <a name="deploy-remote-template"></a>Distribuisci modello remoto

Per distribuire un modello esterno, specificare l'URI del modello, proprio come si farebbe per qualsiasi distribuzione esterna. Il modello esterno potrebbe trovarsi in un repository GitHub o in un account di archiviazione esterno.

1. Aprire il prompt dei Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Apri Cloud Shell":::

1. Per distribuire il modello, usare i comandi seguenti:

   # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Distribuire un modello locale

Per distribuire un modello locale, è prima necessario caricare il modello nell'account di archiviazione connesso alla sessione di Cloud Shell.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare il gruppo di risorse di Cloud Shell. Il modello del nome è `cloud-shell-storage-<region>`.

   ![Selezionare il gruppo di risorse](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Selezionare l'account di archiviazione per Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Selezionare l'account di archiviazione":::

1. Selezionare **condivisioni file**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Seleziona condivisioni file":::

1. Selezionare la condivisione file predefinita per Cloud Shell. Il formato del nome della condivisione file è `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Condivisione file predefinita":::

1. Aggiungere una nuova directory che contenga i modelli. Selezionare la directory.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Aggiungere un'istanza di Active Directory":::

1. Selezionare **Carica**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Carica modello":::

1. Trovare e caricare il modello.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Selezionare il modello":::

1. Aprire il prompt dei Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Apri Cloud Shell":::

1. Passare alla directory **CloudDrive** Passare alla directory aggiunta per contenere i modelli.

1. Per distribuire il modello, usare i comandi seguenti:

   # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui comandi di distribuzione, vedere [distribuire le risorse con i modelli ARM e l'interfaccia](deploy-cli.md) della riga di comando di Azure e [distribuire le risorse con i modelli ARM e Azure PowerShell](deploy-powershell.md).
- Per visualizzare in anteprima le modifiche prima di distribuire un modello, vedere operazione di simulazione della [distribuzione del modello ARM](template-deploy-what-if.md).
