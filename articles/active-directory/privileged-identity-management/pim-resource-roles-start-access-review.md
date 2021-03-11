---
title: Creare una verifica di accesso dei ruoli delle risorse di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/09/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4276b48584ecbad91794de58abafd7e3367f6877
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564034"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management

La necessità di accedere ai ruoli delle risorse di Azure con privilegi dai dipendenti cambia nel tempo. Per ridurre i rischi associati alle assegnazioni di ruolo obsolete, è necessario esaminare periodicamente l'accesso. È possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per creare verifiche di accesso per l'accesso con privilegi ai ruoli delle risorse di Azure. È anche possibile configurare le verifiche di accesso periodiche che vengono eseguite automaticamente. Questo articolo descrive come creare una o più verifiche di accesso.

## <a name="prerequisite-role"></a>Ruolo prerequisiti

 Per creare le verifiche di accesso, è necessario essere assegnati al ruolo di Azure [proprietario](../../role-based-access-control/built-in-roles.md#owner) o [amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) per la risorsa.

## <a name="open-access-reviews"></a>Apri verifiche di accesso

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente assegnato a uno dei ruoli prerequisiti.

1. Aprire **Azure ad Privileged Identity Management**.

1. Nel menu a sinistra selezionare **risorse di Azure**.

1. Selezionare la risorsa che si vuole gestire, ad esempio una sottoscrizione.

1. In Gestisci selezionare verifiche di **accesso**.

    ![Risorse di Azure-elenco di verifiche di accesso che mostra lo stato di tutte le revisioni](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco verifiche di accesso che mostra lo stato della verifica avviata](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni su come [verificare l'accesso ai ruoli delle risorse di Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento dei revisori che completano le proprie revisioni nella pagina **Panoramica** della verifica di accesso. Nessun diritto di accesso viene modificato nella directory fino al [completamento della verifica](pim-resource-roles-complete-access-review.md).

![Pagina di panoramica delle verifiche di accesso che mostra i dettagli della verifica](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se si verifica una sola volta, al termine del periodo di verifica di accesso o se l'amministratore interrompe la verifica di accesso, seguire i passaggi in [completare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-complete-access-review.md) per visualizzare e applicare i risultati.  

Per gestire una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti nelle revisioni pianificate e modificare di conseguenza la data di fine o aggiungere/rimuovere revisori.

In base alle impostazioni selezionate in **al termine**, l'applicazione automatica verrà eseguita dopo la data di fine della verifica o quando si arresta manualmente la revisione. Lo stato della revisione passerà da **completato** a stati intermedi, ad esempio l' **applicazione** e infine lo stato **applicato**. È necessario che gli utenti negati, se presenti, vengano rimossi dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso ai ruoli delle risorse di Azure](pim-resource-roles-perform-access-review.md)
- [Completare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-complete-access-review.md)
- [Creare una verifica di accesso dei ruoli Azure AD](pim-how-to-start-security-review.md)
