---
title: Panoramica di Microsoft Identity Platform - Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui componenti di Microsoft Identity Platform e su come usarli per il supporto della gestione delle identità e degli accessi (IAM) nelle applicazioni.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: a4ce8242bd3110fee038ac826973e6a134413344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426842"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Che cos'è Microsoft Identity Platform?

Microsoft Identity Platform consente di creare applicazioni a cui gli utenti e i clienti possono accedere usando le loro identità Microsoft o gli account di social networking, nonché di fornire l'accesso autorizzato alle loro API o alle API Microsoft come Microsoft Graph.

Diversi componenti costituiscono Microsoft Identity Platform:

- **Servizio di autenticazione conforme agli standard OAuth 2.0 e OpenID Connect**, che consente agli sviluppatori di autenticare diversi tipi di identità, tra cui:
  - Account aziendali o degli istituti di istruzione, di cui viene effettuato il provisioning tramite Azure AD
  - Account Microsoft personali, come Skype, Xbox e Outlook.com
  - Account di social networking o locali, tramite Azure AD B2C
- **Librerie open source**: Microsoft Authentication Library (MSAL) e supporto per altre librerie conformi agli standard
- **Portale di gestione delle applicazioni**: un'esperienza di registrazione e configurazione nel portale di Azure, insieme a tutte le altre funzionalità di gestione di Azure.
- **API di configurazione dell'applicazione e PowerShell**: configurazione a livello di codice delle applicazioni tramite l'API Microsoft Graph e PowerShell, in modo da automatizzare le attività DevOps.
- **Contenuto per sviluppatori**: documentazione tecnica, tra cui argomenti di avvio rapido, esercitazioni, guide pratiche ed esempi di codice.

Per gli sviluppatori, Microsoft Identity Platform offre l'integrazione di moderne innovazioni in termini di sicurezza e gestione delle identità, come l'autenticazione senza password, l'autenticazione incrementale e l'accesso condizionale. Non è necessario implementare queste funzionalità personalmente: le applicazioni integrate con Microsoft Identity Platform sfruttano in modo nativo queste innovazioni.

Con Microsoft Identity Platform è possibile scrivere codice una sola volta e raggiungere qualsiasi utente. È possibile compilare un'app una sola volta e renderla funzionante su molte piattaforme oppure compilare un'app che operi come client nonché come applicazione della risorsa (API).

Per una panoramica video della piattaforma e una demo sull'esperienza di autenticazione, vedere la pagina [relativa alla piattaforma di identità Microsoft per gli sviluppatori](https://youtu.be/uDU1QTSw7Ps).

## <a name="getting-started"></a>Introduzione

Scegliere lo [scenario dell'applicazione](authentication-flows-app-scenarios.md) da creare. Ognuno di questi percorsi di scenario inizia con una panoramica e fornisce collegamenti a un argomento di avvio rapido per diventare subito produttivi:

- [App a pagina singola](scenario-spa-overview.md)
- [App Web che offre l'accesso agli utenti](scenario-web-app-sign-user-overview.md)
- [App Web che chiama le API Web](scenario-web-app-call-api-overview.md)
- [API Web protetta](scenario-protected-web-api-overview.md)
- [App Web che chiama le API Web](scenario-web-api-call-api-overview.md)
- [App desktop](scenario-desktop-overview.md)
- [App daemon](scenario-daemon-overview.md)
- [App per dispositivi mobili](scenario-mobile-overview.md)

Quando si usa Microsoft Identity Platform per integrare l'autenticazione e l'autorizzazione nelle app, è possibile fare riferimento a questa immagine che illustra gli scenari più comuni delle app e i relativi componenti di gestione delle identità. Selezionare l'immagine per visualizzarla a schermo intero.

[![Mappa della metropolitana che illustra diversi scenari di applicazioni in Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Concetti relativi all'autenticazione

Set di articoli consigliati sull'applicazione dei concetti di base dell'autenticazione e di Azure AD a Microsoft Identity Platform:

- [Nozioni di base sull'autenticazione](./authentication-vs-authorization.md)
- [Entità servizio e applicazione](app-objects-and-service-principals.md)
- [Destinatari](v2-supported-account-types.md)
- [Autorizzazioni e consenso](v2-permissions-and-consent.md)
- [Token ID](id-tokens.md)
- [Token di accesso](access-tokens.md)
- [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Altre opzioni di gestione delle identità e degli accessi

[Azure AD B2C](../../active-directory-b2c/overview.md): creare applicazioni rivolte ai clienti a cui gli utenti possono accedere con i loro account di social networking, come Facebook o Google, oppure usando un indirizzo di poste elettronica e una password.

[Azure AD B2B](../external-identities/what-is-b2b.md): invitare utenti esterni nel proprio tenant di Azure AD come utente "guest" a cui è possibile assegnare autorizzazioni e al tempo stesso consentire l'uso di credenziali esistenti per l'autenticazione.

[Azure Active Directory per sviluppatori (v1.0)](../azuread-dev/v1-overview.md): mostrata qui per gli sviluppatori con app esistenti che usano l'endpoint v1.0 precedente. **Non** usare v1.0 per i nuovi progetti.

## <a name="next-steps"></a>Passaggi successivi

Se si dispone di un account Azure, si ha già accesso a un tenant di Azure Active Directory, ma la maggior parte degli sviluppatori di Microsoft Identity Platform necessita di un tenant Azure AD per l'uso durante lo sviluppo di applicazioni, un "tenant di sviluppo".

Leggere informazioni su come creare un proprio tenant da usare per lo sviluppo di applicazioni:

[Avvio rapido: Configurare un tenant di Azure AD](quickstart-create-new-tenant.md)
