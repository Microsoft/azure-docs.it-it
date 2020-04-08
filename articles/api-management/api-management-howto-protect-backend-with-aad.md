---
title: Proteggere un'API utilizzando OAuth 2.0 con AAD e Gestione API
titleSuffix: Azure API Management
description: Informazioni su come proteggere il back-end di un'API Web con Azure Active Directory e Gestione API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 300f44daeeea5e8a774575dabcb00686906bb5de
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804368"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API

Questa guida illustra come configurare l'istanza di Gestione API di Azure per proteggere un'API usando il protocollo OAuth 2.0 con Azure Active Directory (Azure AD). 

> [!NOTE]
> Questa funzionalità è disponibile nei livelli **Developer**, **Standard** e **Premium** di Gestione API.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire i passaggi in questo articolo è necessario avere quanto segue:
* Un'istanza di Gestione API
* Un'API in corso di pubblicazione che usa l'istanza di Gestione API
* Un tenant di Azure AD

## <a name="overview"></a>Panoramica

Ecco una rapida panoramica dei passaggi:

1. Registrare un'applicazione (app back-end) in Azure AD per rappresentare l'API.
2. Registrare un'altra applicazione (app client) in Azure AD per rappresentare un'applicazione client che deve chiamare l'API.
3. In Azure AD concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end.
4. Configurare la Console per sviluppatori per chiamare l'API utilizzando l'autorizzazione utente OAuth 2.0.Configure the Developer Console to call the API using OAuth 2.0 user authorization.
5. Aggiungere i criteri **validate-jwt** per convalidare il token OAuth per ogni richiesta in ingresso.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrare un'applicazione in Azure AD per rappresentare l'API

Per proteggere un'API con Azure AD, il primo passaggio consiste nel registrare un'applicazione in Azure AD che rappresenta l'API. 

