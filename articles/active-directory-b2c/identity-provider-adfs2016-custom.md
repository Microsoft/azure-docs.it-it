---
title: Aggiungere ADFS come provider di identità SAML usando criteri personalizzati
titleSuffix: Azure AD B2C
description: Impostare AD FS 2016 usando il protocollo SAML e i criteri personalizzati in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12845f09ac2eb2342cdb1ab82b703ebd3a67c706
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229732"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Aggiungere AD FS come provider di identità SAML tramite criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo illustra come abilitare l'accesso per un account utente ADFS usando [criteri personalizzati](custom-policy-overview.md) in Azure Active Directory B2C (Azure ad B2C). Per abilitare l'accesso, è necessario aggiungere un [profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md) a un criterio personalizzato.

## <a name="prerequisites"></a>Prerequisiti

- Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
- Verificare di avere accesso al file con estensione pfx del certificato con la chiave privata. È possibile generare il proprio certificato autofirmato e caricarlo in Azure AD B2C. Azure AD B2C usa questo certificato per firmare la richiesta SAML inviata al provider di identità SAML.
- Per consentire ad Azure di accettare la password del file con estensione pfx, la password deve essere crittografata con l'opzione TripleDES-SHA1 nell'utilità di esportazione dell'archivio certificati di Windows invece che in AES256-SHA256.

## <a name="create-a-policy-key"></a>Creare una chiave dei criteri

È necessario archiviare il certificato nel tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Nella pagina Panoramica selezionare **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
6. Per **Opzioni** scegliere `Upload`.
7. Immettere un **nome** per la chiave dei criteri. Ad esempio: `SamlCert`. Verrà aggiunto automaticamente il prefisso `B2C_1A_` al nome della chiave.
8. Individuare e selezionare il file di certificato con estensione pfx con la chiave privata.
9. Scegliere **Crea**.

## <a name="add-a-claims-provider"></a>Aggiungere un provider di attestazioni

Per consentire agli utenti di accedere con un account AD FS, è necessario definire l'account come provider di attestazioni con cui Azure AD B2C possa comunicare tramite un endpoint. L'endpoint offre un set di attestazioni che vengono usate da Azure AD B2C per verificare se un utente specifico è stato autenticato.

È possibile definire un account AD FS come provider di attestazioni aggiungendolo all'elemento **ClaimsProviders** nel file di estensione dei criteri. Per altre informazioni, vedere [definire un profilo tecnico del provider di identità SAML](saml-identity-provider-technical-profile.md).

1. Aprire *TrustFrameworkExtensions.xml*.
1. Trovare l'elemento **ClaimsProviders**. Se non esiste, aggiungerlo nell'elemento radice.
1. Aggiungere un nuovo **ClaimsProvider** come illustrato di seguito:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Sostituire `your-ADFS-domain` con il nome del dominio AD FS e il valore dell'attestazione di output **identityProvider** con il DNS, ossia con il valore arbitrario che indica il dominio.

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

A questo punto, i criteri sono stati configurati in modo che Azure AD B2C possa comunicare con l'account AD FS. Provare a caricare il file di estensione dei criteri per verificare che non siano presenti problemi.

1. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
2. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
3. Fare clic su **Carica**.

> [!NOTE]
> L'estensione B2C di Visual Studio Code USA "socialIdpUserId". Per ADFS è necessario anche un criterio di social networking.
>

## <a name="register-the-claims-provider"></a>Registrare il provider di attestazioni

A questo punto, il provider di identità è stato configurato, ma non è disponibile nelle schermate di iscrizione o accesso. Per renderlo disponibile, si crea un duplicato di un percorso utente modello esistente e quindi si modifica tale duplicato in modo che includa anche il provider di identità AD FS.

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
2. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
3. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
4. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
5. Rinominare l'ID del percorso utente. Ad esempio: `SignUpSignInADFS`.

### <a name="display-the-button"></a>Visualizzare il pulsante

L'elemento **ClaimsProviderSelection** è analogo a un pulsante per il provider di identità in una schermata di iscrizione o accesso. Se si aggiunge un elemento **ClaimsProviderSelection** per un account AD FS, quando un utente apre la pagina viene visualizzato un nuovo pulsante.

1. Trovare l'elemento **OrchestrationStep** che include `Order="1"` nel percorso utente creato.
2. In **ClaimsProviderSelections** aggiungere l'elemento riportato di seguito. Impostare **TargetClaimsExchangeId** su un valore appropriato, ad esempio `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Collegare il pulsante a un'azione

Ora che il pulsante è stato posizionato, è necessario collegarlo a un'azione. In questo caso, l'azione consiste nel far comunicare Azure AD B2C con un account AD FS per ricevere un token.

