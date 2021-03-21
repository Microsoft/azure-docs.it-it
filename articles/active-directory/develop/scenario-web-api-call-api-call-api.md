---
title: API Web che chiama API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038544"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>API Web che chiama API Web: chiamare un'API

Una volta che si dispone di un token, è possibile chiamare un'API Web protetta. Generalmente si chiamano le API downstream dal controller o dalle pagine dell'API Web.

## <a name="controller-code"></a>Codice controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Quando si usa *Microsoft. Identity. Web*, sono disponibili tre scenari di utilizzo:

- [Opzione 1: chiamare Microsoft Graph con l'SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Opzione 2: chiamare un'API Web downstream con la classe helper](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opzione 3: chiamare un'API Web downstream senza la classe helper](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opzione 1: chiamare Microsoft Graph con l'SDK

In questo scenario è stato aggiunto `.AddMicrosoftGraph()` in *Startup. cs* come specificato nella [configurazione del codice](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)ed è possibile inserire direttamente nel `GraphServiceClient` controller o nel costruttore della pagina per l'uso nelle azioni. La pagina Razor di esempio seguente mostra la foto dell'utente che ha eseguito l'accesso.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Opzione 2: chiamare un'API Web downstream con la classe helper

In questo scenario è stato aggiunto `.AddDownstreamWebApi()` in *Startup. cs* come specificato nella [configurazione del codice](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)ed è possibile inserire direttamente un `IDownstreamWebApi` servizio nel controller o nel costruttore della pagina e usarlo nelle azioni:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

Il `CallWebApiForUserAsync` metodo dispone anche di override generici fortemente tipizzati che consentono di ricevere direttamente un oggetto. Il metodo seguente, ad esempio, ha ricevuto un' `Todo` istanza, ovvero una rappresentazione fortemente tipizzata del codice JSON restituito dall'API Web.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Opzione 3: chiamare un'API Web downstream senza la classe helper

Se si è deciso di acquisire un token manualmente usando il `ITokenAcquisition` servizio, è ora necessario usare il token. In tal caso, il codice seguente continua il codice di esempio illustrato in [un'API Web che chiama API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md). Il codice viene chiamato nelle azioni dei controller API. Chiama un'API downstream denominata *todo*.

 Dopo aver acquisito il token, usarlo come bearer token per chiamare l'API downstream.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Il codice seguente continua il codice di esempio illustrato in [un'API Web che chiama API Web: acquisire un token per l'app](scenario-web-api-call-api-acquire-token.md). Il codice viene chiamato nelle azioni dei controller API. Chiama l'API downstream MS Graph.

Dopo aver acquisito il token, usarlo come bearer token per chiamare l'API downstream.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Un esempio che illustra questo flusso con MSAL Python è disponibile in [MS-Identity-Python-on-conto-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of).

---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-web-api-call-api-production.md).