1. Passare al portale di [Azure](https://portal.azure.com) per registrare l'applicazione. Cercare e selezionare **le registrazioni APP**.

1. Selezionare **Nuova registrazione**. 

1. Quando viene visualizzata la **pagina Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information: 
    - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio *backend-app*. 
    - Nella sezione **Tipi di account supportati** selezionare un'opzione adatta allo scenario. 

1. Lasciare vuota la sezione **URI di reindirizzamento.**

1. Selezionare **Registra** per creare l'applicazione. 

1. Nella pagina **Panoramica ** dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota.

1. Selezionare **Esporre un'API** e impostare **l'URI dell'ID applicazione** con il valore predefinito. Registrare questo valore per un secondo momento.

1. Selezionare il pulsante **Aggiungi ambito** per visualizzare la pagina **Aggiungi ambito.** Creare quindi un nuovo ambito supportato dall'API, `Files.Read`ad esempio ). Infine, selezionare il pulsante **Aggiungi ambito** per creare l'ambito. Ripetere questo passaggio per aggiungere tutti gli ambiti supportati dall'API.

1. Quando gli ambiti vengono creati, prendere nota di essi per l'utilizzo in un passaggio successivo. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrare un'altra applicazione in Azure AD per rappresentare un'applicazione client

Ogni applicazione client che chiama l'API deve anche essere registrata come applicazione in Azure AD. In questo esempio, l'applicazione client è la console per sviluppatori nel portale per sviluppatori di Gestione API. Ecco come registrare un'altra applicazione in Azure AD per rappresentare la console per sviluppatori.

1. Passare al portale di [Azure](https://portal.azure.com) per registrare l'applicazione. Cercare e selezionare **le registrazioni APP**.

1. Selezionare **Nuova registrazione**.

1. Quando viene visualizzata la **pagina Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information: 
    - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio *client-app*. 
    - Nella sezione **Tipi di account supportati** selezionare Account in qualsiasi directory organizzativa (Qualsiasi directory di Azure AD - **Multitenant).** 

1. Nella sezione **URI di reindirizzamento** selezionare `Web` e immettere l'URL `https://contoso5.portal.azure-api.net/signin`.

1. Selezionare **Registra** per creare l'applicazione. 

1. Nella pagina **Panoramica ** dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota.

A questo punto, creare un segreto client per questa applicazione da utilizzare in un passaggio successivo.

1. Nell'elenco delle pagine dell'app client selezionare **Certificati & segreti**e quindi Nuovo **segreto client**.

1. In **Aggiungi segreto client**specificare una **descrizione**. Scegliere quando la chiave deve scadere e selezionare **Aggiungi**.

Quando viene creato il segreto, prendere nota del valore della chiave da utilizzare in un passaggio successivo. 

## <a name="grant-permissions-in-azure-ad"></a>Concedere le autorizzazioni in Azure AD

Ora che sono state registrate due applicazioni per rappresentare l'API e la console per sviluppatori, è necessario concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end.  

1. Passare al portale di [Azure](https://portal.azure.com) per concedere le autorizzazioni all'applicazione client. Cercare e selezionare **le registrazioni APP**.

1. Scegli la tua app client. Quindi, nell'elenco delle pagine per l'app, selezionare **Autorizzazioni API**.

1. Selezionare **Aggiungi un'autorizzazione**.

1. In **Selezionare un'API**selezionare **API**personale , quindi individuare e selezionare l'app back-end.

1. In **Autorizzazioni delegate**selezionare le autorizzazioni appropriate per l'app back-end, quindi selezionare Aggiungi **autorizzazioni**.

1. Facoltativamente, nella pagina **Autorizzazioni API** selezionare ** \<Concedi il consenso amministrativo per il nome del tenant>** concedere il consenso per conto di tutti gli utenti in questa directory. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori

A questo punto sono state create le applicazioni in Azure AD e sono state concesse le autorizzazioni appropriate per consentire all'app client di chiamare l'app back-end. 

In questo esempio la console per sviluppatori è l'app client. La procedura seguente descrive come abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori. 

1. Nel portale di Azure passare all'istanza di Gestione API.

1. Selezionare **OAuth 2.0** > **Aggiungi**.

1. Specificare i valori per i campi **Nome visualizzato** e **Descrizione**.

1. Per **URL della pagina di registrazione del client** immettere un valore segnaposto, ad esempio `http://localhost`. L'URL della **pagina di registrazione client** punta a una pagina che gli utenti possono utilizzare per creare e configurare i propri account per i provider OAuth 2.0 che supportano questa funzionalità. In questo esempio gli utenti non creano e configurano i propri account, quindi si usa un segnaposto.

1. Per **Tipi di concessione di autorizzazione** selezionare **Codice di autorizzazione**.

1. Specificare **URL dell'endpoint autorizzazione** e **URL dell'endpoint token**. Recuperare questi valori nella pagina **Endpoint** nel tenant di Azure AD. Passare di nuovo alla pagina **Registrazioni per l'app** e selezionare **Endpoint**.


1. Copiare il valore di **Endpoint di autorizzazione OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint autorizzazione**. Selezionare **POST** in Metodo di richiesta di autorizzazione.

1. Copiare il valore di **Endpoint token OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint token**. 

    >[!IMPORTANT]
    > È possibile utilizzare gli endpoint **v1** o **v2.** Tuttavia, a seconda della versione scelta, il passaggio seguente sarà diverso. È consigliabile usare gli endpoint v2. 

1. Se si utilizzano endpoint **v1,** aggiungere un parametro del corpo denominato **resource**. Per il valore di questo parametro, usare **l'ID applicazione** dell'app back-end. 

1. Se si usano endpoint **v2,** usare l'ambito creato per l'app back-end nel campo **Ambito predefinito.** Assicurarsi inoltre di impostare [`accessTokenAcceptedVersion`](/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) il `2` valore della proprietà su nel [manifesto dell'applicazione.](/azure/active-directory/develop/reference-app-manifest)

1. Specificare quindi le credenziali del client. Queste sono le credenziali per l'app client.

1. Per **ID client**, utilizzare **l'ID applicazione** dell'app client.

1. Per **Segreto client** usare la chiave creata in precedenza per l'app client. 

1. Immediatamente dopo il segreto client è riportato il valore di **redirect_url** per il tipo di concessione con codice di autorizzazione. Prendere nota dell'URL.

1. Selezionare **Create** (Crea).

1. Tornare all'app client e selezionare **Autenticazione**.

1. In URI di **reindirizzamento**selezionare il tipo come **Web**, incollare il **redirect_url** in **URI di reindirizzamento**e quindi salvare.

Ora che è stato configurato un server di autorizzazione OAuth 2.0, la console per sviluppatori può ottenere i token di accesso da Azure AD. 

Il passaggio successivo consiste nell'abilitare l'autorizzazione utente OAuth 2.0 per l'API. In questo modo, la console per sviluppatori sa che deve ottenere un token di accesso per conto dell'utente prima di eseguire le chiamate all'API.

1. Passare all'istanza di Gestione API e quindi ad **API**.

2. Selezionare l'API da proteggere. Ad esempio, è `Echo API`possibile utilizzare il file .

3. Passare a **Settings**.

4. In **Sicurezza** scegliere **OAuth 2.0** e selezionare il server OAuth 2.0 configurato in precedenza. 

5. Selezionare **Salva**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Eseguire la chiamata all'API dal portale per sviluppatori

> [!NOTE]
> Questa sezione non si applica al livello **Consumo**, che non supporta il portale per sviluppatori.

Ora che l'autorizzazione utente OAuth 2.0 è abilitata sull'API, la Developer Console otterrà un token di accesso per conto dell'utente, prima di chiamare l'API.

1. Passare a qualsiasi operazione nell'API nel portale per sviluppatori e selezionare **Prova.** Verrà visualizzata la console per sviluppatori.

2. Si noti un nuovo elemento nella sezione **Autorizzazione,** corrispondente al server di autorizzazione appena aggiunto.

3. Selezionare **Codice di autorizzazione** nell'elenco a discesa delle autorizzazioni. Verrà chiesto di accedere al tenant di Azure AD. Se si è già eseguito l'accesso con l'account, è possibile che la richiesta non venga visualizzata.

4. Dopo l'accesso, alla richiesta viene aggiunta un'intestazione `Authorization` con un token di accesso di Azure AD. Di seguito è riportato un token di esempio (con codifica Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selezionare **Invia** e quindi sarà possibile chiamare l'API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurare criteri di convalida JWT per preautorizzare le richieste

A questo punto, quando un utente prova a eseguire una chiamata dalla console per sviluppatori, viene chiesto di eseguire l'accesso. La Console per sviluppatori ottiene un token di accesso per conto dell'utente e include il token nella richiesta effettuata all'API.

Tuttavia, cosa succede se qualcuno chiama l'API senza un token o con un token non valido? Ad esempio, provare a chiamare `Authorization` l'API senza l'intestazione, la chiamata verrà comunque passare attraverso. Questo avviene perché Gestione API non convalida il token di accesso a questo punto. Passa invece semplicemente l'intestazione `Authorization` all'API back-end.

È possibile usare i criteri di [convalida JWT](api-management-access-restriction-policies.md#ValidateJWT) per pre-autorizzare le richieste in Gestione API convalidando i token di accesso di ogni richiesta in ingresso. Se una richiesta non ha un token valido, Gestione API la blocca. Ad esempio, aggiungere il `<inbound>` criterio seguente `Echo API`alla sezione dei criteri della sezione . In questo modo, viene verificata l'attestazione dei destinatari in un token di accesso e viene restituito un messaggio di errore se il token non è valido. Per informazioni su come configurare i criteri, vedere [Impostare o modificare criteri](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```
> [!NOTE]
> Questo `openid-config` URL corrisponde all'endpoint v1. Per l'endpoint v2, `openid-config`utilizzare `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Creare un'applicazione per chiamare l'API

In questa guida è stata usata la console per sviluppatori di Gestione API come applicazione client di esempio per chiamare `Echo API` a cui è applicata la protezione tramite OAuth 2.0. Per altre informazioni su come creare un'applicazione e implementare OAuth 2.0, vedere [Esempi di codice di Azure Active Directory](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

* [Creare un'istanza](get-started-create-service-instance.md)del servizio Gestione API.

* [Gestire la prima API](import-and-publish.md).
