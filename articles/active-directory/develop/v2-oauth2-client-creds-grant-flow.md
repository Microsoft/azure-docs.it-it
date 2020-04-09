---
title: Flusso delle credenziali del client OAuth 2.0 sulla piattaforma di identità Microsoft Azure
description: Compilare applicazioni Web utilizzando l'implementazione della piattaforma di identità Microsoft del protocollo di autenticazione OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a03f8cb412b6d6ae95165331ae836bdfde5d670d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886297"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Piattaforma di identità Microsoft e flusso delle credenziali client OAuth 2.0

La [concessione di credenziali client OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) specificata in RFC 6749, anche detta *OAuth a due vie*, può essere usata per accedere alle risorse ospitate sul Web tramite l'identità di un'applicazione. Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Questo tipo di applicazioni vengono spesso definite *daemon* o *account di servizio*.

In questo articolo viene descritto come programmare direttamente in base al protocollo nell'applicazione. Quando possibile, è consigliabile utilizzare le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire token e chiamare API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dai anche un'occhiata alle [app di esempio che utilizzano MSAL](sample-v2-code.md).

Il flusso di concessione delle credenziali client OAuth 2.0 consente a un servizio Web (client riservato) di usare le proprie credenziali, invece di rappresentare un utente, per l'autenticazione durante la chiamata a un altro servizio Web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Microsoft Identity Platform consente anche al servizio chiamante di usare un certificato (invece di un segreto condiviso) come credenziale.

> [!NOTE]
> L'endpoint della piattaforma di identità Microsoft non supporta tutti gli scenari e le funzionalità di Azure AD. Per determinare se è necessario utilizzare l'endpoint della piattaforma di identità Microsoft, leggere le informazioni sulle [limitazioni della piattaforma](active-directory-v2-limitations.md)di identità Microsoft .

Nel più comune *OAuth a tre vie*, a un'applicazione client viene concessa l'autorizzazione per accedere a una risorsa per conto di un determinato utente. L'autorizzazione è delegata dall'utente all'applicazione, in genere, durante il processo di [concessione](v2-permissions-and-consent.md). Tuttavia, nel flusso di credenziali client (*OAuth a due vie*), le autorizzazioni vengono concesse direttamente all'applicazione stessa. Quando l'app presenta un token a una risorsa, quest'ultima obbliga l'app stessa, e non l'utente, ad avere l'autorizzazione per eseguire un'azione.

## <a name="protocol-diagram"></a>Diagramma di protocollo

L'intero flusso di credenziali client ha un aspetto simile a quello illustrato nel diagramma seguente. Tutti i passaggi vengono descritti in seguito nell'articolo.

