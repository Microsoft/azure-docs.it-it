---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche quando si usa Azure AD B2C con Microsoft Authentication Library per Android (MSAL. Android
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 902159153bccbea851481e1f81d03e8e70495020
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644273"
---
# <a name="use-msal-for-android-with-b2c"></a>Usare MSAL per Android con B2C

Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/index.yml). Azure AD B2C è un servizio di gestione delle identità, È possibile usarlo per personalizzare e controllare la modalità di iscrizione, accesso e gestione dei profili dei clienti quando usano le applicazioni.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configurare le autorità note e l'URI di Reindirizzamento

In MSAL per Android i criteri B2C (percorsi utente) sono configurati come singole autorità.

Data un'applicazione B2C con due criteri:
- Iscrizione/accesso
    * Chiamato `B2C_1_SISOPolicy`
- Modifica profilo
    * Chiamato `B2C_1_EditProfile`

Il file di configurazione per l'app dichiarerebbe due `authorities` . Uno per ogni criterio. La `type` proprietà di ogni autorità è `B2C` .

>Nota: il `account_mode` deve essere impostato su **multiple** per le applicazioni B2C. Per ulteriori informazioni su [più app client pubbliche dell'account](./single-multi-account.md#multiple-account-public-client-application), vedere la documentazione.

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorities": [
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
      "default": true
    },
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
    }
  ]
}
```

Il `redirect_uri` deve essere registrato nella configurazione dell'app e anche in  `AndroidManifest.xml` per supportare il reindirizzamento durante il flusso di [concessione del codice di autorizzazione](../../active-directory-b2c/authorization-code-flow.md).

## <a name="initialize-ipublicclientapplication"></a>Inizializzare IPublicClientApplication

`IPublicClientApplication` viene costruito da un metodo factory per consentire l'analisi asincrona della configurazione dell'applicazione.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Acquisire un token in modo interattivo

Per acquisire un token in modo interattivo con MSAL, compilare un' `AcquireTokenParameters` istanza di e fornirla al `acquireToken` metodo. La richiesta di token riportata di seguito utilizza l' `default` autorità.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Rinnovare automaticamente un token

Per acquisire un token in modo invisibile all'utente con MSAL, compilare un' `AcquireTokenSilentParameters` istanza di e fornirla al `acquireTokenSilentAsync` metodo. A differenza del `acquireToken` metodo, `authority` è necessario specificare per acquisire un token in modo invisibile all'utente.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Specificare un criterio

Poiché i criteri in B2C sono rappresentati come autorità separate, la chiamata di un criterio diverso da quello predefinito viene eseguita specificando una `fromAuthority` clausola quando si costruiscono `acquireToken` `acquireTokenSilent` parametri o.  Ad esempio:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Gestire i criteri di modifica della password

Il flusso dell'utente per l'iscrizione o l'accesso all'account locale Visualizza una **password dimenticata?** la creazione. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password.

Viene invece restituito il codice di errore `AADB2C90118` all'app. L'app deve gestire questo codice di errore eseguendo un flusso utente specifico che reimposta la password.

Per intercettare un codice di errore di reimpostazione della password, è possibile usare l'implementazione seguente all'interno di `AuthenticationCallback` :

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Usare IAuthenticationResult

L'acquisizione di un token ha avuto esito positivo in un `IAuthenticationResult` oggetto. Contiene il token di accesso, le attestazioni utente e i metadati.

### <a name="get-the-access-token-and-related-properties"></a>Ottenere il token di accesso e le proprietà correlate

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Ottenere l'account autorizzato

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Attestazioni IdToken

Le attestazioni restituite in IdToken vengono popolate dal servizio token di sicurezza (STS), non da MSAL. A seconda del provider di identità (IdP) usato, alcune attestazioni potrebbero essere assenti. Alcuni IDP attualmente non forniscono l' `preferred_username` attestazione. Poiché questa attestazione viene utilizzata da MSAL per la memorizzazione nella cache, `MISSING FROM THE TOKEN RESPONSE` al suo posto viene utilizzato un valore segnaposto. Per altre informazioni sulle attestazioni IdToken B2C, vedere [Cenni preliminari sui token in Azure Active Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Gestione di account e criteri

B2C considera ogni criterio come un'autorità separata. Pertanto, i token di accesso, i token di aggiornamento e i token ID restituiti da ogni criterio non sono intercambiabili. Questo significa che ogni criterio restituisce un `IAccount` oggetto separato i cui token non possono essere usati per richiamare altri criteri.

Ogni criterio aggiunge un `IAccount` alla cache per ogni utente. Se un utente accede a un'applicazione e richiama due criteri, avranno due `IAccount` . Per rimuovere l'utente dalla cache, è necessario chiamare `removeAccount()` per ogni criterio.

Quando si rinnovano i token per un criterio con `acquireTokenSilent` , specificare lo stesso valore `IAccount` restituito dalle chiamate precedenti del criterio a  `AcquireTokenSilentParameters` . Se si specifica un account restituito da un altro criterio, verrà generato un errore.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle Azure Active Directory B2C (Azure AD B2C) in [che cosa sono Azure Active Directory B2C?](../../active-directory-b2c/overview.md)