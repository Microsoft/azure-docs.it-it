---
title: Personalizzare le attestazioni delle app tenant di Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Questa pagina descrive il mapping delle attestazioni di Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 4c7474b001284286ed589f6b7995db6bc7fd50af
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075067"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Procedura: Personalizzare le attestazioni generate nei token per un'app specifica in un tenant (anteprima)

> [!NOTE]
> Questa funzionalità sostituisce e prevale sulla funzionalità di [personalizzazione delle attestazioni](active-directory-saml-claims-customization.md) offerta attualmente tramite il portale. Nella stessa applicazione, se si personalizzano le attestazioni con il portale in aggiunta alla modalità Graph/PowerShell descritta in dettaglio in questo documento, i token emessi per l'applicazione ignoreranno la configurazione nel portale. Le configurazioni eseguite nei modi descritti in dettaglio in questo documento non si rifletteranno nel portale.

Questa funzionalità viene usata dagli amministratori tenant per personalizzare le attestazioni generate nei token per un'applicazione specifica nel tenant. È possibile usare i criteri di mapping delle attestazioni per:

- Selezionare le attestazioni che vengono incluse nei token.
- Creare tipi di attestazione non esistenti.
- Scegliere o modificare l'origine dei dati generati in attestazioni specifiche.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima pubblica. Potrebbe essere necessario ripristinare o rimuovere eventuali modifiche. La funzionalità è disponibile in tutte le sottoscrizioni di Azure Active Directory (Azure AD) durante l'anteprima pubblica. Quando la funzionalità sarà disponibile a livello generale, alcuni aspetti potrebbero tuttavia richiedere una sottoscrizione Azure AD Premium. Questa funzionalità supporta la configurazione di criteri di mapping di attestazioni per i protocolli WS-Fed, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo di criteri di mapping di attestazioni

In Azure AD un oggetto **Criteri** rappresenta un set di regole imposto su singole applicazioni o su tutte le applicazioni in un'organizzazione. Ogni tipo di criteri ha una struttura univoca con un set di proprietà che vengono in seguito applicate agli oggetti a cui sono assegnate.

I criteri di mapping delle attestazioni sono un tipo di oggetto **Criteri** che modifica le attestazioni generate nei token emessi per applicazioni specifiche.

## <a name="claim-sets"></a>Set di attestazioni

Alcuni set di attestazioni definiscono come e quando vengono usate nei token.

| Set di attestazioni | Descrizione |
|---|---|
| Set di attestazioni core | Sono presenti in ogni token, indipendentemente dai criteri. Queste attestazioni sono anche considerate limitate e non possono essere modificate. |
| Set di attestazioni di base | Include le attestazioni generate per impostazione predefinita per i token (oltre al set di attestazioni core). È possibile omettere o modificare le attestazioni di base usando i criteri di mapping delle attestazioni. |
| Set di attestazioni con restrizioni | Non è possibile modificarle con i criteri. L'origine dati non può essere modificata e non viene applicata alcuna trasformazione durante la generazione di queste attestazioni. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabella 1: Set di attestazioni limitate al token JSON Web (JWT)

| Tipo di attestazione (nome) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| codice |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| ruolo |
| roles |
| scope |
| scp |
| sid |
| firma |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabella 2: Set di attestazioni con restrizioni SAML

