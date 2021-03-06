---
title: Registrazione dell'app per le API Web protetta | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta e le informazioni necessarie per registrare l'app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dcfedf2cceddb59d456d421c4846f3cd252a65b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651865"
---
# <a name="protected-web-api-app-registration"></a>API Web protetta: registrazione dell'app

Questo articolo illustra le specifiche della registrazione dell'app per un'API Web protetta.

Per i passaggi comuni per la registrazione di un'app, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Versione del token accettata

La piattaforma di identità Microsoft può emettere token v 1.0 e token v 2.0. Per altre informazioni su questi token, vedere [token di accesso](access-tokens.md).

La versione del token che l'API può accettare dipende dalla selezione dei **tipi di account supportati** quando si crea la registrazione dell'applicazione API web nel portale di Azure.

- Se il valore dei **tipi di account supportati** è **account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**, la versione del token accettata deve essere v 2.0.
- In caso contrario, la versione del token accettata può essere v 1.0.

Dopo aver creato l'applicazione, è possibile determinare o modificare la versione del token accettato attenendosi alla seguente procedura:

1. Nella portale di Azure selezionare l'app e quindi selezionare **manifesto**.
1. Individuare la proprietà **accessTokenAcceptedVersion** nel manifesto.
1. Il valore specifica di Azure Active Directory (Azure AD) la versione del token accettata dall'API Web.
    - Se il valore è 2, l'API Web accetta i token v 2.0.
    - Se il valore è **null**, l'API Web accetta i token v 1.0.
1. Se è stata modificata la versione del token, selezionare **Salva**.

> [!NOTE]
> L'API Web specifica la versione del token accettata. Quando un client richiede un token per l'API Web dalla piattaforma di identità Microsoft, il client ottiene un token che indica la versione del token accettata dall'API Web.

## <a name="no-redirect-uri"></a>Nessun URI di Reindirizzamento

Le API Web non devono registrare un URI di reindirizzamento perché nessun utente è connesso in modo interattivo.

## <a name="exposed-api"></a>API esposta

Altre impostazioni specifiche delle API Web sono l'API esposta e gli ambiti o i ruoli dell'app esposti.

### <a name="application-id-uri-and-scopes"></a>URI e ambiti dell'ID applicazione

Gli ambiti in genere hanno il formato `resourceURI/scopeName` . Per Microsoft Graph, gli ambiti dispongono di tasti di scelta rapida. Ad esempio, `User.Read` è un tasto di scelta rapida per `https://graph.microsoft.com/user.read` .

Durante la registrazione dell'app, definire i parametri seguenti:

- URI della risorsa
- Uno o più ambiti
- Uno o più ruoli dell'app

Per impostazione predefinita, il portale di registrazione delle applicazioni consiglia di usare l'URI della risorsa `api://{clientId}` . Questo URI è univoco ma non leggibile. Se si modifica l'URI, verificare che il nuovo valore sia univoco. Il portale di registrazione delle applicazioni assicurerà di usare un [dominio di pubblicazione configurato](howto-configure-publisher-domain.md).

Per le applicazioni client, gli ambiti vengono visualizzati come autorizzazioni *delegate* e i ruoli app vengono visualizzati come *autorizzazioni dell'applicazione* per l'API Web.

Gli ambiti vengono visualizzati anche nella finestra di consenso presentata agli utenti dell'app. Fornire pertanto le stringhe corrispondenti che descrivono l'ambito:

- Come osservato da un utente.
- Come osservato da un amministratore del tenant, che può concedere il consenso dell'amministratore.

I ruoli dell'app non possono essere consentiti da un utente (perché vengono usati da un'applicazione che chiama l'API Web per conto di se stesso). Un amministratore tenant deve fornire il consenso alle applicazioni client dell'API Web che espone i ruoli dell'app. Per informazioni dettagliate, vedere il [consenso dell'amministratore](v2-admin-consent.md)

### <a name="exposing-delegated-permissions-scopes"></a>Esposizione delle autorizzazioni delegate (ambiti)

1. Selezionare **esporre un'API** nella registrazione dell'applicazione.
1. Selezionare **Aggiungi un ambito**.
1. Se richiesto, accettare l'URI dell'ID applicazione proposto ( `api://{clientId}` ) selezionando **Salva e continua**.
1. Specificare i valori seguenti:
    - Selezionare **nome ambito** e immettere **access_as_user**.
    - Selezionare **chi può** dare il consenso e assicurarsi che **gli amministratori e gli utenti** siano selezionati.
    - Selezionare **nome visualizzato consenso amministratore** e immettere **Access TodoListService come utente**.
    - Selezionare la **Descrizione del consenso dell'amministratore** e immettere **accedere all'API Web TodoListService come utente**.
    - Selezionare **nome visualizzato consenso utente** e immettere **Access TodoListService come utente**.
    - Selezionare **Descrizione consenso utente** e immettere **accessi all'API Web TodoListService come utente**.
    - Mantieni il valore **stato** impostato su **abilitato**.
 1. Selezionare **Aggiungi ambito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se l'API Web viene chiamata da un'app daemon

