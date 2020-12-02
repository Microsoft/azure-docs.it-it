---
title: "Avvio rapido: Concedere l'accesso agli utenti e chiamare Microsoft Graph in un'app desktop della piattaforma UWP (Universal Windows Platform) | Azure"
description: Questa guida di avvio rapido illustra come un'applicazione desktop .NET Windows (XAML) può ottenere un token di accesso e chiamare un'API protetta da un endpoint di Microsoft Identity Platform
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f63dbad398afcd0a9756cc1e9d4f70f1303d00b0
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95993857"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app di Windows Desktop

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione desktop Windows .NET (WPF) che consente agli utenti di accedere e ottenere un token di accesso per chiamare l'API Microsoft Graph. 

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prerequisiti
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con il carico di lavoro [Sviluppo di app per la piattaforma UWP (Universal Windows Platform)](/windows/uwp/get-started/get-set-up) installato
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
> 1. Passare al pannello [Registrazioni app](https://aka.ms/MobileAppReg) per Azure Active Directory nel portale di Azure.
> 1. Selezionare **Nuova registrazione**.
>      - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `Win-App-calling-MsGraph`.
>      - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
>      - Selezionare **Registra** per creare l'applicazione.
> 1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**.
> 1. Nella sezione **URI di reindirizzamento** | **URI di reindirizzamento suggeriti per client pubblici (dispositivi mobili, desktop)** usare **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Selezionare **Salva**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice di questa guida di avvio rapido è necessario aggiungere un URL di risposta come **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-windows-desktop/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-your-visual-studio-project"></a>Passaggio 2: Scaricare il progetto di Visual Studio

> [!div renderon="docs"]
> [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Eseguire il progetto con Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app ed è pronto per essere eseguito.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passaggio 3: Configurare il progetto di Visual Studio
> 1. Estrarre il file ZIP in una cartella locale vicina alla radice del disco, ad esempio **C:\Azure-Samples**.
> 1. Aprire il progetto in Visual Studio.
> 1. Modificare **App.Xaml.cs** e sostituire i valori dei campi `ClientId` e `Tenant` con il codice seguente:
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> Dove:
> - `Enter_the_Application_Id_here` è l'**ID applicazione (client)** per l'applicazione registrata.
> - `Enter_the_Tenant_Info_Here` è impostato su una delle opzioni seguenti:
>   - Se l'applicazione supporta **Account solo in questa directory organizzativa**, sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>   - Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations`
>   - Se l'applicazione supporta **Account in qualsiasi directory organizzativa e account Microsoft personali**, sostituire questo valore con `common`
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** , **ID della directory (tenant)** e **Tipi di account supportati**, passare alla pagina di **panoramica** dell'app nel portale di Azure.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. È possibile installare MSAL eseguendo questo comando in **Console di Gestione pacchetti** in Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```csharp
using Microsoft.Identity.Client;
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Dove: | Descrizione |
> |---------|---------|
> | `ClientId` | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure. |

### <a name="requesting-tokens"></a>Richiesta di token

In MSAL sono disponibili due metodi per acquisire i token: `AcquireTokenInteractive` e `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

In alcune situazioni è necessario forzare gli utenti a interagire con l'endpoint di Microsoft Identity Platform tramite una finestra popup per convalidare le relative credenziali o per concedere il consenso. Di seguito sono riportati alcuni esempi:

- La prima volta che gli utenti accedono all'applicazione
- Quando gli utenti devono immettere nuovamente le credenziali perché la password è scaduta
- Quando l'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
- Quando è necessaria l'autenticazione a due fattori

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Dove:| Descrizione |
> |---------|---------|
> | `_scopes` | Contiene gli ambiti richiesti, ad esempio `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate. |

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Non si desidera richiedere all'utente di convalidare le proprie credenziali ogni volta che deve accedere a una risorsa. La maggior parte delle volte si desidera che le acquisizioni e i rinnovi dei token avvengano senza alcuna interazione da parte dell'utente. È possibile usare il metodo `AcquireTokenSilent` per ottenere i token per accedere alle risorse protette dopo il metodo `AcquireTokenInteractive` iniziale:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Dove: | Descrizione |
> |---------|---------|
> | `scopes` | Contiene gli ambiti richiesti, ad esempio `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate. |
> | `firstAccount` | Specifica il primo utente nella cache (MSAL supporta più utenti in una singola app). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione di Windows Desktop per una guida dettagliata completa sulla creazione di applicazioni e di nuove funzionalità, tra cui una spiegazione completa di questa guida introduttiva.

> [!div class="nextstepaction"]
> [Esercitazione sulla chiamata dell'API Graph](./tutorial-v2-windows-desktop.md)