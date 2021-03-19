---
title: Aggiungere o rimuovere membri dei gruppi - Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere o rimuovere membri di un gruppo con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5a85bad1e7b2a6bf645084d6b78f77e6c0d8b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371835"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Aggiungere o rimuovere membri dei gruppi con Azure Active Directory
Con Azure Active Directory, è possibile aggiungere e rimuovere i membri di un gruppo.

## <a name="to-add-group-members"></a>Per aggiungere membri del gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory** e quindi **Gruppi**.

3. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo in cui si vuole aggiungere un membro. In questo caso, usare il gruppo creato in precedenza **MDM policy - West**.

    ![Pagina Gruppi - Tutti i gruppi, con il nome del gruppo evidenziato](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Nella pagina **Panoramica MDM policy - West** selezionare **Membri** nell'area **Gestione**.

    ![Pagina Panoramica MDM policy - West con l'opzione Membri evidenziata](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selezionare **Aggiungi membri**, cercare e selezionare tutti i membri che si vuole aggiungere al gruppo e quindi scegliere **Seleziona**.

    Verrà visualizzato un messaggio che indica che i membri sono stati aggiunti correttamente.

    ![Pagina di aggiunta dei membri, con la ricerca dei membri visualizzata](media/active-directory-groups-members-azure-portal/update-members.png)

6. Aggiornare la schermata per visualizzare tutti i nomi dei membri aggiunti al gruppo.

## <a name="to-remove-group-members"></a>Per rimuovere membri dei gruppi

1. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo da cui si vuole rimuovere un membro. Anche in questo caso verrà usato il gruppo **MDM policy - West**.

2. Selezionare **Membri** dall'area **Gestione**, cercare e selezionare il nome del membro da rimuovere e quindi selezionare **Rimuovi**.

    ![Pagina delle informazioni sui membri, con l'opzione Rimuovi](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare gruppi e membri](active-directory-groups-view-azure-portal.md)

- [Modificare le impostazioni del gruppo](active-directory-groups-settings-azure-portal.md)

- [Gestire l'accesso alle risorse con i gruppi](active-directory-manage-groups.md)

- [Gestire le regole dinamiche per gli utenti in un gruppo](../enterprise-users/groups-create-rule.md)

- [Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
