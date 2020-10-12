---
title: Configurare l'accesso con un account Azure AD usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Configurare l'accesso con un account Azure Active Directory in Azure Active Directory B2C usando criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06ba565de8ca24c8c0baa576b74e70035384be09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388426"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare l'accesso con un account Azure Active Directory usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per gli utenti da un'organizzazione Azure Active Directory (Azure AD) usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Prerequisiti

Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare la chiave dell'applicazione creata nel tenant di Azure AD B2C.

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Immettere un **nome** per la chiave dei criteri. Ad esempio: `ContosoAppSecret`.  Il prefisso `B2C_1A_` viene aggiunto automaticamente al nome della chiave quando viene creato, quindi il riferimento nel codice XML nella sezione seguente consiste nel *B2C_1A_ContosoAppSecret*.
1. In **Secret**immettere il segreto client registrato in precedenza.
1. In **Uso chiave** selezionare `Signature`.
1. Selezionare **Create** (Crea).

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con Azure AD, è necessario definire Azure AD come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire Azure AD come provider di attestazioni aggiungendo Azure AD all'elemento **ClaimsProvider** nel file di estensione dei criteri.

1. Aprire il file *TrustFrameworkExtensions.xml*.
2. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
3. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Nell'elemento **ClaimsProvider** aggiornare il valore di **Domain** con un valore univoco che ne consenta la distinzione rispetto ad altri provider di identità. Ad esempio `Contoso`. Non aggiungere `.com` alla fine dell'impostazione del dominio.
5. Nell'elemento **ClaimsProvider** aggiornare il valore di **DisplayName** con un nome descrittivo per il provider di attestazioni. Questo valore non è attualmente usato.

### <a name="update-the-technical-profile"></a>Aggiornare il profilo tecnico

Per ottenere un token dall'endpoint di Azure AD, è necessario definire i protocolli che Azure AD B2C deve usare per comunicare con Azure AD. Questa operazione viene eseguita all'interno dell'elemento **TechnicalProfile** di **ClaimsProvider**.

1. Aggiornare l'ID dell'elemento **TechnicalProfile**. Questo ID viene usato per fare riferimento a questo profilo tecnico da altre parti del criterio, ad esempio `OIDC-Contoso` .
1. Aggiornare il valore di **DisplayName**. Questo valore verrà visualizzato sul pulsante di accesso nella schermata di accesso.
1. Aggiornare il valore di **Description**.
1. Azure AD usa il protocollo OpenID Connect, pertanto è necessario verificare che il valore di **Protocol** sia `OpenIdConnect`.
1. Impostare il valore di **METADATA** su `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, dove `tenant-name` è il nome del tenant di Azure AD. Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Impostare **client_id** sull'ID applicazione ottenuto con la registrazione dell'applicazione.
1. In **CryptographicKeys**aggiornare il valore di **ID riferimento archiviazione** con il nome della chiave dei criteri creata in precedenza. Ad esempio: `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Caricare il file di estensione per la verifica

I criteri sono stati a questo punto configurati in modo che Azure AD B2C possa comunicare con la directory di Azure AD. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
1. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
1. Fare clic su **Carica**.

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è ancora disponibile in nessuna delle pagine di iscrizione/accesso. Per renderlo disponibile, creare un duplicato di un percorso utente modello esistente e quindi modificarlo in modo che disponga anche del provider di identità Azure AD:

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante del provider di identità in una pagina di iscrizione/accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per Azure AD, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato in *TrustFrameworkExtensions.xml*.
1. In **ClaimsProviderSelections** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `ContosoExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. L'azione, in questo caso, consiste nel far comunicare Azure AD B2C con Azure AD per ricevere un token. È possibile farlo collegando il profilo tecnico per il provider di attestazioni di Azure AD:

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
1. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare lo stesso valore per **ID** usato per **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l' **ID** del profilo tecnico creato in precedenza. Ad esempio: `OIDC-Contoso`.

1. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione registrata nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInContoso.xml*.
1. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInContoso`.
1. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del percorso utente creato in precedenza. Ad esempio, *SignUpSignInContoso*.
1. Salvare le modifiche e caricare il file.
1. In **criteri personalizzati**selezionare il nuovo criterio nell'elenco.
1. Nell'elenco a discesa **Seleziona applicazione** selezionare l'applicazione Azure ad B2C creata in precedenza. Ad esempio, *testapp1*.
1. Copiare l' **endpoint Run Now** e aprirlo in una finestra del browser privata, ad esempio in modalità in incognito in Google Chrome o in una finestra InPrivate in Microsoft Edge. L'apertura in una finestra del browser privata consente di testare il percorso utente completo senza usare le credenziali Azure AD attualmente memorizzate nella cache.
1. Selezionare il pulsante Azure AD accedi, ad esempio *Contoso Employee*, quindi immettere le credenziali per un utente nel tenant dell'organizzazione Azure ad. Viene richiesto di autorizzare l'applicazione, quindi immettere le informazioni per il profilo.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

## <a name="next-steps"></a>Passaggi successivi

Quando si usano criteri personalizzati, a volte è necessario disporre di informazioni aggiuntive per la risoluzione dei problemi relativi a un criterio durante lo sviluppo.

Per semplificare la diagnosi dei problemi, è possibile impostare temporaneamente i criteri in "modalità di sviluppo" e raccogliere i log con applicazione Azure Insights. Scopri come [Azure Active Directory B2C: raccolta di log](troubleshoot-with-application-insights.md).
