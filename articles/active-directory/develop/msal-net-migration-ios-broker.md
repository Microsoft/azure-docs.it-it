---
title: Eseguire la migrazione di app Xamarin tramite broker a MSAL.NET
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione di app Novell iOS che usano Microsoft Authenticator da ADAL.NET a MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89177432"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Eseguire la migrazione di applicazioni iOS che usano Microsoft Authenticator da ADAL.NET a MSAL.NET

Si sta usando la libreria di autenticazione Azure Active Directory per .NET (ADAL.NET) e il broker iOS. A questo punto è giunto il momento di eseguire la migrazione a [Microsoft Authentication Library](msal-overview.md) per .net (MSAL.NET), che supporta Service Broker in iOS dalla versione 4,3 e successive.

Da dove iniziare? Questo articolo illustra come eseguire la migrazione dell'app Novell iOS da ADAL a MSAL.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che sia già presente un'app Novell iOS integrata con il broker iOS. In caso contrario, passare direttamente a MSAL.NET e iniziare l'implementazione del broker. Per informazioni su come richiamare il broker iOS in MSAL.NET con una nuova applicazione, vedere [questa documentazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Sfondo

### <a name="what-are-brokers"></a>Che cosa sono i broker?

I broker sono applicazioni fornite da Microsoft in Android e iOS. (Vedere l'app [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) in iOS e Android e l'app portale aziendale Intune in Android).

Consentono di:

- Single Sign-On.
- Identificazione del dispositivo, richiesta da alcuni [criteri di accesso condizionale](../conditional-access/overview.md). Per ulteriori informazioni, vedere [gestione dei dispositivi](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Verifica dell'identificazione dell'applicazione, necessaria anche in alcuni scenari aziendali. Per ulteriori informazioni, vedere la pagina relativa alla [gestione di applicazioni mobili (MAM) di Intune](/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Eseguire la migrazione da ADAL a MSAL

### <a name="step-1-enable-the-broker"></a>Passaggio 1: abilitare Service Broker

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
In ADAL.NET il supporto del broker è stato abilitato in base al contesto di autenticazione. È disabilitata per impostazione predefinita. Era necessario impostare un

`useBroker` flag su true nel `PlatformParameters` costruttore per chiamare Service Broker:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
Inoltre, nel codice specifico della piattaforma, in questo esempio, nel renderer di pagina per iOS, impostare il `useBroker`
flag su true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Quindi, includere i parametri nella chiamata al token di acquisizione:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource,
                              ClientId,
                              new Uri(RedirectURI),
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
In MSAL.NET il supporto del broker è abilitato per ogni PublicClientApplication. È disabilitata per impostazione predefinita. Per abilitarla, usare il

`WithBroker()` (impostare su true per impostazione predefinita) per chiamare Service Broker:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Nella chiamata al token di acquisizione:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passaggio 2: impostare un UIViewController ()
In ADAL.NET è stato passato un UIViewController come parte di `PlatformParameters` . Vedere l'esempio nel passaggio 1. In MSAL.NET, per offrire agli sviluppatori maggiore flessibilità, viene usata una finestra oggetto, ma non è necessaria nell'utilizzo normale di iOS. Per utilizzare Service Broker, impostare la finestra oggetto per inviare e ricevere risposte dal broker.
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Un UIViewController viene passato in

`PlatformParameters` nella piattaforma specifica di iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET è possibile eseguire due operazioni per impostare la finestra oggetto per iOS:

1. In `AppDelegate.cs` impostare `App.RootViewController` su un nuovo oggetto `UIViewController()` .
Questa assegnazione garantisce che esista un UIViewController con la chiamata al broker. Se non è impostato correttamente, è possibile che venga ricevuto questo errore: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Nella chiamata AcquireTokenInteractive usare `.WithParentActivityOrWindow(App.RootViewController)` e passare il riferimento alla finestra oggetto che verrà usata.

**Ad esempio:**

In `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Nella chiamata al token di acquisizione:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: aggiornare AppDelegate per gestire il callback
Sia ADAL che MSAL chiamano il broker e il broker a sua volta richiama l'applicazione tramite il `OpenUrl` metodo della `AppDelegate` classe. Per ulteriori informazioni, vedere [questa documentazione](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Non sono state apportate modifiche tra ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: registrare uno schema URL
ADAL.NET e MSAL.NET usano gli URL per richiamare il broker e restituire la risposta di Service Broker all'app. Registrare lo schema URL nel `Info.plist` file per l'app come segue:

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Lo schema URL è univoco per l'app.
</td><td>
Alla classe

`CFBundleURLSchemes` il nome deve includere

`msauth.`

come prefisso, seguito dal `CFBundleURLName`

ad esempio `$"msauth.(BundleId")`

```csharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> Questo schema URL diventa parte dell'URI di reindirizzamento usato per identificare in modo univoco l'app quando riceve la risposta dal broker.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 5: aggiungere l'identificatore di Service Broker alla sezione LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET usano entrambi `-canOpenURL:` per verificare se il broker è installato nel dispositivo. Aggiungere l'identificatore corretto per il broker iOS alla sezione LSApplicationQueriesSchemes del file INFO. plist come indicato di seguito:

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Utilizzi

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utilizzi

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>Passaggio 6: registrare l'URI di reindirizzamento nel portale di Azure

ADAL.NET e MSAL.NET aggiungono un requisito aggiuntivo all'URI di reindirizzamento quando è destinato al broker. Registrare l'URI di reindirizzamento con l'applicazione nel portale di Azure.
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Esempio:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Esempio:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Per altre informazioni su come registrare l'URI di reindirizzamento nella portale di Azure, vedere [passaggio 7: aggiungere un URI di reindirizzamento alla registrazione dell'app](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration).

### <a name="step-7-set-the-entitlementsplist"></a>**Passaggio 7: impostare i diritti. plist**

Abilitare l'accesso a keychain nel file con *estensione plist dei diritti* :

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Per altre informazioni sull'abilitazione dell'accesso keychain, vedere [Enable Keychain Access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni specifiche di Novell per iOS con MSAL.NET](msal-net-xamarin-ios-considerations.md).