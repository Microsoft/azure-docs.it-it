---
title: Registrare app per dispositivi mobili che chiamano API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web (registrazione dell'app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cdd32185d1ffddb4c6bc3d62b91690f68dec17ca
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014723"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrare app per dispositivi mobili che chiamano API Web

Questo articolo contiene istruzioni che consentono di registrare un'applicazione per dispositivi mobili che si sta creando.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati dalle applicazioni per dispositivi mobili dipendono dall'esperienza che si desidera abilitare e dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari per l'acquisizione di token interattivi

La maggior parte delle applicazioni mobili usa l'autenticazione interattiva. Se l'app usa questa forma di autenticazione, è possibile accedere agli utenti da qualsiasi [tipo di account](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Destinatari per autenticazione integrata di Windows, nome utente-password e B2C

Se si dispone di un'app piattaforma UWP (Universal Windows Platform) (UWP), è possibile usare l'autenticazione integrata di Windows per l'accesso degli utenti. Per usare l'autenticazione integrata di Windows o l'autenticazione con nome utente-password, l'applicazione deve eseguire l'accesso degli utenti nel tenant di sviluppo line-of-business (LOB). In uno scenario di fornitori di software indipendenti (ISV), l'applicazione può accedere agli utenti in Azure Active Directory organizzazioni. Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.

È anche possibile accedere agli utenti usando identità social che passano un'autorità e un criterio B2C. Per usare questo metodo, è possibile usare solo l'autenticazione interattiva e l'autenticazione con password di nome utente. Username-password Authentication è attualmente supportata solo in Novell. iOS, Novell. Android e UWP.

Per altre informazioni, vedere [scenari e flussi di autenticazione supportati](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [scenari e piattaforme e linguaggi supportati](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>URI di reindirizzamento e configurazione della piattaforma

### <a name="interactive-authentication"></a>Autenticazione interattiva

Quando si compila un'app per dispositivi mobili che usa l'autenticazione interattiva, il passaggio di registrazione più critico è l'URI di reindirizzamento. È possibile impostare l'autenticazione interattiva tramite la [configurazione della piattaforma nel pannello **autenticazione**](https://aka.ms/MobileAppReg).

Questa esperienza consente all'app di ottenere Single Sign-On (SSO) tramite Microsoft Authenticator (e Portale aziendale Intune in Android). Supporta inoltre i criteri di gestione dei dispositivi.

Il portale di registrazione delle app offre un'esperienza di anteprima per il calcolo dell'URI di risposta negoziato per le applicazioni iOS e Android:

1. Nel portale di registrazione delle app selezionare **autenticazione**  >  **provare la nuova esperienza**.

   ![Il pannello autenticazione, in cui è possibile scegliere una nuova esperienza](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selezionare **Aggiungi una piattaforma**.

   ![Aggiungere una piattaforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.

   ![Scegliere un'applicazione per dispositivi mobili](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Immettere l'ID bundle, quindi selezionare **Register (registra**).

   ![Immettere l'ID bundle](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando si completano i passaggi, l'URI di reindirizzamento viene calcolato per l'utente, come nell'immagine seguente.

![URI di reindirizzamento risultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se si preferisce configurare manualmente l'URI di reindirizzamento, è possibile farlo tramite il manifesto dell'applicazione. Ecco il formato consigliato per il manifesto:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Immettere ad esempio `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - È possibile generare l'hash della firma Android usando la chiave di rilascio o la chiave di debug tramite il comando di chiave.

### <a name="username-password-authentication"></a>Nome utente-autenticazione con password

Se l'app usa solo l'autenticazione con nome utente e password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questo flusso esegue una round trip all'endpoint della versione 2,0 di Microsoft Identity Platform. L'applicazione non verrà richiamata su un URI specifico.

Tuttavia, è necessario identificare l'applicazione come applicazione client pubblica. A tale scopo, procedere nel seguente modo:

1. Sempre nella <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> portale di Azure</a>Selezionare l'app in **registrazioni app** e quindi selezionare **autenticazione**.
1. In **Impostazioni avanzate**  >  **Consenti flussi client pubblici**  >  **Abilita i flussi di dispositivi mobili e desktop seguenti:** selezionare **Sì**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Abilitare l'impostazione client pubblica nel riquadro autenticazione in portale di Azure":::

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni per dispositivi mobili chiamano le API per conto dell'utente che ha eseguito l'accesso. L'app deve richiedere autorizzazioni delegate. Queste autorizzazioni sono denominate anche ambiti. A seconda dell'esperienza desiderata, è possibile richiedere le autorizzazioni delegate in modo statico tramite la portale di Azure. In alternativa, è possibile richiederli dinamicamente in fase di esecuzione.

La registrazione statica delle autorizzazioni consente agli amministratori di approvare facilmente l'app. È consigliabile usare la registrazione statica.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, configurazione del [codice dell'app](scenario-mobile-app-configuration.md).
