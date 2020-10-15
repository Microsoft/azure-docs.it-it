---
title: Applicazioni a pagina singola in Azure Active Directory
description: Descrive che cosa sono le applicazioni a pagina singola e presenta le informazioni di base su flusso del protocollo, registrazione e scadenza del token per questo tipo di app.
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
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154747"
---
# <a name="single-page-applications"></a>Applicazioni a pagina singola

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Le applicazioni a singola pagina (Spa) sono in genere strutturate come un livello di presentazione JavaScript (front-end) eseguito nel browser e un back-end dell'API Web in esecuzione in un server e implementa la logica di business dell'applicazione. Per ulteriori informazioni sulla concessione di autorizzazione implicita e per stabilire se sia adatta allo scenario dell'applicazione, vedere [informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

In questo scenario, quando l'utente accede, il front-end JavaScript utilizza [Active Directory Authentication Library per JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e la concessione di autorizzazione implicita per ottenere un token ID (id_token) da Azure AD. Il token viene memorizzato nella cache e il client lo associa alla richiesta come token di connessione quando si effettuano chiamate al relativo back-end dell'API Web, protetto tramite il middleware OWIN.

## <a name="diagram"></a>Diagramma

![Diagramma Applicazione a pagina singola](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Flusso del protocollo

1. L'utente passa all'applicazione Web.
1. L'applicazione restituisce al browser il front-end JavaScript (livello di presentazione).
1. L'utente avvia l'accesso, ad esempio facendo clic su un collegamento di accesso. Il browser invia una richiesta GET all'endpoint di autorizzazione di Azure AD per richiedere un token ID. Nei parametri di query di questa richiesta sono inclusi l'URL di risposta e l'ID applicazione.
1. Azure AD convalida l'URL di risposta confrontandolo con l'URL di risposta registrato, configurato nel portale di Azure.
1. L'utente accede nella pagina di accesso.
1. Se l'autenticazione ha esito positivo, Azure AD crea un token ID e lo restituisce come frammento di URL (#) all'URL di risposta dell'applicazione. Per un'applicazione di produzione, questo URL di risposta deve essere HTTPS. Il token restituito include le attestazioni sull'utente e Azure AD richieste dall'applicazione per convalidare il token.
1. Il codice client JavaScript in esecuzione nel browser estrae il token dalla risposta da usare per la protezione delle chiamate al back-end dell'API Web dell'applicazione.
1. Il browser chiama il back-end dell'API Web dell'applicazione con il token ID nell'intestazione dell'autorizzazione. Il servizio di autenticazione di Azure AD emette un token ID che può essere usato come token di connessione se la risorsa è uguale all'ID client (in questo caso la condizione è vera perché l'API Web è il back-end stesso dell'app).

## <a name="code-samples"></a>Esempi di codice

Vedere gli [esempi di codice per gli scenari di applicazioni a pagina singola](sample-v1-code.md#single-page-applications). Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi.

## <a name="app-registration"></a>Registrazione delle app

* Singolo tenant: se si compila un'applicazione solo per la propria organizzazione, è necessario registrarla nella directory aziendale usando il portale di Azure.
* Multi-tenant: se si compila un'applicazione che può essere usata da utenti esterni all'organizzazione, è necessario registrarla nella directory aziendale, ma anche nella directory di ogni organizzazione che utilizzerà l'applicazione. Per rendere disponibile l'applicazione nella propria directory, è possibile includere un processo di accesso per i clienti che permetta loro di concedere il consenso all'applicazione. Al momento dell'iscrizione all'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione e quindi viene presentata l'opzione per il consenso. A seconda delle autorizzazioni richieste, è possibile che il consenso debba essere fornito da un amministratore dell'altra organizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione viene registrata nella directory.

Dopo la registrazione dell'applicazione, è necessario configurarla per l'uso del protocollo di concessione implicita OAuth 2.0. Per impostazione predefinita, questo protocollo è disabilitato per le applicazioni. Per abilitare il protocollo di concessione implicita OAuth2 per l'applicazione, modificare il manifesto dell'applicazione dal portale di Azure e impostare il valore "oauth2AllowImplicitFlow" su true. Per altre informazioni, vedere [Manifesto dell'applicazione](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Scadenza del token

L'uso di ADAL.js consente di:

* Aggiornare un token scaduto
* Richiedere un token di accesso per chiamare una risorsa API Web

Dopo che l'autenticazione ha esito positivo, Azure AD scrive un cookie nel browser dell'utente per stabilire una sessione. Tenere presente che la sessione esiste tra l'utente e Azure AD e non tra l'utente e l'applicazione Web. Quando un token scade, ADAL.js usa questa sessione per ottenere automaticamente un altro token. ADAL.js usa un iFrame nascosto per inviare e ricevere la richiesta tramite il protocollo di concessione implicita OAuth. ADAL.js possibile anche usare questo stesso meccanismo per ottenere automaticamente i token di accesso per altre risorse dell'API Web chiamate dall'applicazione, purché queste risorse supportino la condivisione di risorse tra le origini (CORS), siano registrate nella directory dell'utente e che l'utente abbia concesso il consenso richiesto durante l'accesso.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
* Informazioni sulle [nozioni di base sull'autenticazione](v1-authentication-scenarios.md) Azure ad
