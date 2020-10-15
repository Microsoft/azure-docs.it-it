---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app iOS o macOS | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'app iOS o macOS può concedere l'accesso agli utenti, ottenere un token di accesso da Microsoft Identity Platform e chiamare l'API Microsoft Graph.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 6ab826b6816c8f1b71a28c6bf501b651baa2cfff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91613459"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Avvio rapido: Accesso utenti e chiamata dell'API Microsoft Graph da un'app iOS o macOS

Questo avvio rapido contiene un esempio di codice che illustra in che modo un'applicazione nativa iOS o macOS può usare Microsoft Identity Platform per consentire l'accesso ad account personali, di lavoro o dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph.

Questo avvio rapido si applica sia alle app iOS che alle app macOS. Alcuni passaggi sono necessari solo per le app iOS. Questi passaggi indicano che sono solo per iOS.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10+
* iOS 10+
* macOS 10.12+

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare il codice di esempio
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'app
> 1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Passare alla pagina [Registrazioni app](https://aka.ms/MobileAppReg) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
>      - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app quando eseguono l'accesso o forniscono il proprio consenso all'app.
>      - Ignorare le altre configurazioni in questa pagina.
>      - Selezionare `Register`.
> 1. Nella sezione **Gestione** selezionare `Authentication` > `Add Platform` > `iOS`.
>      - Immettere il valore di ***Identificatore del bundle*** per l'applicazione. L'identificatore del bundle è semplicemente una stringa univoca che identifica in modo univoco l'applicazione, ad esempio `com.<yourname>.identitysample.MSALMacOS`. Prendere nota del valore usato.
>      - Si noti che la configurazione di iOS è applicabile anche alle applicazioni macOS.
> 1. Selezionare `Configure` e salvare i dati di ***Configurazione MSAL*** per usarli in seguito in questo avvio rapido.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passaggio 1: Configurare l'applicazione
> Per il funzionamento dell'esempio di codice di questo argomento di avvio rapido è necessario aggiungere un URI di reindirizzamento compatibile con il broker di autenticazione.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-ios/green-check.png) L'applicazione è configurata con questi attributi
>
> #### <a name="step-2-download-the-sample-project"></a>Passaggio 2: Scaricare il progetto di esempio
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Scaricare il codice di esempio per iOS]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Scaricare il codice di esempio per macOS]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Passaggio 2: Scaricare il progetto di esempio
>
> - [Scaricare il codice di esempio per iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Scaricare il codice di esempio per macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Passaggio 3: Installare le dipendenze

In una finestra del terminale passare alla cartella con il codice di esempio scaricato ed eseguire `pod install` per installare la libreria MSAL più recente.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Passaggio 4: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app ed è pronto per essere eseguito.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Passaggio 4: Configurare il progetto
> Se in precedenza è stata selezionata l'opzione 1, è possibile ignorare questi passaggi.
> 1. Estrarre il file con estensione zip e aprire il progetto in XCode.
> 1. Modificare **ViewController.swift** e sostituire la riga che inizia con 'let kClientID' con il frammento di codice seguente. Aggiornare il valore per `kClientID` con l'ID client salvato al momento della registrazione dell'app nel portale in un passaggio precedente di questo avvio rapido:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Se si sta creando un'app per [cloud nazionali Azure AD](/graph/deployments#app-registration-and-token-service-root-endpoints), sostituire la riga che inizia con 'let kGraphEndpoint' e 'let kAuthority' con gli endpoint corretti. Per l'accesso globale, usare i valori predefiniti:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Gli altri endpoint sono documentati [qui](/graph/deployments#app-registration-and-token-service-root-endpoints). Per eseguire ad esempio l'avvio rapido con Azure AD Germania, seguire questa procedura:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Aprire le impostazioni del progetto. Nella sezione **Identità** immettere l'**Identificatore del bundle** immesso nel portale.
> 1. Fare clic con il pulsante destro del mouse su **Info.plist** e scegliere **Apri come** > **Codice sorgente**.
> 1. Nel nodo radice dict sostituire `Enter_the_bundle_Id_Here` con il valore di ***ID bundle*** usato nel portale.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Compilare ed eseguire l'app.

## <a name="more-information"></a>Altre informazioni

Leggere queste sezioni per altre informazioni su questa guida introduttiva.

### <a name="get-msal"></a>Ottenere MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. È possibile aggiungere la libreria MSAL all'applicazione usando la procedura seguente:

```
$ vi Podfile

```
Aggiungere il codice seguente al podfile (con la destinazione del progetto):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Eseguire il comando di installazione CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Inizializzare MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```swift
import MSAL
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Dove: | Descrizione |
> |---------|---------|
> | `clientId` | ID dell'applicazione registrata in *portal.azure.com* |
> | `authority` | Endpoint di Microsoft Identity Platform. Nella maggior parte dei casi sarà *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | URI di reindirizzamento dell'applicazione. È possibile passare nil per usare il valore predefinito oppure specificare un URI di reindirizzamento personalizzato. |

### <a name="for-ios-only-additional-app-requirements"></a>Solo per iOS, requisiti aggiuntivi per l'app

L'app deve inoltre includere quanto segue nel relativo `AppDelegate`. In questo modo, quando si esegue l'autenticazione, MSAL SDK gestisce la risposta del token restituita dall'app broker di autenticazione.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> In iOS 13+ se si adotta `UISceneDelegate` invece di `UIApplicationDelegate` inserire questo codice nel callback `scene:openURLContexts:` (vedere la [documentazione di Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Se si supportano sia UISceneDelegate che UIApplicationDelegate per la compatibilità con le versioni precedenti di iOS, il callback MSAL deve essere inserito in entrambe le posizioni.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Per l'app deve infine essere definita una voce `LSApplicationQueriesSchemes` in ***Info.plist*** insieme a `CFBundleURLTypes`. Nell'esempio queste informazioni sono incluse.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Concedere l'accesso agli utenti e richiedere token

La libreria MSAL dispone di due metodi per acquisire i token: `acquireToken` e `acquireTokenSilent`

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Ottenere un token in modo interattivo

In alcune situazioni gli utenti devono interagire con Microsoft Identity Platform. È ad esempio possibile che l'utente finale debba selezionare il proprio account, immettere le credenziali o fornire il consenso alla richiesta di autorizzazioni dell'app. Ad esempio,

* La prima volta che gli utenti accedono all'applicazione
* Se un utente reimposta la password, dovrà immettere le credenziali
* Quando l'applicazione richiede l'accesso a una risorsa per la prima volta
* Quando è necessario eseguire l'autenticazione a più fattori o soddisfare altri criteri di accesso condizionale

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Dove:| Descrizione |
> |---------|---------|
> | `scopes` | Contiene gli ambiti richiesti, ovvero `[ "user.read" ]` per Microsoft Graph o `[ "<Application ID URL>/scope" ]` per le API Web personalizzate (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Ottenere un token di accesso in modo invisibile all'utente

Le app non dovrebbero richiedere agli utenti di accedere ogni volta che richiedono un token. Se l'utente ha già eseguito l'accesso, questo metodo consente alle app di richiedere i token in modo invisibile all'utente.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Dove: | Descrizione |
> |---------|---------|
> | `scopes` | Contiene gli ambiti richiesti, ovvero `[ "user.read" ]` per Microsoft Graph o `[ "<Application ID URL>/scope" ]` per le API Web personalizzate (`api://<Application ID>/access_as_user`) |
> | `account` | Indica l'account per il quale viene richiesto un token. Questo argomento di avvio rapido riguarda un'applicazione con singolo account. Se si vuole creare un'app con più account, è necessario definire la logica per identificare l'account da usare per le richieste di token usando `accountsFromDeviceForParameters:completionBlock:` e passando il valore corretto di `accountIdentifier` |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'esercitazione dettagliata in cui viene creata un'app iOS o macOS che ottiene un token di accesso da Microsoft Identity Platform e lo usa per chiamare l'API Microsoft Graph.

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso agli utenti e chiamare Microsoft Graph da un'app iOS o macOS](tutorial-v2-ios.md)
