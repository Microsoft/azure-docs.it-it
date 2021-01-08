---
title: "Avvio rapido: Eliminare un'applicazione dal tenant di Azure Active Directory (Azure AD)"
description: In questo argomento di avvio rapido si usa il portale di Azure per eliminare un'applicazione nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 12/28/2020
ms.author: kenwith
ms.openlocfilehash: e2b8bd47f7d124562a38413d36b849d1f7efacee
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802763"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Eliminare un'applicazione dal tenant di Azure Active Directory (Azure AD)

In questo argomento di avvio rapido si usa il portale di Azure per eliminare un'applicazione aggiunta al tenant di Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Prerequisiti

Per eliminare un'applicazione dal tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).
- Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md).
- Facoltativo: completamento di [Assegnare utenti a un'app](add-application-portal-assign-users.md).
- Facoltativo: completamento di [Configurare Single Sign-On](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Eliminare un'applicazione dal tenant di Azure AD

Per eliminare un'applicazione dal tenant di Azure AD:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Quindi trovare e selezionare l'applicazione da eliminare. In questo caso è stata eliminata l'applicazione **GitHub_test** aggiunta nell'argomento di avvio rapido precedente.
1. Nella sezione **Gestisci** nel riquadro sinistro selezionare **Proprietà**.
1. Selezionare **Elimina** e quindi fare clic su **Sì** per confermare che si vuole eliminare l'app dal tenant di Azure AD.

> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questa serie di argomenti di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è stata descritta in questo argomento di avvio rapido.

## <a name="next-steps"></a>Passaggi successivi

La serie di argomenti di avvio rapido è stata completata. È possibile continuare leggendo le informazioni sul Single Sign-On in [Informazioni sull'accesso Single Sign-On (SSO)](what-is-single-sign-on.md) oppure vedere le procedure consigliate per la gestione delle app.
> [!div class="nextstepaction"]
> [Procedure consigliate per la gestione delle applicazioni](application-management-fundamentals.md)
