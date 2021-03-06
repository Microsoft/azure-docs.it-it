---
title: Gestire le identità gestite assegnate dall'utente tramite REST-Azure AD
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente per eseguire chiamate API REST.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546693"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Creare, elencare o eliminare un'identità gestita assegnata dall'utente usando le chiamate API REST

Le identità gestite per le risorse di Azure offrono ai servizi di Azure la possibilità di eseguire l'autenticazione ai servizi che supportano l'autenticazione Azure AD, senza la necessità di credenziali nel codice. 

Questo articolo spiega come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando CURL per effettuare chiamate dell'API REST.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- È possibile eseguire tutti i comandi di questo articolo nel cloud o in locale:
    - Per l'esecuzione nel cloud, usare [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Per eseguire localmente, installare [curl](https://curl.haxx.se/download.html) e l' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

## <a name="obtain-a-bearer-access-token"></a>Ottenere un token di accesso di connessione

1. Se eseguire localmente, accedere ad Azure tramite l'interfaccia della riga di comando di Azure:

    ```
    az login
    ```

1. Ottenere un token di accesso usando [AZ account Get-Access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |Descrizione  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

**Corpo della richiesta**

|Nome  |Descrizione  |
|---------|---------|
|posizione     | Obbligatorio. Percorso della risorsa.        |

## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Intestazione della richiesta  |Descrizione  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Per rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale mediante CURL, vedere [Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Intestazione della richiesta  |Descrizione  |
|---------|---------|
|*Content-Type*     | Obbligatorio. Impostare su `application/json`.        |
|*Autorizzazione*     | Obbligatorio. Impostare su un token di accesso `Bearer` valido.        |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una VM/VMSS di Azure usando CURL, vedere [Configure managed identities for Azure resources on an Azure VM using REST API calls](qs-configure-rest-vm.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure mediante chiamate dell'API REST) e [Configure managed identities for Azure resources on a virtual machine scale set using REST API calls](qs-configure-rest-vmss.md#user-assigned-managed-identity) (Configurare le identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali mediante chiamate dell'API REST).
