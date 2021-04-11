---
title: "Avvio rapido: Concedere l'accesso agli utenti e chiamare Microsoft Graph in un'app della piattaforma UWP (Universal Windows Platform) | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'applicazione della piattaforma UWP (Universal Windows Platform) può ottenere un token di accesso e chiamare un'API protetta da Microsoft Identity Platform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 88fd6260df0039e960ac87b4b83d43ea497a58b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658510"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Avvio rapido: Chiamare l'API Microsoft Graph da un'applicazione della piattaforma UWP (Universal Windows Platform)

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione della piattaforma UWP (Universal Windows Platform) che consente agli utenti di accedere e ottenere un token di accesso per chiamare l'API Microsoft Graph. 

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prerequisiti
>
> * Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> [!div renderon="docs" class="sxs-lookup"]
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app</a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `UWP-App-calling-MsGraph`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
> 1. Selezionare **Registra** per creare l'applicazione e quindi registrare il valore di **ID applicazione (client)** , che verrà usato in un passaggio successivo.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Selezionare **Aggiungi una piattaforma** > **Applicazioni per dispositivi mobili e desktop**.
> 1. In **URI di reindirizzamento** selezionare `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 1. Selezionare **Configura**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Passaggio 1: Configurare l'applicazione
> Per il funzionamento dell'esempio di codice in questa Guida introduttiva, aggiungere un **URI di reindirizzamento** `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-uwp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-visual-studio-project"></a>Passaggio 2: Scaricare il progetto di Visual Studio

> [!div renderon="docs"]
> [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Eseguire il progetto con Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app ed è pronto per essere eseguito.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Passaggio 3: Configurare il progetto di Visual Studio
>
> 1. Estrarre l'archivio ZIP in una cartella locale vicina alla radice dell'unità. Ad esempio, in **C:\Azure-Samples**.
> 1. Aprire il progetto in Visual Studio. Installare il carico di lavoro **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** ed eventuali singoli componenti dell'SDK, se richiesto.
> 1. Nel file *MainPage.Xaml.cs* impostare il valore della variabile `ClientId` sul valore di **ID applicazione (client)** registrato in precedenza.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    È possibile trovare il valore di **ID applicazione (client)** nel riquadro **Panoramica** dell'app nel portale di Azure (**Azure Active Directory** > **Registrazioni app** >  *{Registrazione dell'app}* ).
> 1. Creare e quindi selezionare un nuovo certificato di test autofirmato per il pacchetto:
>     1. In **Esplora soluzioni** fare doppio clic sul file *Package.appxmanifest*.
>     1. Selezionare **Creazione pacchetto** > **Scegli certificato** > **Crea**.
>     1. Immettere una password e quindi selezionare **OK**.
>     1. Scegliere **Seleziona dal file**, selezionare il file *Native_UWP_V2_TemporaryKey.pfx* appena creato, quindi selezionare **OK**.
>     1. Chiudere il file *Package.appxmanifest* (selezionare **OK** se viene chiesto di salvare il file).
>     1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Native_UWP_V2** e scegliere **Proprietà**.
>     1. Selezionare **Firma**, quindi nel menu a discesa **File di chiave con nome sicuro** selezionare il file con estensione pfx creato.

#### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

Per eseguire l'applicazione di esempio nel computer locale:

1. Sulla barra degli strumenti di Visual Studio scegliere la piattaforma corretta (probabilmente **x64** oppure **x86**, non ARM). Il dispositivo di destinazione dovrebbe cambiare da *Dispositivo* a *Computer locale*.
1. Selezionare **Debug** > **Avvia senza eseguire debug**.
    
    Se viene richiesto, può essere necessario abilitare prima **Modalità sviluppatore** e quindi scegliere di nuovo **Avvia senza eseguire debug** per avviare l'app.

Quando viene visualizzata la finestra dell'app, è possibile selezionare il pulsante per **chiamare l'API Microsoft Graph**,immettere le credenziali e concedere il consenso per autorizzazioni richieste dall'applicazione. Se la procedura riesce, l'applicazione visualizza alcune informazioni sul token e i dati ottenuti dalla chiamata all'API Microsoft Graph.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) è la libreria usata per concedere l'accesso agli utenti e richiedere token di sicurezza. I token di sicurezza vengono usati per accedere a un'API protetta dalla piattaforma di identità Microsoft. È possibile installare MSAL eseguendo questo comando in *Console di Gestione pacchetti* in Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```csharp
using Microsoft.Identity.Client;
```

La libreria MSAL viene quindi inizializzata con il codice seguente:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

Il valore di `ClientId` corrisponde a **ID applicazione (client)** dell'app registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure.

### <a name="requesting-tokens"></a>Richiesta di token

In MSAL sono disponibili due metodi per acquisire i token in un'app UWP: `AcquireTokenInteractive` e `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

In alcune situazioni è necessario forzare gli utenti a interagire con la piattaforma di identità Microsoft tramite una finestra popup per convalidare le credenziali o per concedere il consenso. Di seguito sono riportati alcuni esempi:

- La prima volta che gli utenti accedono all'applicazione
- Quando gli utenti devono immettere nuovamente le credenziali perché la password è scaduta
- Quando l'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
- Quando è necessaria l'autenticazione a due fattori

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

Il parametro `scopes` contiene gli ambiti richiesti, ad esempio `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Usare il metodo `AcquireTokenSilent` per ottenere i token per accedere alle risorse protette dopo il metodo `AcquireTokenInteractive` iniziale. Non si desidera richiedere all'utente di convalidare le proprie credenziali ogni volta che deve accedere a una risorsa. La maggior parte delle volte si desidera che le acquisizioni e i rinnovi dei token avvengano senza alcuna interazione da parte dell'utente

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` contiene gli ambiti richiesti, ad esempio `{ "user.read" }` per Microsoft Graph o `{ "api://<Application ID>/access_as_user" }` per le API Web personalizzate.
* `firstAccount` specifica il primo account utente nella cache (MSAL supporta più utenti in una singola app).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione di Windows Desktop per una guida dettagliata completa sulla creazione di applicazioni e di nuove funzionalità, tra cui una spiegazione completa di questa guida introduttiva.

> [!div class="nextstepaction"]
> [Piattaforma UWP (Universal Windows Platform): esercitazione sulla chiamata dell'API Graph](tutorial-v2-windows-uwp.md)
