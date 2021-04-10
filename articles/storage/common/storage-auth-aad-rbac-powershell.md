---
title: Usare PowerShell per assegnare un ruolo di Azure per l'accesso ai dati
titleSuffix: Azure Storage
description: Informazioni su come usare il modulo Azure PowerShell per assegnare autorizzazioni a un'entità di sicurezza Azure Active Directory con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Archiviazione di Azure supporta i ruoli predefiniti e personalizzati di Azure per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e4fea52f56da9f2e84746daf0121df0b4355411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375953"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Usare PowerShell per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento

Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli predefiniti di Azure che comprende i set comuni di autorizzazioni usate per accedere a contenitori o code.

Quando un ruolo di Azure viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare Azure PowerShell per elencare i ruoli predefiniti di Azure e assegnarli agli utenti. Per ulteriori informazioni sull'utilizzo di Azure PowerShell, vedere [Panoramica di Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Ruoli di Azure per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito della risorsa

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Elenca i ruoli di Azure disponibili

Per elencare i ruoli predefiniti di Azure disponibili con Azure PowerShell, usare il comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Verranno visualizzati i ruoli di dati di archiviazione di Azure predefiniti elencati insieme ad altri ruoli predefiniti per Azure:

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

Per assegnare un ruolo di Azure a un'entità di sicurezza, usare il comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Il formato del comando può variare in base all'ambito dell'assegnazione. Per eseguire il comando, è necessario assegnare un ruolo proprietario o collaboratore all'ambito corrispondente. Gli esempi seguenti illustrano come assegnare un ruolo a un utente in vari ambiti, ma è possibile usare lo stesso comando per assegnare un ruolo a un'entità di sicurezza.

> [!IMPORTANT]
> Quando si crea un account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È necessario assegnare in modo esplicito un ruolo di controllo degli accessi in base al ruolo di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.
>
> Se l'account di archiviazione è bloccato con un blocco di sola lettura Azure Resource Manager, il blocco impedisce l'assegnazione dei ruoli di controllo degli accessi in base al ruolo di Azure che hanno come ambito l'account di archiviazione o a un contenitore di dati (contenitore BLOB o coda).

### <a name="container-scope"></a>Ambito del contenitore

Per assegnare un ruolo con ambito a un contenitore, specificare una stringa contenente l'ambito del contenitore per il `--scope` parametro. L'ambito di un contenitore è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L'esempio seguente assegna il ruolo di **collaboratore dati BLOB di archiviazione** a un utente, con ambito a un contenitore denominato *Sample-Container*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Ambito della coda

Per assegnare un ruolo con ambito a una coda, specificare una stringa contenente l'ambito della coda per il `--scope` parametro. L'ambito di una coda è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Nell'esempio seguente il ruolo di **collaboratore dei dati della coda di archiviazione** viene assegnato a un utente, con ambito a una coda denominata *Sample-Queue*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Ambito dell'account di archiviazione

Per assegnare un ruolo nell'ambito dell'account di archiviazione, specificare l'ambito della risorsa dell'account di archiviazione per il `--scope` parametro. L'ambito di un account di archiviazione è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Nell'esempio seguente viene illustrato come definire l'ambito del ruolo **lettore dati BLOB di archiviazione** per un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Gruppo di risorse

Per assegnare un ruolo con ambito al gruppo di risorse, specificare il nome o l'ID del gruppo di risorse per il `--resource-group` parametro. L'esempio seguente assegna il ruolo di **lettore dati della coda di archiviazione** a un utente a livello del gruppo di risorse. Assicurarsi di sostituire i valori di esempio e i segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Ambito della sottoscrizione

Per assegnare un ruolo con ambito alla sottoscrizione, specificare l'ambito per la sottoscrizione per il `--scope` parametro. L'ambito di una sottoscrizione è nel formato seguente:

```
/subscriptions/<subscription>
```

Nell'esempio seguente viene illustrato come assegnare il ruolo **lettore dati BLOB di archiviazione** a un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite il modulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e della coda](storage-auth-aad-rbac-cli.md)
- [Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di accodamento](storage-auth-aad-rbac-portal.md)
