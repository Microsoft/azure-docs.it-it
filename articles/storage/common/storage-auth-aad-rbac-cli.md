---
title: Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati
titleSuffix: Azure Storage
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per assegnare autorizzazioni a un Azure Active Directory di sicurezza con il controllo degli accessi in base al ruolo di Azure. Archiviazione di Azure supporta i ruoli predefiniti e personalizzati di Azure per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785326"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati di BLOB e code

Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il controllo degli accessi in base al ruolo di [Azure.](../../role-based-access-control/overview.md) Archiviazione di Azure definisce un set di ruoli predefiniti di Azure che includono set comuni di autorizzazioni usate per accedere ai dati di BLOB o code.

Quando un ruolo di Azure viene assegnato a un'Azure AD di sicurezza, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per elencare i ruoli predefiniti di Azure e assegnarli agli utenti. Per altre informazioni sull'uso dell'interfaccia della riga di comando di [Azure, vedere Interfaccia Command-Line di Azure](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Ruoli di Azure per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito delle risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Elencare i ruoli di Azure disponibili

Per elencare i ruoli predefiniti di Azure disponibili con l'interfaccia della riga di comando di Azure, usare [il comando az role definition list:](/cli/azure/role/definition#az_role_definition_list)

```azurecli-interactive
az role definition list --out table
```

Verranno elencati i ruoli Archiviazione di Azure dati predefiniti, insieme ad altri ruoli predefiniti per Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Assegnare un ruolo di Azure a un'entità di sicurezza

Per assegnare un ruolo di Azure a un'entità di sicurezza, usare [il comando az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create) Il formato del comando può variare in base all'ambito dell'assegnazione. Gli esempi seguenti illustrano come assegnare un ruolo a un utente in vari ambiti, ma è possibile usare lo stesso comando per assegnare un ruolo a qualsiasi entità di sicurezza.

> [!IMPORTANT]
> Quando si crea un account Archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È necessario assegnare in modo esplicito un ruolo controllo degli accessi in base al ruolo di Azure per l'accesso ai dati. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
>
> Se l'account di archiviazione è bloccato con un blocco Azure Resource Manager sola lettura, il blocco impedisce l'assegnazione dei ruoli del controllo degli accessi in base al ruolo di Azure con ambito all'account di archiviazione o a un contenitore di dati (contenitore BLOB o coda).

### <a name="container-scope"></a>Ambito del contenitore

Per assegnare un ruolo con ambito a un contenitore, specificare una stringa contenente l'ambito del contenitore per il `--scope` parametro . L'ambito di un contenitore è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

L'esempio seguente assegna il ruolo **Collaboratore dati** BLOB di archiviazione a un utente, con ambito al livello del contenitore. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Ambito della coda

Per assegnare un ruolo con ambito a una coda, specificare una stringa contenente l'ambito della coda per il `--scope` parametro . L'ambito di una coda è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Nell'esempio seguente il ruolo **Collaboratore dati** coda di archiviazione viene assegnato a un utente con ambito al livello della coda. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Ambito dell'account di archiviazione

Per assegnare un ruolo con ambito all'account di archiviazione, specificare l'ambito della risorsa dell'account di archiviazione per il `--scope` parametro . L'ambito di un account di archiviazione è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

L'esempio seguente illustra come assegnare il ruolo **Lettore** dati BLOB di archiviazione a un utente a livello dell'account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Gruppo di risorse

Per assegnare un ruolo con ambito al gruppo di risorse, specificare il nome o l'ID del gruppo di risorse per il `--resource-group` parametro . Nell'esempio seguente il ruolo **Lettore dati coda di** archiviazione viene assegnato a un utente a livello del gruppo di risorse. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Ambito della sottoscrizione

Per assegnare un ruolo con ambito alla sottoscrizione, specificare l'ambito per la sottoscrizione per il `--scope` parametro . L'ambito di una sottoscrizione è nel formato seguente:

```
/subscriptions/<subscription>
```

L'esempio seguente illustra come assegnare il ruolo **lettore** dei dati dei BLOB di archiviazione a un utente a livello dell'account di archiviazione. Assicurarsi di sostituire i valori di esempio con valori personalizzati: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il modulo Azure PowerShell azure](../../role-based-access-control/role-assignments-powershell.md)
- [Usare il modulo Azure PowerShell per assegnare un ruolo di Azure per l'accesso ai dati di BLOB e code](storage-auth-aad-rbac-powershell.md)
- [Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di accodamento](storage-auth-aad-rbac-portal.md)
