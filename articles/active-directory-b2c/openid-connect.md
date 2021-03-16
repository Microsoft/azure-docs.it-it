---
title: Accesso Web con OpenID Connect-Azure Active Directory B2C
description: Creare applicazioni Web usando il protocollo di autenticazione OpenID Connect in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 608017c15d039be940d1d67b8f9e1bf7618134b7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491499"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Accesso Web con OpenID Connect in Azure Active Directory B2C

OpenID Connect è un protocollo di autenticazione basato su OAuth 2.0 che può essere usato per consentire agli utenti di accedere in modo sicuro alle applicazioni Web. Tramite l'implementazione in Azure Active Directory B2C (Azure AD B2C) di OpenID Connect è possibile assegnare esperienze di gestione delle iscrizioni, degli accessi e altre esperienze di gestione delle identità nelle applicazioni Web ad Azure AD. Questa guida illustra come eseguire questa operazione in modo indipendente dal linguaggio. La guida descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie Microsoft open source.

> [!NOTE]
> La maggior parte delle librerie di autenticazione open source acquisisce e convalidano i token JWT per l'applicazione. È consigliabile esplorare tali opzioni, anziché implementare codice personalizzato. Per ulteriori informazioni, vedere [Panoramica di Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)e [Microsoft Identity Web Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 per consentirne l'uso come protocollo di *autenticazione*. Questo protocollo di autenticazione consente di eseguire Single Sign-On. Viene introdotto il concetto di *token ID*, che consente al client di verificare l'identità dell'utente e ottenere informazioni di base sul profilo dell'utente.

Poiché estende OAuth 2,0, consente inoltre alle applicazioni di acquisire in modo sicuro i *token di accesso*. I token di accesso possono essere usati per accedere alle risorse protette tramite un [server di autorizzazione](protocols-overview.md). È consigliabile usare OpenID Connect se si sta creando un'applicazione Web ospitata in un server e accessibile tramite un browser. Per ulteriori informazioni sui token, vedere la [Panoramica dei token in Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C estende il protocollo standard OpenID Connect per non limitarsi esclusivamente a semplici operazioni di autenticazione e autorizzazione. Introduce il [parametro flusso utente](user-flow-overview.md)che consente di usare OpenID Connect per aggiungere esperienze utente all'applicazione, ad esempio la gestione dell'iscrizione, dell'accesso e del profilo.

## <a name="send-authentication-requests"></a>Invio di richieste di autenticazione

Quando l'applicazione Web deve autenticare l'utente ed eseguire un flusso utente, può indirizzare l'utente all' `/authorize` endpoint. L'utente esegue un'azione a seconda del flusso utente.

