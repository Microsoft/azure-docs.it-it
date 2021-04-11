---
title: 'Aggiungere o eliminare utenti: Azure Active Directory | Microsoft Docs'
description: Informazioni su come aggiungere nuovi utenti o eliminare utenti esistenti tramite Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594598"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Aggiungere o eliminare utenti tramite Azure Active Directory

Aggiungere nuovi utenti o eliminare gli utenti esistenti dall'organizzazione Azure Active Directory (Azure AD). Per aggiungere o eliminare utenti, è necessario essere un amministratore utente o un amministratore globale.

## <a name="add-a-new-user"></a>Aggiungere un nuovo utente

Con il portale Azure Active Directory è possibile creare un nuovo utente.

Per aggiungere un nuovo utente, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'organizzazione.

1. Cercare e selezionare *Azure Active Directory* in qualsiasi pagina.

1. Selezionare **utenti**, quindi selezionare **nuovo utente**.

    ![Aggiungere un utente tramite utenti: tutti gli utenti in Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Nella pagina **utente** immettere le informazioni per l'utente:

   - **Nome**. Obbligatorio. Nome e cognome del nuovo utente. Ad esempio, *Mary Parker*.

   - **Nome utente**. Obbligatorio. Nome utente del nuovo utente. Ad esempio: `mary@contoso.com`.

     La parte del dominio del nome utente deve usare il nome di dominio predefinito iniziale, *\<yourdomainname> onmicrosoft.com*, o un nome di dominio personalizzato, ad esempio *contoso.com*. Per altre informazioni su come creare un nome di dominio personalizzato, vedere [aggiungere il nome di dominio personalizzato usando il portale di Azure Active Directory](add-custom-domain.md).

   - **Gruppi**. Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento. Per ulteriori informazioni sull'aggiunta di utenti ai gruppi, vedere [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Ruolo della directory**. Se sono necessari autorizzazioni amministrative Azure AD per l'utente, è possibile aggiungerle a un ruolo di Azure AD. È possibile assegnare all'utente un ruolo di amministratore globale o uno o più ruoli di amministratore limitati in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere [Come assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md).

   - **Informazioni sul processo**: è possibile aggiungere altre informazioni sull'utente oppure eseguirle in un secondo momento. Per altre informazioni sull'aggiunta di informazioni sull'utente, vedere [Come aggiungere o modificare informazioni di un profilo utente](active-directory-users-profile-azure-portal.md).

1. Copiare la password generata automaticamente specificata nella casella **password** . Sarà necessario assegnare questa password all'utente per accedere per la prima volta.

1. Selezionare **Crea**.

L'utente viene creato e aggiunto all'organizzazione Azure AD.

## <a name="add-a-new-guest-user"></a>Aggiungere un nuovo utente Guest

È anche possibile invitare un nuovo utente Guest per collaborare con l'organizzazione selezionando **invita utente** nella pagina **nuovo utente** . Se vengono configurate le impostazioni di collaborazione esterna dell'organizzazione in modo che sia possibile invitare i guest, l'utente verrà inviato tramite posta elettronica a un invito che deve accettare per iniziare la collaborazione. Per ulteriori informazioni sull'invito di utenti di collaborazione B2B, vedere [invitare utenti B2B a Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Aggiungere un utente consumer

Potrebbero essere presenti scenari in cui si desidera creare manualmente gli account utente nella directory Azure Active Directory B2C (Azure AD B2C). Per ulteriori informazioni sulla creazione di account utente, vedere [creare ed eliminare utenti consumer in Azure ad B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Aggiungere un nuovo utente all'interno di un ambiente ibrido

Se l'ambiente usa sia Azure Active Directory (cloud) che Windows Server Active Directory (locale), è possibile aggiungere nuovi utenti sincronizzando i dati degli account utente esistenti. Per altre informazioni sugli ambienti ibridi e sugli utenti, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminare un utente

Con il portale Azure Active Directory è possibile eliminare un utente esistente.

>[!Note]
>È necessario disporre di un'assegnazione di ruolo amministratore globale o amministratore utente per eliminare gli utenti nell'organizzazione. Gli amministratori globali possono eliminare qualsiasi utente che includa altri amministratori. Gli amministratori utente possono eliminare eventuali utenti non amministratori, amministratori del supporto tecnico e altri amministratori utenti. Per ulteriori informazioni, vedere [autorizzazioni per i ruoli di amministratore in Azure ad](../roles/permissions-reference.md).

Per eliminare un utente, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore utente per l'organizzazione.

1. Cercare e selezionare *Azure Active Directory* in qualsiasi pagina.

1. Cercare e selezionare l'utente che si vuole eliminare dal tenant di Azure AD. Ad esempio, _Mary Parker_.

1. Selezionare **Elimina utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Elimina utente evidenziato](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

L'utente viene eliminato e non è più visualizzato nella pagina **Utenti - Tutti gli utenti**. L'utente può essere visualizzato nella pagina **Utenti eliminati** per i 30 giorni successivi e durante tale periodo può essere ripristinato. Per ulteriori informazioni sul ripristino di un utente, vedere [ripristinare o rimuovere un utente eliminato di recente utilizzando Azure Active Directory](active-directory-users-restore.md).

Quando un utente viene eliminato, le licenze utilizzate dall'utente vengono rese disponibili per altri utenti.

>[!Note]
>Per aggiornare l'identità, le informazioni di contatto o le informazioni sui processi per gli utenti la cui origine dell'autorità è Windows Server Active Directory, è necessario utilizzare Windows Server Active Directory. Dopo aver completato l'aggiornamento, è necessario attendere il completamento del ciclo di sincronizzazione successivo prima di visualizzare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Una volta aggiunti gli utenti, è possibile eseguire i processi di base seguenti:

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Utilizzare gruppi e utenti dinamici](../enterprise-users/groups-create-rule.md)

In alternativa, è possibile eseguire altre attività di gestione degli utenti, ad esempio l' [aggiunta di utenti Guest da un'altra directory](../external-identities/what-is-b2b.md) o [il ripristino di un utente eliminato](active-directory-users-restore.md). Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../enterprise-users/index.yml).