---
title: Configurare i criteri di registrazione dell'autenticazione a più fattori-Azure Active Directory Identity Protection
description: Informazioni su come configurare i criteri di registrazione per l'autenticazione a più fattori di Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382137"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Procedura: configurare i criteri di registrazione del Multi-Factor Authentication di Azure

Azure AD Identity Protection consente di gestire l'implementazione della registrazione di Azure Multi-Factor Authentication (multi-factor authentication) configurando criteri di accesso condizionale per richiedere la registrazione a più fattori, a prescindere dall'app di autenticazione moderna a cui si accede.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Informazioni sui criteri di registrazione di Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fornisce un mezzo per verificare chi sta usando più di un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi degli utenti. Affinché gli utenti siano in grado di rispondere alle richieste di autenticazione a più fattori, devono prima registrarsi per Multi-Factor Authentication di Azure.

Per gli accessi utente è consigliabile richiedere Azure Multi-Factor Authentication perché:

- Offre autenticazione avanzata tramite una gamma di opzioni di verifica.
- Svolge un ruolo fondamentale nella preparazione dell'organizzazione per la correzione automatica dai rilevamenti dei rischi in Identity Protection.

Per altre informazioni su Azure Multi-Factor Authentication, vedere [che cos'è azure multi-factor authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configurazione dei criteri

1. Passare al [portale di Azure](https://portal.azure.com).
1. Individuare i**criteri di registrazione**dell'autenticazione a più fattori **Azure Active Directory** > **Security** > **Identity Protection** > .
   1. In **assegnazioni**
      1. **Utenti** : scegliere **tutti gli utenti** o **selezionare singoli utenti e gruppi** in caso di limitazione dell'implementazione.
         1. Facoltativamente, è possibile scegliere di escludere gli utenti dai criteri.
   1. Sotto **controlli**
      1. Verificare che la casella di controllo **Richiedi registrazione** autenticazione a più fattori di Azure sia selezionata e scegliere **Seleziona**.
   1. **Applicare i criteri** - **in**
   1. **Salva**

## <a name="user-experience"></a>Esperienza utente

Azure Active Directory Identity Protection chiederà agli utenti di effettuare la registrazione al successivo accesso interattivo e avranno 14 giorni per completare la registrazione. Durante questo periodo di 14 giorni, è possibile ignorare la registrazione, ma al termine del periodo sarà necessario effettuare la registrazione prima di poter completare il processo di accesso.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Esperienze di accesso con Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso e i criteri di rischio utente](howto-identity-protection-configure-risk-policies.md)

- [Abilitare la reimpostazione della password self-service Azure AD](../authentication/howto-sspr-deployment.md)

- [Abilitare Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
