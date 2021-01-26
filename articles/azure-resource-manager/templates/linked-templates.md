---
title: Collegare i modelli per la distribuzione
description: Viene descritto come usare i modelli collegati in un modello di Azure Resource Manager (modello ARM) per creare una soluzione di modello modulare. Mostra come passare i valori dei parametri, specificare un file di parametri e gli URL creati in modo dinamico.
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: 7d4df67b7f69b3e58799f45ad72bd9ed68540dc2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790936"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Uso di modelli collegati e annidati nella distribuzione di risorse di Azure

Per distribuire soluzioni complesse, è possibile suddividere il modello di Azure Resource Manager (modello ARM) in molti modelli correlati e quindi distribuirli insieme tramite un modello principale. I modelli correlati possono essere file separati o la sintassi del modello incorporata nel modello principale. Questo articolo usa il termine **modello collegato** per fare riferimento a un file di modello separato a cui viene fatto riferimento tramite un collegamento del modello principale. Usa il termine **modello annidato** per fare riferimento alla sintassi del modello incorporata all'interno del modello principale.

Per le piccole e medie soluzioni, un modello singolo è più facile da comprendere e gestire. È possibile vedere tutti i valori e le risorse in un unico file. Per gli scenari avanzati, i modelli collegati consentono di suddividere la soluzione in componenti di destinazione. È possibile riutilizzare facilmente questi modelli per altri scenari.

Per un'esercitazione, vedere [esercitazione: distribuire un modello collegato](./deployment-tutorial-linked-template.md).

> [!NOTE]
> Per i modelli collegati o annidati, è possibile impostare la modalità di distribuzione solo come [incrementale](deployment-modes.md). Tuttavia, il modello principale può essere distribuito in modalità completa. Se si distribuisce il modello principale in modalità completa e il modello collegato o annidato è destinato allo stesso gruppo di risorse, le risorse distribuite nel modello collegato o annidato sono incluse nella valutazione per la distribuzione in modalità completa. La raccolta combinata di risorse distribuite nel modello principale e nei modelli collegati o annidati viene confrontata con le risorse esistenti nel gruppo di risorse. Tutte le risorse non incluse in questa raccolta combinata verranno eliminate.
>
> Se il modello collegato o annidato è destinato a un gruppo di risorse diverso, la distribuzione usa la modalità incrementale.
>

## <a name="nested-template"></a>Modello annidato

Per annidare un modello, aggiungere una [risorsa distribuzioni](/azure/templates/microsoft.resources/deployments) al modello principale. Nella `template` Proprietà specificare la sintassi del modello.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Nell'esempio seguente viene distribuito un account di archiviazione tramite un modello annidato.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Ambito di valutazione delle espressioni nei modelli annidati

