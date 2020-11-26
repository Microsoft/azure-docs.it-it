---
title: Codici di errore di autorizzazione Azure AD & di autenticazione
description: Informazioni sui codici di errore AADSTS restituiti dal servizio token di sicurezza di Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fa6fc11441811589967ddd7728501b521f9f9155
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169274"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Codici di errore di autenticazione e autorizzazione di Azure AD

Se sono necessarie informazioni sui codici di errore AADSTS restituiti dal servizio token di sicurezza di Azure Active Directory (Azure AD), consultare questo documento per trovare le descrizioni degli errori AADSTS, le correzioni e alcune soluzioni alternative consigliate.

> [!NOTE]
> Queste informazioni sono preliminari e soggette a modifiche. In caso di domande o se non è possibile trovare le informazioni necessarie, creare un problema in GitHub oppure fare riferimento alle [opzioni di supporto e assistenza per sviluppatori](./developer-support-help-options.md) per informazioni su come ottenere supporto.
>
> Questa documentazione è fornita per istruzioni per sviluppatori e amministratori, ma non deve mai essere usata dal client stesso. I codici di errore sono soggetti a modifiche in qualsiasi momento per fornire messaggi di errore più granulari destinati a aiutare lo sviluppatore durante la compilazione dell'applicazione. Le app che accettano una dipendenza da numeri di codice di errore o di testo verranno interrotte nel tempo.

## <a name="handling-error-codes-in-your-application"></a>Gestione dei codici di errore nell'applicazione

La [specifica OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-5.2) fornisce indicazioni su come gestire gli errori durante l'autenticazione usando la `error` parte della risposta di errore. 

Ecco una risposta di errore di esempio:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parametro         | Descrizione    |
|-------------------|----------------|
| `error`       | Stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e che devono essere utilizzati per rispondere agli errori. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. Non usare mai questo campo per rispondere a un errore nel codice. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica.  |
| `timestamp`   | Ora in cui si è verificato l'errore. |
| `trace_id`    | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |
| `error_uri` |  Collegamento alla pagina di ricerca degli errori con ulteriori informazioni sull'errore.  Questo è solo per l'utilizzo da parte degli sviluppatori e non per gli utenti.  Presente solo quando il sistema di ricerca degli errori contiene informazioni aggiuntive sull'errore, per cui non sono disponibili informazioni aggiuntive.|

Il `error` campo presenta diversi valori possibili: esaminare i collegamenti alla documentazione del protocollo e le specifiche OAuth 2,0 per altre informazioni su errori specifici, ad esempio `authorization_pending` nel [flusso del codice del dispositivo](v2-oauth2-device-code.md), e su come reagire a essi.  Di seguito sono elencate alcune di quelle comuni:

| Codice di errore         | Descrizione        | Azione client    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta.|
| `invalid_grant`    | Il materiale di autenticazione (codice di autorizzazione, token di aggiornamento, token di accesso, PKCE Challenge) non è valido, non è analizzabile, mancante o altrimenti inutilizzabile | Provare una nuova richiesta all' `/authorize` endpoint per ottenere un nuovo codice di autorizzazione.  Prendere in considerazione la revisione e la convalida dell'uso dei protocolli da parte dell'app. |
| `unauthorized_client` | Il client autenticato non è autorizzato a usare questo tipo di concessione dell'autorizzazione. | Questo errore si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `invalid_client` | Autenticazione client non riuscita.  | Credenziali del client non valide. Per risolvere il problema, l'amministratore applicazione aggiorna le credenziali.   |
| `unsupported_grant_type` | Il server di autorizzazione non supporta il tipo di concessione dell'autorizzazione. | Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato durante il test iniziale. |
| `invalid_resource` | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. | Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD.  Durante lo sviluppo, questo indica in genere un tenant di test configurato in modo errato o un errore di digitazione nel nome dell'ambito richiesto. |
| `interaction_required` | La richiesta richiede l'interazione dell'utente. Ad esempio, è necessario un passaggio di autenticazione aggiuntivo. | Ripetere la richiesta con la stessa risorsa, in modo interattivo, in modo che l'utente possa completare le richieste necessarie.  |
| `temporarily_unavailable` | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |

## <a name="lookup-current-error-code-information"></a>Ricerca informazioni sul codice di errore corrente
I codici di errore e i messaggi sono soggetti a modifiche.  Per informazioni aggiornate, vedere la [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) pagina per trovare le descrizioni degli errori AADSTS, le correzioni e alcune soluzioni alternative suggerite.  

Se, ad esempio, è stato ricevuto il codice di errore "AADSTS50058", eseguire una ricerca [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) per "50058".  È anche possibile collegarsi direttamente a un errore specifico aggiungendo il numero di codice di errore all'URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Codici di errore AADSTS

