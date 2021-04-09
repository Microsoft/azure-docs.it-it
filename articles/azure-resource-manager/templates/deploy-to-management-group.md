---
title: Distribuire le risorse al gruppo di gestione
description: Viene descritto come distribuire le risorse nell'ambito del gruppo di gestione in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dc7418d9e93fb50590c5e2502b3a3ffb3847273f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043309"
---
# <a name="management-group-deployments-with-arm-templates"></a>Distribuzioni del gruppo di gestione con i modelli ARM

Man mano che l'organizzazione è matura, è possibile distribuire un modello di Azure Resource Manager (ARM template) per creare risorse a livello di gruppo di gestione. Ad esempio, potrebbe essere necessario definire e assegnare [criteri](../../governance/policy/overview.md) o il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) per un gruppo di gestione. Con i modelli a livello di gruppo di gestione è possibile applicare i criteri in modo dichiarativo e assegnare i ruoli a livello di gruppo di gestione.

## <a name="supported-resources"></a>Risorse supportate

Non tutti i tipi di risorse possono essere distribuiti al livello del gruppo di gestione. Questa sezione elenca i tipi di risorse supportati.

Per i progetti di Azure, usare:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versioni](/azure/templates/microsoft.blueprint/blueprints/versions)

Per criteri di Azure, usare:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), usare:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Per i modelli annidati che vengono distribuiti nelle sottoscrizioni o nei gruppi di risorse, usare:

* [distribuzioni](/azure/templates/microsoft.resources/deployments)

Per la gestione delle risorse, usare:

* [tag](/azure/templates/microsoft.resources/tags)

I gruppi di gestione sono risorse a livello di tenant. Tuttavia, è possibile creare gruppi di gestione in una distribuzione del gruppo di gestione impostando l'ambito del nuovo gruppo di gestione sul tenant. Vedere [gruppo di gestione](#management-group).

## <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni di gruppi di gestione è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandi di distribuzione

Per eseguire la distribuzione in un gruppo di gestione, usare i comandi di distribuzione del gruppo di gestione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure, usare [AZ Deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per Azure PowerShell, usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Per informazioni più dettagliate sui comandi e sulle opzioni di distribuzione per la distribuzione di modelli ARM, vedere:

* [Distribuire le risorse con i modelli ARM e portale di Azure](deploy-portal.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md)
* [Distribuire le risorse con i modelli ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub](deploy-to-azure-button.md)
* [Distribuire modelli ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di gruppo di gestione, è necessario specificare un percorso per la distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione. Per le distribuzioni di [sottoscrizione](deploy-to-subscription.md) e [tenant](deploy-to-tenant.md) è necessaria anche una località. Per le distribuzioni di [gruppi di risorse](deploy-to-resource-group.md) , il percorso del gruppo di risorse viene usato per archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato _azuredeploy.json_ crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Se, ad esempio, si crea una distribuzione del gruppo di gestione con il nome **Deployment1** in **centralus**, non sarà possibile creare in un secondo momento un'altra distribuzione con il nome **Deployment1** ma un percorso di **westus**. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un gruppo di gestione, è possibile distribuire le risorse in:

* gruppo di gestione di destinazione dall'operazione
* un altro gruppo di gestione nel tenant
* sottoscrizioni nel gruppo di gestione
* gruppi di risorse nel gruppo di gestione
* tenant per il gruppo di risorse

Una [risorsa di estensione](scope-extension-resources.md) può avere come ambito una destinazione diversa dalla destinazione di distribuzione.

L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

In questa sezione viene illustrato come specificare ambiti diversi. È possibile combinare questi ambiti diversi in un singolo modello.

### <a name="scope-to-target-management-group"></a>Ambito per il gruppo di gestione di destinazione

Le risorse definite nella sezione Resources del modello vengono applicate al gruppo di gestione dal comando Deployment.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Ambito per un altro gruppo di gestione

Per fare riferimento a un altro gruppo di gestione, aggiungere una distribuzione annidata e specificare la `scope` Proprietà. Impostare la `scope` proprietà su un valore nel formato `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Ambito della sottoscrizione

È anche possibile fare riferimento a sottoscrizioni in un gruppo di gestione. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per fare riferimento a una sottoscrizione all'interno del gruppo di gestione, usare una distribuzione annidata e la `subscriptionId` Proprietà.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Ambito al gruppo di risorse

È anche possibile fare riferimento ai gruppi di risorse all'interno del gruppo di gestione. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per fare riferimento a un gruppo di risorse all'interno del gruppo di gestione, usare una distribuzione nidificata. Impostare le proprietà `subscriptionId` e `resourceGroup`. Non impostare un percorso per la distribuzione annidata perché è distribuito nella posizione del gruppo di risorse.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Per usare una distribuzione del gruppo di gestione per creare un gruppo di risorse all'interno di una sottoscrizione e distribuire un account di archiviazione a tale gruppo di risorse, vedere [distribuire a una sottoscrizione e un gruppo di risorse](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Ambito al tenant

Per creare risorse nel tenant, impostare `scope` su `/` . L'utente che distribuisce il modello deve avere l' [accesso necessario per la distribuzione nel tenant](deploy-to-tenant.md#required-access).

Per utilizzare una distribuzione annidata, impostare `scope` e `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

In alternativa, è possibile impostare l'ambito su `/` per alcuni tipi di risorse, ad esempio i gruppi di gestione. La creazione di un nuovo gruppo di gestione viene descritta nella sezione successiva.

## <a name="management-group"></a>Gruppo di gestione

Per creare un gruppo di gestione in una distribuzione del gruppo di gestione, è necessario impostare l'ambito su `/` per il gruppo di gestione.

Nell'esempio seguente viene creato un nuovo gruppo di gestione nel gruppo di gestione radice.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

Nell'esempio seguente viene creato un nuovo gruppo di gestione nel gruppo di gestione specificato come elemento padre. Si noti che l'ambito è impostato su `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Sottoscrizioni

Per usare un modello ARM per creare una nuova sottoscrizione di Azure in un gruppo di gestione, vedere:

* [Creare sottoscrizioni di Azure Contratto Enterprise a livello di codice](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Creare sottoscrizioni di Azure a livello di codice per un contratto cliente Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Creare sottoscrizioni di Azure a livello di codice per un contratto partner Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Per distribuire un modello che sposta una sottoscrizione di Azure esistente in un nuovo gruppo di gestione, vedere [spostare le sottoscrizioni nel modello ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Criteri di Azure

Le definizioni dei criteri personalizzati che vengono distribuite nel gruppo di gestione sono estensioni del gruppo di gestione. Per ottenere l'ID di una definizione di criteri personalizzata, usare la funzione [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Le definizioni dei criteri predefinite sono risorse a livello di tenant. Per ottenere l'ID di una definizione dei criteri predefinita, usare la funzione [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

Nell'esempio seguente viene illustrato come [definire](../../governance/policy/concepts/definition-structure.md) un criterio a livello di gruppo di gestione e assegnarlo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuisci nella sottoscrizione e nel gruppo di risorse

Da una distribuzione a livello di gruppo di gestione, è possibile scegliere come destinazione una sottoscrizione all'interno del gruppo di gestione. L'esempio seguente crea un gruppo di risorse all'interno di una sottoscrizione e distribuisce un account di archiviazione a tale gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione dei ruoli, vedere [aggiungere assegnazioni di ruolo di Azure usando modelli di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* È anche possibile distribuire modelli a livello di [sottoscrizione](deploy-to-subscription.md) e [tenant](deploy-to-tenant.md).
