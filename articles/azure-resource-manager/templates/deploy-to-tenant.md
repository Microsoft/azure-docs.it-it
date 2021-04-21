---
title: Distribuire le risorse nel tenant
description: Descrive come distribuire le risorse nell'ambito del tenant in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781902"
---
# <a name="tenant-deployments-with-arm-templates"></a>Distribuzioni di tenant con modelli arm

Con la maturità dell'organizzazione, potrebbe [](../../governance/policy/overview.md) essere necessario definire e assegnare criteri o il controllo degli accessi in base al ruolo [di Azure](../../role-based-access-control/overview.md) nell'Azure AD tenant. Con i modelli a livello di tenant, è possibile applicare i criteri in modo dichiarativo e assegnare ruoli a livello globale.

## <a name="supported-resources"></a>Risorse supportate

Non tutti i tipi di risorse possono essere distribuiti a livello di tenant. Questa sezione elenca i tipi di risorse supportati.

Per Criteri di Azure, usare:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Per il controllo degli accessi in base al ruolo di Azure, usare:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Per i modelli annidati che vengono distribuiti in gruppi di gestione, sottoscrizioni o gruppi di risorse, usare:

* [Distribuzioni](/azure/templates/microsoft.resources/deployments)

Per la creazione di gruppi di gestione, usare:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Per la creazione di sottoscrizioni, usare:

* [Alias](/azure/templates/microsoft.subscription/aliases)

Per la gestione dei costi, usare:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [Istruzioni](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Per configurare il portale, usare:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schema

Lo schema usato per le distribuzioni a livello di tenant è diverso rispetto allo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
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

## <a name="required-access"></a>Accesso obbligatorio

L'entità di sicurezza che distribuisce il modello deve avere le autorizzazioni per creare le risorse nell'ambito del tenant. L'entità deve disporre dell'autorizzazione per eseguire le azioni di distribuzione (`Microsoft.Resources/deployments/*`) e per creare le risorse definite nel modello. Per creare un gruppo di gestione, ad esempio, l'entità di sicurezza deve disporre dell'autorizzazione come collaboratore nell'ambito del tenant. Per creare assegnazioni di ruolo, l'entità di sicurezza deve disporre dell'autorizzazione come proprietario.

L'amministratore globale di Azure Active Directory non dispone automaticamente dell'autorizzazione per assegnare ruoli. Per abilitare le distribuzioni di modelli nell'ambito del tenant, l'amministratore globale deve seguire questa procedura:

1. Elevare i privilegi di accesso dell'account in modo che l'amministratore globale possa assegnare ruoli. Per altre informazioni, vedere [Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Assegnare un proprietario o un collaboratore all'entità di sicurezza che deve distribuire i modelli.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

L'entità di sicurezza ha ora le autorizzazioni necessarie per distribuire il modello.

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni a livello di tenant sono diversi rispetto ai comandi per le distribuzioni di gruppi di risorse.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure usare [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per Azure PowerShell usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Per informazioni più dettagliate sui comandi di distribuzione e sulle opzioni per la distribuzione di modelli arm, vedere:

* [Distribuire risorse con modelli e portale di Azure](deploy-portal.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md)
* [Distribuire risorse con i modelli arm e Azure Resource Manager'API REST](deploy-rest.md)
* [Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub](deploy-to-azure-button.md)
* [Distribuire modelli di Arm da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di tenant, è necessario specificare un percorso di distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione. [Anche le](deploy-to-subscription.md) [distribuzioni di sottoscrizioni](deploy-to-management-group.md) e gruppi di gestione richiedono un percorso. Per [le distribuzioni](deploy-to-resource-group.md) di gruppi di risorse, la posizione del gruppo di risorse viene usata per archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato _azuredeploy.json_ crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Ad esempio, se si crea una distribuzione tenant con il nome **deployment1** in **centralus**, non è possibile creare in un secondo momento un'altra distribuzione con il nome **deployment1** ma la località **westus**. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un tenant, è possibile distribuire le risorse in:

* tenant
* gruppi di gestione all'interno del tenant
* subscriptions
* gruppi di risorse

[L'ambito di](scope-extension-resources.md) una risorsa di estensione può essere una destinazione diversa dalla destinazione di distribuzione.

L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Questa sezione illustra come specificare ambiti diversi. È possibile combinare questi ambiti diversi in un singolo modello.

### <a name="scope-to-tenant"></a>Ambito del tenant

Le risorse definite all'interno della sezione resources del modello vengono applicate al tenant.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Ambito del gruppo di gestione

Per impostare come destinazione un gruppo di gestione all'interno del tenant, aggiungere una distribuzione annidata e specificare la `scope` proprietà .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Ambito della sottoscrizione

È anche possibile specificare come destinazione le sottoscrizioni all'interno del tenant. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per impostare come destinazione una sottoscrizione all'interno del tenant, usare una distribuzione annidata e la `subscriptionId` proprietà .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Ambito del gruppo di risorse

È anche possibile scegliere come destinazione i gruppi di risorse all'interno del tenant. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per impostare come destinazione un gruppo di risorse all'interno del tenant, usare una distribuzione annidata. Impostare le proprietà `subscriptionId` e `resourceGroup`. Non impostare un percorso per la distribuzione annidata perché viene distribuito nel percorso del gruppo di risorse.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Creare un gruppo di gestione

Il modello seguente crea un gruppo di gestione.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Se l'account non dispone dell'autorizzazione per la distribuzione nel tenant, è comunque possibile creare gruppi di gestione distribuendo in un altro ambito. Per altre informazioni, vedere [Gruppo di gestione](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Assegnare il ruolo

Il modello seguente assegna un ruolo nell'ambito del tenant.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'assegnazione di ruoli, vedere [Aggiungere assegnazioni di ruolo di Azure usando Azure Resource Manager modelli.](../../role-based-access-control/role-assignments-template.md)
* È anche possibile distribuire modelli a [livello di sottoscrizione](deploy-to-subscription.md) e a [livello di gruppo di gestione](deploy-to-management-group.md).
