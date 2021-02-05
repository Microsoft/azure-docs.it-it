---
title: Microsoft Identity Platform e flusso del codice di autorizzazione OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Creare applicazioni Web usando l'implementazione Microsoft Identity Platform del protocollo di autenticazione OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2687141ea870b0af0a4405ebef2261c5a303c767
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584113"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Flusso del codice di autorizzazione OAuth 2.0 e Microsoft Identity Platform

La concessione del codice di autorizzazione OAuth 2.0 può essere utilizzata nelle app che vengono installate su un dispositivo per ottenere l'accesso alle risorse protette, come l'API web. Usando l'implementazione Microsoft Identity Platform di OAuth 2.0, è possibile aggiungere l'accesso e l'API di accesso alle applicazioni desktop e mobili.

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione in qualsiasi linguaggio.  Quando possibile, è consigliabile usare le librerie di autenticazione Microsoft (MSAL) supportate anziché [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Vedere anche le [app di esempio che usano MSAL](sample-v2-code.md).

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui [app a pagina singola](v2-app-types.md#single-page-apps-javascript), [app Web](v2-app-types.md#web-apps) e [app native](v2-app-types.md#mobile-and-native-apps). Il flusso consente alle app di acquisire in modo sicuro access_tokens che possono essere usate per accedere alle risorse protette dalla piattaforma di identità Microsoft, nonché i token di aggiornamento per ottenere access_tokens aggiuntivi e token ID per l'utente connesso.

## <a name="protocol-diagram"></a>Diagramma di protocollo

A livello generale, l'intero flusso di autenticazione per un'applicazione ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Installazione dell'URI di reindirizzamento necessaria per le app a singola pagina

Il flusso del codice di autorizzazione per le applicazioni a pagina singola richiede una configurazione aggiuntiva.  Seguire le istruzioni per la [creazione dell'applicazione a singola pagina](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) per contrassegnare correttamente l'URI di reindirizzamento come abilitato per CORS. Per aggiornare un URI di reindirizzamento esistente per abilitare CORS, aprire l'editor del manifesto e impostare il `type` campo per l'URI di reindirizzamento su `spa` nella `replyUrlsWithType` sezione. È anche possibile fare clic sull'URI di reindirizzamento nella sezione "Web" della scheda autenticazione e selezionare gli URI di cui si vuole eseguire la migrazione usando il flusso del codice di autorizzazione.

Il `spa` tipo di reindirizzamento è compatibile con le versioni precedenti del flusso implicito. Le app che usano attualmente il flusso implicito per ottenere i token possono passare al `spa` tipo di URI di reindirizzamento senza problemi e continuare a usare il flusso implicito.

Se si tenta di usare il flusso del codice di autorizzazione e si verifica questo errore:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Visitare quindi la registrazione dell'app e aggiornare l'URI di reindirizzamento per l'app al tipo `spa` .

## <a name="request-an-authorization-code"></a>Richiedere un codice di autorizzazione

Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize` . In questa richiesta, il client richiede le autorizzazioni `openid`, `offline_access`e `https://graph.microsoft.com/mail.read ` all'utente.  Alcune autorizzazioni sono riservate all'amministratore, ad esempio la scrittura di dati nella directory di un'organizzazione usando `Directory.ReadWrite.All`. Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente dell'organizzazione, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'applicazione. Per richiedere l'accesso agli ambiti con restrizioni per l'amministratore, è necessario richiederli direttamente da un amministratore globale.  Per altre informazioni, vedere [autorizzazioni riservate all'amministratore](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Fare clic sul collegamento seguente per eseguire questa richiesta. Dopo l'accesso, il browser deve essere reindirizzato a `https://localhost/myapp/` con un `code` nella barra degli indirizzi.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametro    | Obbligatoria/facoltativa | Descrizione |
|--------------|-------------|--------------|
| `tenant`    | obbligatorio    | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | obbligatorio    | L'**ID dell'applicazione (client)** assegnato all'app dall'esperienza [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908).  |
| `response_type` | obbligatorio    | Deve includere `code` per il flusso del codice di autorizzazione. Può anche includere `id_token` o `token` se si usa il [flusso ibrido](#request-an-id-token-as-well-hybrid-flow). |
| `redirect_uri`  | necessarie | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Per le app native e le app per dispositivi mobili è necessario usare il valore predefinito `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | obbligatorio    | Elenco separato da spazi di [ambiti](v2-permissions-and-consent.md) a cui si vuole che l'utente dia il consenso.  Per la parte `/authorize` della richiesta, può coprire più risorse, consentendo all'app di ottenere il consenso per più API Web che si vuole chiamare. |
| `response_mode`   | Consigliato | Specifica il metodo da usare per restituire il token risultante all'app. Può essere uno dei valori seguenti:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornisce il codice come parametro della stringa di query nell'URI di reindirizzamento. Se si sta richiedendo un token ID usando il flusso implicito, non è possibile usare `query` come indicato nella [specifica OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se si sta richiedendo solo il codice, è possibile usare `query`, `fragment` o `form_post`. `form_post` esegue un POST contenente il codice all'URI di reindirizzamento. |
| `state`                 | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [evitare gli attacchi di richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente. Questo valore può essere usato anche per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `prompt`  | facoltativo    | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi al momento sono `login`, `none` e `consent`.<br/><br/>- `prompt=login` forza l'utente a immettere le sue credenziali alla richiesta, negando l'accesso Single Sign-On.<br/>- `prompt=none` è l'opposto: garantisce che all'utente non venga presentata alcuna richiesta interattiva. Se la richiesta non può essere completata in modo invisibile all'utente tramite Single Sign-on, la piattaforma di identità Microsoft restituirà un `interaction_required` errore.<br/>- `prompt=consent` attiva la finestra di dialogo di consenso di OAuth dopo l'accesso dell'utente, che chiede all'utente di concedere le autorizzazioni all'app.<br/>- `prompt=select_account` interromperà Single Sign-On fornendo l'esperienza di selezione dell'account elencando tutti gli account nella sessione o qualsiasi account memorizzato o un'opzione per scegliere di usare un account diverso.<br/> |
| `login_hint`  | facoltativo    | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`.   |
| `domain_hint`  | facoltativo    | Se incluso, non verrà eseguito il processo di individuazione basato sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso. Questo comporta un'esperienza utente leggermente semplificata, ad esempio inviando quest'ultimo al provider di identità federato. Le app usano spesso questo parametro durante la riautenticazione, estraendo `tid` da un accesso precedente. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`.  |
| `code_challenge`  | consigliato / obbligatorio | Usato per proteggere i privilegi concessi sui codici di autorizzazione tramite la chiave di prova per Code Exchange (PKCE). Obbligatorio con `code_challenge_method` incluso. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). Questa operazione è ora consigliata per tutti i tipi di applicazioni: app native, applicazione a pagina singola e client riservati come le app Web. |
| `code_challenge_method` | consigliato / obbligatorio | Metodo usato per codificare `code_verifier` per il parametro `code_challenge`. Questo *dovrebbe* essere `S256` , ma la specifica consente l'uso di `plain` se per qualche motivo il client non è in grado di supportare SHA256. <br/><br/>Se escluso, `code_challenge` viene considerato testo non crittografato se `code_challenge` è incluso. La piattaforma Microsoft Identity supporta sia `plain` che `S256` . Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). Questa operazione è obbligatoria per [app a pagina singola che usano il flusso del codice di autorizzazione](reference-third-party-cookies-spas.md).|


A questo punto, all'utente viene chiesto di immettere le credenziali e completare l'autenticazione. La piattaforma Microsoft Identity garantisce inoltre che l'utente abbia acconsentito alle autorizzazioni indicate nel `scope` parametro di query. Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint chiederà all'utente di fornire il consenso per le autorizzazioni obbligatorie. Questo articolo contiene informazioni dettagliate su [autorizzazioni, consenso e app multi-tenant](v2-permissions-and-consent.md).

Una volta che l'utente esegue l'autenticazione e concede il consenso, la piattaforma di identità Microsoft restituirà una risposta all'app all'indirizzo indicato `redirect_uri` , usando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=query` ha un aspetto simile al seguente:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametro | Descrizione  |
|-----------|--------------|
| `code` | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione hanno una durata breve, in genere scadono dopo circa 10 minuti. |
| `state` | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'app deve verificare che i valori del parametro state siano identici nella richiesta e nella risposta. |

È anche possibile ricevere un token ID se ne viene richiesto uno e si ha la concessione implicita abilitata nella registrazione dell'applicazione.  Questo viene talvolta definito ["flusso ibrido"](#request-an-id-token-as-well-hybrid-flow)e viene usato da framework come ASP.NET.

#### <a name="error-response"></a>Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'app possa gestirle adeguatamente:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione  |
|----------|------------------|
| `error`  | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione

La tabella seguente descrive i diversi codici errore che possono essere restituiti nel parametro `error` della risposta di errore.

| Codice di errore  | Descrizione    | Azione client   |
|-------------|----------------|-----------------|
| `invalid_request` | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| `unauthorized_client` | All'applicazione client non è consentito richiedere un codice di autorizzazione. | Questo errore si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `access_denied`  | Consenso negato dal proprietario della risorsa  | L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsenta. |
| `unsupported_response_type` | Il server di autorizzazione non supporta il tipo di risposta nella richiesta. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. Quando viene visualizzato nel [flusso ibrido](#request-an-id-token-as-well-hybrid-flow), segnala che è necessario abilitare l'impostazione di concessione implicita del token ID nella registrazione dell'app client. |
| `server_error`  | Errore imprevisto rilevato dal server.| ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| `temporarily_unavailable`   | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| `invalid_resource`  | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. | Questo errore indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `login_required` | Troppi o nessun utente | Il client ha richiesto l'autenticazione invisibile all'utente (`prompt=none`), ma non è stato trovato un singolo utente. Questo può significare che sono presenti più utenti attivi nella sessione o nessun utente. Questo prende in considerazione il tenant scelto (ad esempio, se sono presenti due account AD attivi e un account Microsoft e viene scelto `consumers`, l'autenticazione invisibile funzionerà). |
| `interaction_required` | La richiesta richiede l'interazione dell'utente. | È necessario un passaggio di autenticazione o un consenso aggiuntivo. Ripetere la richiesta senza `prompt=none`. |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Richiedere anche un token ID (flusso ibrido)

Per capire chi è l'utente prima di riscattare un codice di autorizzazione, spesso le applicazioni richiedono anche un token ID quando richiedono il codice di autorizzazione. Questa operazione viene definita *flusso ibrido* perché combina la concessione implicita con il flusso del codice di autorizzazione. Il flusso ibrido viene comunemente usato nelle app Web che vogliono eseguire il rendering di una pagina per un utente senza bloccare il riscatto del codice, in particolare [ASP.NET](quickstart-v2-aspnet-core-webapp.md). Sia le app a singola pagina che le app Web tradizionali traggono vantaggio dalla latenza ridotta di questo modello.

Il flusso ibrido è identico al flusso del codice di autorizzazione descritto in precedenza, ma con tre aggiunte, tutte necessarie per richiedere un token ID: nuovi ambiti, una nuova response_type e un nuovo `nonce` parametro di query.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parametro aggiornato | Obbligatoria/facoltativa | Descrizione |
|---------------|-------------|--------------|
|`response_type`| Obbligatoria | L'aggiunta di `id_token` indica al server che l'applicazione desidera un token ID nella risposta dall' `/authorize` endpoint.  |
|`scope`| Necessario | Per i token ID, è necessario aggiornare per includere gli ambiti del token ID `openid` e, facoltativamente, `profile` e `email` . |
|`nonce`| Necessario|     Valore incluso nella richiesta, generato dall'app, che verrà incluso come attestazione nel parametro id_token risultante. L'app può quindi verificare questo valore per l'attenuazione degli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
|`response_mode`| Consigliato | Specifica il metodo da usare per inviare il token risultante a un'app. Il valore predefinito è `query` solo per un codice di autorizzazione, ma `fragment` se la richiesta include un id_token `response_type` .|

L'uso di `fragment` come modalità di risposta può causare problemi per le app Web che leggono il codice dal reindirizzamento, perché i browser non passano il frammento al server Web.  In queste situazioni è consigliabile usare la `form_post` modalità di risposta per assicurarsi che tutti i dati vengano inviati al server. 

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo che usa `response_mode=fragment` ha un aspetto simile al seguente:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parametro | Descrizione  |
|-----------|--------------|
| `code` | Codice di autorizzazione richiesto dall'app. L'app può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. I codici di autorizzazione sono di breve durata, in genere scaduti dopo circa 10 minuti. |
| `id_token` | Token ID per l'utente, emesso tramite *concessione implicita*. Contiene un' `c_hash` attestazione speciale che rappresenta l'hash dell'oggetto `code` nella stessa richiesta. |
| `state` | Se nella richiesta è incluso un parametro state, lo stesso valore deve essere visualizzato nella risposta. L'app deve verificare che i valori del parametro state siano identici nella richiesta e nella risposta. |

## <a name="request-an-access-token"></a>Richiedere un token di accesso

Dopo avere ottenuto un codice di autorizzazione e l'autorizzazione dell'utente, è possibile riscattare il valore `code` per un `access_token` per la risorsa desiderata. A tale scopo, inviare una richiesta `POST` all'endpoint `/token`:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Non dimenticare di sostituire il `code`) [![ Provare a eseguire la richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametro  | Obbligatoria/facoltativa | Descrizione     |
|------------|-------------------|----------------|
| `tenant`   | obbligatorio   | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | obbligatorio  | ID dell'applicazione (client) che la pagina [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `grant_type` | obbligatorio   | Deve essere `authorization_code` per il flusso del codice di autorizzazione.   |
| `scope`      | facoltative   | Elenco di ambiti separato da spazi. Gli ambiti devono appartenere tutti a una singola risorsa, insieme agli ambiti OIDC (`profile`, `openid`, `email`). Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md). Si tratta di un'estensione Microsoft per il flusso del codice di autorizzazione, destinata a consentire alle app di dichiarare la risorsa per cui si desidera il token durante il riscatto del token.|
| `code`          | obbligatorio  | Codice di autorizzazione acquisito durante la prima sezione del flusso. |
| `redirect_uri`  | obbligatorio  | Stesso valore redirect_uri usato per acquisire il codice di autorizzazione. |
| `client_secret` | obbligatorio per app Web riservate | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. Non è consigliabile usare il segreto dell'applicazione in un'app nativa o a pagina singola poiché i segreti client non possono essere archiviati in modo affidabile su dispositivi e pagine Web. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server.  Come tutti i parametri descritti qui, il segreto client deve essere codificato in URL prima dell'invio, un passaggio eseguito in genere dall'SDK. Per altre informazioni sulla codifica URI, vedere la [specifica della sintassi generica URI](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | Consigliato  | Stesso code_verifier usato per ottenere il codice di autorizzazione. Obbligatorio se nella richiesta di concessione del codice di autorizzazione è stato usato PKCE. Per altre informazioni, vedere il [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Risposta con esito positivo

Una risposta di token di esito positivo sarà simile alla seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametro     | Descrizione   |
|---------------|------------------------------|
| `access_token`  | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web.  |
| `token_type`    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| `expires_in`    | Validità del token di accesso (espressa in secondi). |
| `scope`         | Ambiti per i quali il token di accesso è valido. Facoltativo: non è standard e, se omesso, il token sarà per gli ambiti richiesti sulla parte iniziale del flusso. |
| `refresh_token` | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno una durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per informazioni dettagliate sull'aggiornamento di un token di accesso, vedere la [sezione successiva](#refresh-the-access-token). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `offline_access`. |
| `id_token`      | Token JSON Web (JWT). L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli e i client riservati possono usarlo per l'autorizzazione. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |

### <a name="error-response"></a>Risposta di errore

Le risposte di errore hanno un aspetto simile al seguente:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro         | Descrizione    |
|-------------------|----------------|
| `error`       | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica.  |
| `timestamp`   | Ora in cui si è verificato l'errore. |
| `trace_id`    | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

### <a name="error-codes-for-token-endpoint-errors"></a>Codici per gli errori degli endpoint di token

| Codice di errore         | Descrizione        | Azione client    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere la richiesta o la registrazione delle app e inviare di nuovo la richiesta   |
| `invalid_grant`    | Il codice di autorizzazione o PKCE non è valido o è scaduto. | Provare una nuova richiesta per l'endpoint `/authorize` e verificare che il parametro code_verifier sia corretto.  |
| `unauthorized_client` | Il client autenticato non è autorizzato a usare questo tipo di concessione dell'autorizzazione. | Questo errore si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| `invalid_client` | Autenticazione client non riuscita.  | Credenziali del client non valide. Per risolvere il problema, l'amministratore applicazione aggiorna le credenziali.   |
| `unsupported_grant_type` | Il server di autorizzazione non supporta il tipo di concessione dell'autorizzazione. | Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato durante il test iniziale. |
| `invalid_resource` | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. | Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD.  |
| `interaction_required` | Non standard, perché la specifica OIDC chiama solo per l' `/authorize` endpoint. La richiesta richiede l'interazione dell'utente. Ad esempio, è necessario un passaggio di autenticazione aggiuntivo. | Ripetere la `/authorize` richiesta con gli stessi ambiti. |
| `temporarily_unavailable` | Il server è temporaneamente troppo occupato per gestire la richiesta. | Ripetere la richiesta dopo un breve ritardo. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
|`consent_required` | La richiesta richiede il consenso dell'utente. Questo errore non è standard, perché in genere viene restituito solo sull' `/authorize` endpoint per specifiche OIDC. Restituito quando un `scope` parametro è stato usato nel flusso di riscatto del codice che l'app client non dispone dell'autorizzazione per la richiesta.  | Il client deve inviare l'utente all' `/authorize` endpoint con l'ambito corretto per poter attivare il consenso. |
|`invalid_scope` | L'ambito richiesto dall'app non è valido.  | Aggiornare il valore del parametro scope nella richiesta di autenticazione a un valore valido. |

> [!NOTE]
> Le app a pagina singola possono ricevere un errore `invalid_request` che indica che il riscatto del token tra le origini è consentito solo per il tipo di client "Applicazione a pagina singola".  Ciò indica che l'URI di reindirizzamento usato per richiedere il token non è stato contrassegnato come URI di reindirizzamento `spa`.  Esaminare i [passaggi di registrazione dell'applicazione](#redirect-uri-setup-required-for-single-page-apps) su come abilitare questo flusso.

## <a name="use-the-access-token"></a>Usare il token di accesso

Dopo aver ottenuto un `access_token` è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`:

> [!TIP]
> Eseguire la richiesta in Postman. (Sostituire prima l'intestazione `Authorization`) [![Provare a eseguire la richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aggiornare il token di accesso

I token di accesso hanno breve durata ed è quindi necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. A tale scopo, inviare un'altra richiesta `POST` all'endpoint `/token`, specificando il `refresh_token` anziché il valore `code`.  I token di aggiornamento sono validi per tutte le autorizzazioni per cui il client ha già ottenuto il consenso; in questo modo, un token di aggiornamento emesso su richiesta per `scope=mail.read` può essere usato per richiedere un nuovo token di accesso per `scope=api://contoso.com/api/UseResource`.

I token di aggiornamento per app Web e app native non hanno una durata specificata. La durata dei token di aggiornamento è in genere relativamente lunga. In alcuni casi, tuttavia, i token di aggiornamento scadono, vengono revocati o non hanno privilegi sufficienti per l'azione desiderata. L'applicazione deve prevedere e gestire correttamente gli [errori restituiti dall'endpoint di rilascio del token](#error-codes-for-token-endpoint-errors). Le app a pagina singola, tuttavia, ottengono un token con una durata di 24 ore, richiedendo una nuova autenticazione ogni giorno.  Questa operazione può essere eseguita in modo invisibile all'utente in un iframe quando i cookie di terze parti sono abilitati, ma devono essere eseguiti in un frame di livello principale (con navigazione in tutta la pagina o popup) nei browser senza cookie di terze parti, ad esempio Safari.

Anche se i token di aggiornamento non vengono revocati quando vengono usati per acquisire nuovi token di accesso, è necessario rimuovere il token di aggiornamento precedente. La [specifica OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) indica: "Il server di autorizzazione PUÒ eseguire un nuovo token di aggiornamento, nel qual caso il client DEVE rimuovere il token di aggiornamento precedente e sostituirlo con un nuovo token di aggiornamento. Il server di autorizzazione può revocare il token di aggiornamento precedente dopo aver eseguito un nuovo token di aggiornamento per il client".

>[!IMPORTANT]
> Per i token di aggiornamento inviati a un URI di reindirizzamento registrato come `spa`, il token di aggiornamento scadrà dopo 24 ore. I token di aggiornamento aggiuntivi acquisiti usando il token di aggiornamento iniziale riporteranno tale scadenza, quindi le app devono essere preparate a eseguire nuovamente il flusso del codice di autorizzazione usando un'autenticazione interattiva per ottenere un nuovo token di aggiornamento ogni 24 ore. Gli utenti non devono immettere le proprie credenziali e in genere non vedranno alcuna esperienza utente, ma solo un ricaricamento dell'applicazione. Il browser deve visitare la pagina di accesso in un frame di livello principale per visualizzare la sessione di accesso.  Questo problema è dovuto alle [funzionalità di privacy nei browser che bloccano i cookie di terze parti](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Non dimenticare di sostituire il `refresh_token`) [![ Provare a eseguire la richiesta in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parametro     | Tipo           | Descrizione        |
|---------------|----------------|--------------------|
| `tenant`        | obbligatorio     | Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono `common`, `organizations`, `consumers` e gli identificatori del tenant. Per altre informazioni, vedere le [nozioni di base sul protocollo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | obbligatorio    | L'**ID dell'applicazione (client)** assegnato all'app dall'esperienza [Portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `grant_type`    | obbligatorio    | Deve essere `refresh_token` per questa sezione del flusso del codice di autorizzazione. |
| `scope`         | obbligatorio    | Elenco di ambiti separati da spazi. Gli ambiti richiesti in questa sezione devono essere equivalenti agli ambiti richiesti nella sezione di richiesta del codice di autorizzazione originale o un sottoinsieme di questi ultimi. Se gli ambiti specificati in questa richiesta si estendono su più server di risorse, la piattaforma di identità Microsoft restituirà un token per la risorsa specificata nel primo ambito. Per una spiegazione più dettagliata degli ambiti, fare riferimento all'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md). |
| `refresh_token` | obbligatorio    | Token di aggiornamento acquisito durante la seconda sezione del flusso. |
| `client_secret` | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare in modo sicuro il segreto client sul lato server. Questo segreto deve essere codificato con URL. Per altre informazioni, vedere la [specifica della sintassi generica URI](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Risposta con esito positivo

Una risposta di token di esito positivo sarà simile alla seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametro     | Descrizione         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| `token_type`    | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è 'Bearer'. |
| `expires_in`    | Validità del token di accesso (espressa in secondi).   |
| `scope`         | Ambiti per i quali il token di accesso è valido.    |
| `refresh_token` | Nuovo token di aggiornamento di OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con quello appena acquisito, per garantire che i token di aggiornamento rimangano validi il più a lungo possibile. <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `offline_access`.|
| `id_token`      | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. Per altre informazioni sugli oggetti id_token, vedere [`id_token reference`](id-tokens.md). <br> **Nota:** viene fornito solo se è stato richiesto l'ambito `openid`. |

#### <a name="error-response"></a>Risposta di errore

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro         | Descrizione                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione.           |
| `error_codes` |Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| `timestamp` | Ora in cui si è verificato l'errore. |
| `trace_id` | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

Per una descrizione dei codici di errore e l'azione consigliata per il client, vedere [Codici per gli errori degli endpoint di token](#error-codes-for-token-endpoint-errors).
