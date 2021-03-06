---
title: Identificare un gruppo da gestire in Privileged Identity Management-Azure AD | Microsoft Docs
description: Informazioni su come eseguire l'onboarding dei gruppi assegnabili ai ruoli per gestire i gruppi di accesso con privilegi in Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9e2580d2a88fbbab755f0c3df2f923bdc45548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688349"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Portare i gruppi di accesso con privilegi (anteprima) in Privileged Identity Management

In Azure Active Directory (Azure AD), è possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud per semplificare la gestione delle assegnazioni di ruolo. Per proteggere i ruoli di Azure AD e per proteggere l'accesso, è ora possibile usare Privileged Identity Management (PIM) per gestire l'accesso JIT per i membri o i proprietari di questi gruppi. Per gestire un gruppo assegnabile al ruolo Azure AD come gruppo di accesso con privilegi in Privileged Identity Management, è necessario gestirlo in PIM.

## <a name="identify-groups-to-manage"></a>Identificare i gruppi da gestire

È possibile creare un gruppo assegnabile al ruolo in Azure AD come descritto in [creare un gruppo assegnabile al ruolo in Azure Active Directory](../roles/groups-create-eligible.md). L'utente è proprietario del gruppo per gestirlo con Privileged Identity Management.

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni ruolo amministratore ruolo con privilegi.
1. Selezionare **gruppi** e quindi selezionare il gruppo assegnabile al ruolo che si vuole gestire in PIM. È possibile cercare e filtrare l'elenco.

    ![trovare un gruppo assegnabile ai ruoli da gestire in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Aprire il gruppo e selezionare **accesso con privilegi (anteprima)**.

    ![Apri l'esperienza Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Iniziare a gestire le assegnazioni in PIM.

    ![Gestire le assegnazioni in Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Una volta gestito, un gruppo di accesso con privilegi non può essere escluso dalla gestione. In questo modo si impedisce a un altro amministratore di risorse di rimuovere Privileged Identity Management impostazioni.
>

> [!IMPORTANT]
> Se un gruppo di accesso con privilegi viene eliminato da Azure Active Directory, potrebbero essere necessarie fino a 24 ore prima che il gruppo venga rimosso dal pannello gruppi di accesso con privilegi (anteprima). 
>


## <a name="next-steps"></a>Passaggi successivi

- [Configurare le assegnazioni dei gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-assign-roles.md)
