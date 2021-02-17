---
title: Rimuovere l'accesso a una delega
description: Informazioni su come rimuovere l'accesso alle risorse delegate a un provider di servizi per Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: c53b678ba6e37ece1bcaf2860abceb9eea980532
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555790"
---
# <a name="remove-access-to-a-delegation"></a>Rimuovere l'accesso a una delega

Quando la sottoscrizione o il gruppo di risorse di un cliente è stato delegato a un provider di servizi per [Azure Lighthouse](../overview.md), la delega può essere rimossa se necessario. Una volta rimossa la delega, l'accesso alla [gestione delle risorse delegata di Azure](../concepts/azure-delegated-resource-management.md) precedentemente concesso agli utenti nel tenant del provider di servizi non verrà più applicato.

La rimozione di una delega può essere eseguita da un utente nel tenant del cliente o del provider di servizi, purché l'utente disponga delle autorizzazioni appropriate.

> [!TIP]
> Sebbene si faccia riferimento ai provider di servizi e ai clienti in questo argomento, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono utilizzare gli stessi processi.

## <a name="customers"></a>Clienti

Gli utenti del tenant del cliente che hanno un ruolo con l' `Microsoft.Authorization/roleAssignments/write` autorizzazione, ad esempio [owner](../../role-based-access-control/built-in-roles.md#owner), possono rimuovere l'accesso del provider di servizi alla sottoscrizione (o ai gruppi di risorse nella sottoscrizione). A tale scopo, l'utente può accedere alla [pagina dei provider di servizi](view-manage-service-providers.md#add-or-remove-service-provider-offers) della portale di Azure, trovare l'offerta nella schermata **offerte del provider di servizi** e selezionare l'icona del cestino nella riga relativa all'offerta.

Dopo la conferma dell'eliminazione, nessun utente nel tenant del provider di servizi sarà in grado di accedere alle risorse precedentemente Delegate.

## <a name="service-providers"></a>Provider di servizi

Gli utenti di un tenant di gestione possono rimuovere l'accesso alle risorse delegate se sono stati concessi il [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) per le risorse del cliente. Se questo ruolo non è stato assegnato ad alcun utente del provider di servizi, la delega può essere rimossa solo da un utente nel tenant del cliente.

Nell'esempio seguente viene illustrata un'assegnazione che concede il **ruolo eliminazione di assegnazione di registrazione dei servizi gestiti** che può essere incluso in un file di parametri durante il [processo di onboarding](onboard-customer.md):

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Questo ruolo può essere selezionato anche in un' **autorizzazione** quando si [Crea un'offerta di servizio gestita per la](../../marketplace/plan-managed-service-offer.md) pubblicazione in Azure Marketplace.

Un utente con questa autorizzazione può rimuovere una delega in uno dei modi seguenti.

### <a name="azure-portal"></a>Portale di Azure

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **deleghe**.
3. Trovare la delega da rimuovere, quindi selezionare l'icona del cestino che viene visualizzata nella riga.

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

- Informazioni sulla [gestione risorse delegate di Azure](../concepts/azure-delegated-resource-management.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
- Informazioni su come [aggiornare una delega precedente](update-delegation.md).
