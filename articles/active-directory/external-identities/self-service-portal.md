---
title: Portale di iscrizione self-service per la collaborazione B2B - Azure AD
description: Informazioni su come personalizzare il flusso di lavoro di onboarding per Azure Active Directory gli utenti B2B in base alle esigenze dell'organizzazione.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860508"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Iscrizione self-service per la collaborazione B2B di Azure AD

I clienti possono eseguire molte operazioni con le funzionalità predefinite esposte tramite il [portale di Azure](https://portal.azure.com) e il [pannello di accesso alle applicazioni](https://myapps.microsoft.com) per gli utenti finali. Tuttavia, potrebbe essere necessario personalizzare il flusso di lavoro di onboarding per gli utenti B2B in base alle esigenze della propria organizzazione.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Gestione dei diritti Azure AD per l'iscrizione degli utenti Guest B2B

In qualità di organizzazione invitante, è possibile che non si conoscano in anticipo i singoli collaboratori esterni che necessitano dell'accesso alle risorse. È necessario un modo per consentire agli utenti delle aziende partner di iscriversi con i criteri che si controllano. Se si desidera consentire agli utenti di altre organizzazioni di richiedere l'accesso e, al momento dell'approvazione, di effettuare il provisioning degli account Guest e di assegnarli a gruppi, app e siti di SharePoint Online, è possibile utilizzare [Azure ad gestione dei diritti](../governance/entitlement-management-overview.md) per configurare i criteri che [gestiscono l'accesso per gli utenti esterni](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API di invito B2B Azure Active Directory

Le organizzazioni possono usare l' [API di gestione degli inviti Microsoft Graph](/graph/api/resources/invitation) per creare le proprie esperienze di onboarding per gli utenti Guest B2B. Quando si vuole offrire l'iscrizione a un utente Guest B2B self-service, è consigliabile usare la [gestione dei diritti Azure ad](../governance/entitlement-management-overview.md). Tuttavia, se si vuole creare una propria esperienza, è possibile usare l' [API crea invito](/graph/api/invitation-post?tabs=http) per inviare automaticamente il messaggio di invito personalizzato direttamente all'utente B2B, ad esempio. In alternativa, l'app può usare la inviteRedeemUrl restituita nella risposta di creazione per creare il proprio invito (tramite il meccanismo di comunicazione scelto) per l'utente invitato.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Prezzi delle identità esterne](external-identities-pricing.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
