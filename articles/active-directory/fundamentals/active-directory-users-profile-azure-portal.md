---
title: Aggiungere o aggiornare le informazioni sul profilo utente-Azure AD
description: Vengono fornite istruzioni su come aggiungere informazioni al profilo dell'utente in Azure Active Directory, inclusi i dettagli di un'immagine e del processo.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63b716aa22954af51a9eb53a0d04a8299d348c71
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797238"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Aggiungere o aggiornare informazioni di un profilo utente con Azure Active Directory
Aggiungere informazioni sul profilo utente, inclusi un'immagine del profilo, informazioni lavorative specifiche e alcune impostazioni con Azure Active Directory (Azure AD). Per altre informazioni su come aggiungere nuovi utenti, vedere [Procedura: Aggiungere o eliminare utenti in Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Aggiungere o modificare le informazioni sul profilo utente
Come si vedrà, le informazioni disponibili in un profilo utente sono molte di più rispetto a quelle che è possibile aggiungere durante la creazione dell'utente. Tutte queste informazioni aggiuntive sono facoltative e possono essere aggiunte in base alle esigenze dell'organizzazione.

## <a name="to-add-or-change-profile-information"></a>Per aggiungere o modificare le informazioni sul profilo utente
1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'organizzazione.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare un utente. Ad esempio, _Alain Charon_.

    Viene visualizzata la pagina **Alain Charon - Profilo**.

    ![Pagina del profilo utente, incluse le informazioni modificabili](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selezionare **Modifica** per aggiungere o aggiornare facoltativamente le informazioni incluse in ognuna delle sezioni disponibili.

    ![Pagina del profilo utente con le aree modificabili](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Immagine del profilo.** Selezionare un'immagine di anteprima per l'account dell'utente. Questa immagine viene visualizzata in Azure Active Directory e nelle pagine personali dell'utente, ad esempio nella pagina myapps.microsoft.com.

    - **Identità.** Aggiungere o aggiornare un valore Identity aggiuntivo per l'utente, ad esempio un cognome sposato. È possibile impostare questo nome in modo indipendente dai valori del nome e del cognome. Ad esempio, è possibile usarlo per includere le iniziali, il nome della società o per modificare la sequenza di nomi visualizzati. In un altro esempio, per due utenti i cui nomi sono ' Chris Green ', è possibile usare la stringa Identity per impostare i relativi nomi su' Chris B. Green '' Chris R. Green (contoso) .'

    - **Job info.** (Informazioni professionali) Aggiungere eventuali informazioni relative alle mansioni dell'utente, ad esempio posizione, reparto o manager.

    - **Impostazioni.** Decidere se l'utente può accedere al tenant di Azure Active Directory. È anche possibile specificare la posizione globale dell'utente.

    - **Informazioni di contatto.** Aggiungere le informazioni di contatto rilevanti per l'utente, ad eccezione delle informazioni di contatto telefoniche o mobili dell'utente (solo un amministratore globale può aggiornare per gli utenti nei ruoli di amministratore).

    - **Informazioni di contatto per l'autenticazione.** Verificare le informazioni per assicurarsi che vi sia un indirizzo di posta elettronica e numero di telefono attivi per l'utente. Queste informazioni sono usate da Azure Active Directory per accertarsi dell'identità dell'utente durante l'accesso. Le informazioni di contatto per l'autenticazione possono essere aggiornate solo da un amministratore globale.

4. Selezionare **Salva**.

    Tutte le modifiche vengono salvate per l'utente.

    >[!Note]
    >Per aggiornare l'identità, le informazioni di contatto o le informazioni professionali per gli utenti la cui origine dell'autorità è Windows Server Active Directory, è necessario usare Windows Server Active Directory. Dopo aver completato l'aggiornamento, è necessario attendere il ciclo di sincronizzazione successivo prima di poter visualizzare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver aggiornato il profilo dell'utente, è possibile eseguire i processi di base seguenti:

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

Oppure è possibile eseguire altre attività di gestione, ad esempio l'assegnazione di delegati, l'uso di criteri e la condivisione degli account utente. Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).
