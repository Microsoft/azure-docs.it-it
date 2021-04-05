---
title: Criteri di Azure Active Directory Identity Protection
description: Identificazione dei tre criteri abilitati con Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835969"
---
# <a name="identity-protection-policies"></a>Criteri di Identity Protection

Azure Active Directory Identity Protection include tre criteri predefiniti che gli amministratori possono scegliere di abilitare. Questi criteri includono una personalizzazione limitata ma sono applicabili alla maggior parte delle organizzazioni. Tutti i criteri consentono di escludere gli utenti, ad esempio gli [account di accesso di emergenza o gli account amministratore critici](../roles/security-emergency-access.md).

![Criteri di Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Criteri di registrazione di Azure AD multi-factor authentication

Identity Protection può aiutare le organizzazioni a implementare Azure AD Multi-Factor Authentication (multi-factor authentication) usando criteri di accesso condizionale che richiedono la registrazione all'accesso. L'abilitazione di questi criteri è un ottimo modo per garantire che i nuovi utenti dell'organizzazione abbiano effettuato la registrazione per MFA il primo giorno. L'autenticazione a più fattori è uno dei metodi di correzione automatica per gli eventi di rischio all'interno di Identity Protection. La correzione automatica consente agli utenti di agire autonomamente per ridurre il volume di chiamate del supporto tecnico.

Altre informazioni su Azure AD Multi-Factor Authentication sono disponibili nell'articolo relativo al [funzionamento: Azure AD multi-factor authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Criteri di rischio di accesso

Identity Protection analizza i segnali di ogni accesso, sia in tempo reale che offline, e calcola un punteggio di rischio in base alla probabilità che l'accesso non sia stato eseguito dall'utente. Gli amministratori possono prendere una decisione in base al punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso ma richiedere l'autenticazione a più fattori.

Se viene rilevato un rischio, gli utenti possono eseguire l'autenticazione a più fattori per eseguire autonomamente la correzione e chiudere l'evento di accesso rischioso per evitare inutili problemi per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio di accesso, gli utenti devono essere registrati in precedenza per Azure AD Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Criteri di accesso condizionale personalizzati

Gli amministratori possono anche scegliere di creare criteri di accesso condizionale personalizzati che includono il rischio di accesso come condizione di assegnazione. Altre informazioni sul rischio come condizione nei criteri di accesso condizionale sono disponibili nell'articolo [Accesso condizionale: Condizioni](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Criteri di rischio di accesso condizionale personalizzati](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Criteri di rischio utente

Identity Protection è in grado di calcolare ciò che ritiene sia normale per il comportamento di un utente e di usarlo per basare le decisioni relative al rischio. Il rischio utente è un calcolo della probabilità che un'identità sia stata compromessa. Gli amministratori possono prendere una decisione in base al punteggio di rischio per applicare i requisiti dell'organizzazione. Gli amministratori possono scegliere di bloccare l'accesso, consentire l'accesso o consentire l'accesso e richiedere una modifica della password usando la [reimpostazione della password self-service di Azure AD](../authentication/howto-sspr-deployment.md).

Se viene rilevato un rischio, gli utenti possono eseguire la reimpostazione della password self-service per eseguire autonomamente la correzione e chiudere l'evento di rischio utente per evitare inutili problemi per gli amministratori.

> [!NOTE] 
> Prima di attivare i criteri di rischio utente, è necessario che gli utenti abbiano prima effettuato la registrazione per la reimpostazione della password self-service.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare la reimpostazione della password self-service di Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilitare Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Abilita Azure AD Multi-Factor Authentication i criteri di registrazione](howto-identity-protection-configure-mfa-policy.md)

- [Abilitare i criteri di rischio per accessi e utenti](howto-identity-protection-configure-risk-policies.md)
