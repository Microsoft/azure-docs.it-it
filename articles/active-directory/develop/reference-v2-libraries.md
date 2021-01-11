---
title: Librerie di autenticazione della piattaforma Microsoft Identity
description: Librerie client compatibili e librerie middleware del server, insieme alla libreria, all'origine e ai collegamenti di esempio correlati, per l'endpoint della piattaforma di identità Microsoft.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: a7792e989444c87ba80088f8a850cacb4aa0dc3c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064564"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Librerie di autenticazione della piattaforma Microsoft Identity

L' [endpoint della piattaforma Microsoft Identity](../azuread-dev/azure-ad-endpoint-comparison.md) supporta i protocolli standard del settore OAuth 2,0 e OpenID Connect 1,0. Microsoft Authentication Library (MSAL) è progettato per funzionare con l'endpoint della piattaforma Microsoft Identity. È anche possibile usare librerie open source che supportano OAuth 2,0 e OpenID Connect 1,0.

Si consiglia di usare le librerie scritte da esperti del dominio del protocollo che seguono una metodologia Security Development Lifecycle (SDL). Tali metodologie includono quella [che Microsoft segue][Microsoft-SDL]. Se si passa codice per i protocolli, è consigliabile seguire una metodologia, ad esempio Microsoft SDL. Prestare particolare attenzione alle considerazioni sulla sicurezza nelle specifiche degli standard per ogni protocollo.

> [!NOTE]
> Si sta cercando la libreria di autenticazione Azure Active Directory (ADAL)? Vedere la [Guida alla libreria adal](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Tipi di librerie

L'endpoint della piattaforma Microsoft Identity funziona con due tipi di librerie:

* **Librerie client**: i client e i server nativi usano le librerie client per acquisire i token di accesso per chiamare una risorsa, ad esempio Microsoft Graph.
* **Librerie middleware server**: le app Web usano librerie middleware server per l'accesso degli utenti. e dalle API Web per convalidare i token inviati da client nativi o altri server.

## <a name="library-support"></a>Supporto per le librerie

Le librerie dispongono di due categorie di supporto:

* **Librerie supportate da Microsoft**: Microsoft offre correzioni per queste librerie e ha applicato la due diligence SDL a queste librerie.
* **Compatibile**: Microsoft ha testato queste librerie in scenari di base e ha confermato che funzionano con l'endpoint della piattaforma di identità Microsoft. Microsoft non fornisce correzioni per queste librerie e non ha eseguito una revisione di tali librerie. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria.

Per un elenco di librerie che funzionano con l'endpoint della piattaforma di identità Microsoft, vedere le sezioni seguenti.

## <a name="microsoft-supported-client-libraries"></a>Librerie client supportate da Microsoft

Usare le librerie di autenticazione client per acquisire un token per chiamare un'API Web protetta.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Informazioni di riferimento | Documenti di carattere concettuale | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [App a singola pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Riferimento](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Documentazione concettuale](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL angolare | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [SPA angolare](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Riferimento](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Documentazione concettuale](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![Piattaforma UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [App desktop](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview&preserve-view=true) |[Documentazione concettuale](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Icona di .NET Core](media/sample-v2-code/logo_NETCore.png) | Microsoft Identity Web  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web) |[GitHub](https://github.com/AzureAD/microsoft-identity-web) | [Esempi](https://aka.ms/ms-id-web/samples) | [Microsoft. Identity. Web](/dotnet/api/microsoft.identity.web?view=azure-dotnet-preview&preserve-view=true) |[Documentazione concettuale](https://aka.ms/ms-id-web/conceptual-doc) | [Roadmap](https://github.com/AzureAD/microsoft-identity-web/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Esempi](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Esempi](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Riferimento](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS e macOS | MSAL iOS e macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [app iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [app MacOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Riferimento](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Documentazione concettuale](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Repository centrale](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [App Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Documentazione concettuale](msal-overview.md) |[Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Librerie middleware server supportate da Microsoft

Usare le librerie middleware per proteggere le applicazioni Web e le API Web. App Web o API Web scritte con ASP.NET o ASP.NET Core usano le librerie middleware.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Informazioni di riferimento
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Security |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[App MVC](quickstart-v2-aspnet-webapp.md) |[Informazioni di riferimento sulle API REST](/dotnet/api/?view=aspnetcore-2.0&preserve-view=true) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Estensioni IdentityModel per .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [App MVC](quickstart-v2-aspnet-webapp.md) |[Riferimento](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet&preserve-view=true) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Passport Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [App Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Librerie supportate da Microsoft per sistema operativo/lingua

In termini di sistemi operativi supportati rispetto alle lingue, il mapping è il seguente:

| Piattaforma    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Novell. iOS) | MSAL.Net (Novell. Android)|
| Swift <br> Objective-C |            |            | [MSAL per iOS e MacOS](msal-overview.md) | [MSAL per iOS e MacOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.js | Passport. Node | Passport. Node | Passport. Node |

Vedere anche [scenari per piattaforme e linguaggi supportati](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Librerie client compatibili

| Piattaforma | Nome libreria | Versione testata | Codice sorgente | Esempio |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versione 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[MSAL Vue](https://github.com/mvertopoulos/vue-msal) | Versione 3.0.3 |[VME-MSAL](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Versione 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Libreria OpenID Connect Gluu](https://github.com/GluuFederation/oxAuth) | [Versione 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Libreria OpenID Connect Gluu](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Richieste-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versione 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Richieste-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID-client](https://github.com/panva/node-openid-client) | [Versione 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versione 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-OAuth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Autenticazione dell'app nativa React](https://github.com/FormidableLabs/react-native-app-auth) | [Versione 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Autenticazione dell'app nativa React](https://github.com/FormidableLabs/react-native-app-auth) | |

Per qualsiasi libreria conforme agli standard, è possibile usare l'endpoint della piattaforma Microsoft Identity. È importante conoscerne il supporto:

* Per problemi e richieste di nuove funzionalità nel codice della libreria, contattare il proprietario della libreria.
* Per problemi e richieste di nuove funzionalità nell'implementazione del protocollo sul lato servizio, contattare Microsoft.
* Consente di [archiviare una richiesta](https://feedback.azure.com/forums/169401-azure-active-directory) di funzionalità per le funzionalità aggiuntive che si desidera visualizzare nel protocollo.
* [Creare una richiesta di supporto](../../azure-portal/supportability/how-to-create-azure-support-request.md) se si riscontra un problema per cui l'endpoint della piattaforma Microsoft Identity non è conforme a OAuth 2,0 o OpenID Connect 1,0.

## <a name="related-content"></a>Contenuti correlati

Per ulteriori informazioni sull'endpoint della piattaforma Microsoft Identity, vedere la [Panoramica della piattaforma di identità Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
