---
title: Creare un'app Web che chiama API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app Web che chiama le API Web (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756297"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: un'app Web che chiama le API Web

Informazioni su come creare un'app Web che consente agli utenti di accedere alla piattaforma di identità Microsoft e quindi chiama le API Web per conto dell'utente che ha eseguito l'accesso.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario presuppone che sia già stato completato lo [scenario: app Web che esegue l'accesso degli utenti](scenario-web-app-sign-user-overview.md).

## <a name="overview"></a>Panoramica

È possibile aggiungere l'autenticazione all'app Web in modo da consentire agli utenti di accedere e chiamare un'API Web per conto dell'utente che ha eseguito l'accesso.

![App Web che chiama le API Web](./media/scenario-webapp/web-app.svg)

Le app Web che chiamano API Web sono applicazioni client riservate.
Per questo motivo viene registrato un segreto (una password o un certificato dell'applicazione) con Azure Active Directory (Azure AD). Questo segreto viene passato durante la chiamata a Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!NOTE]
> Per aggiungere l'accesso a un'app Web, è necessario proteggere l'app Web. Tale protezione viene eseguita utilizzando le librerie *middleware* , non Microsoft Authentication Library (MSAL). Lo scenario precedente, l' [app Web che](scenario-web-app-sign-user-overview.md)esegue l'accesso degli utenti, ha coperto questo argomento.
>
> Questo scenario illustra come chiamare le API Web da un'app Web. È necessario ottenere i token di accesso per tali API Web. Per acquisire questi token, è possibile usare le librerie MSAL.

Lo sviluppo per questo scenario comporta le attività specifiche seguenti:

- Durante la [registrazione dell'applicazione](scenario-web-app-call-api-app-registration.md), è necessario specificare un URI di risposta, un segreto o un certificato da condividere con Azure ad. Se si distribuisce l'app in diversi percorsi, si fornirà un URI di risposta per ogni località.
- La [configurazione dell'applicazione](scenario-web-app-call-api-app-configuration.md) deve fornire le credenziali client condivise con Azure ad durante la registrazione dell'applicazione.

## <a name="recommended-reading"></a>Letture consigliate

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, registrazione dell' [app](scenario-web-app-call-api-app-registration.md).