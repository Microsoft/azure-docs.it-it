---
title: Uso avanzato di AuthN/AuthZ
description: Informazioni su come personalizzare la funzionalità di autenticazione e autorizzazione nel servizio app per diversi scenari e ottenere attestazioni utente e token diversi.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 50587feff29e1c02a639d63d0c99156dcec4f68e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180871"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avanzato dell'autenticazione e dell'autorizzazione in Servizio app di Azure

Questo articolo illustra come personalizzare i processi predefiniti di [autenticazione e autorizzazione nel servizio app](overview-authentication-authorization.md) e come gestire l'identità dall'applicazione. 

Se si vuole iniziare subito, vedere una delle esercitazioni seguenti:

* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel Servizio app di Azure](tutorial-auth-aad.md)
* [Come configurare un'applicazione per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md)
* [Come configurare un'applicazione per usare l'account di accesso di Facebook](configure-authentication-provider-facebook.md)
* [Come configurare un'applicazione per usare l'account di accesso di Google](configure-authentication-provider-google.md)
* [Come configurare un'applicazione per usare l'account di accesso Microsoft](configure-authentication-provider-microsoft.md)
* [Come configurare un'applicazione per usare l'account di accesso di Twitter](configure-authentication-provider-twitter.md)
* [Come configurare l'app per l'accesso tramite un provider OpenID Connect (anteprima)](configure-authentication-provider-openid-connect.md)
* [Come configurare l'app per l'accesso usando un accesso con Apple (anteprima)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Usare più provider di accesso

La configurazione del portale non offre una soluzione pronta all'uso per presentare agli utenti più provider di accesso, ad esempio tramite Facebook e Twitter. È tuttavia possibile aggiungere facilmente questa funzionalità all'app. Seguire questa procedura:

Prima di tutto, nella pagina **Autenticazione/Autorizzazione** nel portale di Azure configurare ogni provider di identità che si vuole abilitare.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Consenti richieste anonime (nessuna azione)**.

Nella pagina di accesso, sulla barra di spostamento o in qualsiasi altra posizione nell'app aggiungere un collegamento per l'accesso a ognuno dei provider abilitati (`/.auth/login/<provider>`). Ad esempio:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Quando l'utente fa clic su uno dei collegamenti, viene aperta la pagina di accesso corrispondente per l'accesso dell'utente.

Per reindirizzare l'utente dopo l'accesso a un URL personalizzato, usare il parametro della stringa di query `post_login_redirect_url`, da non confondere con l'URI di reindirizzamento nella configurazione del provider di identità. Ad esempio, per passare l'utente a `/Home/Index` dopo l'accesso, usare il seguente codice HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Convalidare i token dai provider

In un accesso client l'applicazione esegue l'accesso manuale dell'utente al provider e poi invia il token di autenticazione al servizio app per la convalida (vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow)). La convalida non garantisce effettivamente l'accesso alle risorse dell'app desiderate, ma il completamento della convalida fornisce un token di sessione da usare per accedere alle risorse dell'app. 

Per convalidare il token del provider, l'app del servizio app deve essere innanzitutto configurata con il provider desiderato. In fase di esecuzione, dopo aver recuperato il token di autenticazione dal provider, inviare il token a `/.auth/login/<provider>` per la convalida. Ad esempio: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Il formato del token varia leggermente in base al provider. Per altri dettagli, vedi la tabella seguente:

| Valore provider | Obbligatorio nel corpo della richiesta | Commenti |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | La proprietà `expires_in` è facoltativa. <br/>Quando si richiede il token da servizi Live, richiedere sempre l'ambito `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | La proprietà `authorization_code` è facoltativa. Se specificata, può essere facoltativamente accompagnata dalla proprietà `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Usare un [token di accesso valido dell'utente](https://developers.facebook.com/docs/facebook-login/access-tokens) da Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se il token del provider viene convalidato, l'API restituisce un token `authenticationToken` nel corpo della risposta, che corrisponde al token di sessione. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Dopo aver ottenuto il token di sessione, è possibile accedere alle risorse dell'app protette aggiungendo l'intestazione `X-ZUMO-AUTH` alle richieste HTTP. Ad esempio: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Disconnettersi da una sessione

Gli utenti possono avviare una disconnessione inviando una richiesta `GET` all'endpoint `/.auth/logout` dell'app. La richiesta `GET` esegue le operazioni seguenti:

- Cancella i cookie di autenticazione dalla sessione corrente.
- Elimina i token dell'utente corrente dall'archivio di token.
- Per Azure Active Directory e Google esegue una disconnessione lato server nel provider di identità.

Ecco un semplice collegamento di disconnessione in una pagina Web:

```html
<a href="/.auth/logout">Sign out</a>
```

Per impostazione predefinita, una corretta disconnessione reindirizza il client all'URL `/.auth/logout/done`. È possibile cambiare la pagina di reindirizzamento post-connessione aggiungendo il parametro di query `post_logout_redirect_uri`. Ad esempio:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

È consigliabile [codificare](https://wikipedia.org/wiki/Percent-encoding) il valore di `post_logout_redirect_uri`.

Quando si usano URL completamente qualificati, l'URL deve essere ospitato nello stesso dominio o configurato come URL di reindirizzamento esterno consentito per l'app. Nell'esempio seguente per eseguire il reindirizzamento a `https://myexternalurl.com` che non è ospitato nello stesso dominio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Eseguire il comando seguente nella [Azure cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Mantenere i frammenti di URL

Dopo che gli utenti hanno eseguito l'accesso all'app, di solito vogliono essere reindirizzati alla stessa sezione della stessa pagina, ad esempio `/wiki/Main_Page#SectionZ`. Tuttavia, poiché i [frammenti di URL](https://wikipedia.org/wiki/Fragment_identifier), ad esempio `#SectionZ`, non vengono mai inviati al server, non sono mantenuti per impostazione predefinita dopo che l'accesso OAuth si completa e ritorna all'app. Gli utenti otterranno un'esperienza ottimale quando dovranno tornare di nuovo al segnalibro. Questa limitazione si applica a tutte le soluzioni di autenticazione lato server.

Nell'autenticazione del servizio app è possibile mantenere i frammenti di URL durante l'accesso OAuth. Per farlo è necessario configurare l'impostazione app `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` su `true`. Si procede dal [portale di Azure](https://portal.azure.com) o semplicemente eseguendo il comando seguente in [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Accedere alle attestazioni utente

Il servizio app passa le attestazioni utente all'applicazione usando intestazioni speciali. Alle richieste esterne non è consentito impostare queste intestazioni, che sono quindi presenti solo se impostate dal servizio app. Ecco alcune intestazioni di esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie da queste intestazioni. Per le app ASP.NET 4.6, i valori appropriati per **ClaimsPrincipal** vengono impostati automaticamente. ASP.NET Core, tuttavia, non fornisce un middleware di autenticazione che si integra con le attestazioni utente del servizio app. Per una soluzione alternativa, vedere [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Se l' [Archivio di token](overview-authentication-authorization.md#token-store) è abilitato per l'app, è anche possibile ottenere ulteriori dettagli sull'utente autenticato chiamando `/.auth/me` . Gli SDK server delle app per dispositivi mobili forniscono metodi di supporto per l'uso di questi dati. Per altre informazioni, vedere [Come usare Node.js SDK per le app per dispositivi mobili di Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) e [Usare l'SDK del server back-end .NET per le app per dispositivi mobili di Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperare i token nel codice dell'app

Dal codice server, i token specifici del provider vengono inseriti nell'intestazione della richiesta, in modo che sia possibile accedervi facilmente. La tabella seguente mostra i nomi di intestazioni token possibili:

| Provider | Nomi dell'intestazione |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token di Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Account Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Dal codice client (ad esempio un'app per dispositivi mobili o JavaScript nel browser), inviare una `GET` richiesta HTTP a `/.auth/me` (l'archivio di[token](overview-authentication-authorization.md#token-store) deve essere abilitato). Il codice JSON restituito include i token specifici del provider.

> [!NOTE]
> I token di accesso consentono l'accesso alle risorse del provider, quindi sono presenti solo se si configura un provider con un segreto client. Per informazioni su come ottenere i token di aggiornamento, vedere Aggiornare i token di accesso.

## <a name="refresh-identity-provider-tokens"></a>Aggiornare i token del provider di identità

Quando il token di accesso del provider (non il [token di sessione](#extend-session-token-expiration-grace-period)) scade, è necessario autenticare nuovamente l'utente prima di riutilizzare tale token. È possibile evitare la scadenza del token eseguendo una chiamata di `GET` nell'endpoint `/.auth/refresh` dell'applicazione. Quando viene chiamato, il servizio app Aggiorna automaticamente i token di accesso nell' [Archivio di token](overview-authentication-authorization.md#token-store) per l'utente autenticato. Le successive richieste di token dal codice dell'app ottengono i token aggiornati. Affinché l'aggiornamento dei token funzioni, tuttavia, l'archivio di token deve contenere i [token di aggiornamento](https://auth0.com/learn/refresh-tokens/) per il provider. Il modo per ottenere i token di aggiornamento dipende dal provider, ma di seguito viene riportato un breve riepilogo:

- **Google**: aggiungere un parametro di stringa di query `access_type=offline` alla chiamata API di `/.auth/login/google`. Se si usa Mobile Apps SDK, è possibile aggiungere il parametro a uno degli overload `LogicAsync`. Vedere [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Token di aggiornamento di Google).
- **Facebook**: non vengono forniti token di aggiornamento. I token di lunga durata scadono dopo 60 giorni. Vedere [Scadenza ed estensione dei token d'accesso di Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension).
- **Twitter**: i token di accesso non scadono. Vedere le [domande frequenti su OAuth di Twitter](https://developer.twitter.com/en/docs/authentication/faq).
- **Account Microsoft**: in fase di [configurazione delle impostazioni di autenticazione dell'account Microsoft](configure-authentication-provider-microsoft.md), selezionare l'ambito `wl.offline_access`.
- **Azure Active Directory**: in [https://resources.azure.com](https://resources.azure.com) seguire questa procedura:
    1. Nella parte superiore della pagina selezionare **Lettura/scrittura**.
    2. Nel browser a sinistra passare a **sottoscrizioni** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * **provider**>  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Fare clic su **Modifica**.
    4. Modificare la proprietà seguente. Sostituire _\<app\_id>_ con il Azure Active Directory ID applicazione del servizio a cui si vuole accedere.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Fare clic su **Put**. 

Dopo aver configurato il provider è possibile [trovare il token di aggiornamento e l'ora di scadenza del token di accesso](#retrieve-tokens-in-app-code) nell'archivio token. 

Per aggiornare il token di accesso in qualsiasi momento, è sufficiente chiamare `/.auth/refresh` in qualsiasi linguaggio. Il frammento di codice seguente usa jQuery per aggiornare i token di accesso da un client JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se un utente revoca le autorizzazioni concesse all'app, la chiamata a `/.auth/me` può avere esito negativo, con una risposta `403 Forbidden`. Per diagnosticare gli errori, controllare i log dell'applicazione per informazioni dettagliate.

## <a name="extend-session-token-expiration-grace-period"></a>Estendere il periodo di tolleranza della scadenza del token di sessione

La sessione autenticata scade dopo 8 ore. Dopo la scadenza di una sessione autenticata, è previsto per impostazione predefinita un periodo di tolleranza di 72 ore. Durante questo periodo di tolleranza, è possibile aggiornare il token di sessione con il servizio app senza dover ripetere l'autenticazione dell'utente. È sufficiente chiamare `/.auth/refresh` quando il token di sessione non è più valido e non è necessario tenere traccia della scadenza del token. Una volta trascorso il periodo di tolleranza di 72 ore, l'utente deve eseguire di nuovo l'accesso per ottenere un token di sessione valido.

Se 72 ore non sono sufficienti, è possibile estendere questo intervallo di scadenza. L'estensione della scadenza per un lungo periodo potrebbe avere implicazioni significative sulla sicurezza (ad esempio quando un token di autenticazione viene perso o rubato). È quindi consigliabile lasciare l'impostazione predefinita di 72 ore o impostare il periodo di estensione sul valore più piccolo possibile.

Per estendere l'intervallo di scadenza predefinito, eseguire il comando seguente in [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Il periodo di tolleranza si applica solo alla sessione autenticata del servizio app e non ai token dei provider di identità. Non è previsto alcun periodo di tolleranza per i token dei provider scaduti. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitare il dominio degli account di accesso

Sia l'account Microsoft che Azure Active Directory consentono l'accesso da più domini. L'account Microsoft consente, ad esempio, gli account _outlook.com_, _live.com_ e _hotmail.com_. Azure AD consente un numero qualsiasi di domini personalizzati per gli account di accesso. Tuttavia, potrebbe essere necessario accelerare gli utenti direttamente alla pagina di accesso personalizzata Azure AD (ad esempio `contoso.com` ). Per suggerire il nome di dominio degli account di accesso, seguire questa procedura.

In [https://resources.azure.com](https://resources.azure.com) passare a **sottoscrizioni** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * **provider**>  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Fare clic su **Modifica**, modificare la proprietà seguente e quindi fare clic su **Put**. Assicurarsi di sostituire _\<domain\_name>_ con il dominio desiderato.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Questa impostazione Accoda il `domain_hint` parametro della stringa di query all'URL di reindirizzamento dell'account di accesso. 

> [!IMPORTANT]
> Il client può rimuovere il `domain_hint` parametro dopo la ricezione dell'URL di reindirizzamento e quindi eseguire l'accesso con un dominio diverso. Anche se questa funzione è comoda, non è una funzionalità di sicurezza.
>

## <a name="authorize-or-deny-users"></a>Autorizzare o negare gli utenti

Sebbene il servizio app si occupi del caso di autorizzazione più semplice (ovvero rifiuta le richieste non autenticate), è possibile che l'app richieda un comportamento di autorizzazione più granulare, ad esempio limitando l'accesso solo a un gruppo specifico di utenti. In alcuni casi, è necessario scrivere il codice dell'applicazione personalizzata per consentire o negare l'accesso all'utente che ha eseguito l'accesso. In altri casi, il servizio app o il provider di identità potrebbe essere in grado di aiutare senza richiedere modifiche al codice.

- [Livello del server](#server-level-windows-apps-only)
- [Livello del provider di identità](#identity-provider-level)
- [Livello applicazione](#application-level)

### <a name="server-level-windows-apps-only"></a>Livello server (solo app di Windows)

Per qualsiasi app di Windows, è possibile definire il comportamento di autorizzazione del server Web IIS modificando il file di *Web.config* . Le app Linux non usano IIS e non possono essere configurate tramite *Web.config*.

1. Passare a `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. In Esplora browser dei file del servizio app passare a *site/wwwroot*. Se un *Web.config* non esiste, crearlo selezionando **+**  >  **nuovo file**. 

1. Selezionare la matita per *Web.config* per modificarla. Aggiungere il codice di configurazione seguente e fare clic su **Salva**. Se *Web.config* esiste già, è sufficiente aggiungere l' `<authorization>` elemento con tutti gli elementi al suo interno. Aggiungere gli account che si desidera consentire nell' `<allow>` elemento.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Livello del provider di identità

Il provider di identità può fornire una certa autorizzazione per chiavi a chiave. Ad esempio:

- Per [app Azure servizio](configure-authentication-provider-aad.md), è possibile [gestire l'accesso a livello aziendale](../active-directory/manage-apps/what-is-access-management.md) direttamente nella Azure ad. Per istruzioni, vedere [come rimuovere l'accesso di un utente a un'applicazione](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Per [Google](configure-authentication-provider-google.md), i progetti API Google che appartengono a un' [organizzazione](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) possono essere configurati in modo da consentire l'accesso solo agli utenti dell'organizzazione. vedere la [pagina relativa alla configurazione del supporto **OAuth 2,0** di Google](https://support.google.com/cloud/answer/6158849?hl=en).

### <a name="application-level"></a>Livello applicazione

Se uno degli altri livelli non fornisce l'autorizzazione necessaria o se la piattaforma o il provider di identità non è supportato, è necessario scrivere codice personalizzato per autorizzare gli utenti in base alle [attestazioni utente](#access-user-claims).

## <a name="updating-the-configuration-version-preview"></a>Aggiornamento della versione di configurazione (anteprima)

Esistono due versioni dell'API di gestione per la funzionalità di autenticazione/autorizzazione. La versione di anteprima V2 è obbligatoria per l'esperienza di autenticazione (anteprima) nel portale di Azure. Un'app che usa già l'API V1 può eseguire l'aggiornamento alla versione v2 dopo che sono state apportate alcune modifiche. In particolare, la configurazione del segreto deve essere spostata nelle impostazioni dell'applicazione con slot-Sticky. Anche la configurazione del provider di account Microsoft non è attualmente supportata nella versione V2.

> [!WARNING]
> La migrazione all'anteprima v2 consente di disabilitare la gestione della funzionalità di autenticazione/autorizzazione del servizio app per l'applicazione tramite alcuni client, ad esempio l'esperienza esistente nell'portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell. Questa operazione non può essere annullata. Durante l'anteprima, la migrazione dei carichi di lavoro di produzione non è consigliata né supportata. Attenersi alla procedura descritta in questa sezione per le applicazioni di test.

### <a name="moving-secrets-to-application-settings"></a>Trasferimento dei segreti nelle impostazioni dell'applicazione

1. Ottenere la configurazione esistente usando l'API V1:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Nel payload JSON risultante prendere nota del valore del segreto usato per ogni provider configurato:

   * AAD `clientSecret`
   * Google `googleClientSecret`
   * Facebook `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Account Microsoft: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > I valori segreti sono importanti credenziali di sicurezza e devono essere gestiti con cautela. Non condividere questi valori o salvarli in modo permanente in un computer locale.

1. Creare le impostazioni dell'applicazione slot-Sticky per ogni valore di segreto. È possibile scegliere il nome di ogni impostazione dell'applicazione. Il valore deve corrispondere a quanto ottenuto nel passaggio precedente oppure [fare riferimento a un segreto Key Vault](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) creato con tale valore.

   Per creare l'impostazione, è possibile utilizzare il portale di Azure o eseguire una variante dei seguenti per ogni provider:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Le impostazioni dell'applicazione per questa configurazione devono essere contrassegnate come Sticky slot, vale a dire che non si sposteranno tra gli ambienti durante un' [operazione di scambio di slot](./deploy-staging-slots.md). Questo è dovuto al fatto che la configurazione dell'autenticazione è associata all'ambiente. 

1. Creare un nuovo file JSON denominato `authsettings.json` . Prendere in precedenza l'output ricevuto e rimuovere ogni valore di segreto. Scrivere l'output rimanente nel file, assicurandosi che non sia incluso alcun segreto. In alcuni casi, è possibile che la configurazione includa matrici contenenti stringhe vuote. Verificare che non sia presente `microsoftAccountOAuthScopes` e, in caso contrario, passare a tale valore `null` .

1. Aggiungere una proprietà a `authsettings.json` che punti al nome dell'impostazione dell'applicazione creata in precedenza per ogni provider:
 
   * AAD `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * Facebook `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Account Microsoft: `microsoftAccountClientSecretSettingName`

   Un file di esempio dopo questa operazione potrebbe essere simile al seguente, in questo caso solo per AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": null,
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Inviare questo file come nuova configurazione di autenticazione/autorizzazione per l'app:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Verificare che l'app continui a funzionare come previsto dopo questo movimento.

1. Eliminare il file usato nei passaggi precedenti.

A questo punto è stata eseguita la migrazione dell'app per archiviare i segreti del provider di identità come impostazioni dell'applicazione.

### <a name="support-for-microsoft-account-registrations"></a>Supporto per registrazioni account Microsoft

L'API v2 attualmente non supporta l'account Microsoft come provider distinto. Si avvale invece della [piattaforma di identità Microsoft](../active-directory/develop/v2-overview.md) convergente per accedere agli utenti con account Microsoft personali. Quando si passa all'API v2, viene usata la configurazione V1 Azure Active Directory per configurare il provider della piattaforma di identità Microsoft.

Se la configurazione esistente contiene un provider di account Microsoft e non contiene un provider di Azure Active Directory, è possibile passare la configurazione al provider Azure Active Directory e quindi eseguire la migrazione. Per eseguire questa operazione:

1. Passare a [**registrazioni app**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nel portale di Azure e trovare la registrazione associata al provider dell'account Microsoft. Potrebbe trovarsi sotto l'intestazione "applicazioni da account personale".
1. Passare alla pagina "autenticazione" per la registrazione. In "URI di reindirizzamento" dovrebbe essere visualizzata una voce che termina con `/.auth/login/microsoftaccount/callback` . Copiare l'URI.
1. Aggiungere un nuovo URI che corrisponda a quello appena copiato, ad eccezione del fatto che termina `/.auth/login/aad/callback` . Questa operazione consentirà di usare la registrazione per la configurazione dell'autenticazione/autorizzazione del servizio app.
1. Passare alla configurazione di autenticazione/autorizzazione del servizio app per l'app.
1. Raccogliere la configurazione per il provider di account Microsoft.
1. Configurare il provider di Azure Active Directory usando la modalità di gestione avanzata, specificando i valori relativi all'ID client e al segreto client raccolti nel passaggio precedente. Per l'URL dell'autorità emittente usare `<authentication-endpoint>/<tenant-id>/v2.0` e sostituire *\<authentication-endpoint>* con l' [endpoint di autenticazione per l'ambiente cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) , ad esempio " https://login.microsoftonline.com " per Azure globale, SOSTITUendo anche con l' *\<tenant-id>* ID di **Directory (tenant)**.
1. Dopo aver salvato la configurazione, testare il flusso di accesso passando al browser all' `/.auth/login/aad` endpoint nel sito e completare il flusso di accesso.
1. A questo punto, la configurazione è stata copiata correttamente, ma la configurazione esistente del provider di account Microsoft rimane. Prima di rimuoverlo, assicurarsi che tutte le parti dell'app facciano riferimento al provider Azure Active Directory tramite i collegamenti di accesso e così via. Verificare che tutte le parti dell'applicazione funzionino come previsto.
1. Una volta convalidati gli elementi che funzionano con il provider di Azure Active Directory AAD, è possibile rimuovere la configurazione del provider di account Microsoft.

Alcune app potrebbero avere già registrazioni separate per Azure Active Directory e account Microsoft. Non è possibile eseguire la migrazione di tali app in questo momento. 

> [!WARNING]
> È possibile eseguire la convergenza delle due registrazioni modificando i [tipi di account supportati](../active-directory/develop/supported-accounts-validation.md) per la registrazione dell'app AAD. Tuttavia, ciò impone una nuova richiesta di consenso per gli utenti dell'account Microsoft e le attestazioni di identità degli utenti possono essere diverse nella struttura, in `sub` particolare cambiando i valori perché viene usato un nuovo ID app. Questo approccio non è consigliato, a meno che non sia ben compreso. È invece necessario attendere il supporto per le due registrazioni nella superficie dell'API v2.

### <a name="switching-to-v2"></a>Passare alla versione V2

Una volta eseguiti i passaggi precedenti, passare all'app nell'portale di Azure. Selezionare la sezione "autenticazione (anteprima)". 

In alternativa, è possibile effettuare una richiesta PUT sulla `config/authsettingsv2` risorsa nella risorsa del sito. Lo schema per il payload è identico a quello acquisito nella sezione [configurare usando un file](#config-file) .

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Configurare usando un file (anteprima)

Facoltativamente, le impostazioni di autenticazione possono essere configurate tramite un file fornito dalla distribuzione. Questa operazione può essere necessaria per determinate funzionalità di anteprima dell'autenticazione/autorizzazione del servizio app.

> [!IMPORTANT]
> Tenere presente che il payload dell'app, quindi questo file, può spostarsi tra gli ambienti, come per gli [slot](./deploy-staging-slots.md). È probabile che si desideri aggiungere una registrazione di app diversa a ogni slot e in questi casi è necessario continuare a usare il metodo di configurazione standard anziché usare il file di configurazione.

### <a name="enabling-file-based-configuration"></a>Abilitazione della configurazione basata su file

> [!CAUTION]
> Durante l'anteprima, l'abilitazione della configurazione basata su file Disabilita la gestione della funzionalità di autenticazione/autorizzazione del servizio app per l'applicazione tramite alcuni client, ad esempio portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.

1. Creare un nuovo file JSON per la configurazione alla radice del progetto (distribuito in D:\home\site\wwwroot nell'app Web/funzione). Compilare la configurazione desiderata in base al [riferimento alla configurazione basata su file](#configuration-file-reference). Se si modifica una configurazione di Azure Resource Manager esistente, assicurarsi di tradurre le proprietà acquisite nella `authsettings` raccolta nel file di configurazione.

2. Modificare la configurazione esistente, acquisita nelle API [Azure Resource Manager](../azure-resource-manager/management/overview.md) sotto `Microsoft.Web/sites/<siteName>/config/authsettings` . Per modificare questa operazione, è possibile usare un [modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md) o uno strumento come [Azure Resource Explorer](https://resources.azure.com/). All'interno della raccolta authsettings, è necessario impostare tre proprietà (e potrebbero essere rimosse altre):

    1.  Impostato `enabled` su "true"
    2.  Impostato `isAuthFromFile` su "true"
    3.  Impostare sul `authFilePath` nome del file, ad esempio "auth.json".

> [!NOTE]
> Il formato di varia a seconda delle `authFilePath` piattaforme. In Windows sono supportati sia i percorsi relativi che assoluti. È consigliabile usare relativa. Per Linux, attualmente sono supportati solo i percorsi assoluti, quindi il valore dell'impostazione deve essere "/Home/site/wwwroot/auth.json" o simile.

Dopo aver eseguito l'aggiornamento della configurazione, il contenuto del file verrà usato per definire il comportamento dell'autenticazione/autorizzazione del servizio app per quel sito. Se si desidera tornare alla configurazione di Azure Resource Manager, è possibile eseguire questa operazione impostando `isAuthFromFile` di nuovo su "false".

### <a name="configuration-file-reference"></a>Riferimento al file di configurazione

Tutti i segreti a cui viene fatto riferimento dal file di configurazione devono essere archiviati come [impostazioni dell'applicazione](./configure-common.md#configure-app-settings). È possibile assegnare qualsiasi nome alle impostazioni. È sufficiente assicurarsi che i riferimenti del file di configurazione utilizzino le stesse chiavi.

Di seguito sono riportate le possibili opzioni di configurazione all'interno del file:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Aggiungere l'app a una versione specifica del runtime di autenticazione

Quando si Abilita l'autenticazione/autorizzazione, il middleware della piattaforma viene inserito nella pipeline di richieste HTTP, come descritto nella [Panoramica delle funzionalità](overview-authentication-authorization.md#how-it-works). Questo middleware della piattaforma viene aggiornato periodicamente con nuove funzionalità e miglioramenti nell'ambito degli aggiornamenti della piattaforma di routine. Per impostazione predefinita, l'app Web o per le funzioni verrà eseguita nella versione più recente del middleware della piattaforma. Questi aggiornamenti automatici sono sempre compatibili con le versioni precedenti. Tuttavia, nel caso raro in cui questo aggiornamento automatico introduce un problema di runtime per l'app Web o per le funzioni, è possibile eseguire temporaneamente il rollback alla versione precedente del middleware. Questo articolo illustra come aggiungere temporaneamente un'app a una versione specifica del middleware di autenticazione.

### <a name="automatic-and-manual-version-updates"></a>Aggiornamenti di versione automatici e manuali 

È possibile aggiungere l'app a una versione specifica del middleware della piattaforma impostando un' `runtimeVersion` impostazione per l'app. L'app viene sempre eseguita nella versione più recente a meno che non si scelga di aggiungerla esplicitamente a una versione specifica. Saranno supportate alcune versioni alla volta. Se si aggiunge una versione non valida che non è più supportata, l'app userà invece la versione più recente. Per eseguire sempre la versione più recente, impostare `runtimeVersion` su ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Visualizzare e aggiornare la versione corrente del runtime

È possibile modificare la versione del runtime usata dall'app. La nuova versione di runtime dovrebbe essere applicata dopo il riavvio dell'app. 

#### <a name="view-the-current-runtime-version"></a>Visualizzare la versione corrente del runtime

È possibile visualizzare la versione corrente del middleware di autenticazione della piattaforma usando l'interfaccia della riga di comando di Azure o tramite uno degli endpoint HTTP della versione incorporata nell'app.

##### <a name="from-the-azure-cli"></a>Dall'interfaccia della riga di comando di Azure

Usando l'interfaccia della riga di comando di Azure, visualizzare la versione corrente del middleware con il comando [AZ webapp auth Show](/cli/azure/webapp/auth#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

In questo codice sostituire `<my_app_name>` con il nome dell'app. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app.

Il campo viene visualizzato `runtimeVersion` nell'output dell'interfaccia della riga di comando. Sarà simile all'output di esempio seguente, che è stato troncato per maggiore chiarezza: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Dall'endpoint della versione

È anche possibile raggiungere l'endpoint/.auth/Version in un'app anche per visualizzare la versione corrente del middleware su cui è in esecuzione l'app. Sarà simile all'output di esempio seguente:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aggiornare la versione corrente del runtime

Usando l'interfaccia della riga di comando di Azure, è possibile aggiornare l' `runtimeVersion` impostazione dell'app con il comando [AZ webapp auth Update](/cli/azure/webapp/auth#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Sostituire `<my_app_name>` con il nome dell'app. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app. Sostituire anche `<version>` con una versione valida del runtime 1. x o `~1` per la versione più recente. È possibile trovare le note sulla versione nelle diverse versioni di runtime [qui] ( https://github.com/Azure/app-service-announcements) per determinare la versione a cui aggiungere.

È possibile eseguire questo comando da [Azure Cloud Shell](../cloud-shell/overview.md) scegliendo **Prova** nell'esempio di codice precedente. È anche possibile usare l'[interfaccia della riga di comando di Azure in locale](/cli/azure/install-azure-cli) per eseguire questo comando dopo l'esecuzione del comando [az login](/cli/azure/reference-index#az-login) per eseguire l'accesso.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Autenticare e autorizzare gli utenti end-to-end](tutorial-auth-aad.md)