| Tipo di attestazione (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Proprietà di criteri di mapping di attestazioni

Per controllare quali attestazioni vengono generate e da quali origini provengono i dati, usare le proprietà dei criteri di mapping delle attestazioni. Se non sono impostati criteri, il sistema emette token che includono il set di attestazioni core, il set di attestazioni di base ed eventuali [attestazioni facoltative](active-directory-optional-claims.md) che l'applicazione ha scelto di ricevere.

> [!NOTE]
> Le attestazioni del set di attestazioni core sono presenti in ogni token, indipendentemente dall'impostazione di questa proprietà.

### <a name="include-basic-claim-set"></a>Includere set di attestazioni di base

**Stringa:** IncludeBasicClaimSet

**Tipo di dati:** Booleano (true o false)

**Riepilogo:** questa proprietà specifica se il set di attestazioni di base sia incluso nei token interessati da questo criterio.

- Se impostata su True, tutte le attestazioni nel set di attestazioni di base verranno generate nei token interessati dal criterio.
- Se impostata su False, le attestazioni nel set di attestazioni di base non vengono generate nei token, a meno che non vengano aggiunte singolarmente nella proprietà dello schema delle attestazioni degli stessi criteri.



### <a name="claims-schema"></a>Schema di attestazioni

**Stringa:** ClaimsSchema

**Tipo di dati:** BLOB JSON con una o più voci dello schema di attestazioni

**Riepilogo:** questa proprietà definisce quali attestazioni sono presenti nei token interessati dai criteri, oltre al set di attestazioni di base e al set di attestazioni core.
Per ogni voce di schema di attestazioni definita in questa proprietà sono necessarie alcune informazioni. Consente di specificare la provenienza dei dati (**valore**, **coppia di origine/ID** o **coppia source/ExtensionID**) e l'attestazione in cui vengono emessi i dati (**tipo di attestazione**).

### <a name="claim-schema-entry-elements"></a>Elementi di voci dello schema di attestazioni

**Value:** l'elemento Value definisce un valore statico come dati da generare nell'attestazione.

**Coppia Source/ID:** gli elementi Source e ID definiscono l'origine da cui verranno ricavati i dati nell'attestazione.

**Coppia origine/ExtensionID:** Gli elementi source e ExtensionID definiscono l'attributo dell'estensione dello schema di directory da cui vengono originati i dati nell'attestazione. Per ulteriori informazioni, vedere [utilizzo degli attributi delle estensioni dello schema di directory nelle attestazioni](active-directory-schema-extensions.md).

Impostare l'elemento Source su uno dei valori seguenti:

- "user": i dati nell'attestazione sono una proprietà dell'oggetto User.
- "application": i dati nell'attestazione sono una proprietà dell'entità servizio application (client).
- "resource": i dati nell'attestazione sono una proprietà dell'entità servizio resource.
- "audience": i dati nell'attestazione sono una proprietà dell'entità servizio che corrisponde al destinatario del token (entità servizio resource o client).
- "company": i dati nell'attestazione sono una proprietà dell'oggetto Company del tenant delle risorse.
- "transformation": i dati nell'attestazione derivano dalla trasformazione delle attestazioni. Vedere la sezione "Trasformazione delle attestazioni" più avanti in questo articolo.

Se l'origine è transformation, anche l'elemento **TransformationID** deve essere incluso in questa definizione di attestazione.

L'elemento ID identifica la proprietà dell'origine che indica il valore per l'attestazione. La tabella seguente elenca i valori di ID validi per ogni valore di Source.

#### <a name="table-3-valid-id-values-per-source"></a>Tabella 3: Valori di ID validi per ogni Source

| Source (Sorgente) | ID | Descrizione |
|-----|-----|-----|
| Utente | surname | Cognome |
| Utente | givenname | Nome |
| Utente | displayname | Nome visualizzato |
| Utente | objectid | ObjectID |
| Utente | mail | Indirizzo di posta elettronica |
| Utente | userprincipalname | Nome entità utente |
| Utente | department|department|
| Utente | onpremisessamaccountname | Nome dell'account SAM locale |
| Utente | netbiosname| Nome NetBios |
| Utente | dnsdomainname | Nome di dominio DNS |
| Utente | onpremisesecurityidentifier | ID di sicurezza locale |
| Utente | companyname| Nome organizzazione |
| Utente | streetaddress | Indirizzo |
| Utente | postalcode | CAP |
| Utente | PreferredLanguage | Lingua preferita |
| Utente | onpremisesuserprincipalname | UPN locale |
| Utente | mailNickname | Nome di posta elettronica alternativo |
| Utente | extensionattribute1 | Attributo di estensione 1 |
| Utente | extensionattribute2 | Attributo di estensione 2 |
| Utente | extensionattribute3 | Attributo di estensione 3 |
| Utente | extensionattribute4 | Attributo di estensione 4 |
| Utente | extensionattribute5 | Attributo di estensione 5 |
| Utente | extensionattribute6 | Attributo di estensione 6 |
| Utente | extensionattribute7 | Attributo di estensione 7 |
| Utente | extensionattribute8 | Attributo di estensione 8 |
| Utente | extensionattribute9 | Attributo di estensione 9 |
| Utente | extensionattribute10 | Attributo di estensione 10 |
| Utente | extensionattribute11 | Attributo di estensione 11 |
| Utente | extensionattribute12 | Attributo di estensione 12 |
| Utente | extensionattribute13 | Attributo di estensione 13 |
| Utente | extensionattribute14 | Attributo di estensione 14 |
| Utente | extensionattribute15 | Attributo di estensione 15 |
| Utente | othermail | Posta elettronica alternativa |
| Utente | country | Paese/Area geografica |
| Utente | city | city |
| Utente | state | State |
| Utente | jobtitle | Posizione |
| Utente | employeeid | ID dipendente |
| Utente | facsimiletelephonenumber | Numero di telefono fax |
| User | assignedroles | elenco dei ruoli dell'app assegnati all'utente|
| application, resource, audience | displayname | Nome visualizzato |
| application, resource, audience | objectid | ObjectID |
| application, resource, audience | tags | Tag di entità servizio |
| Company | tenantcountry | Paese/Area geografica del tenant |

**TransformationID:** l'elemento TransformationID è obbligatorio solo se l'elemento Source è impostato su "transformation".

- Questo elemento deve corrispondere all'elemento ID della voce di trasformazione nella proprietà **ClaimsTransformation** che definisce la modalità di generazione dei dati per questa attestazione.

**Tipo di attestazione:** gli elementi **JwtClaimType** e **SamlClaimType** definiscono l'attestazione a cui questa voce dello schema di attestazioni fa riferimento.

- JwtClaimType deve contenere il nome dell'attestazione da generare nel token JWT.
- SamlClaimType deve contenere l'URI dell'attestazione da generare nei token SAML.

* **attributo onPremisesUserPrincipalName:** Quando si usa un ID alternativo, l'attributo locale userPrincipalName viene sincronizzato con l'attributo Azure AD onPremisesUserPrincipalName. Questo attributo è disponibile solo quando è configurato un ID alternativo, ma è disponibile anche tramite MS Graph beta: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> Non è possibile utilizzare i nomi e gli URI delle attestazioni nel set di attestazioni con restrizioni per gli elementi dei tipi di attestazioni. Per altre informazioni, vedere la sezione "Eccezioni e restrizioni" più avanti in questo articolo.

### <a name="claims-transformation"></a>Trasformazione delle attestazioni

**Stringa:** ClaimsTransformation

**Tipo di dati:** BLOB JSON con una o più voci di trasformazione

**Riepilogo:** questa proprietà consente di applicare trasformazioni comuni ai dati di origine per generare i dati di output per le attestazioni specificate nello schema di attestazioni.

**ID:** l'elemento ID viene usato per fare riferimento a questa voce di trasformazione nella voce TransformationID dello schema di attestazioni. Questo valore deve essere univoco per ogni voce di trasformazione all'interno di questo criterio.

**TransformationMethod:** l'elemento TransformationMethod identifica l'operazione che viene eseguita per generare i dati per l'attestazione.

In base al metodo scelto è previsto un set di input e output. Definire gli input e gli output usando gli elementi **InputClaims**, **InputParameters** e **OutputClaims**.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabella 4: Metodi di trasformazione e input/output previsti

|TransformationMethod|Input previsto|Output previsto|Descrizione|
|-----|-----|-----|-----|
|Join|string1, string2, separator|outputClaim|Esegue il join di stringhe di input dividendole con un separatore. Ad esempio: stringa1: "foo@bar.com", stringa2: "sandbox", separatore: "." comporta in outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|Indirizzo di posta elettronica o UPN|stringa estratta|ExtensionAttributes 1-15 o qualsiasi altra estensione dello schema che archivia un valore UPN o un indirizzo di posta elettronica per l'utente, ad johndoe@contoso.com esempio. Estrae la parte locale di un indirizzo di posta elettronica. Ad esempio: mail:"foo@bar.com" comporta in outputClaim:"foo". Se non è presente un segno \@, la stringa di input originale viene restituita così come è.|

**InputClaims:** un elemento InputClaims può essere usato per passare i dati da una voce dello schema di attestazioni a una trasformazione. Include due attributi: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** viene unito in join con l'elemento ID della voce dello schema di attestazioni per trovare l'attestazione di input appropriata.
- **TransformationClaimType** viene usato per assegnare un nome univoco a questo input. Questo nome deve corrispondere a uno degli input previsti per il metodo di trasformazione.

**InputParameters:** un elemento InputParameters viene usato per passare un valore costante a una trasformazione. Include due attributi: **Value** e **ID**.

- **Value** è il valore costante effettivo da passare.
- **ID** viene usato per assegnare un nome univoco all'input. Il nome deve corrispondere a uno degli input previsti per il metodo di trasformazione.

**OutputClaims:** un elemento OutputClaims viene usato per contenere i dati generati da una trasformazione e associarli a una voce dello schema di attestazioni. Include due attributi: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** viene unito in join all'elemento ID della voce dello schema di attestazioni per individuare l'attestazione di output appropriata.
- **TransformationClaimType** viene usato per assegnare un nome univoco all'output. Il nome deve corrispondere a uno degli output previsti per il metodo di trasformazione.

### <a name="exceptions-and-restrictions"></a>Eccezioni e restrizioni

**NameID e UPN di SAML:** gli attributi da cui hanno origine i valori NameID e UPN, e le trasformazioni di attestazioni consentite, presentano limitazioni. Vedere le tabelle 5 e 6 per visualizzare i valori consentiti.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabella 5: Attributi consentiti come origine dati per NameID di SAML

|Source (Sorgente)|ID|Descrizione|
|-----|-----|-----|
| Utente | mail|Indirizzo di posta elettronica|
| Utente | userprincipalname|Nome entità utente|
| Utente | onpremisessamaccountname|Nome account SAM locale|
| Utente | employeeid|ID dipendente|
| Utente | extensionattribute1 | Attributo di estensione 1 |
| Utente | extensionattribute2 | Attributo di estensione 2 |
| Utente | extensionattribute3 | Attributo di estensione 3 |
| Utente | extensionattribute4 | Attributo di estensione 4 |
| Utente | extensionattribute5 | Attributo di estensione 5 |
| Utente | extensionattribute6 | Attributo di estensione 6 |
| Utente | extensionattribute7 | Attributo di estensione 7 |
| Utente | extensionattribute8 | Attributo di estensione 8 |
| Utente | extensionattribute9 | Attributo di estensione 9 |
| Utente | extensionattribute10 | Attributo di estensione 10 |
| Utente | extensionattribute11 | Attributo di estensione 11 |
| Utente | extensionattribute12 | Attributo di estensione 12 |
| Utente | extensionattribute13 | Attributo di estensione 13 |
| Utente | extensionattribute14 | Attributo di estensione 14 |
| Utente | extensionattribute15 | Attributo di estensione 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabella 6: Metodi di trasformazione consentiti per NameID di SAML

| TransformationMethod | Restrizioni |
| ----- | ----- |
| ExtractMailPrefix | nessuno |
| Join | Il suffisso da aggiungere deve essere un dominio verificato del tenant delle risorse. |

### <a name="cross-tenant-scenarios"></a>Scenari tra tenant

I criteri di mapping delle attestazioni non si applicano agli utenti guest. Se un utente guest prova ad accedere a un'applicazione con criteri di mapping delle attestazioni assegnati all'entità servizio, viene emesso il token predefinito (i criteri non hanno effetto).

## <a name="claims-mapping-policy-assignment"></a>Assegnazione di criteri di mapping delle attestazioni

I criteri di mapping delle attestazioni possono essere assegnati solo a oggetti entità servizio.

### <a name="example-claims-mapping-policies"></a>Criteri di mapping delle attestazioni di esempio

In molti scenari di Azure AD è possibile personalizzare le attestazioni generate nei token per specifiche entità servizio. In questa sezione vengono illustrati alcuni scenari comuni che permettono di comprendere come usare il tipo di criteri di mapping delle attestazioni.

Quando si creano criteri di mapping delle attestazioni, è anche possibile emettere un'attestazione da un attributo di estensione dello schema di directory nei token. Usare *ExtensionID* per l'attributo extension anziché *ID* nell' `ClaimsSchema` elemento.  Per ulteriori informazioni sugli attributi di estensione, vedere [utilizzo degli attributi di estensione dello schema di directory](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Prerequisiti

Gli esempi seguenti mostrano come creare, aggiornare, collegare ed eliminare criteri per le entità servizio. Se non si ha familiarità con Azure AD, è consigliabile [capire come ottenere un tenant di Azure AD](quickstart-create-new-tenant.md) prima di procedere con questi esempi.

Per iniziare, seguire questa procedura:

1. Scaricare l'ultima [versione di anteprima pubblica del modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Eseguire il comando Connect per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Per visualizzare tutti i criteri creati nell'organizzazione, eseguire questo comando. È consigliabile eseguire questo comando dopo la maggior parte delle operazioni negli scenari seguenti per verificare che i criteri siano stati creati come previsto.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Esempio: creare e assegnare un criterio per omettere le attestazioni di base dai token emessi per un'entità servizio

In questo esempio si creano criteri che rimuovono il set di attestazioni di base dai token emessi per le entità servizio collegate.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, rimuovono il set di attestazioni di base dai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Esempio: Creare e assegnare un criterio per includere EmployeeID e TenantCountry come attestazioni nei token emessi per un'entità servizio

In questo esempio si creano criteri che aggiungono EmployeeID e TenantCountry ai token emessi per le entità servizio collegate. EmployeeID viene emesso come tipo di attestazione nome sia nei token SAML sia nei token JWT. TenantCountry viene emesso come tipo di attestazione paese/area geografica sia nei token SAML sia nei token JWT. In questo esempio si continua a includere il set di attestazioni di base nei token.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, aggiungono le attestazioni EmployeeID e TenantCountry ai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Esempio: Creare e assegnare i criteri con una trasformazione di attestazioni in token emessi per un'entità servizio

In questo esempio si creano i criteri che generano un'attestazione personalizzata "JoinedData" nei token JWT emessi per le entità servizio collegate. Questa attestazione contiene un valore creato aggiungendo i dati archiviati nell'attributo extensionattribute1 all'oggetto utente con ".sandbox". In questo esempio si esclude il set di attestazioni di base nei token.

1. Creare i criteri di mapping delle attestazioni. Questi criteri, che vengono collegati a specifiche entità servizio, aggiungono le attestazioni EmployeeID e TenantCountry ai token.
   1. Per creare i criteri, eseguire questo comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Per visualizzare il nuovo criterio e ottenere il relativo ObjectId, eseguire questo comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Assegnare i criteri all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.
   1. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile [eseguire query nell'API di Microsoft Graph](/graph/traverse-the-graph). In alternativa, eseguire l'accesso all'account Azure AD dallo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. Dopo aver ottenuto l'ObjectId dell'entità servizio, eseguire questo comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Le applicazioni che ricevono token si basano sul fatto che i valori delle attestazioni vengono emessi in maniera autorevole da Azure AD e non possono essere manomessi. Tuttavia, quando si modifica il contenuto del token tramite i criteri di mapping delle attestazioni, questi presupposti potrebbero non essere più corretti. Le applicazioni devono confermare esplicitamente che i token sono stati modificati dal creatore dei criteri di mapping delle attestazioni per proteggersi dai criteri di mapping delle attestazioni creati da attori malintenzionati. Questa operazione può essere eseguita nei modi seguenti:

- Configurare una chiave di firma personalizzata
- Aggiornare il manifesto dell'applicazione in modo da accettare le attestazioni mappate.
 
Senza questo, Azure AD restituirà un [ `AADSTS50146` codice di errore](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Chiave di firma personalizzata

Per aggiungere una chiave di firma personalizzata all'oggetto entità servizio, è possibile usare il cmdlet Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) per creare una credenziale della chiave del certificato per l'oggetto applicazione.

Per le app con mapping delle attestazioni abilitato è necessario convalidare le chiavi di firma del token aggiungendo `appid={client_id}` alle [richieste di metadati OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Di seguito è riportato il formato del documento di metadati OpenID Connect.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Aggiornare il manifesto dell'applicazione

In alternativa, è possibile impostare la `acceptMappedClaims` proprietà su `true` nel [manifesto dell'applicazione](reference-app-manifest.md). Come illustrato nel [tipo di risorsa apiApplication](/graph/api/resources/apiapplication#properties), consente a un'applicazione di usare il mapping delle attestazioni senza specificare una chiave di firma personalizzata.

Questa operazione richiede che i destinatari dei token richiesti usino un nome di dominio verificato del tenant di Azure AD, quindi è necessario assicurarsi di impostare `Application ID URI` (rappresentato da `identifierUris` nel manifesto dell'applicazione), ad esempio in `https://contoso.com/my-api` o (usando semplicemente il nome del tenant predefinito) `https://contoso.onmicrosoft.com/my-api` .

Se non si usa un dominio verificato, Azure AD restituirà un `AADSTS501461` codice di errore con *il messaggio "AcceptMappedClaims è supportato solo per i destinatari del token che corrispondono al GUID dell'applicazione o a un gruppo di destinatari nei domini verificati del tenant. Modificare l'identificatore di risorsa o usare una chiave di firma specifica dell'applicazione ".*

## <a name="see-also"></a>Vedere anche

- Per informazioni su come personalizzare le attestazioni rilasciate nel token SAML tramite il portale di Azure, vedere [Procedura: Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](active-directory-saml-claims-customization.md)
- Per ulteriori informazioni sugli attributi di estensione, vedere [utilizzo degli attributi di estensione dello schema di directory nelle attestazioni](active-directory-schema-extensions.md).
