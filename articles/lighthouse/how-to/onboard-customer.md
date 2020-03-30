---
title: Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure
description: Informazioni su come eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure, consentendo l'accesso e la gestione delle risorse tramite il proprio tenant.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6a5f4ce03f762b5903e8b3d6f10810819e02e422
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246875"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure

Questo articolo illustra come un provider di servizi può eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure, consentendo l'accesso e la gestione delle risorse delegate (sottoscrizioni e/o gruppi di risorse) tramite il proprio tenant di Azure Active Directory ( Azure AD). Mentre ci riferiremo ai fornitori di servizi e ai clienti qui, [le aziende che gestiscono più tenant](../concepts/enterprise.md) possono utilizzare lo stesso processo per consolidare la loro esperienza di gestione.

È possibile ripetere questo processo se si gestiscono risorse per più clienti. Quando un utente autorizzato accede al tenant, tale utente può quindi essere autorizzato negli ambiti di tenancy dei clienti a eseguire operazioni di gestione senza dover accedere al tenant di ogni singolo cliente.

Per tenere traccia dell'impatto sulle interazioni con i clienti e ricevere il riconoscimento, associa l'ID Microsoft Partner Network (MPN) ad almeno un account utente che abbia accesso a ogni abbonamento in servizio. Si noti che sarà necessario eseguire questa associazione nel tenant del provider di servizi. Per semplicità, è consigliabile creare un account dell'entità servizio nel tenant associato all'ID MPN e concedergli l'accesso in lettura a ogni cliente attivato. Per altre informazioni, vedere Collegare un ID partner agli account di Azure.For more info, see [Link a partner ID to your Azure accounts.](../../billing/billing-partner-admin-link-started.md) 

> [!NOTE]
> È possibile eseguire l'onboarding dei clienti anche quando acquistano un'offerta di servizi gestiti (pubblica o privata) pubblicata in Azure Marketplace. Per altre informazioni, vedere [Pubblicare offerte di servizi gestiti in Azure Marketplace](publish-managed-services-offers.md). È anche possibile usare il processo di onboarding descritto di seguito insieme a un'offerta pubblicata in Azure Marketplace.You can also use the onboarding process described here along with an offer published to Azure Marketplace.

Il processo di onboarding richiede l'esecuzione di azioni sia dal tenant del provider di servizi che dal tenant del cliente. Tutti questi passaggi sono descritti in questo articolo.

## <a name="gather-tenant-and-subscription-details"></a>Raccogliere i dettagli del tenant e della sottoscrizione

Per eseguire l'onboarding del tenant di un cliente, è necessario che abbia una sottoscrizione di Azure attiva. Sarà necessario conoscere quanto segue:

- ID del tenant del provider di servizi, in cui si gestiranno le risorse del cliente
- ID del tenant del cliente, che avrà le risorse gestite dal provider di servizi
- ID sottoscrizione per ogni sottoscrizione specifica nel tenant del cliente che verrà gestita dal provider di servizi (o che contiene i gruppi di risorse che verranno gestiti dal provider di servizi).

> [!NOTE]
> Anche se si desidera eseguire l'onboarding solo di uno o più gruppi di risorse all'interno di una sottoscrizione, la distribuzione deve essere eseguita a livello di sottoscrizione, pertanto è necessario l'ID sottoscrizione.

Se non si dispone già di questi valori ID, è possibile recuperarli in uno dei modi seguenti. Assicurarsi e usare questi valori esatti nella distribuzione.

### <a name="azure-portal"></a>Portale di Azure

L'ID del tenant può essere visualizzato passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**. Per selezionare e copiare l'ID tenant, cercare "Azure Active Directory" nel portale, quindi selezionare **Proprietà** e copiare il valore visualizzato nel campo **ID directory**. Per trovare l'ID di una sottoscrizione nel tenant del cliente, cercare "Sottoscrizioni" e quindi selezionare l'ID sottoscrizione appropriato.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Quando si esegue l'onboarding di una sottoscrizione (o di uno o più gruppi di risorse all'interno di una sottoscrizione) tramite il processo descritto in questo articolo, il provider di risorse **Microsoft.ManagedServices** verrà registrato per tale sottoscrizione.

## <a name="define-roles-and-permissions"></a>Definire ruoli e autorizzazioni

