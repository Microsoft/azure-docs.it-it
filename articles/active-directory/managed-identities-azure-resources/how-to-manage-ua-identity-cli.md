---
title: Gestire l'identità gestita assegnata dall'utente-interfaccia della riga di comando di Azure-Azure AD
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639769"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante l'interfaccia della riga di comando di Azure


Le identità gestite per le risorse Azure forniscono ai servizi di Azure un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. Accedere ad Azure tramite `az login`, usando un account associato alla sottoscrizione di Azure in cui si vuole distribuire l'identità gestita assegnata dall'utente.


> [!NOTE]
> Per modificare le autorizzazioni utente quando si usa un'entità servivce dell'app usando l'interfaccia della riga di comando, è necessario fornire all'entità servizio autorizzazioni aggiuntive in Azure AD API Graph come parti dell'interfaccia della riga di comando eseguono richieste GET rispetto al API Graph. In caso contrario, è possibile che si riceva un messaggio "privilegi insufficienti per completare l'operazione". A tale scopo, è necessario passare alla registrazione dell'app in Azure Active Directory, selezionare l'app, fare clic su autorizzazioni API, scorrere verso il basso e selezionare Azure Active Directory grafico. Da qui selezionare autorizzazioni applicazione, quindi aggiungere le autorizzazioni appropriate. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Usare il comando [az identity create](/cli/azure/identity#az-identity-create) per creare un'identità gestita assegnata dall'utente. Il parametro `-g` specifica il gruppo di risorse in cui creare l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare il comando [az identity list](/cli/azure/identity#az-identity-list). Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Nella risposta json, le identità gestite assegnate dall'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente, usare il comando [az identity delete](/cli/azure/identity#az-identity-delete).  Il parametro -n specifica il nome e il parametro -g specifica il gruppo di risorse in cui è stata creata l'identità gestita assegnata dall'utente. Sostituire i valori dei parametri `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` con valori personalizzati:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Rimuovere quelli da VM/VMSS tramite il comando `az vm/vmss identity remove`

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo dei comandi di identità dell'interfaccia della riga di comando di Azure, vedere [az identity](/cli/azure/identity).

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure, vedere [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
