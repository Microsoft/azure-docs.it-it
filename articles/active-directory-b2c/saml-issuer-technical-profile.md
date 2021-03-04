---
title: Definire un profilo tecnico per un'autorità di certificazione SAML nell'ambito di criteri personalizzati
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico per un'autorità di certificazione del token SAML (Security Assertion Markup Language) nei criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54869c14cf7c5a7e43f34102f5c95e37689dfee8
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095341"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico per un'autorità di certificazione del token SAML nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) rilascia tipi diversi di token di sicurezza durante l'elaborazione di ogni flusso di autenticazione. Un profilo tecnico per un'autorità di certificazione del token SAML emette un token SAML che viene restituito all'applicazione basata su attestazioni (provider di servizi). In genere questo profilo tecnico è l'ultimo passaggio di orchestrazione nel percorso utente.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `SAML2`. Impostare l'elemento **OutputTokenFormat** su `SAML2`.

Nell'esempio seguente viene illustrato un profilo tecnico per `Saml2AssertionIssuer`:

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Input, output e attestazioni persistenti

Gli elementi **InputClaims**, **OutputClaims** e **PersistClaims** sono vuoti o assenti. Anche gli elementi **InutputClaimsTransformations** e **OutputClaimsTransformations** sono assenti.

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| IssuerUri | No | Nome dell'autorità emittente visualizzato nella risposta SAML. Questo valore deve corrispondere al nome configurato nell'applicazione basata su attestazioni. |
| XmlSignatureAlgorithm | No | Metodo che Azure AD B2C utilizza per firmare l'asserzione SAML. I valori possibili sono: `Sha256`, `Sha384`, `Sha512` o `Sha1`. Verificare di configurare l'algoritmo di firma per entrambe le parti con lo stesso valore. Usare solo l'algoritmo supportato dal certificato. Per configurare la risposta SAML, vedere [Opzioni per la registrazione di un'applicazione SAML](saml-service-provider.md)|
|TokenNotBeforeSkewInSeconds| No| Specifica l'inclinazione, come numero intero, per il timestamp che contrassegna l'inizio del periodo di validità. Maggiore è questo numero, più avanti nel tempo il periodo di validità inizia per quanto riguarda l'ora in cui vengono emesse le attestazioni per l'relying party. Ad esempio, quando TokenNotBeforeSkewInSeconds è impostato su 60 secondi, se il token viene emesso a 13:05:10 UTC, il token è valido da 13:04:10 UTC. Il valore predefinito è 0. Il valore massimo è 3600 (un'ora). |
|TokenLifeTimeInSeconds| No| Specifica la durata dell'asserzione SAML. Questo valore è in secondi rispetto al valore NotBefore a cui si fa riferimento sopra. Il valore predefinito è 300 secondi (5 min). |


## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento CryptographicKeys contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| MetadataSigning | Sì | Certificato X509 (set di chiavi RSA) da usare per firmare i metadati SAML. Azure AD B2C usa questa chiave per firmare i metadati. |
| SamlMessageSigning| Sì| Specificare il certificato X509 (set di chiavi RSA) da usare per firmare i messaggi SAML. Azure AD B2C usa questa chiave per firmare la risposta `<samlp:Response>` inviata all'applicazione basata su attestazioni.|

## <a name="session-management"></a>Gestione delle sessioni

Per configurare le sessioni SAML di Azure AD B2C tra un'applicazione basata su attestazioni e l'attributo dell'elemento `UseTechnicalProfileForSessionManagement`, fare riferimento alla sessione Single Sign-On [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="next-steps"></a>Passaggi successivi

Per esempi relativi all'uso di un profilo tecnico dell'autorità di certificazione SAML, vedere gli articoli seguenti:

- [Registrare un'applicazione SAML in Azure AD B2C](saml-service-provider.md)