In questa richiesta il client indica le autorizzazioni che deve acquisire dall'utente nel `scope` parametro e specifica il flusso utente da eseguire. Per avere un'idea del funzionamento della richiesta, provare a incollare la richiesta in un browser e a eseguirla. Sostituire `{tenant}` con il nome del tenant. Sostituire `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` con l'ID app dell'applicazione registrata in precedenza nel tenant. Modificare anche il nome del criterio ( `{policy}` ) con il nome del criterio presente nel tenant, ad esempio `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| inquilino | Sì | Nome del tenant di Azure AD B2C |
| politica | Sì | Flusso utente da eseguire. Specificare il nome di un flusso utente creato nel tenant del Azure AD B2C. Ad esempio: `b2c_1_sign_in`, `b2c_1_sign_up` o `b2c_1_edit_profile`. |
| client_id | Sì | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione. |
| nonce | Sì | Valore incluso nella richiesta, generato dall'applicazione, incluso nel token ID risultante come attestazione. L'applicazione può quindi verificare questo valore per attenuare gli attacchi di riproduzione del token. Il valore è in genere una stringa casuale univoca che può essere usata per identificare l'origine della richiesta. |
| response_type | Sì | Deve includere un token ID per OpenID Connect. Se l'applicazione Web richiede anche token per chiamare un'API Web, è possibile usare `code+id_token` . |
| ambito | Sì | Elenco di ambiti separato da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. L' `offline_access` ambito è facoltativo per le applicazioni Web. Indica che l'applicazione richiede un token di *aggiornamento* per l'accesso esteso alle risorse. |
| prompt | No | Tipo di interazione utente obbligatoria. L'unico valore valido in questa fase è `login`, che impone all'utente di immettere le credenziali per la richiesta. |
| redirect_uri | No | Il `redirect_uri` parametro dell'applicazione, in cui le risposte di autenticazione possono essere inviate e ricevute dall'applicazione. Deve corrispondere esattamente a uno dei `redirect_uri` parametri registrati nella portale di Azure, ad eccezione del fatto che deve essere codificato in URL. |
| response_mode | No | Metodo utilizzato per inviare di nuovo il codice di autorizzazione risultante all'applicazione. Può essere `query`, `form_post` o `fragment`.  `form_post` è la modalità di risposta consigliata perché offre la sicurezza migliore. |
| state | No | Valore incluso nella richiesta che viene anche restituito nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per evitare gli attacchi di richiesta intersito falsa, viene in genere usato un valore univoco generato casualmente. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'applicazione prima che venga eseguita la richiesta di autenticazione, ad esempio la pagina in cui si trovava. |
| login_hint | No| Può essere usato per compilare il campo nome di accesso della pagina di accesso. Per ulteriori informazioni, vedere [prepopolare il nome di accesso](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Fornisce un suggerimento per Azure AD B2C sul provider di identità di social networking da usare per l'accesso. Se viene incluso un valore valido, l'utente passa direttamente alla pagina di accesso del provider di identità.  Per altre informazioni, vedere [reindirizzare l'accesso a un social provider](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Parametri personalizzati | No| Parametri personalizzati che possono essere utilizzati con i [criteri personalizzati](custom-policy-overview.md). Ad esempio, [URI del contenuto della pagina personalizzati dinamici](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)o [resolver di attestazioni chiave-valore](claim-resolver-overview.md#oauth2-key-value-parameters). |

A questo punto, all'utente viene richiesto di completare il flusso di lavoro. L'utente potrebbe dover immettere il nome utente e la password, accedere con un'identità di social networking o iscriversi alla directory. Potrebbero essere presenti altri passaggi a seconda di come viene definito il flusso utente.

Dopo che l'utente ha completato il flusso utente, viene restituita una risposta all'applicazione in corrispondenza del `redirect_uri` parametro indicato, usando il metodo specificato nel `response_mode` parametro. La risposta è la stessa per ognuno dei casi precedenti, indipendentemente dal flusso utente.

Una risposta con esito positivo che utilizza `response_mode=fragment` ha un aspetto simile al seguente:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --------- | ----------- |
| id_token | Token ID richiesto dall'applicazione. È possibile usare il token ID per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| codice | Il codice di autorizzazione richiesto dall'applicazione, se è stato usato `response_type=code+id_token` . L'applicazione può usare il codice di autorizzazione per richiedere un token di accesso per una risorsa di destinazione. I codici di autorizzazione scadono in genere dopo circa 10 minuti. |
| state | Se nella richiesta è incluso un parametro `state`, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che i `state` valori nella richiesta e nella risposta siano identici. |

Le risposte di errore possono essere inviate anche al `redirect_uri` parametro in modo che l'applicazione sia in grado di gestirle in modo appropriato:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| state | Se nella richiesta è incluso un parametro `state`, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che i `state` valori nella richiesta e nella risposta siano identici. |

## <a name="validate-the-id-token"></a>Convalidare il token ID

La ricezione di un token ID non è sufficiente per autenticare l'utente. Convalidare la firma del token ID e verificare le attestazioni nel token in base ai requisiti dell'applicazione. Azure AD B2C usa i [token Web JSON](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e la crittografia a chiave pubblica per firmare i token e verificarne la validità. 

> [!NOTE]
> La maggior parte delle librerie di autenticazione Open Source convalidano i token JWT per l'applicazione. È consigliabile esplorare tali opzioni anziché implementare una logica di convalida personalizzata. Per ulteriori informazioni, vedere [Panoramica di Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)e [Microsoft Identity Web Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web).

Azure AD B2C dispone di un endpoint di metadati OpenID Connect, che consente a un'applicazione di ottenere informazioni sui Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Il tenant B2C include un documento di metadati JSON per ogni flusso utente. Ad esempio, il documento di metadati del flusso utente `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` si trova in:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Una delle proprietà del documento di configurazione è `jwks_uri`, il cui valore per lo stesso flusso utente è:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Per determinare quale flusso utente è stato usato per la firma di un token ID e da dove ottenere i metadati, sono disponibili due opzioni. Innanzitutto, il nome del flusso utente è incluso nell'attestazione `acr` del token ID. In secondo luogo, è possibile codificare il flusso utente nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quale flusso utente è stato usato. Entrambi i metodi sono validi.

Dopo aver acquisito il documento di metadati dall'endpoint di metadati OpenID Connect, è possibile usare le chiavi pubbliche RSA 256 per convalidare la firma del token ID. È possibile che siano presenti più chiavi elencate in questo endpoint, ognuna identificata da un' `kid` attestazione. L'intestazione del token ID contiene anche un'attestazione `kid`, che indica quali di queste chiavi sono state usate per firmare il token ID.

Per verificare i token da Azure AD B2C, è necessario generare la chiave pubblica usando l'esponente (e) e il modulo (n). È necessario determinare come eseguire questa operazione nel rispettivo linguaggio di programmazione. La documentazione ufficiale sulla generazione di chiavi pubbliche con il protocollo RSA è disponibile qui: https://tools.ietf.org/html/rfc3447#section-3.1

Dopo avere convalidato la firma del token ID, è necessario verificare diverse attestazioni. Ad esempio:

- Convalidare l'attestazione `nonce` per impedire attacchi di riproduzione dei token. Il valore deve corrispondere a quello specificato nella richiesta di accesso.
- Convalidare l' `aud` attestazione per verificare che il token ID sia stato emesso per l'applicazione. Il valore deve corrispondere all'ID applicazione dell'applicazione.
- Convalidare le `iat` `exp` attestazioni e per assicurarsi che il token ID non sia scaduto.

Esistono numerose altre convalide che è consigliabile eseguire. Le convalide sono descritte in dettaglio nella [specifica di OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Potrebbe anche essere necessario convalidare attestazioni aggiuntive, a seconda dello scenario. Alcune convalide comuni includono:

- Verificare che l'utente o l'organizzazione abbia eseguito l'iscrizione all'applicazione.
- Verificare che l'utente abbia le autorizzazioni o i privilegi adeguati.
- Verificare che si sia verificato un certo livello di autenticazione, ad esempio Azure AD Multi-Factor Authentication.

Dopo la convalida del token ID, è possibile avviare una sessione con l'utente. È possibile utilizzare le attestazioni nel token ID per ottenere informazioni sull'utente nell'applicazione. Gli usi di queste informazioni includono la visualizzazione, i record e l'autorizzazione.

## <a name="get-a-token"></a>Acquisizione di un token

Se è necessario che l'applicazione Web esegua solo i flussi utente, è possibile ignorare le prossime sezioni. Queste sezioni sono applicabili solo alle applicazioni Web che devono eseguire chiamate autenticate a un'API Web e sono anche protette da Azure AD B2C.

È possibile riscattare il codice di autorizzazione acquisito, tramite `response_type=code+id_token`, per un token nella risorsa desiderata inviando una richiesta `POST` all'endpoint `/token`. In Azure AD B2C, è possibile [richiedere i token di accesso per altre API](access-tokens.md#request-a-token) come di consueto specificando gli ambiti nella richiesta.

È anche possibile richiedere un token di accesso per l'API Web back-end dell'app per convenzione di usare l'ID client dell'app come ambito richiesto (che comporterà un token di accesso con tale ID client come "audience"):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| inquilino | Sì | Nome del tenant di Azure AD B2C |
| politica | Sì | Flusso utente usato per acquisire il codice di autorizzazione. Non è possibile usare un flusso utente diverso in questa richiesta. Aggiungere questo parametro alla stringa di query, non al corpo del POST. |
| client_id | Sì | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione. |
| client_secret | Sì, nelle app Web | Segreto dell'applicazione generato nel [portale di Azure](https://portal.azure.com/). I segreti client vengono usati in questo flusso per gli scenari di app Web, in cui il client può archiviare in modo sicuro un segreto client. Per gli scenari di app native (client pubblico), i segreti client non possono essere archiviati in modo sicuro, quindi non vengono usati in questo flusso. Se si usa un segreto client, modificarlo periodicamente. |
| codice | Sì | Codice di autorizzazione acquisito all'inizio del flusso utente. |
| grant_type | Sì | Tipo di concessione, che deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| redirect_uri | Sì | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| ambito | No | Elenco di ambiti separato da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di parametri id_token. Può essere usato per ottenere i token per l'API Web back-end dell'applicazione, che è rappresentata dallo stesso ID applicazione del client. L' `offline_access` ambito indica che l'applicazione richiede un token di aggiornamento per l'accesso esteso alle risorse. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer` è l'unico tipo supportato. |
| access_token | Il token JWT firmato richiesto. |
| ambito | Ambiti per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| token di aggiornamento | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. `offline_access`Per ricevere un token di aggiornamento, è necessario che l'ambito sia stato usato nelle richieste di autorizzazione e di token. |

