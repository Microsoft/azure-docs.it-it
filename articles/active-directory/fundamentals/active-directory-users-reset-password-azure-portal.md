---
title: Reimpostare la password di un utente - Azure Active Directory | Microsoft Docs
description: Informazioni su come reimpostare la password di un utente con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8809f8c168e7095f05587c7a572e08287637dc5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034592"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Reimpostare la password di un utente con Azure Active Directory

Se un utente dimentica la propria password, non riesce ad accedere a un dispositivo o non ha mai ricevuto una password, un amministratore può reimpostarne la password.

>[!Note]
>A meno che il tenant di Azure AD non sia la home directory per l'utente, non sarà possibile reimpostare la password. Ciò significa che se l'utente sta eseguendo l'accesso nell'organizzazione usando un account di un'altra organizzazione, un account Microsoft o un account Google, non sarà possibile reimpostare la password.<br><br>Se l'utente ha un'origine dell'autorità come Windows Server Active Directory, sarà possibile reimpostare la password solo se il writeback delle password è stato attivato.<br><br>Se l'utente ha un'origine dell'autorità come istanza esterna di Azure AD, non sarà possibile reimpostare la password. Solo l'utente o un amministratore in Azure AD esterno può reimpostare la password.

>[!Note]
>Se non si è un amministratore e sono necessarie informazioni su come reimpostare la propria password aziendale o dell'istituto di istruzione, vedere [Reimpostare la password aziendale o dell'istituto di istruzione](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Per reimpostare la password

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore utente o password. Per ulteriori informazioni sui ruoli disponibili, vedere [Azure ad ruoli predefiniti](../roles/permissions-reference.md) .

2. Selezionare **Azure Active Directory**, selezionare **Utenti**, cercare e selezionare l'utente che richiede la reimpostazione e quindi selezionare **Reimposta password**.

    Viene visualizzata la pagina **Alain Charon - Profilo** con l'opzione **Reimposta password**.

    ![Pagina del profilo dell'utente, con l'opzione Reimposta password evidenziata](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Nella pagina **Reimposta password** selezionare **Reimposta password**.

    > [!Note]
    > Quando si usa Azure Active Directory, viene generata automaticamente una password temporanea per l'utente. Quando si usa Active Directory in locale, si crea la password per l'utente.

4. Copiare la password e assegnarla all'utente. L'utente dovrà modificare la password durante il processo di accesso successivo.

    >[!Note]
    >La password temporanea non scade mai. All'accesso successivo dell'utente, la password funzionerà indipendentemente dal tempo trascorso da quando è stata generata.

> [!IMPORTANT]
> Se un amministratore non è in grado di reimpostare la password dell'utente e nei registri eventi dell'applicazione nel server Azure AD Connect viene visualizzato il codice di errore seguente HR = 80231367, esaminare gli attributi dell'utente in Active Directory.  Se l'attributo **AdminCount** è impostato su 1, in questo modo si impedisce a un amministratore di reimpostare la password dell'utente.  Per consentire agli amministratori di reimpostare la password dell'utente, l'attributo **AdminCount** deve essere impostato su 0.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver reimpostato la password dell'utente, è possibile eseguire i processi di base seguenti:

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

Oppure è possibile eseguire scenari utente più complessi, ad esempio l'assegnazione di delegati, l'uso di criteri e la condivisione degli account utente. Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../enterprise-users/index.yml).
