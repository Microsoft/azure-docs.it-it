---
title: Definire un profilo tecnico OAuth1 in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico OAuth 1,0 in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96936659"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico OAuth1 in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per il provider di identità del [protocollo OAuth 1,0](https://tools.ietf.org/html/rfc5849) . Questo articolo descrive le specifiche di un profilo tecnico per l'interazione con un provider di attestazioni che supporta questo protocollo standardizzato. Con un profilo tecnico OAuth1, è possibile eseguire la Federazione con un provider di identità basato su OAuth1, ad esempio Twitter. La Federazione con il provider di identità consente agli utenti di accedere con le identità aziendali o di social networking esistenti.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `OAuth1`. Ad esempio, il protocollo per il profilo tecnico **Twitter-OAUTH1** è `OAuth1`.

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Attestazioni di input

Gli elementi **InputClaims** e **InputClaimsTransformations** sono vuoti o assenti.

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni restituite dal provider di identità OAuth1. Può essere necessario eseguire il mapping del nome dell'attestazione definito nei criteri per il nome definito nel provider di identità. È anche possibile includere attestazioni che non sono restituite dal provider di identità purché siano impostate sull'attributo **DefaultValue**.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

L'esempio seguente mostra le attestazioni restituite dal provider di identità Twitter:

- Attestazione **user_id** mappata all'attestazione **issuerUserId** .
- L'attestazione **screen_name** di cui si esegue il mapping per l'attestazione **displayName**.
- L'attestazione **email** senza eseguire il mapping del nome.

Il profilo tecnico restituisce anche le attestazioni che non vengono restituite dal provider di identità:

- L'attestazione **identityProvider** che contiene il nome del provider di identità.
- L'attestazione **authenticationSource** con un valore predefinito di `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_id | Sì | L'identificatore dell'attestazione del provider di identità. |
| ProviderName | No | Il nome del provider di identità. |
| request_token_endpoint | Sì | L'URL dell'endpoint del token richiesta come per RFC 5849. |
| authorization_endpoint | Sì | L'URL dell'endpoint di autorizzazione come per RFC 5849. |
| access_token_endpoint | Sì | L'URL dell'endpoint token come per RFC 5849. |
| ClaimsEndpoint | No | L'URL dell'endpoint di informazioni per l'utente. |
| ClaimsResponseFormat | No | Il formato di risposta delle attestazioni.|

## <a name="cryptographic-keys"></a>Chiavi di crittografia

L'elemento **CryptographicKeys** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| client_secret | Sì | Il segreto client dell'applicazione del provider di identità.   |

## <a name="redirect-uri"></a>URI di reindirizzamento

Quando si configura l'URI di reindirizzamento del provider di identità, immettere `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp`. Assicurarsi di sostituire `{tenant-name}` con il nome del tenant (ad esempio, contosob2c) e `{policy-id}` con l'identificatore del criterio, ad esempio b2c_1a_policy. L'URI di reindirizzamento deve essere tutto minuscolo. Aggiungere un URL di reindirizzamento per tutti i criteri che utilizzano l'account di accesso del provider di identità.

Esempi:

- [Aggiungere Twitter come provider di identità OAuth1 usando i criteri personalizzati](identity-provider-twitter.md)