| Errore | Descrizione |
|---|---|
| AADSTS16000 | SelectUserAccount: interrupt generato da Azure AD, con conseguente attivazione dell'interfaccia utente che permette all'utente di selezionare tra più sessioni SSO valide. Questo errore è piuttosto comune e può essere restituito all'applicazione se è specificato `prompt=none`. |
| AADSTS16001 | UserAccountSelectionInvalid: questo errore viene visualizzato se l'utente fa clic su un riquadro che è stato rifiutato dalla logica di selezione della sessione. Se attivato, questo errore permette all'utente di rimediare effettuando la selezione in un elenco aggiornato di riquadri/sessioni o scegliendo un altro account. Questo errore può essere dovuto a un problema del codice o a una race condition. |
| AADSTS16002 | AppSessionSelectionInvalid: il requisito relativo al SID specificato dall'app non è stato soddisfatto.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant: indica che l'utente non è ancora stato aggiunto in modo esplicito al tenant. |
| AADSTS17003 | CredentialKeyProvisioningFailed: Azure AD non è in grado di effettuare il provisioning della chiave dell'utente. |
| AADSTS20001 | WsFedSignInResponseError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS20012 | WsFedMessageInvalid: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS20033 | FedMetadataInvalidTenantName: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS50000 | TokenIssuanceError: si è verificato un problema relativo al servizio di accesso. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per risolvere il problema. |
| AADSTS50001 | InvalidResource: la risorsa è disabilitata o non esiste. Controllare il codice dell'app per verificare di avere specificato l'URL della risorsa esatta cui si sta tentando di accedere.  |
| AADSTS50002 | NotAllowedTenant: accesso non riuscito a causa di limitazioni di accesso al proxy nel tenant. Se questo errore è dovuto ai propri criteri per il tenant, è possibile modificare le impostazioni di limitazione per il tenant per correggerlo. |
| AADSTS50003 | MissingSigningKey: accesso non riuscito a causa di una chiave o un certificato di firma mancante. Questo errore può essere dovuto al fatto che non è stata configurata alcuna chiave di firma nell'app. Vedere le soluzioni descritte in [. /Manage-apps/application-Sign-in-problem-Federated-SSO-Gallery.MD # certificate-or-Key-non configurata](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured). Se il problema persiste, contattare il proprietario o l'amministratore dell'app. |
| AADSTS50005 | DevicePolicyError-l'utente ha tentato di accedere a un dispositivo da una piattaforma che attualmente non è supportata tramite i criteri di accesso condizionale. |
| AADSTS50006 | InvalidSignature: la verifica della firma non è riuscita a causa di una firma non valida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing: non è stato trovato il certificato di crittografia del partner per l'app. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) presso Microsoft per richiedere la risoluzione del problema. |
| AADSTS50008 | InvalidSamlToken: l'asserzione SAML non è presente o non è configurata correttamente nel token. Contattare il provider di federazione. |
| AADSTS50010 | AudienceUriValidationFailed: la convalida dell'URI destinatario per l'app non è riuscita perché non è stato configurato alcun destinatario del token. |
| AADSTS50011 | InvalidReplyTo: l'indirizzo di risposta è mancante, non è configurato correttamente o non corrisponde agli indirizzi di risposta configurati per l'app.  Per risolvere il problema, aggiungere l'indirizzo di risposta mancante all'applicazione Azure Active Directory o chiedere a un utente con le autorizzazioni necessarie per gestire l'applicazione in Active Directory eseguire questa operazione.|
| AADSTS50012 | AuthenticationFailed: l'autenticazione non è riuscita per uno dei motivi seguenti:<ul><li>Il nome soggetto del certificato di firma non è autorizzato</li><li>Non sono stati trovati criteri di autorità attendibile corrispondenti per il nome soggetto autorizzato</li><li>La catena di certificati non è valida</li><li>Il certificato di firma non è valido</li><li>Non sono configurati criteri nel tenant</li><li>L'identificazione personale del certificato di firma non è autorizzata</li><li>l'asserzione client contiene una firma non valida</li></ul> |
| AADSTS50013 | InvalidAssertion: l'asserzione non è valida per vari motivi: - L'emittente del token non corrisponde alla versione API nell'intervallo di tempo valido - È scaduta - Ha un formato non valido - Il token di aggiornamento nell'asserzione non è un token di aggiornamento primario. |
| AADSTS50014 | GuestUserInPendingState: il riscatto da parte dell'utente è in stato sospeso. L'account utente guest non è stato ancora creato completamente. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState: l'utente richiede il consenso legale per fascia di età. |
| AADSTS50017 | CertificateValidationFailed: la convalida della certificazione non è riuscita a causa dei motivi seguenti:<ul><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>Non è possibile trovare l'elemento CrlSegment previsto</li><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>È configurato un punto di distribuzione CRL differenziale senza un punto di distribuzione CRL corrispondente</li><li>Non è possibile recuperare segmenti CRL validi a causa di un problema di timeout</li><li>Non è possibile scaricare il documento CRL</li></ul>Contattare l'amministratore del tenant. |
| AADSTS50020 | UserUnauthorized: gli utenti non sono autorizzati a chiamare questo endpoint. |
| AADSTS50027 | InvalidJwtToken: token JWT non valido a causa dei motivi seguenti:<ul><li>Non contiene un'attestazione nonce o un'attestazione secondaria</li><li>L'identificatore soggetto non corrisponde</li><li>Attestazione duplicata nelle attestazioni idToken</li><li>Autorità di certificazione imprevista</li><li>Destinatari imprevisti</li><li>Al di fuori dell'intervallo di tempo valido </li><li>Formato del token non corretto</li><li>Il token ID esterno dell'autorità di certificazione non ha superato la verifica della firma</li></ul> |
| AADSTS50029 | URI non valido. Il nome di dominio contiene caratteri non validi. Contattare l'amministratore del tenant. |
| AADSTS50032 | WeakRsaKey: indica il tentativo errato dell'utente di usare una chiave RSA vulnerabile. |
| AADSTS50033 | RetryableError: indica un errore temporaneo non correlato alle operazioni del database. |
| AADSTS50034 | UserAccountNotFound: per accedere all'applicazione, l'account deve essere aggiunto alla directory. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt: il Salt necessario per generare un identificatore pairwise è mancante in principio. Contattare l'amministratore del tenant. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer: il soggetto non corrisponde all'attestazione dell'autorità di certificazione nell'asserzione client. Contattare l'amministratore del tenant. |
| AADSTS50049 | NoSuchInstanceForDiscovery: istanza sconosciuta o non valida. |
| AADSTS50050 | MalformedDiscoveryRequest: la richiesta ha un formato non valido. |
| AADSTS50053 | IdsLocked: l'account è bloccato perché l'utente ha tentato di accedere troppe volte con un ID utente e/o una password non corretti. |
| AADSTS50055 | InvalidPasswordExpiredPassword: la password è scaduta. |
| AADSTS50056 | Password non valida o null: la password non è presente nell'archivio per questo utente. |
| AADSTS50057 | UserDisabled: l'account utente è disabilitato. L'account è stato disabilitato da un amministratore. |
| AADSTS50058 | UserInformationNotProvided: un utente non ha effettuato l'accesso. Si tratta di un errore comune che si verifica in genere quando un utente non è autenticato e non ha ancora effettuato l'accesso.</br>Se questo errore viene visualizzato in un contesto SSO in cui l'utente ha precedentemente effettuato l'accesso, significa che la sessione SSO non è stata trovata o non è valida.</br>Questo errore può essere restituito all'applicazione se è specificato prompt=none. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided: non sono state trovate informazioni di identificazione del tenant nella richiesta né sono state incluse in modo implicito nelle credenziali fornite. L'utente può contattare l'amministratore del tenant per ottenere informazioni per la risoluzione del problema. |
| AADSTS50061 | SignoutInvalidRequest: la richiesta di disconnessione non è valida. |
| AADSTS50064 | CredentialAuthenticationError: la convalida delle credenziali per nome utente e password non è riuscita. |
| AADSTS50068 | SignoutInitiatorNotParticipant-disconnessione non riuscita. L'app che ha avviato la disconnessione non è un partecipante della sessione corrente. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-disconnessione non riuscita. La richiesta di disconnessione ha specificato un identificatore di nome che non corrisponde alle sessioni esistenti. |
| AADSTS50071 | SignoutMessageExpired: la richiesta di disconnessione è scaduta. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt: l'utente deve registrarsi per l'autenticazione a due fattori (interattiva). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt: è necessaria l'autenticazione avanzata e l'utente non ha superato la verifica MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired: a causa di una modifica di configurazione apportata dall'amministratore o del trasferimento dell'utente in una nuova località, l'utente deve usare l'autenticazione a più fattori per accedere alla risorsa. Riprovare con una nuova richiesta di autorizzazione per la risorsa. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired: a causa di una modifica di configurazione apportata dall'amministratore o del trasferimento dell'utente in una nuova località, l'utente è tenuto a usare l'autenticazione a più fattori. |
| AADSTS50085 | Il token di aggiornamento richiede un accesso IDP social. Chiedere all'utente di provare a eseguire di nuovo l'accesso con nome utente e password. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError: il servizio è temporaneamente non disponibile. Riprovare. |
| AADSTS50089 | Il token di flusso è scaduto. L'autenticazione non è riuscita. Chiedere all'utente di provare a eseguire di nuovo l'accesso con nome utente e password. |
| AADSTS50097 | DeviceAuthenticationRequired: l'autenticazione del dispositivo è obbligatoria. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized: la firma del token JWT non è valida. |
| AADSTS50105 | EntitlementGrantsNotFound: all'utente che ha eseguito l'accesso non è assegnato alcun ruolo per l'app connessa. Assegnare l'utente all'app. Per ulteriori informazioni:[.. /Manage-apps/application-Sign-in-problem-Federated-SSO-Gallery.MD # utente-non assegnato-a-Role](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri: l'oggetto dell'area di autenticazione della federazione richiesto non esiste. Contattare l'amministratore del tenant. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat: problema relativo all'intestazione JWT. Contattare l'amministratore del tenant. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter: la trasformazione delle attestazioni contiene un parametro di input non valido. Contattare l'amministratore del tenant per aggiornare i criteri. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt: l'accesso è stato interrotto a causa della reimpostazione della password o di una voce di registrazione password. |
| AADSTS50126 | InvalidUserNameOrPassword: errore durante la convalida delle credenziali a causa di un nome utente e/o di una password non validi. |
| AADSTS50127 | BrokerAppNotInstalled: l'utente deve installare un'app broker per ottenere l'accesso a questo contenuto. |
| AADSTS50128 | Nome di dominio non valido. Non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined: per registrare il dispositivo, è necessaria l'aggiunta alla rete aziendale. |
| AADSTS50131 | ConditionalAccessFailed: indica diversi errori di accesso condizionale, ad esempio stato del dispositivo Windows non valido, richiesta bloccata a causa di attività sospette, criteri di accesso o decisioni relative ai criteri di sicurezza. |
| AADSTS50132 | SsoArtifactInvalidOrExpired: la sessione non è valida perché la password è scaduta o è stata modificata di recente. |
| AADSTS50133 | SsoArtifactRevoked: la sessione non è valida perché la password è scaduta o è stata modificata di recente. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter: data center non corretto. Per autorizzare una richiesta avviata da un'app nel flusso del dispositivo OAuth 2.0, la parte responsabile dell'autorizzazione deve trovarsi nello stesso data center in cui si trova la richiesta originale. |
| AADSTS50135 | PasswordChangeCompromisedPassword: è necessario modificare la password a causa di rischi per l'account. |
| AADSTS50136 | RedirectMsaSessionToApp: è stata rilevata una singola sessione dell'account del servizio gestito. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken: la sessione è non valida a causa di un token di aggiornamento esterno mancante. |
| AADSTS50140 | KmsiInterrupt: questo errore si è verificato a causa di un interrupt del comando "Mantieni l'accesso" durante l'accesso dell'utente. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli. |
| AADSTS50143 | Mancata corrispondenza di sessione. La sessione non è valida perché il tenant dell'utente non corrisponde all'hint di dominio a causa di una risorsa diversa. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword: la password di Active Directory dell'utente è scaduta. Generare una nuova password per l'utente o chiedere all'utente di usare lo strumento di reimpostazione self-service per reimpostare la password. |
| AADSTS50146 | MissingCustomSigningKey: questa app deve essere configurata con una chiave di firma specifica dell'app. O non è configurata con tale chiave oppure la chiave è scaduta o non è ancora valida. |
| AADSTS50147 | MissingCodeChallenge: la dimensione del parametro della richiesta di verifica del codice non è valida. |
| AADSTS50155 | DeviceAuthenticationFailed: l'autenticazione del dispositivo non è riuscita per questo utente. |
| AADSTS50158 | ExternalSecurityChallenge: la richiesta di sicurezza esterna non è stata soddisfatta. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration: le attestazioni inviate dal provider esterno non sono sufficienti oppure manca un'attestazione richiesta al provider esterno. |
| AADSTS50166 | ExternalClaimsProviderThrottled: non è stato possibile inviare la richiesta al provider di attestazioni. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired: il client è in grado di ottenere un token SSO tramite l'estensione per gli account Windows 10, ma il token non è stato trovato nella richiesta o il token specificato è scaduto. |
| AADSTS50169 | InvalidRequestBadRealm: l'area di autenticazione non è un'area di autenticazione configurata dello spazio dei nomi del servizio corrente. |
| AADSTS50170 | MissingExternalClaimsProviderMapping: il mapping dei controlli esterni è mancante. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers: la richiesta di verifica esterna non è supportata per gli utenti pass-through. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers: il controllo della sessione non è supportato per gli utenti pass-through. |
| AADSTS50180 | WindowsIntegratedAuthMissing: è necessaria l'autenticazione integrata di Windows. Abilitare il tenant per Seamless SSO. |
| AADSTS50187 | DeviceInformationNotProvided: il servizio non è stato in grado di eseguire l'autenticazione del dispositivo. |
| AADSTS50196 | LoopDetected-è stato rilevato un ciclo client. Controllare la logica dell'app per assicurarsi che la memorizzazione nella cache dei token venga implementata e che le condizioni di errore siano gestite correttamente.  L'app ha effettuato troppe richieste in un periodo troppo breve, a indicare che si trova in uno stato di errore o che richiede i token in maniera abusiva. |
| AADSTS50197 | ConflictingIdentities-l'utente non è stato trovato. Riprovare ad accedere. |
| AADSTS50199 | CmsiInterrupt: per motivi di sicurezza, è richiesta la conferma dell'utente per questa richiesta.  Poiché si tratta di un errore di "interaction_required", il client deve eseguire l'autenticazione interattiva.  Questo problema si verifica perché è stata usata una visualizzazione di sistema per richiedere un token per un'applicazione nativa. è necessario che all'utente venga chiesto se si tratta effettivamente dell'app a cui si intende accedere. Per evitare questa richiesta, l'URI di reindirizzamento deve essere parte dell'elenco di sicurezza seguente: <br />http://<br />https://<br />msauth://(solo iOS)<br />msauthv2://(solo iOS)<br />Chrome-Extension://(solo browser desktop Chrome) |
| AADSTS51000 | RequiredFeatureNotEnabled: la funzionalità è disabilitata. |
| AADSTS51001 | DomainHintMustbePresent: deve essere presente il suggerimento di dominio con l'ID di sicurezza locale o l'UPN locale. |
| AADSTS51004 | UserAccountNotInDirectory: l'account utente non è presente nella directory. |
| AADSTS51005 | TemporaryRedirect: equivale allo stato HTTP 307, che indica che le informazioni richieste si trovano nell'URI specificato nell'intestazione location. Quando viene restituito questo stato, seguire l'intestazione location associata alla risposta. Quando il metodo di richiesta originale è POST, anche la richiesta reindirizzata utilizza il metodo POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth: è necessaria l'autenticazione integrata di Windows. L'utente ha eseguito l'accesso con un token di sessione in cui manca l'attestazione di autenticazione integrata di Windows. Chiedere all'utente di ripetere l'accesso. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn: l'utente non ha fornito il proprio consenso per l'accesso a risorse di LinkedIn. |
| AADSTS53000 | DeviceNotCompliant: i criteri di accesso condizionale richiedono un dispositivo conforme e il dispositivo non è conforme. L'utente deve registrare il dispositivo con un provider MDM approvato, come Intune. |
| AADSTS53001 | DeviceNotDomainJoined: i criteri di accesso condizionale richiedono un dispositivo aggiunto a un dominio e il dispositivo non è aggiunto a un dominio. Chiedere all'utente di usare un dispositivo aggiunto al dominio. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-l'app usata non è un'app approvata per l'accesso condizionale. L'utente deve usare una delle app incluse nell'elenco delle app approvate per l'uso per ottenere l'accesso. |
| AADSTS53003 | BlockedByConditionalAccess-l'accesso è stato bloccato dai criteri di accesso condizionale. Il criterio di accesso non permette il rilascio di token. |
| AADSTS53004 | ProofUpBlockedDueToRisk: l'utente deve completare il processo di registrazione dell'autenticazione a più fattori prima di accedere al contenuto. L'utente deve eseguire la registrazione per l'autenticazione a più fattori. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa. |
| AADSTS65004 | UserDeclinedConsent: l'utente ha rifiutato il consenso per l'accesso all'app. Chiedere all'utente di riprovare ad accedere e fornire il consenso per l'app.|
| AADSTS65005 | MisconfiguredApplication: l'elenco di accesso alle risorse necessarie per l'applicazione non contiene app individuabili dalla risorsa, l'app client ha richiesto l'accesso a una risorsa non specificata nell'elenco di accesso alle risorse necessarie oppure il servizio Graph ha restituito una risposta di richiesta non valida o di risorsa non trovata. Se l'app supporta SAML, potrebbe essere stata configurata con l'identificatore errato (entità). Provare la risoluzione elencata per SAML usando il collegamento seguente: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | L'app deve accedere a un servizio `(\"{name}\")` che l'organizzazione `\"{organization}\"` non ha sottoscritto o abilitato. Contattare l'amministratore IT per verificare la configurazione delle sottoscrizioni del servizio. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant: autenticazione non riuscita. Il token di aggiornamento non è valido. Errore dovuto ai motivi seguenti:<ul><li>L'intestazione di associazione di token è vuota</li><li>L'hash di associazione di token non corrisponde</li></ul> |
| AADSTS70001 | UnauthorizedClient: l'applicazione è disabilitata. |
| AADSTS70002 | InvalidClient: errore di convalida delle credenziali. Il valore di client_secret specificato non corrisponde al valore previsto per questo client. Correggere il valore di client_secret e riprovare. Per altre informazioni, vedere [Usare un codice di autorizzazione per richiedere un token di accesso](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType: l'app ha restituito un tipo di concessione non supportato. |
| AADSTS70004 | InvalidRedirectUri: l'app ha restituito un URI di reindirizzamento non valido. L'indirizzo di reindirizzamento specificato dal client non corrisponde ad alcun indirizzo configurato o ad alcun indirizzo nell'elenco OIDC approvato. |
| AADSTS70005 | UnsupportedResponseType: l'app ha restituito un tipo di risposta non supportato a causa dei motivi seguenti:<ul><li>Il tipo di risposta "token" non è abilitato per l'app</li><li>Il tipo di risposta "id_token"' richiede l'ambito "OpenID" o contiene un valore di parametro OAuth non supportato nell'elemento wctx codificato</li></ul> |
| AADSTS70007 | UnsupportedResponseMode: l'app ha restituito un valore non supportato per `response_mode` durante la richiesta di un token.  |
| AADSTS70008 | ExpiredOrRevokedGrant: il token di aggiornamento è scaduto a causa di inattività. Il token è stato rilasciato in data XXX ed è rimasto inattivo per un certo periodo di tempo. |
| AADSTS70011 | InvalidScope: l'ambito richiesto dall'app non è valido. |
| AADSTS70012 | MsaServerError: si è verificato un errore del server durante l'autenticazione di un utente (consumer) dell'account del servizio gestito. Riprovare. Se il problema persiste, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | AuthorizationPending: errore del flusso del dispositivo OAuth 2.0. L'autorizzazione è in sospeso. Il dispositivo ritenterà il polling della richiesta. |
| AADSTS70018 | BadVerificationCode: codice di verifica non valido a causa di un errore di digitazione del codice per il flusso del codice del dispositivo da parte dell'utente. L'autorizzazione non è stata approvata. |
| AADSTS70019 | CodeExpired: il codice di verifica è scaduto. Chiedere all'utente di ripetere l'accesso. |
| AADSTS75001 | BindingSerializationError: si è verificato un errore durante l'associazione di messaggi SAML. |
| AADSTS75003 | UnsupportedBindingError: l'app ha restituito un errore relativo a un'associazione non supportata (la risposta del protocollo SAML non può essere inviata tramite associazioni diverse da HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid: Azure AD non supporta la richiesta di SAML inviata dall'app per SSO. |
| AADSTS7500514 | Impossibile trovare un tipo supportato di risposta SAML. I tipi di risposta supportati sono ' Response ' (nello spazio dei nomi XML ' urn: Oasis: Names: TC: SAML: 2.0: Protocol ') o ' Assertion ' (nello spazio dei nomi XML ' urn: Oasis: Names: TC: SAML: 2.0: Assertion '). Errore dell'applicazione: lo sviluppatore gestirà questo errore.|
| AADSTS75008 | RequestDeniedError: la richiesta inviata dall'app è stata negata perché la richiesta SAML contiene una destinazione imprevista. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims: il metodo di autenticazione usato dall'utente per il servizio non corrisponde al metodo di autenticazione richiesto. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy: la richiesta di autenticazione SAML2 contiene un elemento NameIdPolicy non valido. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable: l'agente di autenticazione non è in grado di connettersi ad Active Directory. Verificare che i server degli agenti siano membri della stessa foresta AD degli utenti le cui password devono essere convalidate e che siano in grado di connettersi ad Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-timeout della richiesta di convalida della password. Assicurarsi che Active Directory sia disponibile e risponda alle richieste dagli agenti. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException: si è verificato un errore sconosciuto durante l'elaborazione della risposta dall'agente di autenticazione. ripetere la richiesta. Se il problema persiste, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem: l'agente di autenticazione non riesce a convalidare la password dell'utente. Controllare i log dell'agente per altre informazioni e verificare che Active Directory funzioni come previsto. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException: l'agente di autenticazione non riesce a decrittografare la password. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours: gli utenti hanno tentato di eseguire l'accesso al di fuori delle ore consentite (specificate in Active Directory). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew: non è stato possibile completare il tentativo di autenticazione a causa di uno sfasamento dell'ora tra il computer che esegue l'agente di autenticazione e Active Directory. Correggere i problemi di sincronizzazione dell'ora. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated: un tentativo di autenticazione Kerberos non è riuscito. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported: il pacchetto di autenticazione non è supportato. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader: non è stata trovata alcuna intestazione dell'autorizzazione. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn: il tenant non è abilitato per Seamless SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat: non è stato possibile convalidare il ticket Kerberos dell'utente. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid: l'accesso Seamless SSO non è riuscito perché il ticket Kerberos dell'utente è scaduto o non è valido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed: non è stato possibile trovare l'oggetto utente in base alle informazioni contenute nel ticket Kerberos dell'utente. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn: l'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha effettuato l'accesso al dispositivo. |
| AADSTS90002 | InvalidTenantName: il nome del tenant non è stato trovato nell'archivio dati. Verificare di avere l'ID tenant corretto. |
| AADSTS90004 | InvalidRequestFormat: la richiesta non ha un formato corretto. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements: non è possibile completare la richiesta. La richiesta non è valida perché l'identificatore e l'hint di accesso non possono essere usati insieme.  |
| AADSTS90006 | ExternalServerRetryableError: il servizio è temporaneamente non disponibile.|
| AADSTS90007 | InvalidSessionId: richiesta non valida. L'ID sessione passato non può essere analizzato. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione. Come minimo, l'applicazione richiede l'accesso ad Azure AD, specificando l'autorizzazione di accesso e di lettura del profilo utente. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier: l'applicazione richiede un token per se stessa. Questo scenario è supportato solo se la risorsa specificata usa l'ID applicazione basato su GUID. |
| AADSTS90010 | NotSupported: non è possibile creare l'algoritmo. |
| AADSTS90012 | RequestTimeout: la richiesta è scaduta. |
| AADSTS90013 | InvalidUserInput: l'input dell'utente non è valido. |
| AADSTS90014 | MissingRequiredField: questo codice di errore può essere restituito in diversi casi quando un campo previsto non è presente nelle credenziali. |
| AADSTS90015 | QueryStringTooLong: la stringa di query è troppo lunga. |
| AADSTS90016 | MissingRequiredClaim: il token di accesso non è valido. L'attestazione necessaria è mancante. |
| AADSTS90019 | MissingTenantRealm: Azure AD non è riuscito a determinare l'identificatore del tenant dalla richiesta. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat: il nome dell'entità ha un formato non valido o non soddisfa il formato `name[/host][@realm]` previsto. Il nome dell'entità è obbligatorio, l'host e l'area di autenticazione sono facoltativi e possono essere impostati su null. |
| AADSTS90023 | InvalidRequest: la richiesta di servizio di autenticazione non è valida. |
| AADSTS9002313 | InvalidRequest: il formato della richiesta non è valido o non è valido. -Il problema è dovuto al fatto che si è verificato un errore nella richiesta a un determinato endpoint. Il suggerimento di questo problema è ottenere una traccia di Fiddler dell'errore che si verifica e verificare se la richiesta è effettivamente formattata correttamente. |
| AADSTS90024 | RequestBudgetExceededError: si è verificato un errore temporaneo. Riprovare. |
| AADSTS90033 | MsodsServiceUnavailable: Microsoft Online Directory Service (MSODS) non è disponibile. |
| AADSTS90036 | MsodsServiceUnretryableFailure: si è verificato un errore imprevisto irreversibile relativo al servizio WCF ospitato da MSODS. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore. |
| AADSTS90038 | NationalCloudTenantRedirection: il tenant specificato "Y" appartiene al cloud nazionale "X". L'istanza cloud "Z" corrente non è federata con "X". Viene restituito un errore di reindirizzamento al cloud. |
| AADSTS90043 | NationalCloudAuthCodeRedirection: la funzionalità è disabilitata. |
| AADSTS90051 | InvalidNationalCloudId: l'identificatore del cloud nazionale contiene un identificatore del cloud non valido. |
| AADSTS90055 | TenantThrottlingError: sono presenti troppe richieste in ingresso. Questa eccezione viene generata per i tenant bloccati. |
| AADSTS90056 | BadResourceRequest: per riscattare il codice per un token di accesso, l'app deve inviare una richiesta POST all'endpoint `/token`. Prima di questa operazione, inoltre, è necessario fornire un codice di autorizzazione e inviarlo nella richiesta POST all'endpoint `/token`. Per una panoramica del flusso del codice di autorizzazione OAuth 2,0, fare riferimento a questo articolo [... /azuread-dev/V1-Protocols-OAuth-code.MD](../azuread-dev/v1-protocols-oauth-code.md). Reindirizzare l'utente all'endpoint `/authorize`, che restituirà un elemento authorization_code. Inviando una richiesta all'endpoint `/token`, l'utente ottiene il token di accesso. Accedere al portale di Azure e controllare **Registrazioni per l'app > Endpoint** per verificare che i due endpoint siano stati configurati correttamente. |
| AADSTS90072 | PassThroughUserMfaError: l'account esterno usato dall'utente per accedere non esiste nel tenant in cui l'utente ha effettuato l'accesso. Di conseguenza, l'utente non può soddisfare i requisiti di MFA per il tenant. Questo errore può verificarsi anche se gli utenti vengono sincronizzati, ma esiste una mancata corrispondenza nell'attributo ImmutableID (sourceAnchor) tra Active Directory e Azure AD. L'account deve essere prima di tutto aggiunto come utente esterno nel tenant. Disconnettersi e accedere con un account utente di Azure AD diverso. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid: si è verificato un errore quando il servizio ha tentato di elaborare un messaggio WS-Federation. Il messaggio non è valido. |
| AADSTS90082 | OrgIdWsFederationNotSupported: il criterio di autenticazione selezionato per la richiesta non è attualmente supportato. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed: gli account guest non sono consentiti per questo sito. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed: il servizio non è in grado di rilasciare un token perché non è stato ancora effettuato il provisioning dell'oggetto aziendale. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired: il token DA dell'utente è scaduto. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed: si è verificato un errore durante la creazione del messaggio WS-Federation dall'URI. |
| AADSTS90090 | GraphRetryableError: il servizio è temporaneamente non disponibile. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized: Graph ha restituito un codice di errore di richiesta non consentita. |
| AADSTS90094 | AdminConsentRequired: è necessario il consenso dell'amministratore. |
| AADSTS900382 | Il client riservato non è supportato nella richiesta tra cloud. |
| AADSTS90099 | L'applicazione ' {appId}' ({appName}) non è stata autorizzata nel tenant ' {tenant}'. Le applicazioni devono essere autorizzate ad accedere al tenant del cliente prima di poterle usare dagli amministratori delegati del partner. Fornire il consenso preliminare o eseguire l'API del centro per i partner appropriata per autorizzare l'applicazione. |
| AADSTS90100 | InvalidRequestParameter: il parametro è vuoto o non valido. |
| AADSTS901002 | AADSTS901002: il parametro della richiesta ' Resource ' non è supportato. |
| AADSTS90101 | InvalidEmailAddress: i dati forniti non sono un indirizzo di posta elettronica valido. L'indirizzo di posta elettronica deve avere questo formato: `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter: il valore deve essere un URI assoluto valido. |
| AADSTS90107 | InvalidXml: la richiesta non è valida. Assicurarsi che i dati non contengano caratteri non validi.|
| AADSTS90114 | InvalidExpiryDate: il timestamp di scadenza del token di massa causerà il rilascio di un token scaduto. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode: il codice utente è null o vuoto.|
| AADSTS90120 | InvalidDeviceFlowRequest: la richiesta è stata già autorizzata o rifiutata. |
| AADSTS90121 | InvalidEmptyRequest: richiesta vuota non valida.|
| AADSTS90123 | IdentityProviderAccessDenied: il token non può essere rilasciato perché il provider di rilascio di attestazioni o identità ha rifiutato la richiesta. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported: la risorsa non è supportata negli endpoint `/common` o `/consumers`. Usare invece `/organizations` o l'endpoint specifico del tenant. |
| AADSTS90125 | DebugModeEnrollTenantNotFound: l'utente non è presente nel sistema. Assicurarsi di aver immesso correttamente il nome utente. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred: il tipo di utente non è supportato in questo endpoint. Il sistema non può dedurre il tenant dell'utente dal nome utente. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported: l'applicazione non è supportata negli endpoint `/common` o `/consumers`. Usare invece `/organizations` o l'endpoint specifico del tenant. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated: si è verificato un errore irreversibile.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound: per l'entità utente non è configurata la chiave ID NGC. |
| AADSTS130005 | NgcInvalidSignature: la verifica della firma della chiave NGC non è riuscita.|
| AADSTS130006 | NgcTransportKeyNotFound: la chiave di trasporto NGC non è configurata nel dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled: il dispositivo è disabilitato. |
| AADSTS130008 | NgcDeviceIsNotFound: il dispositivo cui fa riferimento la chiave NGC non è stato trovato. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce: non è stata fornita la chiave nonce della richiesta. |
| AADSTS140001 | InvalidSessionKey: la chiave della sessione non è valida.|
| AADSTS165900 | InvalidApiRequest: richiesta non valida. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion: la versione di WebView per Chrome non è supportata. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource: la risorsa non è configurata per accettare token solo dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized: l'utente non è autorizzato a registrare i dispositivi in Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing: non è possibile usare id_token come concessione `urn:ietf:params:oauth:grant-type:jwt-bearer`.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy: l'amministratore del tenant ha configurato un criterio di sicurezza che blocca questa richiesta. Verificare i criteri di sicurezza definiti a livello di tenant per determinare se la richiesta soddisfa i requisiti dei criteri. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest: l'applicazione non è stata trovata nella directory o nel tenant. Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant o non è consentita da uno degli utenti nel tenant. È possibile che il valore dell'identificatore per l'applicazione sia stato configurato in modo errato o che la richiesta di autenticazione sia stata inviata al tenant sbagliato. |
| AADSTS700020 | InteractionRequired: la concessione dell'accesso richiede l'interazione. |
| AADSTS700022 | InvalidMultipleResourcesScope: il valore specificato per l'ambito del parametro di input non è valido perché contiene più di una risorsa. |
| AADSTS700023 | InvalidResourcelessScope: il valore specificato per l'ambito del parametro di input non è valido quando è necessario un token di accesso. |
| AADSTS7000215 | Viene fornito un segreto client non valido. Errore dello sviluppatore: l'app sta provando a eseguire l'accesso senza i parametri di autenticazione necessari o corretti.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided: le chiavi segrete del client specificate sono scadute. Visitare la portale di Azure per creare nuove chiavi per l'app o prendere in considerazione l'uso delle credenziali del certificato per una maggiore sicurezza: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | Il codice di autorizzazione fornito da InvalidGrantRedeemAgainstWrongTenant è progettato per essere usato con altri tenant, quindi rifiutato. Il codice di autorizzazione di OAuth2 deve essere riscattato rispetto allo stesso tenant per cui è stato acquisito (/Common o/{tenant-ID} in base alle esigenze) |
| AADSTS1000000 | UserNotBoundError: l'API di associazione richiede che l'utente di Azure AD esegua l'autenticazione anche con un provider di identità esterno e questa autenticazione non è stata ancora eseguita. |
| AADSTS1000002 | BindCompleteInterruptError: l'associazione è stata completata correttamente, ma l'utente deve essere informato. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-l'applicazione è disabilitata. |
| AADSTS7000114| L'applicazione ' appIdentifier ' non è consentita per effettuare chiamate per l'applicazione.|
| AADSTS7500529 | Il valore ' SAMLId-GUID ' non è un ID SAML valido-Azure AD usa questo attributo per popolare l'attributo InResponseTo della risposta restituita. L'ID non deve iniziare con un numero, quindi una strategia comune consiste nell'anteporre una stringa come "id" alla rappresentazione di stringa di un GUID. Ad esempio, id6c1c178c166d486687be4aaf5e482730 è un ID valido. |

## <a name="next-steps"></a>Passaggi successivi

* In caso di domande o se non è possibile trovare le informazioni necessarie, creare un problema in GitHub oppure fare riferimento alle [opzioni di supporto e assistenza per sviluppatori](./developer-support-help-options.md) per informazioni su come ottenere supporto.