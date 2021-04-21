---
title: Gestire l'identità gestita assegnata dall'utente - Interfaccia della riga di comando di Azure - Azure AD
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di Azure.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784876"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Creare, elencare o eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di Azure


Le identità gestite per le risorse di Azure forniscono ai servizi di Azure un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di Azure.

Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere [Informazioni sulle identità gestite per le risorse di Azure](overview.md). Per informazioni sui tipi di identità gestite assegnate dal sistema e assegnate dall'utente, vedere [Tipi di identità gestita](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Per modificare le autorizzazioni utente quando si usa un'entità servizio app tramite l'interfaccia della riga di comando, è necessario fornire all'entità servizio autorizzazioni aggiuntive in Azure AD API Graph, in quanto parti dell'interfaccia della riga di comando eseguono richieste GET sul API Graph. In caso contrario, potrebbe essere visualizzato il messaggio "Privilegi insufficienti per completare l'operazione". A tale scopo, è necessario accedere alla registrazione dell'app in Azure Active Directory, selezionare l'app, fare clic su Autorizzazioni API, scorrere verso il basso e selezionare Azure Active Directory Graph. Da qui selezionare Autorizzazioni applicazione e quindi aggiungere le autorizzazioni appropriate. 

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Usare il comando [az identity create](/cli/azure/identity#az_identity_create) per creare un'identità gestita assegnata dall'utente. Il parametro `-g` specifica il gruppo di risorse in cui creare l'identità gestita assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare il comando [az identity list](/cli/azure/identity#az_identity_list). Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

Nella risposta json, le identità gestite assegnate dall'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente, usare il comando [az identity delete](/cli/azure/identity#az_identity_delete).  Il parametro -n specifica il nome e il parametro -g specifica il gruppo di risorse in cui è stata creata l'identità gestita assegnata dall'utente. Sostituire i valori dei parametri `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` con valori personalizzati:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Rimuovere quelli da VM/VMSS tramite il comando `az vm/vmss identity remove`

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo dei comandi di identità dell'interfaccia della riga di comando di Azure, vedere [az identity](/cli/azure/identity).

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure, vedere [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
