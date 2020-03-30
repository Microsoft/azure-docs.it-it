---
title: Mapping delle attestazioni utente di collaborazione B2B - Azure Active Directory
description: Personalizzare le attestazioni rilasciate nel token SAML per gli utenti B2B di Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273192"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory

Azure Active Directory (Azure AD) supporta la personalizzazione delle attestazioni rilasciate nel token SAML per gli utenti di Collaborazione B2B. Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita, sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente.

Nel [portale di Azure](https://portal.azure.com) è possibile visualizzare o modificare le attestazioni inviate all'applicazione nel token SAML. Per accedere alle impostazioni, selezionare **Applicazioni Azure Active Directory** > **Enterprise** >'applicazione configurata per l'accesso Single Sign-On > **Single Sign-On**. Visualizzare le impostazioni del token SAML nella sezione **Attributi utente**.

![Visualizzazione degli attributi del token SAML nell'interfaccia utente](media/claims-mapping/view-claims-in-saml-token.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

1. L'applicazione richiede un set diverso di URI di attestazione o valori di attestazione.

2. L'applicazione richiede che l'attestazione NameIdentifier sia diversa dal nome dell'entità utente archiviato in Azure AD.

Per altre informazioni su come aggiungere e modificare attestazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Per gli utenti di Collaborazione B2B, il mapping tra NameID e UPN tra tenant non è consentito per motivi di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle proprietà degli utenti di Collaborazione B2B, vedere [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](user-properties.md).
- Per informazioni sui token utente per gli utenti di Collaborazione B2B, vedere [Informazioni sui token utente in Collaborazione B2B di Azure AD](user-token.md).

