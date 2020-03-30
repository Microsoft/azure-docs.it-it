---
title: Concedere l'accesso per creare sottoscrizioni di Azure EnterpriseGrant access to create Azure Enterprise subscriptions
description: Informazioni su come fornire a un utente o un'entità servizio la capacità di creare sottoscrizioni di Azure Enterprise a livello di codice.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478877"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concedere l'accesso per creare sottoscrizioni di Azure Enterprise (anteprima)

Come cliente di Azure, in [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), è possibile fornire a un altro utente o entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account. Questo articolo descrive come usare il [controllo degli accessi in base al ruolo (RBAC)](../../active-directory/role-based-access-control-configure.md) per condividere la capacità di creare sottoscrizioni e come controllare le creazioni di sottoscrizioni. È necessario disporre di un ruolo Proprietario per l'account da condividere.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Concedere l'accesso

Per creare sottoscrizioni con un account di [registrazione,](programmatically-create-subscription.md)gli utenti devono disporre del [ruolo RBAC Owner](../../role-based-access-control/built-in-roles.md#owner) per tale account. È possibile concedere a un utente o a un gruppo di utenti il ruolo Proprietario RBAC in un account di registrazione attenendosi alla procedura seguente:

1. Ottenere l'ID oggetto dell'account di registrazione a cui si desidera concedere l'accesso

    Per concedere ad altri utenti il ruolo RBAC Owner in un account di registrazione, è necessario essere il proprietario dell'account o il controllo degli accessi in base al ruolo dell'account.

    # <a name="rest"></a>[REST](#tab/rest)

    Richiesta di elencare tutti gli account di registrazione a cui si ha accesso:Request to list all enrollment accounts you have access to:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure risponde con un elenco di tutti gli account di registrazione a cui si ha accesso:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Utilizzare `principalName` la proprietà per identificare l'account a cui si desidera concedere l'accesso al proprietario del controllo degli accessi in base al ruolo. Copia `name` il tuo account. Ad esempio, se si desidera concedere SignUpEngineering@contoso.com l'accesso Proprietario RBAC ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```all'account di registrazione, copiare . Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in `enrollmentAccountObjectId`cui è possibile utilizzarlo nel passaggio successivo come .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Prova** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse sulle finestre della shell e scegliere **Incolla**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure risponde con un elenco di account di registrazione a cui si ha accesso:Azure responds with a list of enrollment accounts you have access to:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilizzare `principalName` la proprietà per identificare l'account a cui si desidera concedere l'accesso al proprietario del controllo degli accessi in base al ruolo. Copia `ObjectId` il tuo account. Ad esempio, se si desidera concedere SignUpEngineering@contoso.com l'accesso Proprietario RBAC ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```all'account di registrazione, copiare . Incollare l'ID oggetto da qualche parte in `enrollmentAccountObjectId`modo che sia possibile utilizzarlo nel passaggio successivo come file .

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

    Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Prova** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse sulle finestre della shell e scegliere **Incolla**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure risponde con un elenco di account di registrazione a cui si ha accesso:Azure responds with a list of enrollment accounts you have access to:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Utilizzare `principalName` la proprietà per identificare l'account a cui si desidera concedere l'accesso al proprietario del controllo degli accessi in base al ruolo. Copia `name` il tuo account. Ad esempio, se si desidera concedere SignUpEngineering@contoso.com l'accesso Proprietario RBAC ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```all'account di registrazione, copiare . Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in `enrollmentAccountObjectId`cui è possibile utilizzarlo nel passaggio successivo come .

1. <a id="userObjectId"></a>Ottenere l'ID oggetto dell'utente o del gruppo a cui si desidera assegnare il ruolo RBAC Owner

    1. Nel portale di Azure eseguire una ricerca in **Azure Active Directory.**
    1. Se si desidera concedere a un utente l'accesso, fare clic su **Utenti** nel menu a sinistra. Se si desidera concedere l'accesso a un gruppo, fare clic su **Gruppi**.
    1. Selezionare l'utente o il gruppo a cui si desidera assegnare il ruolo di proprietario RBAC.
    1. Se hai selezionato un utente, troverai l'ID oggetto nella pagina Profilo. Se è stato selezionato un gruppo, l'ID oggetto sarà nella pagina Panoramica. Copiare **l'ObjectID** facendo clic sull'icona a destra della casella di testo. Incollare questo da qualche parte in modo `userObjectId`che è possibile utilizzarlo nel passaggio successivo come .

1. Concedere all'utente o al gruppo il ruolo Proprietario controllo degli accessi in base al ruolo nell'account di registrazione

    Utilizzando i valori raccolti nei primi due passaggi, concedere all'utente o al gruppo il ruolo Proprietario RBAC nell'account di registrazione.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Eseguire il comando seguente, ```<enrollmentAccountObjectId>``` `name` sostituendo con l'oggetto```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```copiato nel primo passaggio ( ). Sostituire ```<userObjectId>``` con l'ID oggetto copiato dal secondo passaggio.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando il ruolo di proprietario viene assegnato correttamente nell'ambito dell'account di registrazione, Azure risponde con le informazioni dell'assegnazione del ruolo:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Eseguire il seguente comando [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) sostituendo ```<enrollmentAccountObjectId>``` con il `ObjectId` raccolto nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli-2)

    Eseguire il comando [az role assignment create](../../active-directory/role-based-access-control-manage-access-azure-cli.md) seguente, sostituendo ```<enrollmentAccountObjectId>``` con il `name` comando copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Quando un utente diventa un rbac Owner per l'account di registrazione, può [creare sottoscrizioni](programmatically-create-subscription.md) sotto di esso a livello di codice. Una sottoscrizione creata da un utente delegato ha ancora il proprietario dell'account originale come amministratore del servizio, ma ha anche l'utente delegato come proprietario del controllo degli accessi in base al ruolo per impostazione predefinita.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controllo dei creatori delle sottoscrizioni mediante i log attività

Per monitorare le sottoscrizioni create tramite questa API, usare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs). Attualmente non è possibile usare PowerShell, l'interfaccia della riga di comando o il portale di Azure per monitorare la creazione delle sottoscrizioni.

1. Come amministratore del tenant di Azure AD [elevare l'accesso](../../active-directory/role-based-access-control-tenant-admin-access.md) quindi assegnare un ruolo di lettore all'utente di controllo sull'ambito `/providers/microsoft.insights/eventtypes/management`.
1. Come utente di controllo, chiamare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs) per visualizzare le attività di creazione della sottoscrizione. Esempio:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Per chiamare correttamente questa API dalla riga di comando, provare [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passaggi successivi

* Ora che l'utente o l'entità servizio dispone delle autorizzazioni necessarie per creare una sottoscrizione, è possibile usare tale identità per [creare sottoscrizioni di Azure Enterprise a livello di codice](programmatically-create-subscription.md).
* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Per altre informazioni su Azure Resource Manager e le relative API, vedere [Panoramica di Azure Resource Manager](overview.md).
* Per altre informazioni sulla gestione di un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere Organizzare le risorse con i gruppi di gestione di [AzureTo](../../governance/management-groups/overview.md) learn more about managing large numbers of subscriptions using management groups, see Organize your resources with Azure management groups
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption-guide/subscription-governance)
