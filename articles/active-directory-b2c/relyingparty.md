---
title: 'RelyingParty: Azure Active Directory B2C | Microsoft Docs'
description: Specificare l'elemento RelyingParty di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8c4e65c7ee97b33acbd68bfd8267a334508e25c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203742"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **RelyingParty** specifica il percorso utente da applicare per la richiesta corrente Azure Active Directory B2C (Azure ad B2C). Specifica inoltre l'elenco di attestazioni richiesto dall'applicazione relying party (RP) come parte del token emesso. Un'applicazione RP, ad esempio un'applicazione Web, mobile o desktop, chiama il file dei criteri RP. Il file di criteri RP esegue un'attività specifica, ad esempio l'accesso, la reimpostazione di una password o la modifica di un profilo. Più applicazioni possono usare gli stessi criteri RP e una singola applicazione può usare più criteri. Tutte le applicazioni RP ricevono lo stesso token con attestazioni e l'utente segue lo stesso percorso.

L'esempio seguente mostra un elemento **RelyingParty** nel file dei criteri *B2C_1A_signup_signin*:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

L'elemento **RelyingParty** facoltativo contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Percorso utente predefinito per l'applicazione RP. |
| UserJourneyBehaviors | 0:1 | Ambito dei comportamenti del percorso utente. |
| TechnicalProfile | 1:1 | Profilo tecnico supportato dall'applicazione RP. Il profilo tecnico fornisce un contratto per consentire all'applicazione RP di contattare Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

L'elemento `DefaultUserJourney` specifica un riferimento all'identificatore del percorso utente in genere definito nei criteri di base o delle estensioni. Gli esempi seguenti mostrano il percorso utente di iscrizione o accesso specificato nell'elemento **RelyingParty**:

Criterio *B2C_1A_signup_signin*:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* o *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

L'elemento **DefaultUserJourney** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Identificatore del percorso utente nei criteri. Per altre informazioni, consultare [Percorsi utente](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

L'elemento **UserJourneyBehaviors** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Ambito del comportamento della sessione di Single Sign-On (SSO) di un percorso utente. |
| SessionExpiryType |0:1 | Comportamento di autenticazione della sessione. I valori possibili sono: `Rolling` o `Absolute`. Il valore `Rolling` (predefinito) indica che l'utente rimane connesso, purché sia continuamente attivo nell'applicazione. Il valore `Absolute` indica che l'utente è obbligato a ripetere l'autenticazione una volta superato il periodo di tempo specificato dalla durata della sessione dell'applicazione. |
| SessionExpiryInSeconds | 0:1 | Durata del cookie di sessione di Azure AD B2C specificato come intero e archiviato nel browser dell'utente dopo l'autenticazione. |
| JourneyInsights | 0:1 | Chiave di strumentazione di Azure Application Insights da usare. |
| ContentDefinitionParameters | 0:1 | Elenco di coppie chiave-valore da accodare all'URI di caricamento della definizione del contenuto. |
|ScriptExecution| 0:1| Modalità di esecuzione [JavaScript](javascript-samples.md) supportate. Valori possibili: `Allow` o `Disallow` (impostazione predefinita).

### <a name="singlesignon"></a>SingleSignOn

