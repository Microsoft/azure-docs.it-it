---
title: Aggiungere o rimuovere un gruppo da un altro gruppo-Azure AD
description: Istruzioni su come aggiungere o rimuovere un gruppo da un altro gruppo con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3918016e35029ea6cc1b60d407ad475b35895b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565465"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Aggiungere o rimuovere un gruppo da un altro gruppo con Azure Active Directory
Questo articolo descrive come aggiungere o rimuovere un gruppo da un altro gruppo con Azure Active Directory.

>[!Note]
>Se si sta tentando di eliminare il gruppo padre, vedere [Come aggiornare o eliminare un gruppo e i relativi membri](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Aggiungere un gruppo a un altro gruppo
È possibile aggiungere un gruppo di sicurezza esistente a un altro gruppo di sicurezza esistente, ovvero creare gruppi annidati, creando un gruppo membri, o sottogruppo, e un gruppo padre. Il gruppo membri eredita gli attributi e le proprietà del gruppo padre, consentendo di risparmiare tempo di configurazione.

>[!Important]
>Attualmente non sono supportate le operazioni seguenti:<ul><li>Aggiunta di gruppi a un gruppo sincronizzato con Active Directory locale.</li><li>Aggiunta di gruppi di sicurezza a gruppi di Microsoft 365.</li><li>Aggiunta di gruppi di Microsoft 365 ai gruppi di sicurezza o ad altri gruppi di Microsoft 365.</li><li>Assegnazione di app a gruppi annidati.</li><li>Applicazione di licenze a gruppi annidati.</li><li>Aggiunta di gruppi di distribuzione in scenari di annidamento.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Per aggiungere un gruppo come membro di un altro gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory** e quindi **Gruppi**.

3. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo che deve diventare membro di un altro gruppo. Per questo esercizio, viene usato il gruppo **MDM policy - West**.

    >[!Note]
    >È possibile aggiungere il gruppo come membro di un solo gruppo alla volta. La casella **Seleziona gruppo** filtra la visualizzazione in base alla corrispondenza dei dati immessi con una parte qualsiasi del nome dell'utente o del dispositivo. Tuttavia, i caratteri jolly non sono supportati.

    ![Pagina Gruppi - Tutti i gruppi con il gruppo MDM policy - West selezionato](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Nella pagina **MDM policy - West - Appartenenze a gruppi** selezionare **Appartenenze a gruppi**, **Aggiungi**, individuare il gruppo di cui si vuole che il gruppo selezionato faccia parte e scegliere **Seleziona**. Per questo esercizio, viene usato il gruppo **MDM policy - All org**.

    Il gruppo **MDM policy - West** ora è un membro del gruppo **MDM policy - All org** ed eredita tutte le proprietà e la configurazione del gruppo MDM policy - All org.

    ![Creare l'appartenenza a un gruppo tramite l'aggiunta di un gruppo a un altro gruppo](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Rivedere la pagina **MDM policy - West - Appartenenze a gruppi** per visualizzare la relazione tra il gruppo e i membri.

6. Per una visualizzazione più dettagliata della relazione tra gruppo e membri, selezionare il nome del gruppo (**MDM policy - All org**) e osservare i dettagli della pagina **MDM policy - West**.

## <a name="remove-a-group-from-another-group"></a>Rimuovere un gruppo da un altro gruppo
È possibile rimuovere un gruppo di sicurezza esistente da un altro gruppo di sicurezza. Tuttavia, rimuovendo il gruppo vengono rimossi anche tutti gli attributi e le proprietà ereditati per i membri.

### <a name="to-remove-a-member-group-from-another-group"></a>Per rimuovere un gruppo membri da un altro gruppo
1. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo da rimuovere come membro di un altro gruppo. Per questo esercizio, viene usato ancora il gruppo **MDM policy - West**.

2. Nella pagina **Panoramica MDM policy - West ** selezionare **Appartenenze a gruppi**.

3. Selezionare il gruppo **MDM policy - All org** nella pagina **MDM policy - West - Appartenenze a gruppi** e quindi selezionare **Rimuovi** dai dettagli della pagina **MDM policy - West**.

    ![Pagina delle appartenenze ai gruppi con i dettagli sia del membro che del gruppo](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Altre informazioni
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

- [Visualizzare gruppi e membri](active-directory-groups-view-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Aggiungere o rimuovere membri di un gruppo](active-directory-groups-members-azure-portal.md)

- [Modificare le impostazioni del gruppo](active-directory-groups-settings-azure-portal.md)

- [Uso di un gruppo per gestire l'accesso ad applicazioni SaaS](../users-groups-roles/groups-saasapps.md)

- [Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
