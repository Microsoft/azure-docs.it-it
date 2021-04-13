---
title: Creare il file di parametri
description: Creare il file di parametri per passare i valori durante la distribuzione di un modello di Azure Resource Manager
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d557bcdfe246dc2c9bfccde17b7f9590c2686358
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312043"
---
# <a name="create-resource-manager-parameter-file"></a>Creare il file di parametri di Resource Manager

Invece di passare i parametri come valori inline nello script, può risultare più facile usare un file JSON che contenga i valori dei parametri. Questo articolo illustra come creare il file di parametri.

## <a name="parameter-file"></a>File di parametri

Il file di parametri ha il formato seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Notare che i valori dei parametri sono archiviati come testo normale nel file di parametri. Questo approccio funziona per i valori non sensibili, ad esempio quando si specifica lo SKU per una risorsa. Non funziona per i valori sensibili, ad esempio le password. Se è necessario passare un valore sensibile come parametro, archiviare il valore in un insieme di credenziali delle chiavi e fare riferimento a quest'ultimo nel file di parametri. Il valore sensibile verrà recuperato in modo sicuro durante la distribuzione.

Il file di parametri seguente include un valore in testo normale e uno archiviato in un insieme di credenziali delle chiavi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Per altre informazioni sull'uso dei valori contenuti in un insieme di credenziali delle chiavi, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definire i valori dei parametri

Per comprendere come definire i valori dei parametri aprire il modello che si sta distribuendo. Esaminare la sezione del modello relativa ai parametri. L'esempio seguente illustra i parametri di un modello.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Il primo dettaglio da osservare è il nome di ogni parametro. I valori presenti nel file di parametri devono corrispondere ai nomi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Osservare il tipo di parametro. I valori presenti nel file di parametri devono avere gli stessi tipi. Per questo modello, è possibile specificare entrambi i parametri come stringhe.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Cercare quindi un valore predefinito. Se un parametro ha un valore predefinito, è possibile specificare un valore, ma non è obbligatorio.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Esaminare infine i valori consentiti ed eventuali restrizioni come la lunghezza massima. Questi indicano l'intervallo di valori che è possibile specificare per il parametro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

Il file dei parametri può contenere solo valori per i parametri definiti nel modello. Se il file di parametri contiene parametri aggiuntivi che non corrispondono a parametri nel modello, viene visualizzato un errore.

## <a name="parameter-type-formats"></a>Formati dei tipi di parametro

L'esempio seguente illustra i formati di diversi tipi di parametro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>Distribuire un modello con un file di parametri

Per passare un file di parametri locale con l'interfaccia della riga di comando di Azure, usare @ e il nome del file di parametri.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Per altre informazioni, vedere [distribuire le risorse con i modelli ARM e l'interfaccia](./deploy-cli.md#parameters)della riga di comando di Azure.

Per passare un file di parametri locale con Azure PowerShell, usare il `TemplateParameterFile` parametro.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Per altre informazioni, vedere [distribuire le risorse con i modelli ARM e Azure PowerShell](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> Non è possibile usare un file di parametri con il pannello modello personalizzato nel portale.

Se si usa il [progetto gruppo di risorse di Azure in Visual Studio](create-visual-studio-deployment-project.md), assicurarsi che l' **azione di compilazione** del file di parametri sia impostata su **contenuto**.

## <a name="file-name"></a>Nome file

La convenzione generale per la denominazione del file di parametri è l'aggiunta di **.parameters** al nome del modello. Ad esempio, se il modello è denominato **azuredeploy.json**, il file di parametri sarà denominato **azuredeploy.parameters.json**. Questa convenzione di denominazione consente di vedere la connessione tra il modello e i parametri.

Per la distribuzione in ambienti diversi, creare più di un file di parametri. Quando si denomina il file di parametri, identificarne l'uso in qualche modo. Ad esempio, usare **azuredeploy.parameters-dev.json** e **azuredeploy.parameters-prod.json**.

## <a name="parameter-precedence"></a>Precedenza dei parametri

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

È possibile usare un file di parametri esterno specificando l'URI per il file. Quando si usa un file di parametri esterno, non è possibile passare altri valori inline o da un file locale. Tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno.

## <a name="parameter-name-conflicts"></a>Conflitti nei nomi di parametro

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, PowerShell aggiunge al parametro del modello il suffisso **FromTemplate**. Ad esempio, un parametro denominato **ResourceGroupName** nel modello sarà in conflitto con il parametro **ResourceGroupName** nel cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Verrà quindi richiesto di fornire un valore per **ResourceGroupNameFromTemplate**. È possibile evitare questa confusione usando nomi di parametro non usati per i comandi di distribuzione.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come definire i parametri nel modello, vedere [Parametri nei modelli di Azure Resource Manager](template-parameters.md).
- Per altre informazioni sull'uso dei valori contenuti in un insieme di credenziali delle chiavi, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](key-vault-parameter.md).
- Per altre informazioni sui parametri, vedere [Parametri nei modelli di Azure Resource Manager](template-parameters.md).
