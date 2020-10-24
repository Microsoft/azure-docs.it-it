---
title: Ripristinare o rimuovere definitivamente gli utenti eliminati di recente Azure AD
description: Informazioni su come visualizzare gli utenti ripristinabili, ripristinare un utente eliminato o eliminare definitivamente un utente con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514390"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Ripristinare o rimuovere un utente eliminato di recente con Azure Active Directory
Dopo aver eliminato un utente, l'account rimane in uno stato di sospensione per 30 giorni. Durante tale intervallo di 30 giorni, l'account utente può essere ripristinato, con tutte le relative proprietà. Al termine di tale intervallo di 30 giorni, viene avviato automaticamente il processo di eliminazione permanente.

È possibile visualizzare gli utenti da ripristinare, ripristinare un utente eliminato o eliminare definitivamente un utente con Azure Active Directory (Azure AD) nel portale di Azure.

>[!Important]
>Un utente eliminato definitivamente non può essere ripristinato, nemmeno dal supporto tecnico Microsoft.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per ripristinare o eliminare definitivamente gli utenti, è necessario avere uno dei ruoli seguenti.

- Amministratore globale

- Supporto partner - Livello 1

- Supporto partner - Livello 2

- Amministratore utenti

## <a name="view-your-restorable-users"></a>Visualizzare gli utenti ripristinabili
È possibile visualizzare tutti gli utenti che sono stati eliminati negli ultimi 30 giorni e che possono essere ripristinati.

### <a name="to-view-your-restorable-users"></a>Per visualizzare gli utenti ripristinabili
1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account di amministratore globale per l'organizzazione.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare **Utenti eliminati**.

    Rivedere l'elenco di utenti che sono disponibili per il ripristino.

    ![Pagina Utenti - Utenti eliminati con gli utenti che possono essere ancora ripristinati](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Ripristinare un utente eliminato di recente

Quando un account utente viene eliminato dall'organizzazione, l'account è in uno stato sospeso e tutte le informazioni sull'organizzazione correlate vengono mantenute. Quando si ripristina un utente, vengono ripristinate anche le informazioni sull'organizzazione.

> [!Note]
> Una volta ripristinato un utente, vengono ripristinate anche le licenze assegnate all'utente al momento dell'eliminazione anche se non sono disponibili postazioni per tali licenze. Se si utilizzano più licenze rispetto a quelle acquistate, è possibile che l'organizzazione sia temporaneamente fuori dalla conformità per l'utilizzo delle licenze.

### <a name="to-restore-a-user"></a>Per ripristinare un utente
1. Nella pagina **Utenti - Utenti eliminati** cercare e selezionare uno degli utenti disponibili. Ad esempio, _Mary Parker_.

2. Selezionare **Ripristina utente**.

    ![Pagina Utenti - Utenti eliminati, con l'opzione Ripristina utente evidenziata](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>eliminare in modo definitivo un utente
È possibile eliminare definitivamente un utente dall'organizzazione senza attendere 30 giorni per l'eliminazione automatica. Un utente eliminato in modo permanente non può essere ripristinato né dall'utente, né da un altro amministratore, né dal supporto tecnico Microsoft.

>[!Note]
>Se si elimina definitivamente un utente per errore, sarà necessario creare un nuovo utente e immettere manualmente tutte le informazioni precedenti. Per altre informazioni sulla creazione di un utente, vedere [Aggiungere o eliminare utenti](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Per eliminare definitivamente un utente

1. Nella pagina **Utenti - Utenti eliminati** cercare e selezionare uno degli utenti disponibili. Ad esempio, _Rae Huff_.

2. Selezionare **Elimina definitivamente**.

    ![Utenti-pagina utenti eliminati, con l'opzione Elimina utente evidenziata](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver eseguito il ripristino o l'eliminazione degli utenti, è possibile:

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti Guest da un'altra organizzazione](../external-identities/what-is-b2b.md)

Per ulteriori informazioni sulle altre attività di gestione utente disponibili, [Azure ad documentazione sulla gestione degli utenti](../enterprise-users/index.yml).
