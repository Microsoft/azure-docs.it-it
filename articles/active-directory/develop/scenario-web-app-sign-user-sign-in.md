---
title: Scrivere un'app Web per l'accesso o l'uscita degli utenti | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app Web per l'accesso degli utenti
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 10ddee404de21c5bc04672fdb6dd32c30f481ba3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578244"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>App Web per l'accesso degli utenti: accesso e disconnessione

Informazioni su come aggiungere l'accesso al codice per l'app Web che accede agli utenti. Quindi, informazioni su come lasciarli disconnettersi.

## <a name="sign-in"></a>Accesso

L'accesso è costituito da due parti:

- Pulsante di accesso nella pagina HTML
- Azione di accesso nel code-behind nel controller

### <a name="sign-in-button"></a>Pulsante Accedi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core, per le applicazioni della piattaforma Microsoft Identity, il pulsante **Accedi** viene esposto in `Views\Shared\_LoginPartial.cshtml` (per un'app MVC) o `Pages\Shared\_LoginPartial.cshtm` (per un'app Razor). Viene visualizzato solo quando l'utente non è autenticato. Ovvero, viene visualizzato quando l'utente non ha ancora eseguito l'accesso o si è disconnesso. Al contrario, il pulsante di **disconnessione** viene visualizzato quando l'utente ha già eseguito l'accesso. Si noti che il controller di account è definito nel pacchetto NuGet **Microsoft. Identity. Web. UI** , nell'area denominata **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml` . Viene visualizzato solo quando è presente un account autenticato. Ovvero viene visualizzato quando l'utente ha già eseguito l'accesso.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Nella Guida introduttiva di Java il pulsante di accesso si trova nel file [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) .

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Nella Guida introduttiva Node.js non è disponibile alcun pulsante di accesso. Il code-behind chiede automaticamente all'utente di accedere quando raggiunge la radice dell'app Web.

```javascript
app.get('/', (req, res) => {
    // authentication logic
});
```

# <a name="python"></a>[Python](#tab/python)

