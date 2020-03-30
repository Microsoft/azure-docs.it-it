---
title: Funzioni modello - distribuzione
description: Informazioni sulle funzioni che è possibile usare in un modello di Azure Resource Manager per recuperare informazioni relative alla distribuzione.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156328"
---
# <a name="deployment-functions-for-arm-templates"></a>Funzioni di distribuzione per i modelli ARM 

Resource Manager offre le funzioni seguenti per ottenere i valori correlati alla distribuzione corrente del modello di Azure Resource Manager (ARM):

* [Distribuzione](#deployment)
* [Ambiente](#environment)
* [Parametri](#parameters)
* [Variabili](#variables)

Per ottenere valori da risorse, gruppi di risorse o sottoscrizioni, vedere [Funzioni delle risorse](template-functions-resource.md).

## <a name="deployment"></a>distribuzione

`deployment()`

Restituisce informazioni sull'operazione di distribuzione corrente.

### <a name="return-value"></a>Valore restituito

Questa funzione restituisce l'oggetto che viene passato durante la distribuzione. Le proprietà nell'oggetto restituito variano a seconda che l'oggetto di distribuzione venga passato come un collegamento o come un oggetto inline. Quando l'oggetto di distribuzione viene passato inline, ad esempio quando si usa il parametro **-TemplateFile** in Azure PowerShell per puntare a un file locale, l'oggetto restituito è nel formato seguente:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando l'oggetto viene passato come collegamento, come quando si usa il parametro **-TemplateUri** per puntare a un oggetto remoto, l'oggetto viene restituito nel formato seguente: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando si [distribuisce in una sottoscrizione di Azure](deploy-to-subscription.md) anziché in un gruppo di risorse, l'oggetto restituito include una proprietà `location`. La proprietà location è inclusa quando si distribuisce un modello locale o un modello esterno.

### <a name="remarks"></a>Osservazioni

È possibile usare deployment() per il collegamento a un altro modello in base all'URI del modello padre.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Se si ridistribuisce un modello dalla cronologia di distribuzione nel portale, il modello viene distribuito come file locale. La proprietà `templateLink` non viene restituita nella funzione di distribuzione. Se il modello si basa su `templateLink` per costruire un collegamento a un altro modello, non usare il portale per la ridistribuzione. Usare invece i comandi usati in origine per distribuire il modello.

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) seguente restituisce l'oggetto di distribuzione:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

L'esempio precedente restituisce l'oggetto seguente:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Per un modello a livello di sottoscrizione che usa la funzione di distribuzione, vedere la [funzione di distribuzione per sottoscrizione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). La distribuzione si esegue con i comandi `az deployment create` o `New-AzDeployment`.

## <a name="environment"></a>environment

`environment()`

Restituisce informazioni sull'ambiente di Azure usato per la distribuzione.

### <a name="return-value"></a>Valore restituito

Questa funzione restituisce le proprietà per l'ambiente Azure corrente. L'esempio seguente mostra le proprietà di Azure globale. I cloud sovrani possono restituire proprietà leggermente diverse.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Esempio

Il modello di esempio seguente restituisce l'oggetto ambiente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

The preceding example returns the following object when deployed to global Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parametri

`parameters(parameterName)`

Restituisce un valore di parametro. Il nome del parametro specificato deve essere definito nella sezione parameters del modello.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| parameterName |Sì |string |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore del parametro specificato.

### <a name="remarks"></a>Osservazioni

Per impostare i valori delle risorse, si usano in genere i parametri. Nell'esempio seguente il nome del sito Web viene impostato sul valore del parametro passato durante la distribuzione.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) seguente mostra un uso semplificato della funzione parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| stringOutput | string | option 1 |
| intOutput | Int | 1 |
| objectOutput | Oggetto | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | string | option 1 |

Per altre informazioni sull'uso dei parametri, vedere Modello Parametri in Azure Resource Manager.For more information about using parameters, see [Parameters in Azure Resource Manager template.](template-parameters.md)

## <a name="variables"></a>variables

`variables(variableName)`

Restituisce il valore della variabile. Il nome della variabile specificato deve essere definito nella sezione variables del modello.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| variableName |Sì |string |Nome della variabile da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore della variabile specificata.

### <a name="remarks"></a>Osservazioni

Per semplificare il modello creando valori complessi una sola volta, si usano in genere le variabili. Nell'esempio seguente viene creato un nome univoco per un account di archiviazione.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Esempio

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) seguente restituisce valori di variabile diversi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| exampleOutput1 | string | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | string | myVariable |
| exampleOutput4 |  Oggetto | {"property1": "value1", "property2": "value2"} |

Per altre informazioni sull'uso delle variabili, vedere [Variabili nel modello di Azure Resource Manager.For](template-variables.md)more information about using variables, see Variables in Azure Resource Manager template .

## <a name="next-steps"></a>Passaggi successivi
* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere Creazione di modelli di [Azure Resource Manager.](template-syntax.md)
* Per unire più modelli, vedere [Uso di modelli collegati con Azure Resource Manager](linked-templates.md).
* Per scorrere un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Azure Resource Manager.To](copy-resources.md)iterate a specified number of times when creating a type of resource, see Create multiple instances of resources in Azure Resource Manager.
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](deploy-powershell.md).