Quando si usa un modello annidato, è possibile specificare se le espressioni del modello vengono valutate nell'ambito del modello padre o del modello annidato. L'ambito determina il modo in cui vengono risolti i parametri, le variabili e le funzioni come [resourceGroup](template-functions-resource.md#resourcegroup) e la [sottoscrizione](template-functions-resource.md#subscription) .

L'ambito viene impostato tramite la `expressionEvaluationOptions` Proprietà. Per impostazione predefinita, la `expressionEvaluationOptions` proprietà è impostata su `outer` , il che significa che usa l'ambito del modello padre. Impostare il valore su `inner` per determinare la valutazione delle espressioni nell'ambito del modello annidato.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

> [!NOTE]
>
> Quando l'ambito è impostato su `outer` , non è possibile usare la `reference` funzione nella sezione Outputs di un modello annidato per una risorsa distribuita nel modello annidato. Per restituire i valori per una risorsa distribuita in un modello annidato, usare l' `inner` ambito o convertire il modello annidato in un modello collegato.

Il modello seguente illustra come vengono risolte le espressioni di modello in base all'ambito. Contiene una variabile denominata `exampleVar` definita sia nel modello padre che nel modello annidato. Restituisce il valore della variabile.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Il valore di `exampleVar` cambia a seconda del valore della `scope` Proprietà in `expressionEvaluationOptions` . Nella tabella seguente vengono illustrati i risultati di entrambi gli ambiti.

| Ambito di valutazione | Output |
| ----- | ------ |
| interno | da modello annidato |
| Outer (o predefinito) | dal modello padre |

Nell'esempio seguente viene distribuito un server SQL e viene recuperato un segreto dell'insieme di credenziali delle chiavi da usare per la password. L'ambito è impostato su `inner` perché crea in modo dinamico l'ID dell'insieme di credenziali delle chiavi (vedere `adminPassword.reference.keyVault` nei modelli esterni `parameters` ) e lo passa come parametro al modello annidato.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Prestare attenzione quando si usano i valori dei parametri sicuri in un modello annidato. Se si imposta l'ambito su esterno, i valori sicuri vengono archiviati come testo normale nella cronologia di distribuzione. Un utente che Visualizza il modello nella cronologia di distribuzione potrebbe visualizzare i valori sicuri. Usare invece l'ambito interno o aggiungere al modello padre le risorse che necessitano di valori sicuri.

Nell'estratto di codice seguente vengono illustrati i valori sicuri e non sicuri.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>Modello collegato

Per collegare un modello, aggiungere una [risorsa distribuzioni](/azure/templates/microsoft.resources/deployments) al modello principale. Nella `templateLink` Proprietà specificare l'URI del modello da includere. Nell'esempio seguente viene collegato a un modello che si trova in un account di archiviazione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Quando si fa riferimento a un modello collegato, il valore di `uri` non può essere un file locale o un file disponibile solo nella rete locale. Azure Resource Manager deve essere in grado di accedere al modello. Fornire un valore URI scaricabile come HTTP o HTTPS.

È possibile fare riferimento ai modelli utilizzando parametri che includono HTTP o HTTPS. Ad esempio, un modello comune consiste nell'usare il `_artifactsLocation` parametro. È possibile impostare il modello collegato con un'espressione simile alla seguente:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Se si sta eseguendo il collegamento a un modello in GitHub, usare l'URL non elaborato. Il formato del collegamento è: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Per ottenere il collegamento non elaborato, selezionare **RAW**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Seleziona URL non elaborato":::

### <a name="parameters-for-linked-template"></a>Parametri per il modello collegato

È possibile specificare i parametri per il modello collegato in un file esterno o inline. Quando si specifica un file di parametri esterno, utilizzare la `parametersLink` proprietà:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Per passare i valori dei parametri inline, utilizzare la `parameters` Proprietà.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

Non è possibile usare i parametri inline e un collegamento a un file di parametri. La distribuzione ha esito negativo con un errore quando vengono specificati sia `parametersLink` che `parameters`.

## <a name="template-specs"></a>Specifiche dei modelli

Anziché gestire i modelli collegati in un endpoint accessibile, è possibile creare una [specifica del modello](template-specs.md) che inserisce il modello principale e i relativi modelli collegati in una singola entità che è possibile distribuire. La specifica del modello è una risorsa nella sottoscrizione di Azure. Consente di condividere in modo sicuro il modello con gli utenti dell'organizzazione. Usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere l'accesso alla specifica del modello. Questa funzionalità è attualmente disponibile in anteprima.

Per altre informazioni, vedere:

- [Esercitazione: creare una specifica di modello con i modelli collegati](./template-specs-create-linked.md).
- [Esercitazione: distribuire una specifica di modello come modello collegato](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Dependencies

Come per gli altri tipi di risorse, è possibile impostare le dipendenze tra i modelli collegati. Se le risorse in un modello collegato devono essere distribuite prima delle risorse in un secondo modello collegato, impostare il secondo modello dipendente dalla prima.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

Non è necessario specificare la `contentVersion` proprietà per la `templateLink` proprietà o `parametersLink` . Se non si specifica un oggetto `contentVersion` , viene distribuita la versione corrente del modello. Se si specifica un valore per la versione del contenuto, questa deve corrispondere alla versione del modello collegato. In caso contrario, la distribuzione ha esito negativo con un errore.

## <a name="using-variables-to-link-templates"></a>Uso di variabili per collegare i modelli

Negli esempi precedenti sono illustrati i valori di URL hard-coded per i collegamenti del modello. Questo approccio potrebbe funzionare per un modello semplice, ma non è adatto per un set di modelli modulari di grandi dimensioni. In alternativa, è possibile creare una variabile statica contenente un URL di base per il modello principale e quindi creare dinamicamente gli URL per i modelli collegati da tale URL di base. Il vantaggio di questo approccio consiste nel fatto che è possibile spostare o creare un fork del modello in modo semplice perché è necessario modificare solo la variabile statica nel modello principale. Il modello principale passa gli URI corretti a tutto il modello scomposto.

Nell'esempio seguente viene illustrato come utilizzare un URL di base per creare due URL per i modelli collegati ( `sharedTemplateUrl` e `vmTemplateUrl` ).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

È inoltre possibile usare [deployment ()](template-functions-deployment.md#deployment) per ottenere l'URL di base per il modello corrente e usarlo per ottenere l'URL per altri modelli nello stesso percorso. Questo approccio è utile se la posizione del modello cambia o si vuole evitare la codifica di URL nel file del modello. La `templateLink` proprietà viene restituita solo in caso di collegamento a un modello remoto con un URL. Se si usa un modello locale, tale proprietà non è disponibile.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Infine, si utilizzerà la variabile nella `uri` proprietà di una `templateLink` Proprietà.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Uso di Copy

Per creare più istanze di una risorsa con un modello annidato, aggiungere l' `copy` elemento al livello della `Microsoft.Resources/deployments` risorsa. In alternativa, se l'ambito è `inner` , è possibile aggiungere la copia all'interno del modello annidato.

Il modello di esempio seguente mostra come usare `copy` con un modello annidato.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Ottenere valori dal modello collegato

Per ottenere un valore di output da un modello collegato, recuperare il valore della proprietà con una sintassi analoga a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Quando si recupera una proprietà di output da un modello collegato, il nome della proprietà non deve includere un trattino.

Gli esempi seguenti illustrano come fare riferimento a un modello collegato e recuperare un valore di output. Il modello collegato restituisce un messaggio semplice. Primo, il modello collegato:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Il modello principale distribuisce il modello collegato e ottiene il valore restituito. Si noti che fa riferimento alla risorsa di distribuzione in base al nome e usa il nome della proprietà restituito dal modello collegato.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

L'esempio seguente illustra un modello che distribuisce un indirizzo IP pubblico e restituisce l'ID risorsa della risorsa di Azure per l'indirizzo IP pubblico:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Per usare l'indirizzo IP pubblico del modello precedente quando si distribuisce un servizio di bilanciamento del carico, collegarsi al modello e dichiarare una dipendenza dalla `Microsoft.Resources/deployments` risorsa. L'indirizzo IP pubblico nel servizio di bilanciamento del carico è impostato sul valore di output del modello collegato.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Cronologia di distribuzione

Resource Manager elabora ogni modello come distribuzione distinta nella cronologia di distribuzione. Un modello principale con tre modelli collegati o annidati viene visualizzato nella cronologia di distribuzione come segue:

![Cronologia di distribuzione](./media/linked-templates/deployment-history.png)

È possibile usare queste voci distinte nella cronologia per recuperare i valori di output dopo la distribuzione. Il modello seguente crea un indirizzo IP pubblico e restituisce l'indirizzo IP:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Il modello seguente stabilisce un collegamento al modello precedente. Crea tre indirizzi IP pubblici.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Dopo la distribuzione, è possibile recuperare i valori di output con lo script di PowerShell seguente:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

In alternativa, lo script dell'interfaccia della riga di comando di Azure in una shell Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Protezione di un modello esterno

Anche se il modello collegato deve essere disponibile esternamente, non è necessario che sia pubblicamente disponibile. È possibile aggiungere il modello a un account di archiviazione privato accessibile solo al proprietario dell'account di archiviazione. Creare quindi un token di firma di accesso condiviso per consentire l'accesso durante la distribuzione. Aggiungere il token con firma di accesso condiviso all'URI del modello collegato. Anche se il token viene passato come stringa sicura, l'URI del modello collegato, incluso il token di firma di accesso condiviso, è registrato nelle operazioni di distribuzione. Per limitare l'esposizione, impostare una scadenza per il token.

È anche possibile limitare l'accesso al file dei parametri solo tramite un token di firma di accesso condiviso.

Attualmente, non è possibile collegarsi a un modello in un account di archiviazione che si trova dietro un [firewall di archiviazione di Azure](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> Anziché proteggere il modello collegato con un token di firma di accesso condiviso, provare a creare una [specifica del modello](template-specs.md). La specifica del modello archivia in modo sicuro il modello principale e i relativi modelli collegati come risorsa nella sottoscrizione di Azure. Usare il controllo degli accessi in base al ruolo di Azure per concedere l'accesso agli utenti che devono distribuire il modello.

L'esempio seguente mostra come passare un token di firma di accesso condiviso quando si stabilisce un collegamento a un modello:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In PowerShell ottenere un token per il contenitore e distribuire i modelli con i comandi seguenti. Si noti che il `containerSasToken` parametro è definito nel modello. Non è un parametro nel `New-AzResourceGroupDeployment` comando.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Per l'interfaccia della riga di comando di Azure in una shell Bash, ottenere un token per il contenitore e distribuire i modelli con il codice seguente:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti mostrano gli usi più frequenti dei modelli collegati.

|Modello principale  |Modello collegato |Descrizione  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Restituisce una stringa dal modello collegato. |
|[Load Balancer con indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Restituisce l'indirizzo IP pubblico dal modello collegato e imposta tale valore nel servizio di bilanciamento del carico. |
|[Più indirizzi IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modello collegato](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Crea diversi indirizzi IP pubblici nel modello collegato.  |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [esercitazione: distribuire un modello collegato](./deployment-tutorial-linked-template.md).
* Per informazioni sulla definizione dell'ordine di distribuzione per le risorse, vedere [definire l'ordine per la distribuzione delle risorse nei modelli ARM](define-resource-dependency.md).
* Per informazioni su come definire una risorsa, ma crearne molte istanze, vedere [iterazione delle risorse nei modelli ARM](copy-resources.md).
* Per la procedura di configurazione di un modello in un account di archiviazione e la generazione di un token di firma di accesso condiviso, vedere [distribuire risorse con modelli ARM e Azure PowerShell](deploy-powershell.md) o [distribuire risorse con modelli ARM e l'interfaccia](deploy-cli.md)della riga di comando di Azure
