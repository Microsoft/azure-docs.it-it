---
title: Aggiungere le informazioni sulla privacy dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere le informazioni sulla privacy dell'organizzazione nella sezione Proprietà di Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f1877ae630919ba7da5a851b5f8291ade2d165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897574"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Aggiungere le informazioni sulla privacy dell'organizzazione tramite Azure Active Directory
Questo articolo illustra come un amministratore tenant può aggiungere le informazioni sulla privacy ai tenant di Azure Active Directory (Azure AD) dell'organizzazione, tramite il portale di Azure.

È consigliabile aggiungere sia il contatto privacy globale sia l'informativa sulla privacy della propria organizzazione, in modo che i dipendenti interni e gli utenti guest possano leggere l'informativa. Poiché le informative sulla privacy sono esclusive e personalizzate per ogni attività, è consigliabile contattare un legale per assistenza.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Aggiungere le informazioni sulla privacy in Azure AD
Aggiungere le informazioni sulla privacy dell'organizzazione nella sezione **Proprietà** di Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Per accedere all'area Proprietà e aggiungere le informazioni sulla privacy

1.  Accedere al portale di Azure come amministratore tenant.

2.  Nella barra di spostamento a sinistra, selezionare **Azure Active Directory** e quindi selezionare **Proprietà**.

    Verrà visualizzata la sezione **Proprietà**.

    ![Sezione Proprietà di Azure Active Directory che evidenzia le informazioni sulla privacy](media/active-directory-properties-area/properties-area.png)

3.  Aggiungere le informazioni sulla privacy per i dipendenti:

    - **Contatto tecnico**. Inserire l'indirizzo di posta elettronica della persona da contattare per supporto tecnico all'interno dell'organizzazione.
    
    - **Contatto privacy globale**. Inserire l'indirizzo di posta elettronica della persona da contattare per le richieste di informazioni sulla privacy dei dati personali. Questa persona viene contattata anche da Microsoft qualora venga riscontrata una violazione dei dati. Se l'elenco non contiene nessun contatto, Microsoft contatterà gli amministratori globali.

    - **URL dell'informativa sulla privacy**. Inserire il collegamento al documento dell'organizzazione che descrive come viene gestita la privacy dei dati degli interni e dei guest esterni.

        >[!Important]
        >Se l'informativa sulla privacy o il contatto del responsabile della privacy non vengono inclusi, il guest esterno visualizzerà un messaggio di testo nella casella **Verifica autorizzazioni**, ad esempio **<_nome dell'azienda_> non ha fornito collegamenti alle rispettive condizioni per la verifica da parte dell'utente.** Ad esempio, un utente guest visualizza questo messaggio quando riceve un invito per accedere a un'organizzazione tramite la collaborazione B2B.

        ![Finestra di dialogo per la verifica delle autorizzazioni di Collaborazione B2B con messaggio](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](https://aka.ms/b2bredemption)
- [Aggiungere o modificare informazioni sul profilo per un utente in Azure Active Directory](active-directory-users-profile-azure-portal.md)
