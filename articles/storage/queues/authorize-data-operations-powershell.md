---
title: Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati della coda
titleSuffix: Azure Storage
description: PowerShell supporta l'accesso con le credenziali Azure AD per eseguire comandi nei dati di archiviazione code di Azure. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo di Azure assegnato alla Azure AD entità di sicurezza.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 61bcf7abca2860078bd89da070309a0057360f0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370224"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati della coda

Archiviazione di Azure fornisce estensioni per PowerShell che consentono di accedere ed eseguire comandi di script con le credenziali di Azure Active Directory (Azure AD). Quando si accede a PowerShell con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente da PowerShell per autorizzare le operazioni sui dati successive sull'archiviazione code. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per accodare i dati a un'entità di sicurezza Azure AD tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Per altre informazioni sui ruoli di Azure in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC di Azure](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni di archiviazione di Azure sono supportate per le operazioni sui dati della coda. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede a PowerShell. Le autorizzazioni per le code vengono assegnate tramite RBAC di Azure. Se ad esempio è stato assegnato il ruolo **lettore dati coda** , è possibile eseguire i comandi di scripting per leggere i dati da una coda. Se è stato assegnato il ruolo di **collaboratore dei dati della coda** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di una coda o dei dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in una coda, vedere [chiamare le operazioni di archiviazione con token OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> Quando un account di archiviazione è bloccato con un blocco Azure Resource Manager **ReadOnly** , l'operazione [list keys](/rest/api/storagerp/storageaccounts/listkeys) non è consentita per l'account di archiviazione. L' **elenco delle chiavi** è un'operazione post e tutte le operazioni post vengono impedite quando si configura un blocco **ReadOnly** per l'account. Per questo motivo, quando l'account è bloccato con un blocco **ReadOnly** , gli utenti che non dispongono già delle chiavi dell'account devono usare Azure ad credenziali per accedere ai dati della coda. In PowerShell includere il `-UseConnectedAccount` parametro per creare un oggetto **AzureStorageContext** con le credenziali Azure ad.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chiamare i comandi di PowerShell usando credenziali Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per usare Azure PowerShell per accedere ed eseguire operazioni successive sull'archiviazione di Azure usando Azure AD credenziali, creare un contesto di archiviazione per fare riferimento all'account di archiviazione e includere il `-UseConnectedAccount` parametro.

L'esempio seguente illustra come creare una coda in un nuovo account di archiviazione da Azure PowerShell usando le credenziali Azure AD. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

1. Accedere al proprio account Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Per altre informazioni sull'accesso ad Azure con PowerShell, vedere [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Per creare un gruppo di risorse di Azure, chiamare [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Creare un account di archiviazione chiamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Ottenere il contesto dell'account di archiviazione che specifica il nuovo account di archiviazione chiamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Quando si agisce su un account di archiviazione, è possibile fare riferimento al contesto anziché passare ripetutamente le credenziali. Includere il `-UseConnectedAccount` parametro per chiamare qualsiasi operazione di dati successiva usando le credenziali Azure ad:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Prima di creare la coda, assegnare il ruolo di [collaboratore dati della coda di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli di Azure, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

1. Creare una coda chiamando [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue). Poiché questa chiamata usa il contesto creato nei passaggi precedenti, la coda viene creata usando le credenziali Azure AD.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-powershell.md)
- [Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md)
