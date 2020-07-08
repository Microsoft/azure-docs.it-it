---
title: Gestire la personalizzazione di token e SSO tramite criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come gestire la personalizzazione di token e SSO con criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a35662a3f21aec1306b7b6994e7a08f9cbd467e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389531"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gestire la personalizzazione di token e SSO con criteri personalizzati in Azure Active Directory B2C

Questo articolo fornisce informazioni su come è possibile gestire le configurazioni di token, sessione e Single Sign-On (SSO) usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure ad B2C).

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>Durata e configurazione delle attestazioni del token JTW

Per modificare le impostazioni per le durate dei token, si aggiunge un elemento [ClaimsProviders](claimsproviders.md) nel file di relying party dei criteri su cui si vuole intervenire.  **ClaimsProviders** è un elemento figlio dell'elemento [TrustFrameworkPolicy](trustframeworkpolicy.md).

Inserire l'elemento ClaimsProviders tra l'elemento BasePolicy e l'elemento RelyingParty del file relying party.

Sarà necessario inserirvi le informazioni che influiscono sulle durate dei token. L'XML è simile all'esempio seguente:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Nell'esempio precedente vengono impostati i valori seguenti.

- **Durate dei token di accesso**: il valore di durata dei token viene impostato con l'elemento dei metadati **token_lifetime_secs**. Il valore predefinito è 3600 secondi (60 minuti).
- **Durata del token ID**: il valore di durata del token ID viene impostato con l'elemento dei metadati **id_token_lifetime_secs**. Il valore predefinito è 3600 secondi (60 minuti).
- **Durata del token di aggiornamento**: il valore di durata del token di aggiornamento viene impostato con l'elemento dei metadati **refresh_token_lifetime_secs**. Il valore predefinito è 1209600 secondi (14 giorni).
- **Durata della finestra temporale scorrevole del token di aggiornamento**: per impostare una durata della finestra temporale scorrevole per il token di aggiornamento, impostare il valore dell'elemento dei metadati **rolling_refresh_token_lifetime_secs**. Il valore predefinito è 7776000 giorni (90 giorni). Se non si vuole applicare una durata della finestra temporale scorrevole, sostituire l'elemento con `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Attestazione autorità di certificazione (iss)**: l'attestazione dell'autorità di certificazione (iss) viene impostata con l'elemento dei metadati **IssuanceClaimPattern**. I valori applicabili sono `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Impostazione dell'attestazione che rappresenta l'ID criteri**: per impostare questo valore sono disponibili le opzioni `TFP` (criteri del framework attendibilità) e `ACR` (riferimento al contesto di autenticazione). Il valore consigliato è `TFP`. Impostare **AuthenticationContextReferenceClaimPattern** con il valore `None`.

    Nell'elemento **ClaimsSchema**, aggiungere l'elemento seguente:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Nell'elemento **OutputClaims** aggiungere questo elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Per ACR, rimuovere l'elemento **AuthenticationContextReferenceClaimPattern**.

- **Attestazione soggetto (sub)**: l'impostazione predefinita di questa opzione è ObjectID. Per modificare l'impostazione in `Not Supported`, sostituire questa riga:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    con la riga seguente:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sessione Azure AD B2C](session-overview.md).
- Informazioni su come [configurare il comportamento delle sessioni in criteri personalizzati](session-behavior-custom-policy.md).
- Riferimento: [JwtIssuer](jwt-issuer-technical-profile.md).