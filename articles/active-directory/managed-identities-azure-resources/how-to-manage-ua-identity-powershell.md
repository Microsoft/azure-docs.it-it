---
title: Creare, elencare & eliminare l'identità gestita assegnata dall'utente usando Azure PowerShell-Azure AD
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente con Azure PowerShell.
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
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548223"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Creare, elencare o eliminare un'identità gestita assegnata dall'utente usando Azure PowerShell

Le identità gestite per le risorse di Azure offrono servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md), che è possibile aprire usando il pulsante **Prova** nell'angolo in alto a destra dei blocchi di codice.
    - Eseguire gli script in locale con Azure PowerShell, come descritto nella sezione successiva.

### <a name="configure-azure-powershell-locally"></a>Configurare Azure PowerShell in locale

Per usare Azure PowerShell in locale per questo articolo (invece di usare Cloud Shell), completare i passaggi seguenti:

1. Installare [la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps), se non è già installata.

1. Accedere ad Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installare la [versione più recente di PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Potrebbe essere necessario uscire (`Exit`) dalla sessione corrente di PowerShell dopo aver eseguito questo comando per il passaggio successivo.

1. Installare la versione non definitiva del modulo `Az.ManagedServiceIdentity` per eseguire le operazioni di identità gestite assegnate dall'utente in questo articolo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per creare un'identità gestita assegnata dall'utente, usare il comando `New-AzUserAssignedIdentity`. Il parametro `ResourceGroupName` specifica il gruppo di risorse in cui creare l'identità gestita assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare il comando [Get-AzUserAssigned].  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità gestita assegnata dall'utente. Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Nella risposta, le identità gestite assegnate dall'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente, usare il comando `Remove-AzUserAssignedIdentity`.  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Le assegnazioni delle identità devono essere rimosse separatamente.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo e altri dettagli sui comandi delle identità gestite di Azure PowerShell per le risorse di Azure, vedere [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
