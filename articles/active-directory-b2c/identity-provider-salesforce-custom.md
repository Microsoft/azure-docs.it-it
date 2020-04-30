---
title: Configurare l'accesso con un provider SAML Salesforce usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Configurare l'accesso con un provider SAML Salesforce usando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 183fe1604cc363a9121d5eef3737751c54e9bdf1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229715"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un provider SAML Salesforce usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti di un'organizzazione Salesforce usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure ad B2C). Per abilitare l'accesso, è necessario aggiungere un [profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md) a un criterio personalizzato.

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
- Se non è già stato fatto, iscriversi per ottenere un [account Developer Edition gratuito](https://developer.salesforce.com/signup). Questo articolo usa [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Configurazione di un dominio personale](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) per la propria organizzazione Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurare Salesforce come provider di identità

1. [Eseguire l'accesso a Salesforce](https://login.salesforce.com/).
2. Nel menu a sinistra espandere **Identity** (Identità) in **Settings** (Impostazioni) e quindi selezionare **Identity Provider** (Provider di identità).
3. Selezionare **Enable Identity Provider** (Abilita provider di identità).
4. In **Select the certificate** (Selezionare il certificato) selezionare il certificato che si desidera venga usato da Salesforce per comunicare con Azure AD B2C. È possibile usare il certificato predefinito.
5. Fare clic su **Save**.

### <a name="create-a-connected-app-in-salesforce"></a>Creare un'app connessa in Salesforce

1. Nella pagina **provider di identità** selezionare i **provider di servizi vengono ora creati tramite app connesse. Fare clic qui.**
2. In **Basic Information** (Informazioni di base) immettere i valori richiesti per l'app connessa.
3. In **Web App Settings** (Impostazioni app Web) selezionare la casella **Enable SAML** (Abilita SAML).
4. Nel campo **Entity ID** (ID entità) immettere l'URL seguente. Assicurarsi di sostituire il valore `your-tenant` con il nome del tenant di Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Nel campo **ACS URL** (URL ACS) immettere l'URL seguente. Assicurarsi di sostituire il valore `your-tenant` con il nome del tenant di Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Scorrere l'elenco fino alla fine e quindi fare clic su **Save** (Salva).

### <a name="get-the-metadata-url"></a>Ottenere l'URL dei metadati

1. Nella pagina di panoramica dell'app connessa fare clic su **Manage** (Gestisci).
2. Copiare il valore per **Metadata Discovery Endpoint** (Endpoint di individuazione dei metadati) e salvarlo. Questo valore sarà usato più avanti in questo articolo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurare gli utenti di Salesforce per la federazione

1. Nella pagina **Manage** (Gestisci) dell'app connessa fare clic su **Manage Profiles** (Gestisci profili).
2. Selezionare i profili (o gruppi di utenti) di cui si intende eseguire la federazione con Azure AD B2C. Come amministratore di sistema, è necessario selezionare la casella per **System Administrator** (Amministratore di sistema) in modo che sia possibile eseguire la federazione con l'account di Salesforce.

## <a name="generate-a-signing-certificate"></a>Generare un certificato di firma

Le richieste inviate a Salesforce devono essere firmate da Azure AD B2C. Per generare un certificato di firma, aprire Azure PowerShell ed eseguire i comandi seguenti.

> [!NOTE]
> Assicurarsi di aggiornare il nome del tenant e la password nelle prime due righe.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il certificato creato nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Upload`.
7. Immettere un **nome** per il criterio. Ad esempio, SAMLSigningCert. Al nome della chiave viene aggiunto automaticamente il prefisso `B2C_1A_`.
8. Cercare e selezionare il certificato B2CSigningCert.pfx creato.
9. Immettere la **password** per il certificato.
3. Scegliere **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account Salesforce, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account Salesforce come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri. Per altre informazioni, vedere [definire un profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md).

1. Aprire *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aggiornare il valore di **PartnerEntity** con l'URL dei metadati di Salesforce copiato in precedenza.
1. Aggiornare il valore di entrambe le istanze di **StorageReferenceId** con il nome della chiave del certificato di firma. Ad esempio, B2C_1A_SAMLSigningCert.
1. Individuare la `<ClaimsProviders>` sezione e aggiungere il frammento di codice XML seguente. Se il criterio contiene già il `SM-Saml-idp` profilo tecnico, andare al passaggio successivo. Per ulteriori informazioni, vedere [Single Sign-on Session Management](custom-policy-reference-sso.md).

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Salvare il file.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C possa comunicare con l'account Salesforce. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è disponibile nelle schermate di iscrizione o accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità Salesforce.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account LinkedIn, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente appena creato.
2. In **ClaimsProviderSelects** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con un account Salesforce per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare lo stesso valore per **ID** usato per **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l' **ID** del profilo tecnico creato in precedenza. Ad esempio: `LinkedIn-OAUTH`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione registrata nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file di relying party che avvia il percorso utente appena creato:

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInSalesforce.xml*.
2. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInSalesforce`.
3. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente che è stato creato (SignUpSignInSalesforce).
5. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
6. Verificare che nel campo **Selezionare l'applicazione** sia selezionata l'applicazione Azure AD B2C creata e quindi testarla facendo clic su **Esegui adesso**.
