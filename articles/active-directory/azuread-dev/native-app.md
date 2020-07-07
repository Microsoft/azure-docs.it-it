---
title: App native in Azure Active Directory
description: Descrive le app native e fornisce nozioni di base sul flusso del protocollo, la registrazione e la scadenza del token per questo tipo di app.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80154798"
---
# <a name="native-apps"></a>App native

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Le app native sono applicazioni che chiamano un'API Web per conto di un utente. Questo scenario è basato sul tipo di concessione del codice di autorizzazione OAuth 2.0 con un client pubblico, come descritto nella sezione 4.1 della [specifica OAuth 2.0](https://tools.ietf.org/html/rfc6749). L'applicazione nativa ottiene un token di accesso per l'utente tramite il protocollo OAuth 2.0. Questo token di accesso viene quindi inviato nella richiesta all'API Web, che autorizza l'utente e restituisce la risorsa desiderata.

## <a name="diagram"></a>Diagramma

![Diagramma Da applicazione nativa ad API Web](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Flusso del protocollo

Se si usano le librerie di autenticazione AD, la maggior parte dei dettagli del protocollo descritti di seguito viene gestita automaticamente, ad esempio i popup del browser, la memorizzazione dei token nella cache e la gestione dei token di aggiornamento.

1. Con un popup del browser l'applicazione nativa invia una richiesta all'endpoint di autorizzazione in Azure AD. Questa richiesta include l'ID applicazione e l'URI di reindirizzamento dell'applicazione nativa, come illustrato nel portale di Azure, nonché l'URI ID dell'applicazione per l'API Web. Se l'utente non ha già eseguito l'accesso, viene richiesto di eseguire di nuovo l'accesso
1. Azure AD autentica l'utente. Se si tratta di un'applicazione multi-tenant e il consenso è necessario per usare l'applicazione, l'utente dovrà fornire il consenso se non è già stato fatto. Dopo aver concesso il consenso e dopo aver completato l'autenticazione, Azure AD emette una risposta del codice di autorizzazione all'URI di reindirizzamento dell'applicazione client.
1. Quando Azure AD restituisce un codice di autorizzazione all'URI di reindirizzamento, l'applicazione interrompe l'interazione con il browser ed estrae il codice di autorizzazione dalla risposta. Con questo codice di autorizzazione, l'applicazione client invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
1. Il codice di autorizzazione e le informazioni sull'applicazione client e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT. Azure AD restituisce anche informazioni di base sull'utente, ad esempio il nome visualizzato e l'ID tenant.
1. Tramite HTTPS, l'applicazione client usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.
1. Alla scadenza del token di accesso l'applicazione client riceverà un errore che indica che l'utente deve ripetere il processo di autenticazione. Se l'applicazione ha un token di aggiornamento valido, può essere usato per acquisire un nuovo token di accesso senza richiedere all'utente di ripetere l'accesso. Se il token di aggiornamento scade, l'applicazione dovrà autenticare di nuovo l'utente in modo interattivo.

> [!NOTE]
> Il token di aggiornamento emesso da Azure AD può essere usato per accedere a più risorse. Ad esempio, se un'applicazione client è autorizzata a chiamare due API Web, il token di aggiornamento può essere usato per ottenere un token di accesso anche all'altra API Web.

## <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per gli scenari Da applicazione nativa ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi: [Da applicazione nativa ad API Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registrazione delle app

Per registrare un'applicazione con l'endpoint Azure AD v 1.0, vedere [registrare un'app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Tenant singolo: l'applicazione nativa e l'API Web devono essere registrate nella stessa directory di Azure AD. L'API Web può essere configurata in modo da esporre un set di autorizzazioni, che vengono usate per limitare l'accesso dell'applicazione nativa alle relative risorse. L'applicazione client seleziona quindi le autorizzazioni desiderate dal menu a discesa "autorizzazioni per altre applicazioni" nel portale di Azure.
* Multi-tenant: per prima cosa, l'applicazione nativa è stata registrata solo nella directory dello sviluppatore o dell'editore. Quindi, l'applicazione nativa viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, solo l'API Web viene registrata nella directory. 

## <a name="token-expiration"></a>Scadenza del token

Quando l'applicazione nativa usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere di ripetere l'accesso. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
- Informazioni sulle [nozioni di base sull'autenticazione](v1-authentication-scenarios.md) Azure ad
