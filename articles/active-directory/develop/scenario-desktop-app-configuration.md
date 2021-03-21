---
title: Configurare le app desktop che chiamano API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare il codice di un'app desktop che chiama API Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 372efa217e400ffd4bcecd286f3c3dc7b81f64d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007966"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>App desktop che chiama le API Web: configurazione del codice

Ora che è stata creata l'applicazione, si apprenderà come configurare il codice con le coordinate dell'applicazione.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Librerie Microsoft che supportano le applicazioni desktop

Le librerie Microsoft seguenti supportano le app desktop:

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Applicazione client pubblica

Dal punto di vista del codice, le applicazioni desktop sono applicazioni client pubbliche. La configurazione sarà leggermente diversa a seconda che si usi o meno l'autenticazione interattiva.

# <a name="net"></a>[.NET](#tab/dotnet)

Sarà necessario compilare e modificare MSAL.NET `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Esclusivamente per codice

Il codice seguente crea un'istanza di un'applicazione client pubblica e firma gli utenti nella Microsoft Azure cloud pubblico con un account aziendale o dell'Istituto di istruzione o un account Microsoft personale.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se si intende usare l'autenticazione interattiva o il flusso del codice del dispositivo, come illustrato in precedenza, usare il `.WithRedirectUri` modificatore.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Usare i file di configurazione

Il codice seguente crea un'istanza di un'applicazione client pubblica da un oggetto di configurazione, che può essere compilato a livello di codice o letto da un file di configurazione.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

Per elaborare la compilazione di applicazioni, è possibile aggiungere un numero di modificatori. Ad esempio, se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, ad esempio il governo degli Stati Uniti, è possibile scrivere:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contiene anche un modificatore per Active Directory Federation Services 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Infine, se si desidera acquisire i token per un tenant di Azure Active Directory (Azure AD) B2C, specificare il tenant, come illustrato nel frammento di codice seguente:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Altre informazioni

Per altre informazioni su come configurare un'applicazione desktop MSAL.NET:

- Per un elenco di tutti i modificatori disponibili in `PublicClientApplicationBuilder` , vedere la documentazione di riferimento [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Per una descrizione di tutte le opzioni esposte in `PublicClientApplicationOptions` , vedere [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) nella documentazione di riferimento.

### <a name="complete-example-with-configuration-options"></a>Esempio completo con le opzioni di configurazione

Si supponga di disporre di un'applicazione console .NET Core con il seguente `appsettings.json` file di configurazione:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Il codice per leggere in questo file è ridotto utilizzando. Framework di configurazione fornito da NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

A questo punto, per creare l'applicazione, scrivere il codice seguente:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Prima della chiamata al `.Build()` metodo, è possibile eseguire l'override della configurazione con le chiamate ai `.WithXXX` metodi, come illustrato in precedenza.

# <a name="java"></a>[Java](#tab/java)

Di seguito è illustrata la classe utilizzata negli esempi di sviluppo Java per MSAL per configurare gli esempi: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Il codice seguente crea un'istanza di un'applicazione client pubblica e firma gli utenti nella Microsoft Azure cloud pubblico con un account aziendale o dell'Istituto di istruzione o un account Microsoft personale.

### <a name="quick-configuration"></a>Configurazione rapida

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configurazione più elaborata

Per elaborare la compilazione di applicazioni, è possibile aggiungere un numero di modificatori. Ad esempio, se si vuole che l'applicazione sia un'applicazione multi-tenant in un cloud nazionale, ad esempio il governo degli Stati Uniti, è possibile scrivere:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [acquisire un token per l'app desktop](scenario-desktop-acquire-token.md).