---
title: Registrare un'applicazione SaaS | Azure Marketplace
description: Viene illustrato come registrare un'applicazione SaaS usando il portale di Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275714"
---
# <a name="register-a-saas-application"></a>Registrare un'applicazione SaaS

Questo articolo illustra come registrare un'applicazione SaaS usando il [portale di Azure](https://portal.azure.com/)Microsoft.  Al termine della registrazione, si riceverà un token di sicurezza Azure Active Directory (Azure AD) che è possibile usare per accedere alle API di evasione SaaS.  Per ulteriori informazioni su Azure AD, vedere [che cos'è l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Flusso di autenticazione da servizio a servizio

Il diagramma seguente mostra il flusso di sottoscrizione di un nuovo cliente e quando vengono usate queste API:

![Flusso di API dell'offerta SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure non impone i vincoli per l'autenticazione che il servizio SaaS espone ai propri utenti finali. Tuttavia, l'autenticazione con le API di evasione SaaS viene eseguita con un token di sicurezza Azure AD, in genere ottenuto registrando l'app SaaS tramite il portale di Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registrare un'app protetta da Azure AD

Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione di dettagli di Azure AD sull'applicazione, ad esempio l'URL in cui si trova, l'URL per inviare risposte dopo che un utente viene autenticato, l'URI che identifica l'app e così via.  Per registrare una nuova applicazione nel portale di Azure, seguire i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Se l'account consente di accedere a più di uno, fare clic sull'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3.  Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**.

    ![Registrazioni di App SaaS AD](./media/saas-offer-app-registration-v1.png)

4.  Nella pagina Crea, immettere le informazioni di registrazione\' dell'applicazione:
    -   **Nome:** Immettere un nome significativo per l'applicazione
    -   **Tipo di applicazione**: 
        - Selezionare **nativo** per [le applicazioni client](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) installate localmente in un dispositivo. Questa impostazione viene usata per i [client nativi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth pubblici.
        - Selezionare **app Web/API** per [le applicazioni client](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [le applicazioni di risorse/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) installate in un server protetto. Questa impostazione viene usata per [client Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth riservati e [client pubblici basati su agenti utente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        La stessa applicazione può anche esporre sia un'API client che un'API di risorse.
    -   **URL di accesso**: per le applicazioni API/app Web, specificare l'URL di base dell'app. Ad esempio, **http://localhost:31544** può essere l'URL di un'app Web in esecuzione nel computer locale. Gli utenti possono quindi usare questo URL per accedere a un'applicazione client Web.
    -   **URI di reindirizzamento**: per le applicazioni native, fornire l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico per l'applicazione, ad esempio **http://MyFirstAADApp**.

        ![Registrazioni di App SaaS AD](./media/saas-offer-app-registration-v1-2.png)

        Per esempi specifici per le applicazioni Web o le applicazioni native, vedere le configurazioni guidate introduttive disponibili nella sezione *Introduzione* della [guida per sviluppatori Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Al termine, fare clic su **Crea**. Azure AD assegna un *ID applicazione* univoco all'applicazione e viene\'nuovamente eseguita la pagina di registrazione principale dell'\'applicazione. A seconda che si tratti di un'applicazione Web o nativa, sono fornite opzioni diverse per l'aggiunta di altre funzionalità all'applicazione.

>[!Note]
>Per impostazione predefinita, l'applicazione appena registrata viene configurata in modo da consentire solo agli utenti dello stesso tenant di accedere all'applicazione stessa.


## <a name="using-the-azure-ad-security-token"></a>Uso del token di sicurezza Azure AD

Dopo aver registrato l'applicazione, è possibile richiedere a livello di codice un token di sicurezza Azure AD.  Il server di pubblicazione deve usare questo token ed effettuare una richiesta per risolverlo.  Quando si usano le varie API di evasione, il parametro di query del token si trova nell'URL quando l'utente viene reindirizzato al sito Web SaaS da Azure.  Questo token è valido solo per un'ora.  Inoltre, è consigliabile decodificare l'URL del valore del token dal browser prima di usarlo.

Per ulteriori informazioni su questi token, vedere [Azure Active Directory token di accesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Ottenere un token basato sull'app Azure AD

Metodo HTTP

`POST`

*URL richiesta*

**https://login.microsoftonline.com/*{tenantId}*/OAuth2/token**

*Parametro URI*

|  **Nome parametro**  | **Richiesto**  | **Descrizione**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| TenantId             | True          | ID tenant dell'applicazione AAD registrata   |
|  |  |  |


*Intestazione della richiesta*

|  **Nome dell'intestazione**  | **Richiesto** |  **Descrizione**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Tipo di contenuto associato alla richiesta. Il valore predefinito è `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo della richiesta*

| **Nome proprietà**   | **Richiesto** |  **Descrizione**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Tipo di concessione. Il valore predefinito è `client_credentials`.                    |
|  Client_id          | True         |  Identificatore del client/app associato all'app di Azure AD.                  |
|  client_secret      | True         |  Password associata all'app di Azure AD.                               |
|  Risorsa           | True         |  Risorsa di destinazione per cui è richiesto il token. Il valore predefinito è `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Risposta*

|  **Nome**  | **Type**       |  **Descrizione**    |
| ---------- | -------------  | ------------------- |
| 200 - OK    | TokenResponse  | La richiesta ha avuto esito positivo   |
|  |  |  |

*TokenResponse*

Token di risposta di esempio:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Passaggi successivi

L'app protetta da Azure AD ora può usare l' [API di evasione Saas versione 2](./pc-saas-fulfillment-api-v2.md).