![Diagramma che mostra il flusso delle credenziali client](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Ottenere l'autorizzazione diretta

Un'app riceve generalmente l'autorizzazione diretta per accedere a una risorsa in uno dei due modi:

* [Tramite un elenco di controllo di accesso (ACL) per la risorsa](#access-control-lists)
* [Tramite l'assegnazione di autorizzazioni dell'applicazione in Azure AD](#application-permissions)

Questi due metodi sono i più comuni in Azure AD e sono consigliati per i client e le risorse che eseguono il flusso di credenziali client. Una risorsa può anche scegliere di autorizzare i client in altri modi. Ciascun server di risorse può scegliere il metodo più adatto all'applicazione.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso

Un provider di risorse potrebbe imporre un controllo delle autorizzazioni in base a un elenco di ID applicazione (client) che riconosce e a cui concede uno specifico livello di accesso. Quando la risorsa riceve un token dall'endpoint della piattaforma di identità Microsoft, può `appid` decodificare il token ed estrarre l'ID applicazione del client dalle attestazioni e `iss` . A quel punto, la risorsa verifica la presenza dell'applicazione nell'elenco di controllo di accesso (ACL) esistente. La granularità e il metodo relativi all'elenco possono variare in maniera sostanziale da risorsa a risorsa.

Un caso d'uso comune prevede l'uso di un ACL per eseguire test per un'applicazione Web o per un'API Web. L'API Web potrebbe fornire solo un sottoinsieme delle autorizzazioni complete a un client specifico. Per eseguire test end-to-end sull'API, creare un client di test che acquisisca i token dall'endpoint della piattaforma di identità Microsoft e li invii all'API. L'API controlla quindi nell'ACL se è presente l'ID dell'applicazione del client di test per concedere accesso completo alle funzionalità dell'API. Se si usa questo tipo di ACL, assicurarsi di convalidare non solo il valore `appid` del chiamante, ma verificare anche che il valore `iss` del token sia attendibile.

Questo tipo di autorizzazione è comune per gli account daemon e di servizio che devono accedere ai dati di proprietà di utenti che dispongono di account Microsoft personale. Per i dati appartenenti a organizzazioni, è consigliabile acquisire l'autorizzazione necessaria tramite le autorizzazioni dell'applicazione.

### <a name="application-permissions"></a>Autorizzazioni dell'applicazione

Anziché utilizzare gli ACL, è possibile utilizzare le API per esporre un set di **autorizzazioni dell'applicazione.** Un'autorizzazione dell'applicazione viene concessa a un'applicazione da un amministratore dell'organizzazione e può essere usata solo per accedere ai dati di proprietà dell'organizzazione e dei propri dipendenti. Ad esempio, Microsoft Graph espone diverse autorizzazioni dell'applicazione per le operazioni seguenti:

* Lettura di e-mail in tutte le caselle e-mail
* Lettura e scrittura di e-mail in tutte le caselle e-mail
* Invio di e-mail come utente
* Leggi i dati della directory

Per ulteriori informazioni sulle autorizzazioni per le applicazioni, vedere [Microsoft Graph](https://developer.microsoft.com/graph).

Per usare le autorizzazioni per le applicazioni nell'app, seguire i passaggi illustrati nelle sezioni successive.


> [!NOTE]
> Quando si esegue l'autenticazione come applicazione, anziché con un utente, non è possibile utilizzare "autorizzazioni delegate" (ambiti concessi da un utente).  È necessario utilizzare "autorizzazioni dell'applicazione", note anche come "ruoli", concesse da un amministratore per l'applicazione (o tramite pre-autorizzazione da parte dell'API Web).    


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app

1. Registra e crea un'app tramite la nuova esperienza di [registrazione dell'app (anteprima).](quickstart-register-app.md)
2. Passare all'applicazione nell'esperienza di registrazione dell'app (anteprima). Passare alla sezione **Certificati & segreti** e aggiungere un nuovo **segreto client,** poiché è necessario almeno un segreto client per richiedere un token.
3. Individuare la sezione **Autorizzazioni API** e quindi aggiungere le **autorizzazioni dell'applicazione** richieste dall'app.
4. **Salvare** la registrazione dell'app.

#### <a name="recommended-sign-the-user-into-your-app"></a>Consigliato: accedi all'utente nella tua app

In genere, durante la compilazione di un'applicazione che usa le autorizzazioni, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di accesso all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

Se si connette l'utente all'app, è possibile identificare l'organizzazione a cui appartiene l'utente prima di chiedere all'utente di approvare le autorizzazioni dell'applicazione. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti. Per accedere all'utente, seguire le esercitazioni sul protocollo della piattaforma di [identità Microsoft](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti a richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di *consenso dell'amministratore*della piattaforma di identità Microsoft.

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Usa il tuo ID app per ottenere risultati ottimali: l'applicazione tutorial non richiederà autorizzazioni utili.) [Prova a eseguire questa richiesta in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito nel formato di nome descrittivo o GUID. Se non si conosce il tenant di appartenenza dell'utente e si desidera consentire l'accesso con qualsiasi tentant, usare `common`. |
| `client_id` | Obbligatoria | ID **applicazione (client)** assegnato all'app dal [portale di Azure.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `redirect_uri` | Obbligatoria | URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL e disporre di segmenti di percorso aggiuntivi. |
| `state` | Consigliato | Valore incluso nella richiesta che viene restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |

A questo punto, Azure AD impone che solo un amministratore tenant possa accedere per completare la richiesta. L'amministratore dovrà approvare tutte le autorizzazioni dirette dell'applicazione richieste per l'app nel portale di registrazione.

##### <a name="successful-response"></a>Risposta con esito positivo

Se l'amministratore approva le autorizzazioni per l'applicazione, la risposta con esito positivo si presenta come segue:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- |
| `tenant` | Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| `state` | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `admin_consent` | Impostare su **True**. |

##### <a name="error-response"></a>Risposta di errore

Se l'amministratore non approva le autorizzazioni per l'applicazione, la risposta di errore avrà l'aspetto seguente:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di provisioning dell'app, l'applicazione ha ottenuto le autorizzazioni dirette dell'applicazione richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="get-a-token"></a>Acquisizione di un token

Dopo aver acquisito l'autorizzazione necessaria per l'applicazione, è possibile procedere con l'acquisizione dei token di accesso per le API. Per ottenere un token utilizzando la concessione delle credenziali `/token` client, inviare una richiesta POST all'endpoint della piattaforma di identità Microsoft:To get a token by using the client credentials grant, send a POST request to the Microsoft identity platform endpoint:

> [!TIP]
> Provare a eseguire la richiesta in Postman. (Usa il tuo ID app per ottenere risultati ottimali: l'applicazione tutorial non richiederà autorizzazioni utili.) [Prova a eseguire questa richiesta in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatoria | ID applicazione assegnato all'app. È possibile trovare queste informazioni nel portale in cui è stata registrata l'app. |
| `scope` | Obbligatoria | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. <br/>Questo valore indica all'endpoint della piattaforma di identità Microsoft che di tutte le autorizzazioni dirette dell'applicazione configurate per l'app, l'endpoint deve emettere un token per quelle associate alla risorsa che si vuole usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obbligatoria | Segreto client generato per l'app nel portale di registrazione dell'app. Prima di essere inviato, il segreto client deve essere codificato come URL. |
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametro | Condizione | Descrizione |
| --- | --- | --- |
| `tenant` | Obbligatoria | Tenant di directory su cui l'applicazione intende agire, in formato di GUID o nome di dominio. |
| `client_id` | Obbligatoria |ID applicazione (client) assegnato all'app. |
| `scope` | Obbligatoria | Il valore passato per il parametro `scope` nella richiesta deve essere l'identificatore della risorsa (URI ID applicazione) della risorsa desiderata, con l'aggiunta del suffisso `.default`. Per l'esempio di Microsoft Graph, il valore deve essere `https://graph.microsoft.com/.default`. <br/>Questo valore informa l'endpoint della piattaforma di identità Microsoft che di tutte le autorizzazioni dirette dell'applicazione configurate per l'app deve emettere un token per quelle associate alla risorsa che si vuole usare. Per altre informazioni sull'ambito `/.default`, vedere la [documentazione relativa al consenso](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obbligatoria | Il valore deve essere impostato su `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Leggere l'articolo relativo alle [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sulla registrazione del certificato e il formato dell'asserzione.|
| `grant_type` | Obbligatoria | Il valore deve essere impostato su `client_credentials`. |

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro client_secret che viene sostituito da due parametri: client_assertion_type e client_assertion.

### <a name="successful-response"></a>Risposta con esito positivo

Una risposta con esito positivo ha un aspetto simile al seguente:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametro | Descrizione |
| --- | --- |
| `access_token` | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| `token_type` | Indica il valore del tipo di token. L'unico tipo supportato dalla `bearer`piattaforma di identità Microsoft è . |
| `expires_in` | Periodo di validità di un token di accesso (in secondi). |

### <a name="error-response"></a>Risposta di errore

La risposta di errore è simile alla seguente:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| `error_description` | Messaggio di errore specifico che consente di identificare la causa principale di un errore di autenticazione. |
| `error_codes` | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| `timestamp` | Data/ora in cui si è verificato l'errore. |
| `trace_id` | Identificatore univoco per la richiesta utile per la diagnostica. |
| `correlation_id` | Identificatore univoco per la richiesta utile per la diagnostica dei componenti. |

## <a name="use-a-token"></a>Usare di un token

Ora che è stato acquisito un token, è possibile usarlo per inoltrare richieste alla risorsa. Alla scadenza del token, ripetere la richiesta all'endpoint `/token` per ottwnere un token di accesso aggiornato.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Esempi di codice e altra documentazione

Leggere la [documentazione generale sulle credenziali client](https://aka.ms/msal-net-client-credentials) da Microsoft Authentication Library

| Esempio | Piattaforma |Descrizione |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console .NET Core 2.1 | Un'applicazione .NET Core semplice che visualizza gli utenti di un tenant eseguendo una query in Microsoft Graph con la propria identità, anziché per conto di un utente. L'esempio illustra anche la variante in cui vengono usati certificati per l'autenticazione. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Un'applicazione Web che sincronizza i dati da Microsoft Graph usando la propria identità, anziché per conto di un utente. |
