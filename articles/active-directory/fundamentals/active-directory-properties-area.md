---
title: Aggiungere le informazioni sulla privacy dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere le informazioni sulla privacy dell'organizzazione nella sezione Proprietà di Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996756"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Aggiungere le informazioni sulla privacy dell'organizzazione tramite Azure Active Directory
Questo articolo illustra in che modo un amministratore tenant può aggiungere informazioni relative alla privacy al tenant Azure Active Directory (Azure AD) di un'organizzazione, tramite il portale di Azure.

Si consiglia vivamente di aggiungere il contatto globale sulla privacy e l'informativa sulla privacy dell'organizzazione, in modo che i dipendenti interni e gli utenti esterni possano rivedere i criteri. Poiché le informative sulla privacy sono esclusive e personalizzate per ogni attività, è consigliabile contattare un legale per assistenza.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Aggiungere le informazioni sulla privacy in Azure AD
Aggiungere le informazioni sulla privacy dell'organizzazione nella sezione **Proprietà** di Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Per accedere all'area Proprietà e aggiungere le informazioni sulla privacy

1. Accedere al portale di Azure come amministratore tenant.

2. Nella barra di spostamento a sinistra, selezionare **Azure Active Directory** e quindi selezionare **Proprietà**.

    Verrà visualizzata la sezione **Proprietà**.

    ![Sezione Proprietà di Azure Active Directory che evidenzia le informazioni sulla privacy](media/active-directory-properties-area/properties-area.png)

3. Aggiungere le informazioni sulla privacy per i dipendenti:

    - **Contatto tecnico**. Inserire l'indirizzo di posta elettronica della persona da contattare per supporto tecnico all'interno dell'organizzazione.
    
    - **Contatto privacy globale**. Inserire l'indirizzo di posta elettronica della persona da contattare per le richieste di informazioni sulla privacy dei dati personali. Questa persona viene contattata anche da Microsoft qualora venga riscontrata una violazione dei dati. Se l'elenco non contiene nessun contatto, Microsoft contatterà gli amministratori globali.

    - **URL dell'informativa sulla privacy.** Digitare il collegamento al documento dell'organizzazione che descrive il modo in cui l'organizzazione gestisce la privacy dei dati dei guest interni ed esterni.

        >[!Important]
        >Se non si include la propria informativa sulla privacy o l'informativa sulla privacy, i guest esterni visualizzeranno un testo nella casella **Controlla autorizzazioni** che indica che **< _il nome dell'organizzazione_> non ha fornito collegamenti alle relative condizioni da rivedere**. Ad esempio, un utente guest visualizza questo messaggio quando riceve un invito per accedere a un'organizzazione tramite la collaborazione B2B.

        ![Finestra di dialogo per la verifica delle autorizzazioni di Collaborazione B2B con messaggio](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](../external-identities/redemption-experience.md)
- [Aggiungere o modificare informazioni sul profilo per un utente in Azure Active Directory](active-directory-users-profile-azure-portal.md)