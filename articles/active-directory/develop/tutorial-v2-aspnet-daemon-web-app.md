---
title: 'Esercitazione: Creare un daemon multi-tenant che accede ai dati aziendali Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Questa esercitazione illustra come chiamare un'API Web ASP.NET protetta da Azure Active Directory da un'applicazione desktop di Windows (WPF). Il client WPF autentica un utente, richiede un token di accesso e chiama l'API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4fccff70fd267aef84550b4e2f5d6f5f9422a341
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103023"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Esercitazione: Creare un daemon multi-tenant che usa Microsoft Identity Platform

In questa esercitazione verrà scaricata ed eseguita un'app Web daemon ASP.NET che illustra l'uso della concessione di credenziali client OAuth 2.0 per ottenere un token di accesso per chiamare l'API Microsoft Graph.

In questa esercitazione:

> [!div class="checklist"]
> * Integrazione di un'app daemon con Microsoft Identity Platform
> * Concedere le autorizzazioni dell'applicazione direttamente all'app da un amministratore
> * Reperimento di un token di accesso per chiamare l'API Microsoft Graph
> * Chiamata all'API Microsoft Graph

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2017 o 2019](https://visualstudio.microsoft.com/downloads/).
- Un tenant di Azure AD. Per altre informazioni, vedere [Come ottenere un tenant di Azure AD](quickstart-create-new-tenant.md).
- Uno o più account utente nel tenant di Azure AD. Questo esempio non funziona con un account Microsoft. Se è stato effettuato l'accesso al [portale di Azure](https://portal.azure.com) con un account Microsoft e non è mai stato creato un account utente nella directory, eseguire subito questa operazione.

## <a name="scenario"></a>Scenario

L'app viene creata come applicazione MVC ASP.NET. Usa il middleware OWIN OpenID Connect per concedere l'accesso agli utenti.

Il componente "daemon" in questo esempio è un controller API, `SyncController.cs`. Quando viene chiamato, il controller recupera da Microsoft Graph un elenco di utenti del tenant di Azure Active Directory (Azure AD) del cliente. `SyncController.cs` viene attivato da una chiamata AJAX nell'applicazione Web. Usa [Microsoft Authentication Library (MSAL) per .NET](msal-overview.md) per acquisire un token di accesso per Microsoft Graph.

Poiché l'app è multi-tenant per i clienti aziendali Microsoft, deve fornire un modo per "iscriversi" o "connettere" l'applicazione ai dati aziendali. Durante il flusso di connessione, un amministratore globale concede le *autorizzazioni dell'applicazione* direttamente all'app, in modo che possa accedere ai dati aziendali in modo non interattivo, senza la presenza di un utente connesso. La maggior parte della logica di questo esempio mostra come ottenere questo flusso di connessione usando l'endpoint del [consenso dell'amministratore](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) di Identity Platform.