Nella Guida introduttiva di Python non è disponibile alcun pulsante di accesso. Il code-behind chiede automaticamente all'utente di accedere quando raggiunge la radice dell'app Web. Vedere [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` azione del controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, selezionando il pulsante di **accesso** nell'app Web, viene attivata l' `SignIn` azione sul `AccountController` controller. Nelle versioni precedenti dei modelli ASP.NET Core il `Account` controller era incorporato con l'app Web. Non è più così perché il controller fa ora parte del pacchetto NuGet **Microsoft. Identity. Web. UI** . Per informazioni dettagliate, vedere [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Questo controller gestisce anche le applicazioni Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la disconnessione viene attivata dal `SignOut()` metodo su un controller, ad esempio [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23). Questo metodo non fa parte del framework ASP.NET (contrariamente a quanto accade in ASP.NET Core). Invia una richiesta di accesso OpenID dopo aver proposto un URI di reindirizzamento.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

In Java la disconnessione viene gestita chiamando direttamente l'endpoint della piattaforma Microsoft Identity `logout` e specificando il `post_logout_redirect_uri` valore. Per informazioni dettagliate, vedere [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A differenza di altre piattaforme, qui il nodo MSAL si occupa di consentire all'utente di accedere dalla pagina di accesso.

```javascript

// 1st leg of auth code flow: acquire a code
app.get('/', (req, res) => {
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // get url to sign user in and consent to scopes needed for application
    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

// 2nd leg of auth code flow: exchange code for token
app.get('/redirect', (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

Diversamente da altre piattaforme, MSAL Python si occupa di consentire all'utente di accedere dalla pagina di accesso. Vedere [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Il `_build_msal_app()` metodo è definito in [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) come indicato di seguito:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Dopo che l'utente ha eseguito l'accesso all'app, è opportuno abilitarne la disconnessione.

## <a name="sign-out"></a>Disconnessione

La disconnessione da un'app Web prevede più di rimuovere le informazioni relative all'account connesso dallo stato dell'app Web.
L'app Web deve anche reindirizzare l'utente all'endpoint della piattaforma Microsoft Identity `logout` per disconnettersi.

Quando l'app Web reindirizza l'utente all' `logout` endpoint, questo endpoint Cancella la sessione dell'utente dal browser. Se l'app non è stata inviata all' `logout` endpoint, l'utente eseguirà di nuovo l'autenticazione all'app senza immettere di nuovo le credenziali. Il motivo è che avranno una sessione Single Sign-in valida con la piattaforma di identità Microsoft.

Per altre informazioni, vedere la sezione [inviare una richiesta](v2-protocols-oidc.md#send-a-sign-out-request) di disconnessione nella [piattaforma di identità Microsoft e nella documentazione del protocollo OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registrazione dell'applicazione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante la registrazione dell'applicazione, si registra un URL di disconnessione front-Channel. In questa esercitazione è stata eseguita `https://localhost:44321/signout-oidc` la registrazione nel campo **URL di disconnessione front-Channel** della pagina di **autenticazione** . Per informazioni dettagliate, vedere [registrare l'app webapp](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante la registrazione dell'applicazione, non è necessario registrare un URL di disconnessione front-Channel aggiuntivo. L'app verrà richiamata sull'URL principale. 

# <a name="java"></a>[Java](#tab/java)

Nella registrazione dell'applicazione non è richiesto alcun URL di disconnessione front-Channel.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Nella registrazione dell'applicazione non è richiesto alcun URL di disconnessione front-Channel.

# <a name="python"></a>[Python](#tab/python)

Durante la registrazione dell'applicazione, non è necessario registrare un URL di disconnessione front-Channel aggiuntivo. L'app verrà richiamata sull'URL principale.

---

### <a name="sign-out-button"></a>Pulsante di disconnessione

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET, se si seleziona il pulsante di **disconnessione** nell'app Web, viene attivata l' `SignOut` azione sul `AccountController` Controller (vedere di seguito)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC il pulsante di disconnessione viene esposto in `Views\Shared\_LoginPartial.cshtml` . Viene visualizzato solo quando è presente un account autenticato. Ovvero viene visualizzato quando l'utente ha già eseguito l'accesso.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Nella Guida introduttiva di Java il pulsante di disconnessione si trova nel file Main/Resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Questa applicazione di esempio non implementa la disconnessione.

# <a name="python"></a>[Python](#tab/python)

Nella Guida introduttiva di Python, il pulsante di disconnessione è disponibile nel file [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` azione del controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Nelle versioni precedenti dei modelli ASP.NET Core il `Account` controller era incorporato con l'app Web. Non è più così perché il controller fa ora parte del pacchetto NuGet **Microsoft. Identity. Web. UI** . Per informazioni dettagliate, vedere [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- Imposta un URI di reindirizzamento OpenID su in `/Account/SignedOut` modo che il controller venga richiamato quando Azure ad ha completato la disconnessione.
- Chiama `Signout()` , che consente al middleware di OpenID Connect di contattare l'endpoint della piattaforma di identità Microsoft `logout` . L'endpoint:

  - Cancella il cookie di sessione dal browser.
  - Richiama l'URI di reindirizzamento dopo la disconnessione. Per impostazione predefinita, l'URI di reindirizzamento dopo la disconnessione Visualizza la pagina di visualizzazione disconnesso [. cshtml. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs). Questa pagina viene fornita anche come parte di Microsoft. Identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET, la disconnessione viene attivata dal `SignOut()` metodo su un controller, ad esempio [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31). Questo metodo non fa parte del framework ASP.NET, contrariamente a quanto accade in ASP.NET Core. È

- Invia una richiesta di disconnessione OpenID.
- Cancella la cache.
- Reindirizza alla pagina che desidera.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

In Java la disconnessione viene gestita chiamando direttamente l'endpoint della piattaforma Microsoft Identity `logout` e specificando il `post_logout_redirect_uri` valore. Per informazioni dettagliate, vedere [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Questa applicazione di esempio non implementa la disconnessione.

# <a name="python"></a>[Python](#tab/python)

Il codice che effettua la disattivazione dell'utente si trova in [app. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Intercettazione della chiamata all' `logout` endpoint

L'URI di post-disconnessione consente alle applicazioni di partecipare alla disconnessione globale.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Il middleware OpenID Connect ASP.NET Core consente all'app di intercettare la chiamata all'endpoint della piattaforma `logout` di identità Microsoft fornendo un evento OpenID Connect denominato `OnRedirectToIdentityProviderForSignOut` . Questa operazione viene gestita automaticamente da Microsoft. Identity. Web (che cancella gli account nel caso in cui l'app Web chiami API Web)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET si delega al middleware per eseguire la disconnessione, cancellando il cookie di sessione:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

Nella Guida introduttiva per Java, l'URI di reindirizzamento di post-disconnessione Visualizza solo la pagina index.html.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Questa applicazione di esempio non implementa la disconnessione.

# <a name="python"></a>[Python](#tab/python)

Nella Guida introduttiva di Python, l'URI di reindirizzamento di post-disconnessione Visualizza solo la pagina index.html.

---

## <a name="protocol"></a>Protocollo

Per altre informazioni sulla disconnessione, vedere la documentazione del protocollo disponibile in [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-web-app-sign-user-production.md).