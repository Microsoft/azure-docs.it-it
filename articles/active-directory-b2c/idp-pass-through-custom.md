---
title: Passare un token di accesso tramite criteri personalizzati all'appPass an access token through a custom policy to your app
titleSuffix: Azure AD B2C
description: Informazioni su come passare un token di accesso per i provider di identità OAuth 2.0 come attestazione tramite un criterio personalizzato all'applicazione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187793"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passare un token di accesso tramite un criterio personalizzato all'applicazione in Azure Active Directory B2C

Un [criterio personalizzato](custom-policy-get-started.md) in Azure Active Directory B2C (Azure AD B2C) offre agli utenti dell'applicazione l'opportunità di iscriversi o accedere con un provider di identità. Quando questo si verifica, Azure AD B2C riceve un [token di accesso](tokens-overview.md) dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Aggiungere un tipo di attestazione e l'attestazione di output nel criterio personalizzato per passare il token attraverso le applicazioni registrate in Azure AD B2C.

Azure AD B2C supporta il passaggio del token di accesso dei provider di identità [OAuth 2.0](authorization-code-flow.md) e [OpenID Connect](openid-connect.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="prerequisites"></a>Prerequisiti

* I criteri personalizzati sono configurati con un provider di identità OAuth 2.0 o OpenID Connect.

## <a name="add-the-claim-elements"></a>Aggiungere gli elementi di attestazione

1. Aprire il file *TrustframeworkExtensions.xml* e aggiungere il seguente elemento **ClaimType** con un identificatore di `identityProviderAccessToken` per l'elemento **ClaimsSchema**:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Aggiungere l'elemento **OutputClaim** all'elemento **TechnicalProfile** per ogni provider di identità OAuth 2.0 per cui si desidera il token di accesso. L'esempio seguente illustra l'elemento aggiunto al profilo tecnico di Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Salvare il file *TrustframeworkExtensions.xml*.
4. Aprire un file dei criteri relying party come *SignUpOrSignIn.xml* e aggiungere l'elemento **OutputClaim** elemento al **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Salvare il file dei criteri.

## <a name="test-your-policy"></a>Verificare i criteri

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per riuscire a esaminare le attestazioni in essa.

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD facendo clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Identity Experience Framework**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustframeworkExtensions.xml*.
7. Selezionare **Carica**.
8. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_signup_signin*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Selezionare **Esegui adesso**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato in jwt.ms con idp_access_token blocco evidenziato](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui token, vedere [Azure Active Directory B2C token reference](tokens-overview.md).
