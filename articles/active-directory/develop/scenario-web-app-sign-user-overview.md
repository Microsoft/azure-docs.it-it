---
title: Accedere gli utenti da un'app Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app Web per l'accesso degli utenti (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0bbc799f946d318c305a96d9cb8c6831d9242ff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578295"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: App Web per l'accesso degli utenti

Scopri tutto quello che ti serve per creare un'app Web che usa la piattaforma di identità Microsoft per l'accesso degli utenti.

## <a name="getting-started"></a>Guida introduttiva

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se si vuole creare la prima app Web portabile (ASP.NET Core) che accede agli utenti, seguire questa Guida introduttiva:

[Guida introduttiva: ASP.NET Core app Web che accede agli utenti](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Per informazioni su come aggiungere l'accesso a un'applicazione Web ASP.NET esistente, provare a eseguire la Guida introduttiva seguente:

[Guida introduttiva: app Web ASP.NET che esegue l'accesso degli utenti](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se si è uno sviluppatore Java, provare a eseguire la Guida introduttiva seguente:

[Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Java](quickstart-v2-java-webapp.md)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Se si è uno sviluppatore Node.js, provare a eseguire la Guida introduttiva seguente:

[Guida introduttiva: aggiungere l'accesso con Microsoft a un'app Web Node.js](quickstart-v2-nodejs-webapp-msal.md)

# <a name="python"></a>[Python](#tab/python)

Se si sviluppa con Python, provare a eseguire la Guida introduttiva seguente:

[Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Panoramica

Aggiungere l'autenticazione all'app Web in modo che possa accedere agli utenti. L'aggiunta dell'autenticazione consente all'app Web di accedere a informazioni limitate sul profilo per personalizzare l'esperienza degli utenti.

App Web autenticare un utente in un Web browser. In questo scenario l'app Web indirizza il browser dell'utente per l'accesso al Azure Active Directory (Azure AD). Azure AD restituisce una risposta di accesso tramite il browser dell'utente, che contiene le attestazioni relative all'utente in un token di sicurezza. L'accesso degli utenti sfrutta il protocollo standard [Open ID Connect](./v2-protocols-oidc.md) , semplificato dall'utilizzo delle [librerie](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)middleware.

![App Web che concede l'accesso agli utenti](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Come seconda fase, è possibile abilitare l'applicazione per chiamare le API Web per conto dell'utente che ha eseguito l'accesso. Questa fase successiva è uno scenario diverso, disponibile nell' [app Web che chiama le API Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Per aggiungere l'accesso a un'app Web, è necessario proteggere l'app Web e convalidare un token utente, ovvero le librerie  **middleware** . Nel caso di .NET, questo scenario non richiede ancora Microsoft Authentication Library (MSAL), che sta per acquisire un token per chiamare le API protette. Le librerie di autenticazione per .NET verranno introdotte nello scenario di completamento, quando l'app Web deve chiamare le API Web.

## <a name="specifics"></a>Specifiche

- Durante la registrazione dell'applicazione, è necessario fornire uno o più (se si distribuisce l'app in più percorsi) URI di risposta. In alcuni casi (ASP.NET e ASP.NET Core), è necessario abilitare il token ID. Infine, è necessario configurare un URI di disconnessione in modo che l'applicazione reagisca alla disconnessione degli utenti.
- Nel codice dell'applicazione è necessario fornire l'autorità a cui l'app Web delega l'accesso. Potrebbe essere necessario personalizzare la convalida del token (in particolare negli scenari partner).
- Le applicazioni Web supportano qualsiasi tipo di account. Per ulteriori informazioni, vedere [tipi di account supportati](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Letture consigliate

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Passare all'articolo successivo in questo scenario, registrazione dell' [app](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Passare all'articolo successivo in questo scenario, registrazione dell' [app](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Passare all'articolo successivo in questo scenario, registrazione dell' [app](./scenario-web-app-sign-user-app-registration.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Passare all'articolo successivo in questo scenario, registrazione dell' [app](./scenario-web-app-sign-user-app-registration.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Passare all'articolo successivo in questo scenario, registrazione dell' [app](./scenario-web-app-sign-user-app-registration.md?tabs=python).

---
