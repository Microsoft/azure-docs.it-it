---
title: Usare MSAL con Azure Active Directory B2CLearn . Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) rende possibile l'interoperabilità delle applicazioni con Azure AD B2C e l'acquisizione di token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o API Web proprie.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696453"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Usare Microsoft Authentication Library (MSAL) per l'interoperabilità con Azure Active Directory B2C

Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C è un servizio di gestione delle identità, che consente di personalizzare e controllare il modo in cui i clienti effettuano l'iscrizione, l'accesso e la gestione dei profili quando usano le applicazioni fornite.

Azure AD B2C permette anche di personalizzare e ottimizzare l'interfaccia utente delle applicazioni per offrire un'esperienza uniforme ai clienti.

Questa esercitazione illustra come usare MSAL per l'interoperabilità con Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crearlo ora. È anche possibile usare un tenant Azure AD B2C esistente.

## <a name="javascript"></a>JavaScript

I passaggi seguenti descrivono in che modo un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso e per chiamare un'API Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è prima di tutto necessario registrare l'applicazione. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: Scaricare l'applicazione di esempioStep 2: Download the sample application

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: Configurare l'autenticazioneStep 3: Configure authentication

1. Aprire il file **index.html** nell'esempio.

1. Configurare l'esempio con l'ID client e la chiave registrati in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con i nomi della directory e delle API:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Il nome del [flusso utente](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) in questa esercitazione è **B2C_1_signupsignin1**. Se si usa un nome di flusso utente diverso, impostare il valore dell'**autorità** su tale nome.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Passaggio 4: Configurare l'applicazione per l'utilizzoStep 4: Configure your application to use`b2clogin.com`

È possibile usare `b2clogin.com` al posto di `login.microsoftonline.com` come URL di reindirizzamento. Questa operazione viene eseguita nell'applicazione Azure AD B2C quando si configura un provider di identità per l'iscrizione e l'accesso.

L'uso di `b2clogin.com` nel contesto di `https://your-tenant-name.b2clogin.com/your-tenant-guid` ha gli effetti seguenti:

- I servizi Microsoft utilizzano meno spazio nell'intestazione del cookie.
- Gli URL non includono più un riferimento a Microsoft. Ad esempio, l'applicazione Azure AD B2C probabilmente fa riferimento a `login.microsoftonline.com`.

 Per usare `b2clogin.com`, è necessario aggiornare la configurazione dell'applicazione.  

- Impostare la proprietà **validateAuthority** su `false`, in modo che possano essere eseguiti reindirizzamenti con `b2clogin.com`.

L'esempio seguente illustra come si può impostare la proprietà:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> L'applicazione Azure AD B2C fa probabilmente riferimento a `login.microsoftonline.com` in diverse posizioni, ad esempio nei riferimenti dei flussi utente e negli endpoint di token. Verificare che l'endpoint di autorizzazione, l'endpoint di token e l'autorità di certificazione siano stati aggiornati in modo da usare `your-tenant-name.b2clogin.com`.

Seguire [questo esempio JavaScript MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) per indicazioni su come usare l'anteprima di MSAL per JavaScript (MSAL.js). L'esempio ottiene un token di accesso e chiama un'API protetta da Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalizzazione dell'interfaccia utente](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)