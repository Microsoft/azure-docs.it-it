---
title: "Avvio rapido: Aggiungere l'autenticazione a un'app Web Node.js con MSAL Node | Azure"
titleSuffix: Microsoft identity platform
description: Questo argomento di avvio rapido illustra come implementare l'autenticazione con un'app Web Node.js e Microsoft Authentication Library (MSAL) per Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 72eb6e77cfbcae662181f642393085185514eed6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550949"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Avvio rapido: Consentire l'accesso agli utenti e ottenere un token di accesso in un'applicazione Web Node con il flusso di codice di autorizzazione

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'app Web Node.js che consente agli utenti di accedere usando il flusso di codice di autorizzazione. L'esempio di codice dimostra anche come ottenere un token di accesso per chiamare l'API Microsoft Graph. 

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

Questo argomento di avvio rapido usa Microsoft Authentication Library per Node.js (MSAL Node) con il flusso di codice di autorizzazione.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - [Creare una sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrare e scaricare l'app dell'avvio rapido
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **nome** per l'applicazione. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Impostare il valore del campo **URI di reindirizzamento** su `http://localhost:3000/redirect`.
> 1. Selezionare **Registra**. 
> 1. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. In **Gestisci** selezionare **Certificati e segreti** > **Nuovo segreto client**.  Lasciare vuota la descrizione e mantenere invariata la scadenza predefinita, quindi selezionare **Aggiungi**.
> 1. Prendere nota del **valore** di **Segreto client** per usarlo successivamente.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passaggio 1: configurare l'applicazione in portale di Azure
> Per il funzionamento dell'esempio di codice per questa Guida introduttiva, è necessario creare un segreto client e aggiungere un URL di risposta come **http://localhost:3000/redirect** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-windows-desktop/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-project"></a>Passaggio 2: Scaricare il progetto

> [!div renderon="docs"]
> Per eseguire il progetto con un server Web, ad esempio Node.js, [scaricare i file di progetto di base](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto con un server Web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Passaggio 3: Configurare l'app Node
>
> Estrarre il progetto e aprire la cartella *ms-identity-node-main*, quindi aprire il file *index.js*.
> Impostare `clientID`con l'**ID applicazione (client)** .
> Impostare `clientSecret` con il **valore** di **Segreto client**.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Modificare i valori nella sezione `config` come descritto di seguito:
>
> - `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.
>
>    Per trovare il valore di **ID applicazione (client)** , passare alla pagina **Panoramica** della registrazione dell'app nel portale di Azure.
> - `Enter_the_Client_Secret_Here` è il **valore** di **Segreto client** per l'applicazione registrata.
>
>    Per recuperare o generare un nuovo **segreto client**, in **Gestisci** Selezionare **certificati & segreti**.
>
> Il valore `authority` predefinito rappresenta il cloud di Azure principale (globale):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Passaggio 4: Eseguire il progetto

Eseguire il progetto usando Node.js:

1. Per avviare il server, eseguire i comandi seguenti nella directory del progetto:
    ```console
    npm install
    npm start
    ```
1. Passare a `http://localhost:3000/`.

1. Selezionare **Accedi** per avviare il processo di accesso.

    Al primo accesso viene chiesto di concedere il proprio consenso per consentire all'applicazione di accedere al profilo e completare la procedura di accesso. Dopo l'accesso, verrà visualizzato un messaggio del log nella riga di comando.

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio

Quando viene eseguito, l'esempio ospita un server Web sulla porta localhost 3000. Quando un Web browser accede a questo sito, l'esempio reindirizza immediatamente l'utente a una pagina di autenticazione Microsoft. Per questo motivo, l'esempio non contiene alcun elemento *HTML* o di visualizzazione. Se l'autenticazione riesce, viene visualizzato il messaggio "OK".

### <a name="msal-node"></a>MSAL Node

Con la libreria MSAL Node è possibile concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. È possibile scaricare l'ultima versione usando Node.js Package Manager (npm):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiunta dell'autenticazione a un'app Web esistente - esempio di codice GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
