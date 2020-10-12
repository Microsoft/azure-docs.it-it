---
title: Esportazione di un'API ospitata in Azure in PowerApps e Microsoft Flow
description: Panoramica su come esporre un'API ospitata nel servizio app in PowerApps e Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 7d968c62afbfc92952f747e1e7627c98fe07436d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015086"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Esportazione di un'API ospitata in Azure in PowerApps e Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) è un servizio per la creazione e l'uso di applicazioni aziendali personalizzate in grado di connettersi ai dati dell'utente e di funzionare su più piattaforme. [Microsoft Flow](/learn/modules/get-started-with-flow/index) facilita l'automazione dei flussi di lavoro e dei processi aziendali tra le app e i servizi preferiti dagli utenti. Sia PowerApps che Microsoft Flow dispongono di un'ampia varietà di connettori predefiniti a origini di dati come Office 365, Dynamics 365, Salesforce e così via. In alcuni casi, chi crea app e flussi vuole anche connettersi alle origini dati e alle API create dall'organizzazione.

Analogamente, gli sviluppatori che vogliono esporre ulteriormente le proprie API in un'organizzazione possono renderle disponibili a chi crea app e flussi. Questo articolo illustra come esportare un'API compilata con [funzioni di Azure](../azure-functions/functions-overview.md) o [servizio app Azure](../app-service/overview.md). L'API esportata diventa un *connettore personalizzato*, che viene usato in PowerApps e Microsoft Flow proprio come un connettore predefinito.

