---
title: Nuova esperienza di registrazione di app nel portale di Azure
titleSuffix: Microsoft identity platform
description: Introduzione alla nuova esperienza di registrazione delle app nel portale di Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154594"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Nuova esperienza di registrazione delle app portale di Azure

Sono stati apportati numerosi miglioramenti alla nuova esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Se si ha familiarità con l'esperienza del portale di registrazione delle applicazioni (apps.dev.microsoft.com) per la registrazione o la gestione di applicazioni convergenti, denominate esperienza precedente, questa guida alla formazione consente di iniziare a usare la nuova esperienza.

## <a name="whats-not-changing"></a>Cosa non cambia?

- Le applicazioni e le configurazioni correlate possono essere trovate così come sono nella nuova esperienza. Non è necessario registrare di nuovo le applicazioni e gli utenti delle applicazioni non dovranno eseguire di nuovo l'accesso.

    > [!NOTE]
    > È necessario accedere con l'account usato per registrare le applicazioni per trovarle nel portale di Azure. Si consiglia di controllare che l'utente connesso nella portale di Azure corrisponda all'utente che ha eseguito l'accesso al portale di registrazione delle applicazioni confrontando l'indirizzo di posta elettronica dal profilo.
    >
    > In alcuni casi, in particolare quando si effettua l'accesso con account Microsoft personali (ad esempio Outlook, Live, Xbox e così via) con un indirizzo di posta elettronica Azure AD, si è scoperto che, quando si passa alla portale di Azure dalla vecchia esperienza, si accede a un account diverso con lo stesso messaggio di posta elettronica nel tenant di Azure AD. Se si ritiene che le applicazioni siano mancanti, disconnettersi e accedere con l'account corretto.

- Le app Live SDK create con account Microsoft personali non sono ancora supportate nella portale di Azure e continueranno a rimanere nella vecchia esperienza nel prossimo futuro.

## <a name="key-changes"></a>Modifiche principali

-   Nell'esperienza precedente, le app sono state registrate per impostazione predefinita come app convergenti che supportano tutti gli account aziendali (multi-tenant) e gli account Microsoft personali. Questa operazione non può essere modificata tramite la vecchia esperienza, rendendo difficile la creazione di app che supportano solo account aziendali (multi-tenant o tenant singolo).
    La nuova esperienza consente di registrare le app che supportano tutte queste opzioni. [Altre informazioni sui tipi di app](active-directory-v2-registration-portal.md).

-   Nella nuova esperienza, se il account Microsoft personale si trova anche in un tenant Azure AD, vengono visualizzate tre schede, tutte le applicazioni nel tenant, le applicazioni di proprietà del tenant e le applicazioni dell'account personale. Quindi, se si ritiene che le app registrate con i account Microsoft personali risultano mancanti, controllare le **applicazioni dalla scheda dell'account personale** .

-   Nella nuova esperienza è possibile passare facilmente tra i tenant passando al profilo e scegliendo Cambia directory.

## <a name="list-of-applications"></a>Elenco delle applicazioni

