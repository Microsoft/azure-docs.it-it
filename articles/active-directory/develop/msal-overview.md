---
title: Informazioni su MSAL | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire token per chiamare le API Web protette. Queste API Web possono essere la Microsoft Graph, altre API Microsoft, API Web di terze parti o un'API Web personalizzata. MSAL supporta più architetture e piattaforme applicative.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 90e81e30e26846bf05032dddf1441e8428bc7158
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550983"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Panoramica di Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) consente agli sviluppatori di acquisire [token](developer-glossary.md#security-token) dalla piattaforma di identità Microsoft per autenticare gli utenti e accedere alle API Web protette. Può essere usato per fornire un accesso sicuro alle Microsoft Graph, ad altre API Microsoft, a API Web di terze parti o all'API Web. MSAL supporta molte architetture e piattaforme di applicazioni diverse, tra cui .NET, JavaScript, Java, Python, Android e iOS.

MSAL offre molti modi per ottenere i token con un'API coerente per diverse piattaforme. L'uso di MSAL offre i vantaggi seguenti:

* Non richiede l'uso diretto del codice o delle librerie OAuth con il protocollo nell'applicazione.
* Acquisisce i token per conto di un utente o di un'applicazione (se applicabile alla piattaforma).
* Mantiene una cache dei token, che vengono aggiornati automaticamente quando stanno per scadere. Non è necessario occuparsi personalmente della scadenza dei token.
* Consente di specificare a quali destinatari si desidera far accedere l'applicazione (la propria organizzazione, diverse organizzazioni, account aziendali o degli istituti di istruzione e account personali Microsoft, identità di social networking con Azure AD B2C e utenti in cloud sovrani e nazionali).
* Consente di impostare l'applicazione da file di configurazione.
* Consente di risolvere i problemi dell'app mediante l'esposizione delle eccezioni su cui è possibile eseguire azioni, nonché dei dati di registrazione e di telemetria.

## <a name="application-types-and-scenarios"></a>Tipi di applicazioni e scenari
Con MSAL è possibile acquisire un token da diversi tipi di applicazioni: applicazioni Web, API Web, app a pagina singola (JavaScript), applicazioni native e per dispositivi mobili, daemon e applicazioni sul lato server.

È possibile usare MSAL in numerosi scenari applicativi, inclusi i seguenti:

* [Applicazioni a pagina singola (JavaScript)](scenario-spa-overview.md)
* [App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
* [Applicazione Web per l'accesso di un utente e la chiamata di un'API Web per conto dell'utente](scenario-web-app-call-api-overview.md)
* [Protezione di un'API Web in modo che possano accedervi solo gli utenti autenticati](scenario-protected-web-api-overview.md)
* [API Web che chiama un'altra API Web downstream per conto dell'utente che ha eseguito l'accesso](scenario-web-api-call-api-overview.md)
* [Applicazione desktop che chiama un'API Web per conto dell'utente che ha eseguito l'accesso](scenario-desktop-overview.md)
* [Applicazione per dispositivi mobili che chiama un'API Web per conto dell'utente che ha eseguito l'accesso in modo interattivo](scenario-mobile-overview.md).
* [Applicazione daemon di servizio/desktop che chiama un'API Web per proprio conto](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Linguaggi e framework

| Libreria | Piattaforme e framework supportati|
| --- | --- |
| [MSAL per Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| App a singola pagina con Framework angolari e Angular.js|
| [MSAL per iOS e MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS e macOS|
| [MSAL Go (anteprima)](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows, macOS, Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| Framework JavaScript/TypeScript, ad esempio Vue.js, Ember.js o Durandal.js|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, piattaforma UWP (Universal Windows Platform)|
| [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|App Web con app desktop Express e con Electron e app console multipiattaforma|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|
| [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| App a singola pagina con librerie basate su React e React (Next.js, Gatsby.js)|

## <a name="differences-between-adal-and-msal"></a>Differenze tra ADAL e MSAL

Active Directory Authentication Library (ADAL) si integra con l'endpoint Azure AD for Developers (v 1.0), in cui MSAL si integra con la piattaforma di identità Microsoft. L'endpoint v1.0 supporta gli account aziendali, ma non quelli personali. L'endpoint v2.0 riunisce gli account personali e aziendali Microsoft in un unico sistema di autenticazione. Con MSAL è anche possibile ottenere le autenticazioni per Azure AD B2C.

Per informazioni più specifiche, leggere gli articoli relativi alla [migrazione a MSAL.NET da ADAL.NET](msal-net-migration.md) e alla [migrazione a MSAL.js da ADAL.js](msal-compare-msal-js-and-adal-js.md).