> [!IMPORTANT]
> La funzionalità di definizione API illustrata in questo articolo è supportata solo per [la versione 1. x del runtime di funzioni di Azure e delle](functions-versions.md#creating-1x-apps) app dei servizi app. La versione 2. x di funzioni si integra con gestione API per creare e gestire definizioni OpenAPI. Per altre informazioni, vedere [creare una definizione openapi per una funzione con gestione API di Azure](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Creare ed esportare una definizione dell'API
Prima di esportare un'API, è necessario descrivere l'API usando una definizione OpenAPI (nota in passato come file [Swagger](https://swagger.io/)). Questa definizione contiene informazioni sulle operazioni disponibili in un'API e su come devono essere strutturati i dati della richiesta e della risposta per l'API. PowerApps e Microsoft Flow possono creare connettori personalizzati per qualsiasi definizione OpenAPI 2.0. Funzioni di Azure e Servizio app di Azure includono supporto predefinito per creazione, hosting e gestione di definizioni OpenAPI. Per altre informazioni, vedere [Ospitare un'API RESTful con CORS in Servizio app di Azure](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> È anche possibile creare connettori personalizzati nell'interfaccia utente di PowerApps e Microsoft Flow, senza usare una definizione OpenAPI. Per altre informazioni, vedere [Registrare e usare connettori personalizzati in PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [Registrare e usare connettori personalizzati in Microsoft Flow](/power-automate/developer/register-custom-api).

Per esportare la definizione dell'API, seguire questi passaggi:

1. Nella [portale di Azure](https://portal.azure.com)passare all'app per le funzioni o a un'applicazione del servizio app.

    Nel menu a sinistra, in **API**, selezionare **definizione API**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Definizione dell'API di Funzioni di Azure":::

2. Il pulsante **Esporta in PowerApps e Microsoft Flow** dovrebbe essere disponibile. In caso contrario, è necessario creare prima una definizione OpenAPI. Selezionare questo pulsante per avviare il processo di esportazione.

    ![Pulsante Esporta in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selezionare la **modalità di esportazione**:

    **Rapida**: consente di creare il connettore personalizzato dall'interno del portale di Azure. È necessario aver effettuato l'accesso in PowerApps o Microsoft Flow e avere l'autorizzazione per creare connettori nell'ambiente di destinazione. Questo approccio è consigliato se questi due requisiti possono essere soddisfatti. Se si usa questa modalità, seguire le istruzioni incluse nella sezione [Usare l'esportazione rapida](#express) di seguito.

    **Manuale**: permette di esportare la definizione dell'API, che può quindi essere importata tramite il portale di PowerApps o di Microsoft Flow. Questo approccio è consigliato se l'utente di Azure e l'utente con l'autorizzazione per la creazione di connettori sono persone diverse o se il connettore deve essere creato in un altro tenant di Azure. Se si usa questa modalità, seguire le istruzioni incluse nella sezione [Usare l'esportazione manuale](#manual) di seguito.

    ![Modalità di esportazione](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Poiché il connettore personalizzato usa una *copia* della definizione dell'API, PowerApps e Microsoft Flow non riconoscono immediatamente se sono state apportate modifiche all'applicazione e alla definizione dell'API associata. Se si apportano modifiche, ripetere i passaggi di esportazione per la nuova versione.

<a name="express"></a>
## <a name="use-express-export"></a>Usare l'esportazione rapida

Per completare l'esportazione in modalità **Rapida**, seguire questi passaggi:

1. Assicurarsi di aver effettuato l'accesso al tenant di PowerApps o Microsoft Flow in cui si vuole eseguire l'esportazione. 

2. Usare le impostazioni specificate nella tabella.

    |Impostazione|Descrizione|
    |--------|------------|
    |**Environment**|Selezionare l'ambiente in cui deve essere salvato il connettore personalizzato. Per altre informazioni, vedere [Panoramica degli ambienti](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome dell'API personalizzata**|Immettere un nome, che gli sviluppatori che usano PowerApps e Microsoft Flow visualizzeranno nel proprio elenco di connettori.|
    |**Preparare la configurazione di sicurezza**|Se necessario, fornire i dettagli di configurazione di sicurezza necessari per concedere agli utenti l'accesso all'API. Questo esempio mostra una chiave API. Per altre informazioni, vedere [Specificare il tipo di autenticazione](#auth) di seguito.|
 
    ![Esportazione rapida in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Fare clic su **OK**. Il connettore personalizzato viene ora creato e aggiunto all'ambiente specificato.

<a name="manual"></a>
## <a name="use-manual-export"></a>Usare l'esportazione manuale

Per completare l'esportazione in modalità **Manuale**, seguire questi passaggi:

1. Fare clic su **Scarica** e salvare il file oppure fare clic sul pulsante Copia e salvare l'URL. Durante l'importazione verrà usato il file di download o l'URL.
 
    ![Esportazione manuale in PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se la definizione dell'API include definizioni di sicurezza, queste definizioni sono denominate nel passaggio #2. Durante l'importazione, PowerApps e Microsoft Flow rileva queste definizioni e richiede informazioni di sicurezza. Raccogliere le credenziali relative a ogni definizione per l'utilizzo nella sezione successiva. Per altre informazioni, vedere [Specificare il tipo di autenticazione](#auth) di seguito.

    ![Sicurezza per l'esportazione manuale](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Questo esempio mostra la definizione di sicurezza con chiave API che è stata inclusa nella definizione OpenAPI.

Dopo aver esportato la definizione dell'API, è necessario importarla per creare un connettore personalizzato in PowerApps e Microsoft Flow. Poiché i connettori personalizzati sono condivisi tra i due servizi, è necessario importare la definizione una sola volta.

Per importare la definizione API in PowerApps e Microsoft Flow, seguire questi passaggi:

1. Passare a [powerapps.com](https://web.powerapps.com) o [flow.microsoft.com](https://flow.microsoft.com).

2. Nell'angolo superiore destro fare clic sull'icona a forma di ingranaggio e quindi su **Connettori personalizzati**.

   ![Icona a forma di ingranaggio nel servizio](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Fare clic su **Crea connettore personalizzato** e quindi su **Importa un file OpenAPI**.

   ![Crea connettore personalizzato](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Immettere un nome per il connettore personalizzato e quindi passare alla definizione OpenAPI esportata e fare clic su **Continua**.

   ![Caricare la definizione OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Nella scheda **Generale** esaminare le informazioni derivate dalla definizione OpenAPI.

5. Se nella scheda **Sicurezza** viene chiesto di fornire i dettagli di autenticazione, immettere i valori appropriati per il tipo di autenticazione. Fare clic su **Continua**.

    ![Scheda Sicurezza](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Questo esempio mostra i campi obbligatori per l'autenticazione con chiave API. I campi differiscono a seconda del tipo di autenticazione.

6. Nella scheda **Definizioni** tutte le operazioni definite nel file OpenAPI vengono popolate automaticamente. Se sono state definite tutte le operazioni necessarie, si può procedere al passaggio successivo. In caso contrario, è possibile aggiungere e modificare qui le operazioni.

    ![Scheda Definizioni](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Questo esempio include un'unica operazione, chiamata `CalculateCosts`. I metadati, come **Descrizione**, provengono tutti dal file OpenAPI.

7. Fare clic su **Crea connettore** nella parte superiore della pagina.

È ora possibile connettersi al connettore personalizzato in PowerApps e Microsoft Flow. Per altre informazioni sulla creazione di connettori nei portali di PowerApps e Microsoft Flow, vedere [Registrare il connettore personalizzato (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [Registrare il connettore personalizzato (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Specificare il tipo di autenticazione

PowerApps e Microsoft Flow supportano una raccolta di provider di identità che forniscono l'autenticazione per i connettori personalizzati. Se l'API richiede l'autenticazione, assicurarsi che venga acquisita come _definizione di sicurezza_ nel documento OpenAPI, come nell'esempio seguente:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Durante l'esportazione, è necessario specificare i valori di configurazione che consentono a PowerApps e Microsoft Flow di autenticare gli utenti.

Questa sezione descrive i tipi di autenticazione supportati in modalità **Rapida**: chiave API, Azure Active Directory e OAuth 2.0 generica. PowerApps e Microsoft Flow supportano anche l'autenticazione di base e OAuth 2.0 per servizi specifici, tra cui Dropbox, Facebook e SalesForce.

### <a name="api-key"></a>Chiave API
Quando si usa una chiave API, agli utenti del connettore viene chiesto di specificare una chiave quando creano una connessione. È necessario specificare il nome della chiave API per aiutarli a identificare la chiave necessaria. Nell'esempio precedente viene usato il nome `API Key (contact meganb@contoso.com)` per indicare agli utenti dove ottenere informazioni sulla chiave API. Per Funzioni di Azure, la chiave è in genere una delle chiavi host, che svolge diverse funzioni all'interno dell'app per le funzioni.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Quando si usa Azure AD, sono necessarie due registrazioni per l'applicazione Azure AD: una per l'API stessa e l'altra per il connettore personalizzato:

- Per configurare la registrazione per l'API, usare la funzionalità di [autenticazione/autorizzazione del servizio app](../app-service/configure-authentication-provider-aad.md).

- Per configurare la registrazione per il connettore, seguire i passaggi riportati in [Aggiunta di un'applicazione Azure AD](../active-directory/develop/quickstart-register-app.md). La registrazione deve avere accesso delegato all'API e un URL di risposta `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Per altre informazioni, vedere gli esempi di registrazione in Azure AD per [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](/connectors/custom-connectors/azure-active-directory-authentication). Questi esempi usano Azure Resource Manager come API. Usare la propria API se si seguono questi passaggi.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client della registrazione in Azure AD del connettore
- **Segreto client** - Il segreto client della registrazione in Azure AD del connettore
- **URL di accesso** - L'URL di base per Azure AD. In Azure, in genere `https://login.windows.net` .
- **ID tenant** - L'ID del tenant da usare per l'accesso. Questo ID deve essere "comune" o l'ID del tenant in cui viene creato il connettore.
- **URL risorsa** - URL della risorsa della registrazione in Azure AD per l'API

> [!IMPORTANT]
> Se un altro utente importa la definizione dell'API in PowerApps e Microsoft Flow nell'ambito del flusso manuale, è necessario fornire all'utente l'ID client e il segreto client della *registrazione del connettore*, nonché l'URL della risorsa dell'API. Assicurarsi che questi segreti siano gestiti in modo sicuro. **Non condividere le credenziali di sicurezza dell'API stessa.**

### <a name="generic-oauth-20"></a>OAuth 2.0 generica
Quando si usa l'autenticazione OAuth 2.0 generica, è possibile eseguire l'integrazione con qualsiasi provider OAuth 2.0. In questo modo è possibile utilizzare provider personalizzati che non sono supportati in modalità nativa.

Sono richiesti i valori di configurazione seguenti:
- **ID client** - L'ID client OAuth 2.0
- **Segreto client** - Il segreto client OAuth 2.0
- **URL dell'autorizzazione** - L'URL dell'autorizzazione OAuth 2.0
- **URL del token** - L'URL del token OAuth 2.0
- **Aggiorna URL** - L'URL di aggiornamento OAuth 2.0
