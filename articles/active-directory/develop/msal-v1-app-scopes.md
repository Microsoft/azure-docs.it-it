---
title: Ambiti per le app v 1.0 (MSAL) | Azure
description: Informazioni sugli ambiti per un'applicazione v1.0 con Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7e2fcf2dc0dc53038b82bbf182cb12f580d88357
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583587"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Ambiti per un'API Web che accettano i token v 1.0

Le autorizzazioni di OAuth2 sono ambiti di autorizzazione che un'applicazione di API Web (risorsa) Azure Active Directory (Azure AD) per sviluppatori (v 1.0) espone alle applicazioni client. Questi ambiti di autorizzazione possono essere concessi alle applicazioni client durante il consenso. Vedere la sezione relativa a `oauth2Permissions` nelle [informazioni di riferimento sul manifesto dell'applicazione in Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Ambiti per richiedere l'accesso a specifiche autorizzazioni OAuth2 di un'applicazione v1.0

Per acquisire i token per ambiti specifici di un'applicazione v 1.0, ad esempio l'API Microsoft Graph, ovvero https://graph.microsoft.com) creare gli ambiti concatenando un identificatore di risorsa desiderato con l'autorizzazione OAuth2 desiderata per tale risorsa.

Ad esempio, per accedere per conto dell'utente a un'API Web v1.0 dove l'URI dell'ID app è `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Per leggere e scrivere con MSAL.NET Azure AD usando l'API Microsoft Graph (https: \/ /Graph.Microsoft.com/), creare un elenco di ambiti, come illustrato negli esempi seguenti:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Per scrivere l'ambito corrispondente all'API Azure Resource Manager (https: \/ /Management.Core.Windows.NET/), richiedere l'ambito seguente (prendere nota delle due barre):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Usare due barre perché l'API Azure Resource Manager prevede una barra nell'attestazione del pubblico (AUD) e quindi esiste una barra per separare il nome dell'API dall'ambito.

La logica usata da Azure AD è la seguente:

- Per l'endpoint ADAL (Azure AD v 1.0) con un token di accesso v 1.0 (l'unico possibile), AUD = Resource
- Per MSAL (piattaforma di identità Microsoft) che richiede un token di accesso per una risorsa che accetta i token v 2.0, `aud=resource.AppId`
- Per MSAL (endpoint v 2.0) che richiede un token di accesso per una risorsa che accetta un token di accesso v 1.0 (ovvero il caso precedente), Azure AD analizza i destinatari desiderati dall'ambito richiesto, prendendo tutti gli elementi prima dell'ultima barra e usandola come identificatore di risorsa. Se, pertanto, https: \/ /database.Windows.NET prevede un pubblico di "https: \/ /database.Windows.NET/", è necessario richiedere un ambito di "https: \/ /database.Windows.NET//.default". Vedere anche il problema [di GitHub #747: la barra finale dell'URL della risorsa viene omessa, causando un errore di autenticazione SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Ambiti per richiedere l'accesso a tutte le autorizzazioni di un'applicazione v1.0

Per acquisire un token per tutti gli ambiti statici di un'applicazione v1.0, accodare ".default" all'URI dell'ID app dell'API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Ambiti da richiedere per un flusso di credenziali client/App daemon

Anche nel caso del flusso di credenziali client, l'ambito da passare è `/.default`. Ciò indica ad Azure AD: tutte le autorizzazioni a livello di app per cui l'amministratore ha concesso il consenso durante la registrazione dell'applicazione.