Questa sezione illustra come registrare l'API Web protetta in modo che le app daemon possano chiamarla in modo sicuro.

- Si dichiarano ed espongono solo le *autorizzazioni dell'applicazione* perché le app daemon non interagiscono con gli utenti. Le autorizzazioni delegate non hanno senso.
- Gli amministratori tenant possono richiedere Azure AD per rilasciare i token dell'API Web solo per le applicazioni che hanno effettuato la registrazione per accedere a una delle autorizzazioni dell'applicazione dell'API.

#### <a name="exposing-application-permissions-app-roles"></a>Esposizione delle autorizzazioni dell'applicazione (ruoli app)

Per esporre le autorizzazioni dell'applicazione, modificare il manifesto.

1. Nella registrazione dell'applicazione per l'applicazione selezionare **manifesto**.
1. Per modificare il manifesto, trovare l' `appRoles` impostazione e aggiungere i ruoli applicazione. Le definizioni di ruolo sono disponibili nel blocco JSON di esempio seguente.
1. Lasciare `allowedMemberTypes` impostato su `"Application"` only.
1. Assicurarsi che `id` sia un GUID univoco.
1. Assicurarsi `displayName` che `value` non contengano spazi.
1. Salvare il manifesto.

Nell'esempio seguente viene illustrato il contenuto di `appRoles` , in cui il valore di `id` può essere un GUID univoco.

```json
"appRoles": [
  {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
  }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Verifica che Azure AD rilascia i token per l'API Web solo ai client autorizzati

L'API Web verifica il ruolo app. Questo ruolo è uno sviluppatore software per esporre le autorizzazioni dell'applicazione. È anche possibile configurare Azure AD per emettere token API solo per le app approvate dall'amministratore del tenant per l'accesso all'API.

Per aggiungere questa maggiore sicurezza:

1. Passare alla pagina di **Panoramica** dell'app per la registrazione dell'app.
1. **In applicazione gestita in directory locale** selezionare il collegamento con il nome dell'app. L'etichetta per questa selezione potrebbe essere troncata. Ad esempio, è possibile che venga visualizzata l' **applicazione gestita in...**

   > [!NOTE]
   >
   > Quando si seleziona questo collegamento, si passa alla pagina **Panoramica dell'applicazione aziendale** . Questa pagina è associata all'entità servizio per l'applicazione nel tenant in cui è stata creata. È possibile accedere alla pagina di registrazione dell'app usando il pulsante indietro del browser.

1. Selezionare la pagina **Proprietà** nella sezione **Gestisci** delle pagine dell'applicazione aziendale.
1. Se si desidera Azure AD per consentire l'accesso all'API Web solo da determinati client, impostare **assegnazione utente obbligatoria?** su **Sì**.

   > [!IMPORTANT]
   >
   > Se si imposta **assegnazione utente obbligatoria?** su **Sì**, Azure ad controlla le assegnazioni di ruolo dell'app di un client quando richiede un token di accesso all'API Web. Se il client non è assegnato ad alcun ruolo app, Azure AD restituirà il messaggio di errore "invalid_client: AADSTS501051: l'applicazione \<application name\> non è assegnata a un ruolo per \<web API\> ".
   >
   > Se si mantiene l' **assegnazione utente obbligatoria?** impostare su **No**, Azure ad non verificherà le assegnazioni di ruolo dell'app quando un client richiede un token di accesso per l'API Web. Qualsiasi client daemon, ovvero qualsiasi client che usa il flusso di credenziali client, può ottenere un token di accesso per l'API semplicemente specificandone i destinatari. Qualsiasi applicazione può accedere all'API senza dover richiedere le autorizzazioni.
   >
   > Tuttavia, come illustrato nella sezione precedente, l'API Web può sempre verificare che l'applicazione disponga del ruolo corretto, che è autorizzato dall'amministratore del tenant. L'API esegue questa verifica convalidando che il token di accesso disponga di un'attestazione Roles e che il valore di questa attestazione sia corretto. Nell'esempio JSON precedente, il valore è `access_as_application` .

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, configurazione del [codice dell'app](scenario-protected-web-api-app-configuration.md).
