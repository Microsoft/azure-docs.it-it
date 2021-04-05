---
title: Multi-factor authentication o 2FA e Privileged Identity Management-Azure AD | Microsoft Docs
description: Informazioni su come Azure Active Directory Privileged Identity Management (PIM) convalida l'autenticazione Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835000"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticazione a più fattori e Privileged Identity Management

È consigliabile richiedere l'autenticazione Multi-Factor Authentication (MFA) per tutti gli amministratori. Ciò consente di ridurre il rischio di attacchi causato da una password compromessa.

È possibile richiedere agli utenti di completare una richiesta di autenticazione a più fattori quando eseguono l'accesso. È anche possibile richiedere che gli utenti completino una richiesta di autenticazione a più fattori quando attivano un ruolo in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). In questo modo, se l'utente non ha completato una richiesta di autenticazione a più fattori quando ha eseguito l'accesso, verrà richiesto di farlo da Privileged Identity Management.

> [!IMPORTANT]
> Al momento, Azure AD Multi-Factor Authentication funziona solo con account aziendali o dell'Istituto di istruzione, non con account personali Microsoft (in genere un account personale usato per accedere ai servizi Microsoft, ad esempio Skype, Xbox o Outlook.com). Per questo motivo, chiunque usi un account personale non può essere un amministratore idoneo perché non può usare l'autenticazione a più fattori per attivare i ruoli. Se devono continuare a gestire i carichi di lavoro con un account Microsoft, elevare questi utenti a livello di amministratori permanenti per ora.

## <a name="how-pim-validates-mfa"></a>Modalità di convalida di MFA da parte di PIM

Sono disponibili due opzioni per la convalida dell'autenticazione a più fattori quando un utente attiva un ruolo.

L'opzione più semplice consiste nell'utilizzare Azure AD Multi-Factor Authentication per gli utenti che attivano un ruolo con privilegi. A tale scopo, verificare prima che questi utenti siano provvisti di licenza, se necessario, e abbiano effettuato la registrazione per Azure AD Multi-Factor Authentication. Per altre informazioni su come distribuire Azure AD Multi-Factor Authentication, vedere [distribuire multi-factor authentication di Azure ad basati sul cloud](../authentication/howto-mfa-getstarted.md). È consigliabile, ma non obbligatorio, configurare Azure AD per applicare l'autenticazione a più fattori per questi utenti al momento dell'accesso. Ciò è dovuto al fatto che i controlli di autenticazione a più fattori verranno effettuati dal Privileged Identity Management stesso.

In alternativa, se gli utenti eseguono l'autenticazione in locale, il provider di identità può essere responsabile dell'autenticazione a più fattori. Ad esempio, se AD Federation Services è stato configurato per richiedere l'autenticazione basata su smart card prima di accedere Azure AD, la [protezione delle risorse cloud con Azure AD multi-factor authentication e ad FS](../authentication/howto-mfa-adfs.md) include istruzioni per la configurazione di ad FS per l'invio di attestazioni a Azure ad. Quando un utente tenta di attivare un ruolo, Privileged Identity Management accetta che l'autenticazione a più fattori sia già stata convalidata per l'utente dopo la ricezione delle attestazioni appropriate.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
