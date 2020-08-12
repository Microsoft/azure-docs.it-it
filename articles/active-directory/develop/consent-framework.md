---
title: Framework di consenso Azure AD
titleSuffix: Microsoft identity platform
description: Informazioni sul framework di consenso in Azure Active Directory e sul suo uso per semplificare lo sviluppo di applicazioni client native e Web multi-tenant.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: fe321de279397d24e0926cf1ef26a1d66adac8c4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117312"
---
# <a name="azure-active-directory-consent-framework"></a>Framework di consenso di Azure Active Directory

Il framework di consenso di Azure Active Directory (Azure AD) semplifica lo sviluppo di applicazioni client native e Web multi-tenant. Queste applicazioni consentono l'accesso da parte degli account utente da un tenant di Azure AD diverso da quello in cui l'applicazione è registrata. Le applicazioni possono anche dover accedere ad API Web, come l'API Microsoft Graph (per l'accesso ad Azure AD, Intune e ai servizi di Office 365) e alle API di altri servizi Microsoft, oltre che alle API Web personalizzate.

Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla richiesta di registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory. Ad esempio, se un'applicazione client Web deve leggere le informazioni del calendario dell'utente da Office 365, l'utente deve prima fornire il consenso all'applicazione client. Una volta ottenuto il consenso, l'applicazione client potrà chiamare l'API di Microsoft Graph per conto dell'utente e usare le informazioni del calendario nel modo necessario. L'[API Microsoft Graph](https://developer.microsoft.com/graph) fornisce l'accesso ai dati in Office 365 (come calendari e messaggi di Exchange, siti ed elenchi di SharePoint, documenti di OneDrive, blocchi appunti di OneNote, attività di Planner e cartelle di lavoro di Excel), nonché a utenti e gruppi di Azure AD e ad altri oggetti dati di più servizi cloud Microsoft.

Il framework di consenso è basato su OAuth 2.0 e i relativi flussi diversi, come la concessione del codice di autorizzazione e delle credenziali utente, mediante client pubblici o riservati. Tramite OAuth 2.0, Azure AD consente di creare numerosi tipi diversi di applicazioni client, ad esempio su telefono, tablet, server o applicazione Web, e di ottenere l'accesso alle risorse necessarie.

Per altre informazioni sull'uso del framework di consenso con le concessioni di autorizzazione OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web tramite OAuth 2.0 e Azure AD](v2-oauth2-auth-code-flow.md) e [Scenari di autenticazione per Azure AD](./authentication-vs-authorization.md). Per informazioni su come ottenere l'autorizzazione per l'accesso a Office 365 tramite Microsoft Graph, vedere [App authentication with Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview) (Autenticazione di app con Microsoft Graph).

## <a name="consent-experience---an-example"></a>Esempio di esperienza di consenso

I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione.

1. Si supponga di avere un'applicazione client Web che deve richiedere autorizzazioni specifiche per accedere a una risorsa/API. Si apprenderà come eseguire questa configurazione nella sezione successiva, ma essenzialmente il portale di Azure viene usato per dichiarare le richieste di autorizzazione al momento della configurazione. Come altre impostazioni di configurazione, diventano parte della registrazione dell'applicazione in Azure Active Directory:

    ![Autorizzazioni per altre applicazioni](./media/consent-framework/permissions.png)

1. Considerare che sono state aggiornate le autorizzazioni dell'applicazione, che l'applicazione è in esecuzione e che un utente sta per usarla per la prima volta. L'applicazione deve prima ottenere un codice di autorizzazione dall'endpoint `/authorize` di Azure AD. Il codice di autorizzazione può quindi essere usato per acquisire un nuovo un token di accesso e aggiornamento.

1. Se l'utente non è già autenticato, l'endpoint `/authorize` di Azure AD chiede all'utente di effettuare l'accesso.

    ![Accesso utente o amministratore ad Azure AD](./media/consent-framework/usersignin.png)

1. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione. Se il consenso non è già stato concesso, Azure AD lo richiede all'utente e visualizza le autorizzazioni necessarie per il funzionamento. Il set di autorizzazioni visualizzate nella finestra di dialogo di consenso corrisponde a quelle selezionate in **Autorizzazioni delegate** nel portale di Azure.

    ![Mostra un esempio di autorizzazioni visualizzate nella finestra di dialogo di consenso](./media/consent-framework/consent.png)

1. Dopo che l'utente ha concesso il consenso, all'applicazione viene restituito un codice di autorizzazione, che viene riscattato per acquisire un token di accesso e di aggiornamento. Per altre informazioni su questo flusso, vedere il [flusso del codice di autorizzazione OAuth 2,0](v2-oauth2-auth-code-flow.md).

1. In qualità di amministratore, è possibile inoltre consentire le autorizzazioni delegate di un'applicazione per conto di tutti gli utenti nel proprio tenant. Il consenso amministrativo impedisce la visualizzazione della finestra di dialogo di consenso per ogni utente nel tenant e può essere eseguita nel [portale di Azure](https://portal.azure.com) dagli utenti con il ruolo di amministratore. Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Per fornire il consenso per le autorizzazioni delegate di un'app**

   1. Passare alla pagina **autorizzazioni API** per l'applicazione
   1. Fare clic sul pulsante **Concedi autorizzazione amministratore** .

      ![Concedere le autorizzazioni per il consenso esplicito dell'amministratore](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > La concessione esplicita del consenso usando il pulsante **Concedi autorizzazioni** è attualmente richiesta per le applicazioni a pagina singola (SPA) che usano ADAL.js. In caso contrario, l'applicazione non funziona quando viene richiesto il token di accesso.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come convertire un'app multi-tenant](howto-convert-app-to-be-multi-tenant.md)
* Per informazioni più dettagliate, scoprire [come viene supportato il consenso a livello del protocollo OAuth 2.0 durante il flusso di concessione del codice di autorizzazione](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code).