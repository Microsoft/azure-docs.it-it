---
title: Prerequisiti per le API | Azure Marketplace
description: Prerequisiti per l'utilizzo di API del portale Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255975"
---
<a name="api-prerequisites"></a>Prerequisiti per API
================

> [!NOTE]
> Le API portale Cloud Partner sono integrate con il centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

Esistono due risorse programmatiche necessarie per usare le API del portale Cloud Partner: un'entità servizio e un token di accesso Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Creare un'entità servizio nel tenant di Azure Active Directory
----------------------------------------------------------------

In primo luogo, creare un'entità servizio nel tenant di Azure AD. A questo tenant verrà assegnato un proprio set di autorizzazioni nel portale Cloud Partner. Il codice chiamerà le API usando il tenant invece delle credenziali personali.  Per una spiegazione completa sulla creazione di un'entità servizio, vedere [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Aggiungere l'entità servizio all'account
-----------------------------------------

Ora che è stato creata l'entità servizio nel tenant, aggiungerla come utente all'account del portale Cloud Partner. Esattamente come un utente, l'entità servizio può agire come proprietario o come collaboratore del portale.

Usare la procedura seguente per aggiungere l'entità servizio:

1. Accedere al portale Cloud Partner. 
2. Fare clic su **Utenti** nella barra dei menu a sinistra e scegliere **Aggiungi utente**.

   ![Aggiungere un utente al portale](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Selezionare **Entità servizio** dall'elenco a discesa **Tipo** e aggiungere i dettagli seguenti:

-   Un **Nome descrittivo** per l'entità servizio, ad esempio `spAccount`.
-   L'**ID applicazione**. Per trovare questo identificatore, passare alla [portale di Azure](https://portal.azure.com), fare clic su **Azure Active Directory**, scegliere **registrazioni app**, quindi fare clic sull'app.
-   L'**ID tenant**, noto anche come **ID directory**, per il tenant di Azure AD. È possibile trovare questo identificatore nella pagina di Azure Active Directory nel [portale di Azure](https://portal.azure.com), in **Proprietà**.
-   L'**ID oggetto** per l'oggetto entità servizio. È possibile ottenere questo identificatore dal portale di Azure. Passare ad **Azure Active Directory**, scegliere **Registrazioni app**, fare clic sull'app e poi sul nome dell'app in **Applicazione gestita nella directory locale**. Passare quindi alla pagina **Proprietà**, per trovare l'ID oggetto. Assicurarsi di non acquisire l'ID oggetto iniziale sull'app, bensì quello presente all'interno dell'applicazione gestita.
-   Il **Ruolo** associato all'account, viene usato per il Controllo degli accessi in base al ruolo.

     ![Aggiungere un'app gestita al portale](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Fare clic su **Aggiungi** per aggiungere l'entità servizio all'account.

   ![Aggiungere un'entità servizio](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Ottenere un token di accesso di Azure AD
----------------------------

Le API del portale Cloud Partner usano le risorse e i protocolli seguenti durante l'autenticazione:

- Un bearer token Web JSON (JWT) per richiedere l'accesso alle risorse
- Il protocollo[OpenID Connect](https://openid.net/connect/) (OIDC) per verificare l'identità
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) come autorità di identità

Esistono due principali approcci per l'acquisizione a livello di codice di un token JWT:

- L'utilizzo di Microsoft Authentication Library (MSAL) per .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Questo approccio di livello superiore è consigliato per gli sviluppatori .NET. 
- La creazione di una richiesta **HTTP POST** all'endpoint Azure AD oauth **token** che si presenta come:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

A questo punto, trasmettere questo token come parte dell'intestazione di autorizzazione per le richieste API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Per tutte le API in questo riferimento, l'intestazione dell'autorizzazione si presuppone sempre come trasmessa, quindi non esplicitamente menzionata.

Se si verificano errori di autenticazione nella richiesta, vedere [Risoluzione dei problemi relativi a errori di autenticazione](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