L'elemento **SingleSignOn** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Scope | Sì | Ambito del comportamento di Single Sign-On (SSO). I valori possibili sono: `Suppressed`, `Tenant`, `Application` o `Policy`. Il `Suppressed` valore indica che il comportamento viene eliminato e all'utente viene sempre richiesta una selezione del provider di identità.  Il valore `Tenant` indica che il comportamento viene applicato a tutti i criteri nel tenant. Ad esempio, a un utente che naviga in due percorsi di criteri per un tenant non viene richiesto di selezionare un provider di identità. Il valore `Application` indica che il comportamento viene applicato a tutti i criteri per l'applicazione che esegue la richiesta. Ad esempio, a un utente che naviga in due percorsi di criteri per un'applicazione non viene richiesto di selezionare un provider di identità. Il valore `Policy` indica che il comportamento si applica solo a un criterio. Ad esempio, a un utente che naviga in due percorsi di criteri per un framework attendibilità viene richiesto di selezionare un provider di identità in caso passaggio da un criterio a un altro. |
| KeepAliveInDays | Sì | Controlla per quanto tempo l'utente rimane connesso. Se si imposta il valore su 0, la funzionalità KMSI viene disattivata. Per altre informazioni, vedere [Mantenere l'accesso](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| No|  Forzare il passaggio di un token ID emesso in precedenza all'endpoint di disconnessione come hint per la sessione autenticata corrente dell'utente finale con il client. I valori possibili sono: `false` (impostazione predefinita) o `true`. Per altre informazioni, vedere [accesso Web con OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

L'elemento **JourneyInsights** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| TelemetryEngine | Sì | Il valore deve essere `ApplicationInsights`. |
| InstrumentationKey | Sì | Stringa che contiene la chiave di strumentazione per l'elemento Application Insights. |
| DeveloperMode | Sì | I valori possibili sono: `true` o `false`. Se il valore è `true`, Application Insights velocizza la telemetria nella pipeline di elaborazione. Questa impostazione è utile per lo sviluppo, ma vincolata a volumi elevati. I log attività dettagliati sono concepiti solo per supportare lo sviluppo di criteri personalizzati. Non usare la modalità di sviluppo in fase di produzione. I log raccolgono tutte le attestazioni inviate verso e dai provider di identità durante lo sviluppo. Se usato in fase di produzione, lo sviluppatore si assume la responsabilità delle informazioni personali raccolte nel log di App Insights di cui è proprietario. Questi log dettagliati vengono raccolti solo quando il valore è impostato su `true`.|
| ClientEnabled | Sì | I valori possibili sono: `true` o `false`. Se il valore è `true`, invia lo script di Application Insights lato client per tenere traccia della visualizzazione della pagina e degli errori del client. |
| ServerEnabled | Sì | I valori possibili sono: `true` o `false`. Se il valore è `true`, invia l'elemento JSON UserJourneyRecorder esistente come evento personalizzato ad Application Insights. |
| TelemetryVersion | Sì | Il valore deve essere `1.0.0`. |

Per altre informazioni, vedere [Raccolta dei log](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

I criteri personalizzati di Azure AD B2C consentono di inviare un parametro in una stringa di query. Passando il parametro all'endpoint HTML, è possibile modificare dinamicamente il contenuto della pagina. È ad esempio possibile modificare l'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C in base a un parametro passato dall'applicazione Web o per dispositivi mobili. Azure AD B2C passa i parametri delle stringhe di query al file HTML dinamico, ad esempio il file con estensione aspx.

L'esempio seguente passa un parametro denominato `campaignId` con un valore `hawaii` nella stringa di query:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

L'elemento **ContentDefinitionParameters** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Stringa che contiene la coppia chiave-valore accodata alla stringa di query di un URI di caricamento della definizione del contenuto. |

L'elemento **ContentDefinitionParameter** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Nome | Sì | Nome della coppia chiave-valore. |

Per altre informazioni, vedere [Configurare l'interfaccia utente con contenuto dinamico usando criteri personalizzati](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>TechnicalProfile

L'elemento **TechnicalProfile** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Il valore deve essere `PolicyProfile`. |

L'elemento **TechnicalProfile** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Stringa che contiene il nome del profilo tecnico. |
| Descrizione | 0:1 | Stringa che contiene la descrizione del profilo tecnico. |
| Protocollo | 1:1 | Protocollo usato per la federazione. |
| Metadati | 0:1 | Raccolta di *Item* delle coppie chiave-valore usate dal protocollo di comunicazione con l'endpoint durante una transazione per configurare l'interazione tra la relying party e altri membri della community. |
| OutputClaims | 1:1 | Elenco di tipi di attestazione eseguiti come output nel profilo tecnico. Ognuno di questi elementi contiene un riferimento a un **ClaimType** già definito nella sezione **ClaimsSchema** o in un criterio da cui eredita questo file di criteri. |
| SubjectNamingInfo | 1:1 | Nome del soggetto usato nei token. |

L'elemento **Protocol** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Nome | Sì | Nome di un protocollo valido supportato da Azure AD B2C usato come parte del profilo tecnico. I valori possibili sono: `OpenIdConnect` o `SAML2`. Il valore `OpenIdConnect` rappresenta lo standard del protocollo OpenID Connect 1.0 in base alla specifica di OpenID Foundation. `SAML2` rappresenta lo standard del protocollo SAML 2.0 in base alla specifica di OASIS. |

## <a name="outputclaims"></a>OutputClaims

L'elemento **OutputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Nome di un tipo di attestazione previsto nell'elenco supportato per i criteri a cui la relying party esegue la sottoscrizione. Questa attestazione viene usata come output per il profilo tecnico. |

L'elemento **OutputClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sì | Riferimento a un **ClaimType** già definito nella sezione **ClaimsSchema** nel file dei criteri. |
| DefaultValue | No | Valore predefinito che può essere usato se il valore dell'attestazione è vuoto. |
| PartnerClaimType | No | Invia l'attestazione in un nome diverso, come configurato nella definizione ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Con l'elemento **SubjectNameingInfo** viene controllato il valore del soggetto del token:
- **Token JWT** : `sub` attestazione. Indica l'entità di sicurezza su cui il token rilascia informazioni, ad esempio l'utente di un'applicazione. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione sicuri, ad esempio quando il token viene usato per accedere a una risorsa. Per impostazione predefinita, l'attestazione dell'oggetto viene popolata con l'ID oggetto dell'utente nella directory. Per altre informazioni, vedere [Configurazione di token, sessione e accesso Single Sign-On](session-behavior.md).
- **Token SAML** - Elemento `<Subject><NameID>` che identifica il soggetto.

L'elemento **SubjectNamingInfo** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ClaimType | Sì | Riferimento all'elemento **PartnerClaimType** di un'attestazione di output. Le attestazioni di output devono essere definite nella raccolta **OutputClaims** di criteri della relying party. |

Nell'esempio seguente viene illustrato come definire un relying party OpenID Connect. Le informazioni sul nome del soggetto sono configurate come `objectId`:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Il token JWT include l'attestazione `sub` con l'elemento objectId dell'utente:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
