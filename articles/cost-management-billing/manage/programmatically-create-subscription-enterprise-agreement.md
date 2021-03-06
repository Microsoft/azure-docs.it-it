---
title: Creare sottoscrizioni con contratto Enterprise di Azure a livello di codice con le API più recenti
description: Informazioni su come creare sottoscrizioni di Azure Contratto Enterprise a livello di codice usando le versioni più recenti dell'API REST, dell'interfaccia della riga di comando di Azure, Azure PowerShell e Azure Resource Manager personalizzati.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e57f385dce6446ebb3aa2df0ceb48f97a7e0c2f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877904"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Creare sottoscrizioni con contratto Enterprise di Azure a livello di codice con le API più recenti

Questo articolo illustra come creare a livello di codice sottoscrizioni con contratto Enterprise di Azure per un account di fatturazione EA usando le versioni più recenti dell'API. Se si usa ancora la versione di anteprima precedente, vedere [Creare sottoscrizioni di Azure a livello di codice con le API in anteprima](programmatically-create-subscription-preview.md). 

Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

Quando si crea una sottoscrizione di Azure a livello di codice, tale sottoscrizione è regolamentata dal contratto in base al quale l'utente ha ottenuto i servizi di Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per creare una sottoscrizione, è necessario avere un ruolo Proprietario o un account di registrazione. È possibile ottenere il ruolo in due modi:

* L'amministratore dell'organizzazione della registrazione può [configurare l'utente come proprietario dell'account](https://ea.azure.com/helpdocs/addNewAccount) (accesso obbligatorio), in modo che diventi un proprietario dell'account di registrazione.
* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Analogamente, per usare un'entità servizio per creare la sottoscrizione con Contratto Enterprise, è necessario [concedere a tale entità servizio la possibilità di creare sottoscrizioni](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).  
    Se si usa un nome SPN per creare sottoscrizioni, usare l'ObjectId della registrazione dell'applicazione Azure AD come ObjectId dell'entità servizio [usando Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) o l'interfaccia della riga di [comando di Azure.](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list)
  > [!NOTE]
  > Assicurarsi di usare la versione corretta dell'API per concedere le autorizzazioni di proprietario dell'account di registrazione. Per questo articolo e per le API documentate, usare l'API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Se si esegue la migrazione per usare le API più recenti, è necessario concedere nuovamente l'autorizzazione di proprietario usando [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). La configurazione precedente eseguita con la [versione 2015-07-01](grant-access-to-create-subscription.md) non viene convertita automaticamente per l'uso con le API più recenti.

## <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo l'aggiunta dell'utente a un account di registrazione associato a un proprietario dell'account, Azure usa la relazione tra account e registrazione per determinare il destinatario degli addebiti della sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione con Contratto Enterprise in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione.

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

### <a name="rest"></a>[REST](#tab/rest)

Richiesta per ottenere l'elenco di tutti gli account di registrazione a cui si può accedere:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

La risposta dell'API elenca tutti gli account di registrazione a cui si può accedere:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

I valori per un ambito di fatturazione `id` e sono uguali. L'`id` dell'account di registrazione è l'ambito di fatturazione in cui è stata iniziata la richiesta di sottoscrizione. È importante conoscere l'ID in quanto è un parametro obbligatorio che verrà usato più avanti nell'articolo per creare una sottoscrizione.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere questo valore, usare l'interfaccia della riga di comando di Azure o l'API REST.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Richiesta per ottenere l'elenco di tutti gli account di registrazione a cui si può accedere:

```azurecli-interactive
> az billing account list
```

La risposta elenca tutti gli account di registrazione a cui si ha accesso

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

I valori per un ambito di fatturazione `id` e sono uguali. L'`id` dell'account di registrazione è l'ambito di fatturazione in cui è stata iniziata la richiesta di sottoscrizione. È importante conoscere l'ID in quanto è un parametro obbligatorio che verrà usato più avanti nell'articolo per creare una sottoscrizione.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

L'esempio seguente crea una sottoscrizione denominata *Dev Team Subscription* nell'account di registrazione selezionato nel passaggio precedente. 

### <a name="rest"></a>[REST](#tab/rest)

Chiamare l'API PUT per creare una richiesta/un alias di creazione della sottoscrizione.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

Nel corpo della richiesta fornire come `billingScope` il valore di `id` da uno di `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

I valori consentiti per `Workload` sono `Production` e `DevTest`.

#### <a name="response"></a>Risposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

È possibile eseguire un'operazione GET nello stesso URL per ottenere lo stato della richiesta.

### <a name="request"></a>Richiesta

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Risposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Uno stato in corso viene restituito come stato `Accepted` in `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per installare la versione più recente del modulo che contiene il cmdlet `New-AzSubscriptionAlias`, eseguire `Install-Module Az.Subscription`. Per installare una versione recente di PowerShellGet, vedere [Ottenere il modulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Eseguire il comando [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) seguente, usando l'ambito di fatturazione `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

Si ottiene il valore subscriptionId come parte della risposta dal comando.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Installare prima di tutto l'estensione eseguendo `az extension add --name account` e `az extension add --name alias`.

Eseguire il comando [az account alias create](/cli/azure/account/alias#az_account_alias_create) seguente e fornire `billing-scope` e `id` da uno di `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Si ottiene il valore subscriptionId come parte della risposta dal comando.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>Usare un modello di Azure Resource Manager

La sezione precedente ha illustrato come creare una sottoscrizione con PowerShell, l'interfaccia della riga di comando o l'API REST. Se è necessario automatizzare la creazione di sottoscrizioni, è consigliabile usare un modello di Azure Resource Manager (modello arm).

Il modello seguente crea una sottoscrizione. Per `billingScope` , specificare l'ID dell'account di registrazione. Per `targetManagementGroup` , specificare il gruppo di gestione in cui si vuole creare la sottoscrizione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Distribuire il modello a livello [di gruppo di gestione.](../../azure-resource-manager/templates/deploy-to-management-group.md)

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Con un corpo della richiesta:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando l'API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 2000 sottoscrizioni per account di registrazione. Dopo tale limite, sarà possibile creare altre sottoscrizioni per l'account nel portale di Azure. Per creare più sottoscrizioni tramite l'API, creare un altro account di registrazione. Le sottoscrizioni annullate, eliminate e trasferite vengono conteggiate ai fini del limite di 2000.
- Gli utenti che non sono proprietari dell'account, ma che sono stati aggiunti a un account di registrazione tramite Controllo degli accessi in base al ruolo di Azure, non possono creare sottoscrizioni nel portale di Azure.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Passaggi successivi

* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni sulla gestione di un numero elevato di sottoscrizioni mediante i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](../../governance/management-groups/overview.md).
* Per modificare il gruppo di gestione per una sottoscrizione, vedere [Spostare le sottoscrizioni.](../../governance/management-groups/manage.md#move-subscriptions)