1. Trovare l'elemento **OrchestrationStep** che include `Order="2"` nel percorso utente.
2. Aggiungere l'elemento **ClaimsExchange** seguente assicurandosi di usare per ID lo stesso valore usato per **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza. Ad esempio: `Contoso-SAML2`.

3. Salvare il file *TrustFrameworkExtensions.xml* e caricarlo di nuovo per la verifica.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurare un trust della relying party di AD FS

Per usare AD FS come provider di identità in Azure AD B2C, è necessario creare un trust della relying party di AD FS con i metadati SAML di Azure AD B2C. L'esempio seguente mostra un indirizzo URL che punta ai metadati SAML di un profilo tecnico di Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Sostituire i valori seguenti:

- **tenant** con il nome del tenant, ad esempio your-tenant.onmicrosoft.com.
- **your-policy** con il nome dei criteri. Ad esempio, B2C_1A_signup_signin_adfs.
- **profilo tecnico** con il nome del profilo tecnico del provider di identità SAML. Ad esempio, Contoso-SAML2.

Aprire un browser e passare all'URL. Assicurarsi di digitare l'URL corretto e di avere accesso al file dei metadati XML. Per aggiungere un nuovo un trust della relying party usando lo snap-in di gestione di AD FS e configurare manualmente le impostazioni, eseguire la procedura seguente in un server federativo. Per completare questa procedura, è necessaria almeno l'appartenenza al gruppo **Administrators** o a un gruppo equivalente nel computer locale.

1. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
2. Scegliere **Aggiungi attendibilità componente**.
3. Nella pagina **iniziale** scegliere **In grado di riconoscere attestazioni** e quindi fare clic su **Avvia**.
4. Nella pagina **Seleziona origine dati** selezionare **Import data about the relying party publish online or on a local network** (Importa dati sul relying party pubblicata online o in una rete locale), specificare l'URL dei metadati di Azure AD B2C e quindi fare clic su **Avanti**.
5. Nella pagina **Specifica nome visualizzato** digitare un nome in **Nome visualizzato**, in **Note** digitare una descrizione del trust della relying party e quindi fare clic su **Avanti**.
6. Nella pagina **Scegli criteri di controllo di accesso** selezionare i criteri desiderati e fare clic su **Avanti**.
7. Nella pagina **Aggiunta attendibilità** esaminare le impostazioni e quindi fare clic su **Avanti** per salvare le informazioni sul trust della relying party.
8. Nella pagina **Fine** fare clic su **Chiudi**. Viene automaticamente visualizzata la finestra di dialogo **Modifica regole attestazione**.
9. Selezionare **Aggiungi regola**.
10. In **Modello di regola attestazione** selezionare **Inviare attributi LDAP come attestazioni**.
11. Specificare un nome in **Nome regola attestazione**. Per **archivio attributi**selezionare **Seleziona Active Directory**, aggiungere le attestazioni seguenti, quindi fare clic su **fine** e su **OK**.

    | Attributo LDAP | Tipo di attestazione in uscita |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Si noti che questi nomi non verranno visualizzati nell'elenco a discesa tipo di attestazione in uscita. È necessario digitarli manualmente in. (L'elenco a discesa è effettivamente modificabile).

12.  In base al tipo di certificato è possibile che sia necessario impostare l'algoritmo HASH. Nella finestra delle proprietà del trust della relying party (B2C Demo) selezionare la scheda **Avanzate** e impostare **Secure hash algorithm** su `SHA-256`, quindi fare clic su **OK**.
13. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
14. Selezionare il trust della relying party creato, selezionare **Update from Federation Metadata** (Aggiorna da metadati federazione) e quindi fare clic su **Aggiorna**.

## <a name="create-an-azure-ad-b2c-application"></a>Creare un'applicazione Azure AD B2C

La comunicazione con Azure AD B2C avviene tramite un'applicazione registrata nel tenant B2C. Questa sezione elenca i passaggi facoltativi che è possibile completare per creare un'applicazione di test, se non è già stato fatto.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Aggiornare e testare il file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Creare una copia di *SignUpOrSignIn.xml* nella directory di lavoro e rinominare la copia. Ad esempio, assegnare il nome *SignUpSignInADFS.xml*.
2. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. Ad esempio: `SignUpSignInADFS`.
3. Aggiornare il valore di **PublicPolicyUri** con l'URI dei criteri. Ad esempio, `http://contoso.com/B2C_1A_signup_signin_adfs`
4. Aggiornare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** in modo che corrisponda all'ID del nuovo percorso utente che è stato creato (SignUpSignInADFS).
5. Salvare le modifiche, caricare il file e quindi selezionare i nuovi criteri nell'elenco.
6. Verificare che nel campo **Selezionare l'applicazione** sia selezionata l'applicazione Azure AD B2C creata e quindi testarla facendo clic su **Esegui adesso**.

