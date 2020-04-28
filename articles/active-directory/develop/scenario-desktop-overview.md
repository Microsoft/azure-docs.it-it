---
title: Creare un'app desktop che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app desktop che chiama le API Web (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883002"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: app desktop che chiama le API Web

Scopri tutto quello che ti serve per creare un'app desktop che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introduzione

Se non è già stato fatto, creare la prima applicazione seguendo la Guida introduttiva per desktop .NET, la Guida introduttiva piattaforma UWP (Universal Windows Platform) (UWP) o la Guida introduttiva all'app nativa macOS:

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app di Windows Desktop](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app nativa macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Panoramica

Si scrive un'applicazione desktop e si vogliono accedere gli utenti all'applicazione e chiamare API Web, ad esempio Microsoft Graph, altre API Microsoft o un'API Web personalizzata. Sono disponibili diverse possibilità:

- È possibile usare l'acquisizione di token interattivo:

  - Se l'applicazione desktop supporta i controlli grafici, ad esempio, se si tratta di un'applicazione Windows. Form, di un'applicazione WPF o di un'applicazione nativa macOS.
  - In alternativa, se si tratta di un'applicazione .NET Core e si accetta di fare in modo che l'interazione di autenticazione con Azure Active Directory (Azure AD) venga eseguita nel browser del sistema.

- Per le applicazioni ospitate in Windows, è anche possibile che le applicazioni in esecuzione su computer aggiunti a un dominio di Windows o Azure AD Unite per acquisire un token in modo invisibile all'utente usando l'autenticazione integrata di Windows.
- Infine, anche se non è consigliabile, è possibile usare un nome utente e una password nelle applicazioni client pubbliche. È ancora necessario in alcuni scenari, ad esempio DevOps. L'uso di esso impone vincoli sull'applicazione. Ad esempio, non può accedere a un utente che deve eseguire l'autenticazione a più fattori (accesso condizionale). Inoltre, l'applicazione non trarrà vantaggio da Single Sign-On (SSO).

  Il flusso è anche contrario ai principi dell'autenticazione moderna e viene fornito solo per motivi legacy.

  ![Applicazione desktop](media/scenarios/desktop-app.svg)

- Se si scrive uno strumento da riga di comando portabile, probabilmente un'applicazione .NET Core eseguita in Linux o Mac e si accetta che l'autenticazione verrà delegata al browser di sistema, è possibile usare l'autenticazione interattiva. .NET Core non fornisce un [Web browser](https://aka.ms/msal-net-uses-web-browser), quindi l'autenticazione viene eseguita nel browser del sistema. In caso contrario, l'opzione migliore in questo caso consiste nell'usare il flusso del codice del dispositivo. Questo flusso viene usato anche per le applicazioni senza un browser, ad esempio le applicazioni Internet.

  ![Applicazione browser](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifiche

Le applicazioni desktop hanno diverse peculiarità. Dipendono principalmente dal fatto che l'applicazione usi o meno l'autenticazione interattiva.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: registrazione dell'app](scenario-desktop-app-registration.md)