Le risposte di errore si presentano nel modo seguente:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="use-the-token"></a>Uso del token

Dopo avere acquisito un token di accesso, è possibile usarlo nelle richieste alle API Web back-end includendolo nell'intestazione `Authorization`:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aggiornamento del token

I token ID scadono entro un breve periodo di tempo. Aggiornare i token dopo la scadenza per continuare ad accedere alle risorse. È possibile aggiornare un token inviando un'altra `POST` richiesta all' `/token` endpoint. Specificare questa volta il parametro `refresh_token` al posto del parametro `code`:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| inquilino | Sì | Nome del tenant di Azure AD B2C |
| politica | Sì | Flusso utente usato per acquisire il token di aggiornamento originale. Non è possibile usare un flusso utente diverso in questa richiesta. Aggiungere questo parametro alla stringa di query, non al corpo del POST. |
| client_id | Sì | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione. |
| client_secret | Sì, nelle app Web | Segreto dell'applicazione generato nel [portale di Azure](https://portal.azure.com/). I segreti client vengono usati in questo flusso per gli scenari di app Web, in cui il client può archiviare in modo sicuro un segreto client. Per gli scenari di app native (client pubblico), i segreti client non possono essere archiviati in modo sicuro, quindi non vengono usati in questa chiamata. Se si usa un segreto client, modificarlo periodicamente. |
| grant_type | Sì | Tipo di concessione, che deve essere `refresh_token` per questa parte del flusso del codice di autorizzazione. |
| token di aggiornamento | Sì | Token di aggiornamento originale acquisito nella seconda parte del flusso. `offline_access`Per ricevere un token di aggiornamento, è necessario usare l'ambito nelle richieste di autorizzazione e di token. |
| redirect_uri | No | Parametro `redirect_uri` dell'applicazione dove è stato ricevuto il codice di autorizzazione. |
| ambito | No | Elenco di ambiti separato da spazi. L'ambito `openid` indica un'autorizzazione per l'accesso dell'utente e per ottenere i dati relativi all'utente sotto forma di token ID. Può essere usato per inviare token all'API Web back-end dell'applicazione, che è rappresentata dallo stesso ID applicazione del client. L' `offline_access` ambito indica che l'applicazione richiede un token di aggiornamento per l'accesso esteso alle risorse. |