Il provider di servizi può eseguire più attività per un singolo cliente, per cui deve avere un accesso diverso per ambiti diversi. È possibile definire tutte le autorizzazioni necessarie per assegnare [ruoli predefiniti Controllo degli accessi in base al ruolo](../../role-based-access-control/built-in-roles.md) agli utenti nel tenant.

Per semplificare la gestione, è consigliabile usare i gruppi di utenti di Azure AD per ogni ruolo, che consentono di aggiungere o rimuovere singoli utenti al gruppo invece di assegnare le autorizzazioni direttamente a tale utente. È anche possibile assegnare ruoli a un'entità servizio. Assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il lavoro. Per consigli e informazioni sui ruoli supportati, vedere [Tenant, ruoli e utenti in scenari di Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo di Azure AD, il tipo di **gruppo** deve essere **Sicurezza** e non **Office 365.** Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Per definire le autorizzazioni, è necessario conoscere i valori ID per ogni utente, gruppo di utenti o entità servizio nel tenant del provider di servizi a cui si desidera concedere l'accesso. È anche necessario l'ID di definizione del ruolo per ogni ruolo predefinito che si vuole assegnare. Se non sono già presenti, è possibile recuperarli eseguendo i comandi riportati di seguito dall'interno del tenant del provider di servizi.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> È consigliabile assegnare il [ruolo di eliminazione dell'assegnazione della registrazione di servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente, in modo che gli utenti nel tenant possano [rimuovere l'accesso alla delega](#remove-access-to-a-delegation) in un secondo momento se necessario. Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

## <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per eseguire l'onboarding del cliente, sarà necessario creare un modello di [Azure Resource Manager](../../azure-resource-manager/index.yml) per l'offerta con le informazioni seguenti. I valori **mspOfferName** e **mspOfferDescription** saranno visibili al cliente durante la visualizzazione dei dettagli dell'offerta nella [pagina Provider](view-manage-service-providers.md) di servizi del portale di Azure.

|Campo  |Definizione  |
|---------|---------|
|**mspOfferName**     |Nome che descrive questa definizione. Questo valore viene visualizzato al cliente come titolo dell'offerta.         |
|**mspOfferDescription**     |Breve descrizione dell'offerta (ad esempio, "Offerta di gestione delle macchine virtuali Contoso").      |
|**managedByTenantId**     |ID tenant.          |
|**Autorizzazioni**     |I valori **principalId** per gli utenti/gruppi/SPN del tenant, ognuno con un **principalIdDisplayName** per consentire al cliente di comprendere lo scopo dell'autorizzazione ed è stato eseguito il mapping a un valore **roleDefinitionId** predefinito per specificare il livello di accesso.      |

Il processo di onboarding richiede un modello di Azure Resource Manager (fornito nel [repository degli esempi)](https://github.com/Azure/Azure-Lighthouse-samples/)e un file di parametri corrispondente che è possibile modificare in base alla configurazione e definire le autorizzazioni.

Il modello scelto dipenderà dal fatto che si stia esatuando un'intera sottoscrizione, un gruppo di risorse o più gruppi di risorse all'interno di una sottoscrizione. Viene anche fornito un modello che può essere usato per i clienti che hanno acquistato un'offerta di servizio gestito pubblicata in Azure Marketplace, se si preferisce eseguire l'onboarding delle sottoscrizioni in questo modo.

|Onboarding di  |Usare questo modello di Azure Resource Manager  |E modificare questo file dei parametri |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Più gruppi di risorse all'interno di una sottoscrizione   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Sottoscrizione (quando si usa un'offerta pubblicata in Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Il processo descritto di seguito richiede una distribuzione a livello di sottoscrizione separata per ogni sottoscrizione in fase di onboarding, anche se si esegue l'onboarding delle sottoscrizioni nello stesso tenant del cliente. Sono necessarie distribuzioni separate anche se si esegue l'onboarding di più gruppi di risorse all'interno di sottoscrizioni diverse nello stesso tenant del cliente. Tuttavia, l'onboarding di più gruppi di risorse all'interno di una singola sottoscrizione può essere eseguito in una distribuzione a livello di sottoscrizione.
>
> Le distribuzioni separate sono necessarie anche se si applicano più offerte alla stessa sottoscrizione (o gruppi di risorse all'interno di una sottoscrizione). Ogni offerta applicata deve usare un diverso **mspOfferName**.

Nell'esempio seguente viene illustrato un file **delegatedResourceManagement.parameters.json** modificato che può essere usato per eseguire l'onboarding di una sottoscrizione. I file di parametri dei gruppi di risorse (disponibili nella cartella [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) sono simili, ma includono anche un parametro**rgName** per identificare i gruppi di risorse specifici di cui eseguire l'onboarding.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

L'ultima autorizzazione nell'esempio precedente aggiunge un **principalId** con il ruolo Amministratore Accesso utenti (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Quando si assegna questo ruolo, è necessario includere la proprietà **delegatedRoleDefinitionIds** e uno o più ruoli predefiniti. L'utente creato in questa autorizzazione sarà in grado di assegnare questi ruoli predefiniti alle [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) nel tenant del cliente, operazione necessaria per [distribuire criteri che possono essere corretti.](deploy-policy-remediation.md) Nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuire i modelli di Azure Resource Manager

Dopo aver aggiornato il file dei parametri, un utente nel tenant del cliente deve distribuire il modello di Azure Resource Manager all'interno del tenant come distribuzione a livello di sottoscrizione. Per ogni sottoscrizione di cui si vuole eseguire l'onboarding nella gestione risorse delegate di Azure (o per ogni sottoscrizione che contiene i gruppi di risorse di cui si vuole eseguire l'onboarding) è necessaria una distribuzione separata.

Trattandosi di una distribuzione a livello di sottoscrizione, non è possibile avviarla nel portale di Azure. È possibile eseguire la distribuzione tramite PowerShell o l'interfaccia della riga di comando di Azure, come illustrato di seguito.

> [!IMPORTANT]
> Questa distribuzione a livello di sottoscrizione deve essere eseguita da un account non guest nel tenant del cliente che ha il [ruolo predefinito Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) per l'onboarding della sottoscrizione o che contiene i gruppi di risorse che vengono in fase di onboarding. Per visualizzare tutti gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire **Controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Verificare che l'onboarding sia riuscito

Quando l'onboarding della sottoscrizione di un cliente è stato eseguito correttamente nella gestione risorse delegate di Azure, gli utenti nel tenant del provider di servizi potranno visualizzare la sottoscrizione e le relative risorse (se è stato concesso l'accesso tramite il processo precedente, singolarmente o come membro di un gruppo Azure AD con le autorizzazioni appropriate). Per esserne certi, verificare che la sottoscrizione venga visualizzata in uno dei modi seguenti.  

### <a name="azure-portal"></a>Portale di Azure

Nel tenant del provider di servizi:

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **Clienti**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!IMPORTANT]
> Per visualizzare la sottoscrizione delegata in [Clienti personali](view-manage-customers.md), è necessario che agli utenti nel tenant del provider di servizi sia stato concesso il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che preveda l'accesso in lettura) durante l'onboarding della sottoscrizione per la gestione delle risorse delegate di Azure.

Nel tenant del cliente:

1. Passare alla [pagina Provider di servizi](view-manage-service-providers.md).
2. Selezionare **Offerte del provider di servizi**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!NOTE]
> Potrebbero essere necessari alcuni minuti dopo il completamento della distribuzione prima che gli aggiornamenti siano visibili nel portale di Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Rimuovere l'accesso a una delega

Per impostazione predefinita, gli utenti nel tenant del cliente che dispongono delle autorizzazioni appropriate possono rimuovere l'accesso del provider di servizi alle risorse delegate nella pagina Provider di servizi del portale di Azure.By default, users in the customer's tenant who have the appropriate permissions can remove service provider access to delegated resources in the [Service providers page](view-manage-service-providers.md#add-or-remove-service-provider-offers) of the Azure portal. In questo caso, nessun utente nel tenant del provider di servizi sarà in grado di accedere alle risorse delegate in precedenza.

Se sono stati caricati utenti con il ruolo di eliminazione [dell'assegnazione](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) di registrazione dei servizi gestiti durante l'onboarding di un cliente per la gestione delle risorse delegate di Azure, tali utenti saranno anche in grado di rimuovere la delega.

Nell'esempio seguente viene illustrata un'assegnazione che concede il **ruolo di eliminazione dell'assegnazione della registrazione di servizi gestiti** che può essere inclusa nel file dei parametri:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Un utente con questa autorizzazione può rimuovere una delega in uno dei modi seguenti.

### <a name="azure-portal"></a>Portale di Azure

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **Delegazioni**.
3. Trova la delega che desideri rimuovere, quindi seleziona l'icona del cestino visualizzata nella relativa riga.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