-   Il nuovo elenco di app mostra le applicazioni registrate tramite l'esperienza di registrazione delle app legacy nel portale di Azure (app che hanno effettuato l'accesso solo Azure AD account) e le app registrate nel [portale di registrazione](https://apps.dev.microsoft.com/) delle applicazioni (app che firmano sia Azure ad che Personal account Microsoft).

-   Il nuovo elenco di app include due colonne aggiuntive: **create sulla** colonna e sui **certificati & colonna Secrets** che mostra lo stato (corrente, scadenza imminente o scaduta) delle credenziali registrate nell'app.

## <a name="new-app-registration"></a>Registrazione nuova applicazione

Nell'esperienza precedente, per registrare un'app convergente, è necessario specificare solo un nome. Le app create sono state registrate come app convergenti che supportano tutte le directory dell'organizzazione (multi-tenant) e gli account Microsoft personali.  Questa operazione non può essere modificata tramite la vecchia esperienza, rendendo difficile la creazione di app che supportano solo account aziendali (multi-tenant o tenant singolo). [Altre informazioni sui tipi di account supportati](v2-supported-account-types.md)

Nella nuova esperienza è necessario fornire un nome per l'app e scegliere i tipi di conto supportati. Facoltativamente, è possibile specificare un URI di reindirizzamento.
Se si specifica un URI di reindirizzamento, è necessario specificare se è Web/pubblico (nativo/mobile e desktop). Per altre informazioni su come registrare un'app usando la nuova esperienza di registrazione delle app, vedere [questa Guida introduttiva](quickstart-register-app.md).

## <a name="app-management-page"></a>Pagina di gestione delle app

L'esperienza precedente aveva una singola pagina di gestione delle app per le app con convergenza con le sezioni seguenti: proprietà, segreti dell'applicazione, piattaforme, proprietari, autorizzazioni Microsoft Graph, profilo e opzioni avanzate.

La nuova esperienza nell'portale di Azure rappresenta queste funzionalità in pagine separate. Qui è possibile trovare la funzionalità equivalente:

-   Properties: il nome e l'ID applicazione si trova nella pagina Overview.

-   I segreti dell'applicazione si trova nella pagina certificati & segreti

-   La configurazione delle piattaforme si trova nella pagina di autenticazione

-   Microsoft Graph autorizzazioni si trova nella pagina autorizzazioni API insieme ad altre autorizzazioni

-   Il profilo è nella pagina Personalizzazione

-   Opzione avanzata-il supporto Live SDK si trova nella pagina di autenticazione.

## <a name="application-secretscertificates--secrets"></a>Segreti dell'applicazione/certificati & segreti

Nella nuova esperienza, i **segreti dell'applicazione** sono stati rinominati in **certificati & segreti**. Inoltre, le **chiavi pubbliche** sono denominate **certificati** e le **password** sono denominate **segreti client**. Abbiamo scelto di non introdurre questa funzionalità insieme alla nuova esperienza per motivi di sicurezza, pertanto non è più possibile generare una nuova coppia di chiavi.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Piattaforme/autenticazione: URL di risposta/URI di Reindirizzamento
Nell'esperienza precedente, un'app aveva una sezione Platforms per l'API Web, nativa e Web per configurare gli URL di reindirizzamento, l'URL di disconnessione e il flusso implicito.

Nella nuova esperienza è possibile trovare gli URL di risposta nella sezione relativa\'all'autenticazione di un'app. Vengono inoltre definiti URI di reindirizzamento e il formato degli URI di reindirizzamento è stato modificato. È necessario che siano associati a un tipo di app (Web o client pubblico-mobile e desktop). [Altre informazioni](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Le API Web sono configurate nella pagina esporre un'API.

> [!NOTE]
> Provare la nuova esperienza per le Impostazioni di autenticazione che consente di configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione. [Altre informazioni](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Autorizzazioni Microsoft Graph/autorizzazioni API

-   Quando si seleziona un'API nell'esperienza precedente, è possibile scegliere tra Microsoft Graph solo API. Nella nuova esperienza è possibile scegliere tra molte API Microsoft, tra cui Microsoft Graph, API della propria organizzazione e API, che vengono presentate in tre schede: API Microsoft, API utilizzate dall'organizzazione o API personali. La barra di ricerca sulle API utilizzate dall'organizzazione cerca le entità servizio nel tenant.

    > [!NOTE]
    > Questa scheda non viene visualizzata se l'applicazione non è associata a un tenant. Per altre informazioni su come richiedere le autorizzazioni usando la nuova esperienza, vedere [questa Guida introduttiva](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   L'esperienza precedente non disponeva di un pulsante **Concedi autorizzazioni** . Nella nuova esperienza è disponibile una sezione di consenso alla concessione con un pulsante di **autorizzazione Concedi amministratore** nella sezione autorizzazioni API di un'app. Solo un amministratore può concedere il consenso e questo pulsante è abilitato solo per gli amministratori. Quando un amministratore seleziona il pulsante di **autorizzazione Concedi amministratore** , viene concesso il consenso dell'amministratore a tutte le autorizzazioni richieste.

## <a name="profile"></a>Profilo
Nell'esperienza precedente, il profilo aveva il logo, l'URL della Home page, l'URL delle condizioni di servizio e la configurazione URL dell'informativa sulla privacy. Nella nuova esperienza sono disponibili nella pagina personalizzazione.

## <a name="application-manifest"></a>Manifesto dell'applicazione
Nella pagina manifesto della nuova esperienza è possibile modificare e aggiornare gli attributi dell'app. Per altre informazioni, vedere [Manifesto dell'applicazione](reference-app-manifest.md).

## <a name="new-ui"></a>Nuova interfaccia utente
È disponibile una nuova interfaccia utente per le proprietà che in precedenza potevano essere impostate solo tramite l'editor del manifesto o l'API oppure non esistevano.

-   Il flusso di concessione implicito (oauth2AllowImplicitFlow) si trova nella pagina di autenticazione. Diversamente dall'esperienza precedente, è possibile abilitare i token di accesso o i token ID o entrambi.

-   Gli ambiti definiti da questa API (oauth2Permissions) e dalle applicazioni client autorizzate (preAuthorizedApplications) possono essere configurati tramite la pagina esporre un'API. Per altre informazioni su come configurare un'app in modo che sia un'API Web ed esporre le autorizzazioni e gli ambiti, vedere [questa Guida introduttiva](quickstart-configure-app-expose-web-apis.md).

-   Il dominio del server di pubblicazione (visualizzato agli utenti [nella\'richiesta di consenso dell'applicazione](application-consent-experience.md)) è disponibile nella pagina del pannello personalizzazione. Per ulteriori informazioni su come configurare un dominio del server di pubblicazione, vedere [questa procedura](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitazioni

La nuova esperienza presenta le limitazioni seguenti:

-   La nuova esperienza non supporta ancora Registrazioni app per Azure AD B2C tenant.

-   La nuova esperienza non supporta ancora le app Live SDK create con account Microsoft personali.

-   La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app, a meno\'che non si ripassi da Azure ad a tenant singolo e multi-tenant.

   > [!NOTE]
   > Se si è un utente account Microsoft personale in Azure AD tenant e l'amministratore del tenant ha accesso limitato ai portale di Azure, è possibile che venga negato l'accesso. Tuttavia, se si passa attraverso il collegamento digitando Registrazioni app nella barra di ricerca o aggiungendola, sarà possibile accedere alla nuova esperienza.