Una risposta di token con esito positivo ha un aspetto simile al seguente:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| not_before | Il momento in cui il token viene considerato valido, nel periodo. |
| token_type | Valore del tipo di token. `Bearer` è l'unico tipo supportato. |
| access_token | Token JWT firmato che è stato richiesto. |
| ambito | Ambito per il quale il token è valido. |
| expires_in | Periodo di validità del token di accesso (in secondi). |
| token di aggiornamento | Token di aggiornamento di OAuth 2.0. L'applicazione può usare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. I token di aggiornamento possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |

Le risposte di errore si presentano nel modo seguente:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| --------- | ----------- |
| error | Codice che può essere usato per classificare i tipi di errori che si verificano. |
| error_description | Messaggio che consente di identificare la causa principale di un errore di autenticazione. |

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Quando si desidera disconnettersi l'utente dall'applicazione, non è sufficiente cancellare i cookie dell'applicazione o terminare la sessione con l'utente. Reindirizza l'utente a Azure AD B2C per la disconnessione. Se l'operazione non riesce, l'utente potrebbe essere in grado di eseguire di nuovo l'autenticazione all'applicazione senza immettere di nuovo le credenziali. Per ulteriori informazioni, vedere [Azure ad B2C sessione](session-behavior.md).

Per disconnettere l'utente, reindirizzare l'utente all' `end_session` endpoint elencato nel documento di metadati di OpenID Connect descritto in precedenza:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametro | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| inquilino | Sì | Nome del tenant di Azure AD B2C |
| politica | Sì | Flusso utente da usare per disconnettere l'utente dall'applicazione. |
| id_token_hint| No | Token ID emesso in precedenza da passare all'endpoint di disconnessione come hint per la sessione autenticata corrente dell'utente finale con il client. `id_token_hint`Garantisce che `post_logout_redirect_uri` sia un URL di risposta registrato nelle impostazioni dell'applicazione Azure ad B2C. Per altre informazioni, vedere [proteggere il reindirizzamento di disconnessione](#secure-your-logout-redirect). |
| client_id | No* | ID applicazione assegnato dall' [portale di Azure](https://portal.azure.com/) all'applicazione.<br><br>\**Questa operazione è necessaria quando `Application` si usa la configurazione SSO di isolamento e il _token ID richiesto_ nella richiesta di disconnessione è impostato su `No` .* |
| post_logout_redirect_uri | No | URL a cui l'utente deve essere reindirizzato dopo la disconnessione. Se non è incluso, Azure AD B2C Visualizza un messaggio generico da parte dell'utente. A meno che non si fornisca un `id_token_hint` , è consigliabile non registrare questo URL come URL di risposta nelle impostazioni dell'applicazione Azure ad B2C. |
| state | No | Se nella richiesta è incluso un parametro `state`, lo stesso valore deve essere visualizzato nella risposta. L'applicazione deve verificare che i `state` valori nella richiesta e nella risposta siano identici. |

### <a name="secure-your-logout-redirect"></a>Proteggere il reindirizzamento di disconnessione

Dopo la disconnessione, l'utente viene reindirizzato all'URI specificato nel `post_logout_redirect_uri` parametro, indipendentemente dagli URL di risposta specificati per l'applicazione. Tuttavia, se viene passato un oggetto valido `id_token_hint` e il **token ID richiesto nelle richieste di disconnessione** viene attivato, Azure ad B2C verifica che il valore `post_logout_redirect_uri` corrisponda a uno degli URI di reindirizzamento configurati dell'applicazione prima di eseguire il reindirizzamento. Se per l'applicazione non è stato configurato alcun URL di risposta corrispondente, viene visualizzato un messaggio di errore e l'utente non viene reindirizzato.

Per impostare il token ID necessario nelle richieste di disconnessione, vedere [configurare il comportamento della sessione in Azure Active Directory B2C](session-behavior.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sessione Azure AD B2C](session-behavior.md).
