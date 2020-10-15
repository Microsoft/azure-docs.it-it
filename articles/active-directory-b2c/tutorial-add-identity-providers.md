---
title: 'Esercitazione: Aggiungere provider di identità alle app'
titleSuffix: Azure AD B2C
description: Seguire questa esercitazione per informazioni su come aggiungere i provider di identità alle applicazioni in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f9abf9105da773ec5f8321c0f8e70e20516618c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922150"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C

È possibile consentire agli utenti di accedere alle applicazioni con provider di identità diversi. Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. È possibile aggiungere provider di identità supportati da Azure Active Directory B2C (Azure AD B2C) ai propri [flussi utente](user-flow-overview.md) usando il portale di Azure.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant, sia in Facebook che in Azure Active Directory
> * Aggiungere i provider di identità al flusso utente

In genere si usa un solo provider di identità nelle applicazioni, ma è possibile aggiungerne altri. Questa esercitazione illustra come aggiungere un provider di identità di Azure AD e un provider di identità di Facebook all'applicazione. L'aggiunta di entrambi i provider di identità all'applicazione è facoltativa. Si possono aggiungere anche altri provider di identità, come [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md) o [Twitter](identity-provider-twitter.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="create-applications"></a>Creare applicazioni

Le applicazioni di provider di identità forniscono l'identificatore e la chiave necessari per consentire la comunicazione con il tenant di Azure AD B2C. In questa sezione dell'esercitazione si creeranno un'applicazione di Azure AD e un'applicazione di Facebook da cui ottenere gli identificatori e le chiavi per aggiungere i provider di identità al tenant. Se si intende aggiungere solo uno dei provider di identità, occorre creare solo l'applicazione per tale provider.

### <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Per consentire agli utenti di accedere da Azure AD, è necessario registrare un'applicazione dall'interno del tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un nome per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Accettare la selezione di **Account solo in questa directory dell'organizzazione** per questa applicazione.
1. Per **URI di reindirizzamento** accettare il valore **Web** e immettere l'URL seguente, interamente in lettere minuscole, sostituendo `your-B2C-tenant-name` con il nome del tenant di Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

1. Selezionare **Registra**, quindi registrare l'**ID applicazione (client)** che verrà usato in un passaggio successivo.
1. In **Gestisci** nel menu dell'applicazione selezionare **Certificati e segreti**, quindi selezionare **Nuovo segreto client**.
1. Immettere una **Descrizione** per il segreto client. Ad esempio: `Azure AD B2C App Secret`.
1. Selezionare il periodo di scadenza. Per questa applicazione, accettare la selezione di **Tra 1 anno**.
1. Selezionare **Aggiungi**, quindi registrare il valore del nuovo segreto client che verrà usato in un passaggio successivo.

### <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come provider di identità in Azure AD B2C, è necessario creare un'applicazione di Facebook. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com/).

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
1. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, selezionare **Accedi** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
1. Selezionare **Le mie app** e quindi **Creazione di un'app**.
1. Inserire un **nome visualizzato** e una **email di contatto** valida.
1. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
1. Selezionare **Impostazioni** > **Base**.
1. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non è usato per Azure AD B2C.
1. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
1. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant.
1. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com/`. L'URL dell'informativa sulla privacy è una pagina da gestire per fornire le informazioni sulla privacy per l'applicazione.
1. Selezionare **Save changes** (Salva modifiche).
1. Nella parte superiore della pagina prendere nota del valore di **ID app**.
1. Accanto a **Segreto app** selezionare **Mostra** e prendere nota del valore. L'ID app e il segreto app sono entrambi necessari per configurare Facebook come provider di identità nel tenant. **Segreto app** è un'importante credenziale di sicurezza che deve essere archiviata in modo sicuro.
1. Selezionare il segno + accanto a **PRODOTTI**, quindi selezionare **Configura** sotto **Facebook Login** (Accesso a Facebook).
1. In **Facebook Login** (Accesso a Facebook) nel menu a sinistra selezionare **Impostazioni**.
1. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
1. Per rendere disponibile l'applicazione Facebook per Azure AD B2C, fare clic sul selettore **Stato** nella parte superiore destra della pagina e impostarlo su **Sì** per rendere pubblica l'applicazione e quindi fare clic su **Conferma**. A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="add-the-identity-providers"></a>Aggiungere i provider di identità

Dopo aver creato l'applicazione per il provider di identità che si vuole aggiungere, occorre aggiungere il provider di identità al tenant.

### <a name="add-the-azure-active-directory-identity-provider"></a>Aggiungere il provider di identità di Azure Active Directory

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. In **URL dei metadati** immettere l'URL seguente, sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Per **ID client**, immettere l'ID applicazione registrato in precedenza.
1. In **Segreto client** immettere il segreto client registrato in precedenza.
1. Lasciare i valori predefiniti per **Ambito**, **Tipo di risposta** e **Modalità di risposta**.
1. (Facoltativo) Immettere un valore per **Domain_hint**. Ad esempio, *ContosoAD*. Gli [hint di dominio](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) sono direttive incluse nella richiesta di autenticazione da un'applicazione. Possono essere usate per accelerare l'indirizzamento dell'utente alla rispettiva pagina di accesso dell'IdP federato. Oppure possono essere usati da un'applicazione multi-tenant per accelerare l'utente direttamente verso la pagina di accesso di Azure AD personalizzata per il suo tenant.
1. In **Mapping delle attestazioni del provider di identità** immettere i valori dei mapping delle attestazioni seguenti:

    * **ID utente**: *oid*
    * **Nome visualizzato**: *name*
    * **Nome**: *given_name*
    * **Cognome**: *family_name*
    * **Indirizzo di posta elettronica**: *unique_name*

1. Selezionare **Salva**.

### <a name="add-the-facebook-identity-provider"></a>Aggiungere il provider di identità Facebook

1. Selezionare **Provider di identità** e quindi selezionare **Facebook**.
1. Immettere un **Nome**. Ad esempio, *Facebook*.
1. Per **ID client**, immettere l'ID app dell'applicazione Facebook creata in precedenza.
1. Per **Segreto client** immettere il Segreto app annotato in precedenza.
1. Selezionare **Salva**.

## <a name="update-the-user-flow"></a>Aggiornare il flusso utente

Nell'esercitazione completata come parte dei prerequisiti è stato creato un flusso utente per l'iscrizione e l'accesso denominato *B2C_1_signupsignin1*. In questa sezione si aggiungeranno i provider di identità al flusso utente *B2C_1_signupsignin1*.

1. Selezionare **Flussi utente** e quindi il flusso utente *B2C_1_signupsignin1*.
2. Selezionare **Provider di identità** e quindi selezionare i provider di identità **Facebook** e **Contoso Azure AD** aggiunti.
3. Selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente** e quindi accedere con un provider di identità aggiunto in precedenza.
1. Ripetere i passaggi da 1 a 3 per gli altri provider di identità aggiunti.

Se l'operazione di accesso ha esito positivo, si verrà reindirizzati a `https://jwt.ms`, che visualizza il token decodificato, simile a:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare le applicazioni dei provider di identità
> * Aggiungere i provider di identità al tenant
> * Aggiungere i provider di identità al flusso utente

Successivamente, verrà illustrato come personalizzare l'interfaccia utente delle pagine visualizzate agli utenti come parte dell'esperienza di identità nelle applicazioni:

> [!div class="nextstepaction"]
> [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md)
