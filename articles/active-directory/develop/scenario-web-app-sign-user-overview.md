---
title: Utenti di accesso da un'app Web-piattaforma di identità Microsoft | Azure
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
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881486"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: app Web che accede agli utenti

Scopri tutto quello che ti serve per creare un'app Web che usa la piattaforma di identità Microsoft per l'accesso degli utenti.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se si vuole creare la prima app Web portabile (ASP.NET Core) che accede agli utenti, seguire questa Guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva: ASP.NET Core app Web che accede agli utenti](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Per informazioni su come aggiungere l'accesso a un'applicazione Web ASP.NET esistente, provare a eseguire la Guida introduttiva seguente:

> [!div class="nextstepaction"]
> [Guida introduttiva: app Web ASP.NET che esegue l'accesso degli utenti](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se si è uno sviluppatore Java, provare a eseguire la Guida introduttiva seguente:

> [!div class="nextstepaction"]
> [Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se si sviluppa con Python, provare a eseguire la Guida introduttiva seguente:

> [!div class="nextstepaction"]
> [Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Panoramica

Aggiungere l'autenticazione all'app Web in modo che possa accedere agli utenti. L'aggiunta dell'autenticazione consente all'app Web di accedere a informazioni limitate sul profilo per personalizzare l'esperienza degli utenti. 

App Web autenticare un utente in un Web browser. In questo scenario l'app Web indirizza il browser dell'utente per l'accesso al Azure Active Directory (Azure AD). Azure AD restituisce una risposta di accesso tramite il browser dell'utente, che contiene le attestazioni relative all'utente in un token di sicurezza. L'accesso degli utenti sfrutta il protocollo standard [Open ID Connect](./v2-protocols-oidc.md) , semplificato dall'utilizzo delle [librerie](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)middleware.

![App Web che concede l'accesso agli utenti](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Come seconda fase, è possibile abilitare l'applicazione per chiamare le API Web per conto dell'utente che ha eseguito l'accesso. Questa fase successiva è uno scenario diverso, disponibile nell' [app Web che chiama le API Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Per aggiungere l'accesso a un'app Web, è necessario proteggere l'app Web e convalidare un token utente, ovvero le librerie **middleware** . Nel caso di .NET, questo scenario non richiede ancora Microsoft Authentication Library (MSAL), che sta per acquisire un token per chiamare le API protette. Le librerie di autenticazione verranno introdotte nello scenario di completamento, quando l'app Web deve chiamare le API Web.

## <a name="specifics"></a>Specifiche

- Durante la registrazione dell'applicazione, è necessario fornire uno o più (se si distribuisce l'app in più percorsi) URI di risposta. In alcuni casi (ASP.NET e ASP.NET Core), è necessario abilitare il token ID. Infine, è necessario configurare un URI di disconnessione in modo che l'applicazione reagisca alla disconnessione degli utenti.
- Nel codice dell'applicazione è necessario fornire l'autorità a cui l'app Web delega l'accesso. Potrebbe essere necessario personalizzare la convalida del token (in particolare negli scenari partner).
- Le applicazioni Web supportano qualsiasi tipo di account. Per ulteriori informazioni, vedere [tipi di account supportati](v2-supported-account-types.md).

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registrazione dell'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registrazione dell'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registrazione dell'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registrazione dell'app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