![Diagramma che mostra l'app UserSync con tre elementi locali che si connettono ad Azure, con Start.Auth che acquisisce un token in modo interattivo per connettersi ad Azure AD, AccountController che ottiene il consenso dell'amministratore per connettersi ad Azure AD e SyncController che legge l'utente per connettersi a Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Per ulteriori informazioni sui concetti usati in questo esempio, vedere la [documentazione relativa al protocollo di credenziali client per la piattaforma di identità](v2-oauth2-client-creds-grant-flow.md).

## <a name="clone-or-download-this-repository"></a>Clonare o scaricare questo repository

Dalla shell o dalla riga di comando immettere questo comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

In alternativa [scaricare l'esempio in un file ZIP](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registrare l'applicazione

L'esempio contiene un unico progetto. Per registrare l'applicazione con il tenant di Azure AD è possibile:

- Seguire le procedure riportate in [Registrare l'esempio con il tenant di Azure Active Directory](#register-the-client-app-dotnet-web-daemon-v2) e [Configurare l'esempio per l'uso del tenant di Azure AD](#choose-the-azure-ad-tenant).
- Usare gli script di PowerShell per:
  - Creare *automaticamente* le applicazioni di Azure AD e gli oggetti correlati (password, autorizzazioni, dipendenze).
  - Modificare i file di configurazione dei progetti di Visual Studio.

Se si vuole usare questa automazione:

1. In Windows eseguire PowerShell e passare alla radice della directory clonata.
1. Eseguire questo comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Eseguire lo script per creare l'applicazione di Azure AD e configurare di conseguenza il codice dell'applicazione di esempio:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Altre modalità di esecuzione degli script sono descritte in [Script di creazione app](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Aprire la soluzione in Visual Studio e selezionare **Avvia** per eseguire il codice.

Se non si vuole usare l'automazione, seguire i passaggi delle sezioni seguenti.

### <a name="choose-the-azure-ad-tenant"></a>Scegliere il tenant di Azure AD

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrare l'app client (dotnet-web-daemon-v2)

1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
1. Immettere un **Nome** per l'applicazione, ad esempio `dotnet-web-daemon-v2`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
1. Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione**.
1. Nella sezione **URI di reindirizzamento (facoltativo)** selezionare **Web** nella casella combinata e immettere `https://localhost:44316/` e `https://localhost:44316/Account/GrantPermissions` come URI di reindirizzamento.

    Se sono presenti più URI di reindirizzamento, è necessario aggiungerli dalla scheda **Autenticazione** dopo che l'app sarà stata creata correttamente.
1. Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota per usarlo in seguito. Sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto.
1. In **Gestisci** selezionare **Autenticazione**.
1. Impostare l' **URL di disconnessione front-Channel** su `https://localhost:44316/Account/EndSession` .
1. Nella sezione **concessione implicita e flussi ibridi** selezionare **token di accesso** e **token ID**. Per questo esempio è necessario abilitare il [flusso di concessione implicita](v2-oauth2-implicit-grant-flow.md) per consentire all'utente di accedere e chiamare un'API.
1. Selezionare **Salva**.
1. In **Gestisci**, selezionare **Certificati e segreti**.
1. Nella sezione **Segreti client** selezionare **Nuovo segreto client**. 
1. Immettere una descrizione della chiave, ad esempio **segreto dell'app**.
1. Selezionare una durata della chiave scegliendo **Tra 1 anno**, **Tra 2 anni** o **Non scade mai**.
1. Selezionare **Aggiungi**. Prendere nota del valore della chiave e conservarlo in un luogo sicuro. Questa chiave sarà necessaria in seguito per configurare il progetto in Visual Studio.
1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**.
1. Nella sezione **API Microsoft più usate** selezionare **Microsoft Graph**.
1. Nella sezione **Autorizzazioni dell'applicazione** verificare che siano selezionate le autorizzazioni corrette: **User.Read.All**.
1. Selezionare **Aggiungi autorizzazioni**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurare l'esempio per l'uso del tenant di Azure AD

Nei passaggi seguenti **ClientID** corrisponde a "ID applicazione" o **AppId**.

Aprire la soluzione in Visual Studio per configurare i progetti.

### <a name="configure-the-client-project"></a>Configurare il progetto client

Se sono stati usati gli script di installazione, saranno state applicate automaticamente le modifiche seguenti.

1. Aprire il file **UserSync\Web.Config**.
1. Trovare la chiave dell'app **ida:ClientId**. Sostituire il valore esistente con l'ID dell'applicazione **dotnet-web-daemon-v2** copiato dal portale di Azure.
1. Trovare la chiave dell'app **ida:ClientSecret**. Sostituire il valore esistente con la chiave salvata durante la creazione dell'app **dotnet-web-daemon-v2** nel portale di Azure.

## <a name="run-the-sample"></a>Eseguire l'esempio

Pulire la soluzione, ricompilarla ed eseguire l'applicazione UserSync, quindi accedere come amministratore nel tenant di Azure AD. Se non si dispone di un tenant di Azure AD per il test, è possibile [seguire queste istruzioni](quickstart-create-new-tenant.md) per ottenerne uno.

Quando si accede, l'app chiede prima di tutto l'autorizzazione per consentire l'accesso e per leggere il profilo utente. Con questo consenso si conferma all'app di essere un utente aziendale.

![Consenso dell'utente](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

L'app prova quindi a sincronizzare un elenco di utenti del tenant di Azure AD, tramite Microsoft Graph. Se il tentativo non riesce, chiede all'amministratore del tenant di connettere il tenant all'app.

L'app chiede quindi l'autorizzazione per leggere l'elenco di utenti nel tenant.

![Consenso dell'amministratore](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Dopo aver concesso l'autorizzazione, si viene disconnessi dall'app. La disconnessione assicura che tutti i token di accesso esistenti per Microsoft Graph vengano rimossi dalla cache dei token. Quando si esegue di nuovo l'accesso, il nuovo token ottenuto avrà le autorizzazioni necessarie per effettuare chiamate a Microsoft Graph.


Quando si concede l'autorizzazione, l'app può eseguire query per trovare gli utenti in qualsiasi punto. Per verificarlo, selezionare il pulsante **Sincronizza utenti** e aggiornare l'elenco di utenti. Provare ad aggiungere o a rimuovere un utente e quindi a risincronizzare l'elenco. Si noti però che l'app sincronizza solo la prima pagina di utenti.

## <a name="about-the-code"></a>Informazioni sul codice

Il codice pertinente per questo esempio si trova nei file seguenti:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: accesso iniziale. In particolare, le azioni sul controller hanno un attributo **Authorize**, che forza l'accesso dell'utente. L'applicazione usa il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) per far accedere l'utente.
- **Controllers\SyncController.cs**: sincronizzazione dell'elenco di utenti con l'archivio in memoria locale.
- **Controllers\UserController.cs**: visualizzazione dell'elenco di utenti dall'archivio in memoria locale.
- **Controllers\AccountController.cs**: acquisizione delle autorizzazioni dall'amministratore del tenant tramite l'endpoint di consenso dell'amministratore.

## <a name="re-create-the-sample-app"></a>Ricreare l'app di esempio

1. In Visual Studio creare un nuovo progetto di **applicazione Web ASP.NET (.NET Framework)** in **Visual C#** .
1. Nella schermata successiva scegliere il modello di progetto **MVC**. Aggiungere anche la cartella e i riferimenti principali per **API Web**, perché in seguito verrà aggiunto un controller API Web. Lasciare la modalità di autenticazione del progetto selezionata come impostazione predefinita: **Nessuna autenticazione**.
1. Selezionare il progetto nella finestra **Esplora soluzioni** e premere **F4**.
1. Nelle proprietà del progetto impostare **SSL abilitato** su **True**. Prendere nota del valore di **URL SSL**. Sarà necessario quando si configura la registrazione di questa applicazione nel portale di Azure.
1. Aggiungere i seguenti pacchetti NuGet del middleware OWIN ASP.NET:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. Nella cartella **App_Start**:
   1. Creare una classe denominata **Startup.Auth.cs**.
   1. Rimuovere **.App_Start** dal nome dello spazio dei nomi.
   1. Sostituire il codice della classe **Startup** con il codice dello stesso file dell'app di esempio.
   Assicurarsi di inserire l'intera definizione della classe. La definizione cambia da **public class Startup** a **public partial class Startup**.
1. In **Startup.Auth.cs** risolvere i riferimenti mancanti aggiungendo istruzioni **using** come suggerito da IntelliSense di Visual Studio.
1. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi**, quindi selezionare **Classe**.
1. Nella casella di ricerca immettere **OWIN**. Viene visualizzata l'opzione **Classe di avvio di OWIN**. Selezionarla e assegnare alla classe il nome **Startup.cs**.
1. In **Startup.cs** sostituire il codice della classe **Startup** con il codice dello stesso file dell'app di esempio. Anche in questo caso, la definizione cambia da **public class Startup** a **public partial class Startup**.
1. Nella cartella **Models** aggiungere una nuova classe denominata **MsGraphUser.cs**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller MVC 5 - Vuoto** denominata **AccountController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller MVC 5 - Vuoto** denominata **UserController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiungere una nuova istanza di **Controller Web API 2 - Vuoto** denominata **SyncController**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Per l'interfaccia utente, nella cartella **Views\Account** aggiungere tre istanze di **visualizzazioni vuote (senza modello)** denominate **GrantPermissions**, **Index** e **UserMismatch**. Aggiungerne una denominata **Index** nella cartella **Views\User**. Sostituire l'implementazione con il contenuto del file con lo stesso nome dell'esempio.
1. Aggiornare **Shared\_Layout.cshtml** e **Home\Index.cshtml** per collegare correttamente le varie visualizzazioni tra loro.

## <a name="deploy-the-sample-to-azure"></a>Distribuire l'esempio in Azure

Questo progetto include i progetti di app Web e API Web. Per distribuirli nei siti Web di Azure, seguire questi passaggi per ognuno:

1. Creare un sito Web di Azure.
1. Pubblicare l'app Web e le API Web nel sito Web.
1. Aggiornare i client per chiamare il sito Web invece di IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Creare e pubblicare dotnet-web-daemon-v2 in un sito Web di Azure

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Nell'angolo in alto a sinistra della schermata selezionare **Crea una risorsa**.
1. Selezionare **Web** > **App Web** e quindi assegnare un nome al sito Web, ad esempio **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selezionare le informazioni per la **sottoscrizione**, il **gruppo di risorse** e il **piano e la località del servizio app**. Per **Sistema operativo** scegliere **Windows** e per **Pubblica** scegliere **Codice**.
1. Selezionare **Crea** e attendere che venga creato il servizio app.
1. Quando si riceve la notifica **Distribuzione riuscita**, selezionare **Vai alla risorsa** per passare al servizio app appena creato.
1. Dopo aver creato il sito Web, individuarlo nel **Dashboard** e selezionarlo per aprire la schermata **Panoramica** del servizio app.
1. Nella scheda **Panoramica** del servizio app scaricare il profilo di pubblicazione selezionando il collegamento **Recupera profilo di pubblicazione**, quindi salvarlo. È possibile usare altri meccanismi di distribuzione, ad esempio dal controllo del codice sorgente.
1. Passare a Visual Studio e quindi:
   1. Passare al progetto **dotnet-web-daemon-v2**.
   1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e quindi scegliere **Pubblica**.
   1. Selezionare **Importa profilo** sulla barra inferiore e importare il profilo di pubblicazione scaricato in precedenza.
1. Selezionare **Configura**.
1. Nella scheda **Connessione** aggiornare l'URL di destinazione in modo che usi "https". Ad esempio, usare `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Selezionare **Avanti**.
1. Nella scheda **Impostazioni** verificare che l'opzione **Abilita autenticazione a livello aziendale** sia deselezionata.
1. Selezionare **Salva**. Selezionare **Pubblica** nella schermata principale.

Visual Studio pubblicherà il progetto e aprirà automaticamente un browser all'URL corrispondente. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione ha avuto esito positivo.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aggiornare la registrazione dell'applicazione nel tenant di Azure AD per dotnet-web-daemon-v2

1. Tornare al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app**.
1. Selezionare l'applicazione **dotnet-web-daemon-v2**.
1. Nella pagina **autenticazione** per l'applicazione aggiornare i campi di **URL di disconnessione front-Channel** con l'indirizzo del servizio. Ad esempio, usare `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession`.
1. Nel menu **Personalizzazione** aggiornare il valore di **URL pagina iniziale** impostandolo sull'indirizzo del servizio. Ad esempio, usare `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Salvare la configurazione.
1. Aggiungere lo stesso URL nell'elenco di valori del menu **Autenticazione** > **URI di reindirizzamento**. Se sono presenti più URL di reindirizzamento, assicurarsi che vi sia una nuova voce che usa l'URI del servizio app per ognuno di essi.

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, eliminare l'oggetto app creato nel passaggio [Registrare l'applicazione](#register-your-application).  Per rimuovere l'applicazione, seguire le istruzioni riportate in [Rimuovere un'applicazione creata dall'utente o dalla relativa organizzazione](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Ottenere aiuto

Usare [Microsoft Q&](https://docs.microsoft.com/answers/products/) a per ottenere supporto dalla community.
È possibile porre le domande su [Microsoft Q&un](https://docs.microsoft.com/answers/products/) primo ed esaminare i problemi esistenti per verificare se qualcuno ha posto la domanda prima.
Assicurarsi che le domande o i commenti siano contrassegnati con "Azure-ad-adal-Deprecation", "Azure-ad-MSAL" e "DotNet-standard".

Se nell'esempio si trova un bug, segnalarlo nella pagina di [problemi di GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se in MSAL.NET si trova un bug, segnalarlo nella pagina di [problemi di GitHub MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Per inviare suggerimenti, visitare la pagina [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla creazione di app daemon che usano Microsoft Identity Platform per accedere ad API Web protette:

> [!div class="nextstepaction"]
> [Scenario: Applicazione daemon che chiama le API Web](scenario-daemon-overview.md)
