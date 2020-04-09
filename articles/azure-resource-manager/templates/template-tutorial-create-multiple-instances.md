---
title: Creare più istanze di risorse
description: Informazioni su come creare un modello di Azure Resource Manager per creare più istanze di risorse di Azure.
author: mumian
ms.date: 04/08/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70c86c82cb28bf767da50cca20f7c1d052d4bf01
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982540"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Esercitazione: Creare più istanze di risorse con modelli di Azure Resource Manager

Informazioni su come eseguire un'iterazione del modello di Azure Resource Manager (ARM) per creare più istanze di una risorsa di Azure. In questa esercitazione si modifica un modello per creare tre istanze di account di archiviazione.

![Diagramma creazione di più istanze con Azure Resource Manager](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Usare Visual Studio Code per creare i modelli di Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

I [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) costituiscono un repository di modelli di Azure Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa guida introduttiva è denominato [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Creare un account di archiviazione Standard). Il modello definisce una risorsa account di archiviazione di Azure.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Nel modello è definita una risorsa "Microsoft.Storage/storageAccounts". Confrontare il modello con le [informazioni di riferimento sui modelli](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
5. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Il modello esistente crea un account di archiviazione. Personalizzare il modello per creare tre account di archiviazione.

Da Visual Studio Code, apportare le quattro modifiche seguenti:

![Creazione di più istanze con Azure Resource Manager](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Aggiungere un elemento `copy` alla definizione di risorsa dell'account di archiviazione. Nell'elemento copy si specifica il numero di iterazioni e una variabile per il ciclo. Il valore del conteggio deve essere un numero intero positivo e non può essere maggiore di 800.
2. La funzione `copyIndex()` restituisce l'iterazione attuale nel ciclo. L'indice viene usato come prefisso del nome. `copyIndex()` è in base zero. Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione copyIndex(). Ad esempio, *copyIndex(1)* .
3. Eliminare l'elemento **variabili**, in quanto non è più usato.
4. Eliminare l'elemento **output**. Non è più necessario.

Il modello completato è simile a:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Per altre informazioni sulla creazione di più istanze, vedere [Distribuire più istanze di una risorsa o proprietà nei modelli di Azure Resource Manager](./copy-resources.md).

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) della guida introduttiva di Visual Studio Code per la procedura di distribuzione.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per elencare tutti e tre gli account di archiviazione, omettere il --parametro nome:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Confrontare i nomi degli account di archiviazione con la definizione del nome nel modello.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare più istanze dell'account di archiviazione.  Nell'esercitazione successiva verrà sviluppato un modello con più risorse e più tipi di risorse. alcune con risorse dipendenti.

> [!div class="nextstepaction"]
> [Creare le risorse dipendenti](./template-tutorial-create-templates-with-dependent-resources.md)
