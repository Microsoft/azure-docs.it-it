---
title: Azure Active Directory Authentication Library | Documentazione Microsoft
description: Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861171"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Library

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory Authentication Library (ADAL) versione 1.0 consente agli sviluppatori di applicazioni di autenticare gli utenti in Active Directory (AD) locale o nel cloud e di ottenere token per proteggere le chiamate API. ADAL semplifica l'autenticazione per gli sviluppatori grazie a funzionalità come:

- Cache dei token configurabili in cui archiviare i token di accesso e i token di aggiornamento
- Aggiornamento del token automatico quando un token di accesso scade ed è disponibile un token di aggiornamento
- Supporto per le chiamate di metodo asincrone

> [!NOTE]
> Si cercano le librerie v2.0 di Azure AD (MSAL)? Controllare la [Guida alle librerie MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Librerie client supportate da Microsoft

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Informazioni di riferimento
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [App desktop](../develop/quickstart-v2-windows-desktop.md) |[Riferimento](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[App a singola pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[app iOS](../develop/quickstart-v2-ios.md) | [Riferimento](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[App Android](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [ App WebNode.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Riferimento](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[App Web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Riferimento](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[App Web Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Riferimento](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Librerie server supportate da Microsoft

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Informazioni di riferimento
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN per AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Applicazione MVC](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN per OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[App Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN per WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[App Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Estensioni del protocollo di identità per .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestore token JWT per .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Passport Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Scenari

Ecco tre scenari comuni per l'uso di ADAL in un client che accede a una risorsa remota:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticazione di utenti di un'applicazione client nativa in esecuzione su un dispositivo

In questo scenario uno sviluppatore ha un'applicazione client per dispositivi mobili o desktop che deve accedere a una risorsa remota, ad esempio un'API Web. L'API Web non consente chiamate anonime e deve essere chiamata nel contesto di un utente autenticato. L'API Web è stata preconfigurata per considerare attendibili i token di accesso rilasciati da uno specifico tenant di Azure AD. Azure AD è preconfigurato per rilasciare token di accesso per tale risorsa. Per chiamare l'API Web dal client, lo sviluppatore usa ADAL per semplificare l'autenticazione con Azure AD. Il modo più sicuro per usare ADAL è fare in modo che esegua il rendering dell'interfaccia utente per raccogliere le credenziali dell'utente (il rendering viene eseguito come finestra del browser).

ADAL agevola l'autenticazione dell'utente, il recupero di un token di accesso e di un token di aggiornamento da Azure AD e quindi chiama l'API Web usando il token di accesso.

Per un esempio di codice che illustra questo scenario usando l'autenticazione ad Azure AD, vedere [chiamata di un'API Web da parte di un'applicazione WPF client nativa](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticazione di un'applicazione client riservata in esecuzione in un server Web

In questo scenario uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota, ad esempio un'API Web. L'API Web non consente chiamate anonime, pertanto deve essere chiamata da un servizio autorizzato. L'API Web è stata preconfigurata per considerare attendibili i token di accesso rilasciati da uno specifico tenant di Azure AD. Azure AD è preconfigurato per rilasciare token di accesso per quella risorsa a un servizio con le credenziali del client (ID client e segreto). ADAL facilita l'autenticazione del servizio con Azure AD restituendo un token di accesso che può essere usato per chiamare l'API Web. ADAL gestisce anche la durata del token di accesso memorizzandolo nella cache e rinnovandolo quando necessario. Per un esempio di codice che illustra questo scenario, vedere [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet) (Accesso di un'applicazione console daemon a un'API Web).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticazione di un'applicazione client riservata in esecuzione in un server, per conto di un utente

In questo scenario uno sviluppatore ha un'applicazione Web in esecuzione in un server che deve accedere a una risorsa remota, ad esempio un'API Web. L'API Web non consente chiamate anonime, pertanto deve essere chiamata da un servizio autorizzato per conto di un utente autenticato. L'API Web è stata preconfigurata per considerare attendibili i token di accesso rilasciati da un tenant di Azure specifico e Azure AD è preconfigurato per rilasciare token di accesso per quella risorsa a un servizio con le credenziali del client. Una volta che l'utente ha eseguito l'autenticazione nell'applicazione Web, l'applicazione può ottenere un codice di autorizzazione per l'utente da Azure AD. L'applicazione Web può quindi usare Azure AD Authentication Library (ADAL) per ottenere un token di accesso e un token di aggiornamento per conto di un utente da Azure AD usando il codice di autorizzazione e le credenziali client associati all'applicazione. Non appena l'applicazione Web è in possesso del token di accesso, può chiamare l'API Web fino a quando il token non raggiunge la data di scadenza. Alla scadenza del token, l'applicazione Web potrà usare ADAL per ottenere un nuovo token di accesso usando il token di aggiornamento ricevuto in precedenza. Per un esempio di codice che illustra questo scenario, vedere [Native client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) (Accesso di un client nativo a un'API Web).

## <a name="see-also"></a>Vedere anche

- [Guida per gli sviluppatori di Azure Active Directory](v1-overview.md)
- [Scenari di autenticazione per Azure Active Directory](v1-authentication-scenarios.md)
- [Esempi di codice per Azure Active Directory](sample-v1-code.md)
