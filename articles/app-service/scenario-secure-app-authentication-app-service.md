---
title: Esercitazione - Aggiungere l'autenticazione a un'app Web nel Servizio app di Azure | Azure
description: Questa esercitazione illustra come abilitare l'autenticazione e l'autorizzazione per un'app Web in esecuzione nel Servizio app di Azure. Limitare l'accesso all'app Web agli utenti dell'organizzazione.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: b17cb6906a37d2cab4383fac18400b35dc8adb2f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223191"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Esercitazione: Aggiungere l'autenticazione all'app Web in esecuzione nel Servizio app di Azure

Informazioni su come abilitare l'autenticazione per l'app Web in esecuzione nel Servizio app di Azure e limitare l'accesso agli utenti dell'organizzazione.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagramma che mostra l'accesso dell'utente." border="false":::

Il servizio app fornisce supporto integrato per l'autenticazione e l'autorizzazione, quindi è possibile consentire l'accesso degli utenti e l'accesso ai dati senza scrivere codice, o con una minima quantità di codice, nell'app Web. L'uso del modulo di autenticazione/autorizzazione del servizio app non è obbligatorio, ma consente di semplificare il processo di autenticazione e autorizzazione per l'app. Questo articolo illustra come proteggere l'app Web con il modulo di autenticazione/autorizzazione del servizio app usando Azure Active Directory (Azure AD) come provider di identità.

Il modulo di autenticazione/autorizzazione è abilitato e configurato tramite il portale di Azure e le impostazioni dell'app. Non sono necessari SDK, linguaggi specifici o modifiche al codice dell'applicazione. Sono supportati diversi provider di identità, tra cui Azure AD, account Microsoft, Facebook, Google e Twitter. Quando il modulo di autenticazione/autorizzazione è abilitato, ogni richiesta HTTP in ingresso passa attraverso tale modulo prima di essere gestita dal codice dell'app. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Configurare l'autenticazione per l'app Web.
> * Limitare l'accesso all'app Web agli utenti dell'organizzazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Creare e pubblicare un'app Web nel servizio app

Per questa esercitazione è necessaria un'app Web distribuita nel servizio app. È possibile usare un'app Web esistente oppure è possibile seguire la guida di [avvio rapido per ASP.NET Core](quickstart-dotnetcore.md) per creare e pubblicare una nuova app Web nel servizio app.

Sia che si usi un'app Web esistente o che se ne crei una nuova, prendere nota del nome dell'app Web e del nome del gruppo di risorse in cui è distribuita l'app Web. Questi nomi saranno necessari nel corso di questa esercitazione. 

## <a name="configure-authentication-and-authorization"></a>Configurare l'autenticazione e l'autorizzazione

Si dispone ora di un'app Web in esecuzione nel servizio app. Si procederà quindi all'abilitazione dell'autenticazione e dell'autorizzazione per l'app Web. Si userà Azure AD come provider di identità. Per altre informazioni, vedere [Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account di accesso di Azure AD](configure-authentication-provider-aad.md).

Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** oppure cercare e selezionare **Gruppi di risorse** in una pagina qualsiasi.

In **Gruppi di risorse** cercare e selezionare il gruppo di risorse desiderato. In **Panoramica** selezionare la pagina di gestione dell'app.

:::image type="content" alt-text="Screenshot che mostra la selezione della pagina di gestione dell'app." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Nel menu a sinistra dell'app selezionare **autenticazione** e quindi fare clic su **Aggiungi provider di identità**.

Nella pagina **Aggiungi un provider di identità** selezionare **Microsoft** come provider di **identità** per accedere alle identità Microsoft e Azure ad.

Per tipo di registrazione app **registrazione** app  >  selezionare **Crea una nuova registrazione** per l'app.

Per la **registrazione dell'app**  >  **tipi di conto supportati**, selezionare tenant **corrente-singolo tenant**.

Nella sezione **impostazioni di autenticazione del servizio app** lasciare impostato **l'autenticazione** per **Richiedi autenticazione** e **richieste non autenticate** impostate su **http 302 trovato Reindirizzamento: consigliato per i siti Web**.

Nella parte inferiore della pagina **Aggiungi un provider di identità** fare clic su **Aggiungi** per abilitare l'autenticazione per l'app Web.

:::image type="content" alt-text="Screenshot che mostra la configurazione dell'autenticazione." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

A questo punto si dispone di un'app protetta tramite l'autenticazione e l'autorizzazione del servizio app.

## <a name="verify-limited-access-to-the-web-app"></a>Verificare l'accesso limitato all'app Web

Quando è stato abilitato il modulo di autenticazione/autorizzazione del servizio app, è stata creata una registrazione app nel tenant di Azure AD. Il nome visualizzato della registrazione app corrisponde a quello dell'app Web. Per controllare le impostazioni, selezionare **Azure Active Directory** nel menu del portale e quindi selezionare **Registrazioni app**. Selezionare la registrazione app creata in precedenza. Nella panoramica verificare che l'opzione **Tipi di account supportati** sia impostata su **Solo l'organizzazione personale**.

:::image type="content" alt-text="Screenshot che mostra la verifica dell'accesso." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Per verificare che l'accesso all'app sia limitato agli utenti dell'organizzazione, avviare un browser in modalità privata o anonima e passare a `https://<app-name>.azurewebsites.net`. Si dovrebbe essere indirizzati a una pagina di accesso protetta per verificare che gli utenti non autenticati non siano autorizzati ad accedere al sito. Eseguire l'accesso come utente nell'organizzazione per ottenere l'accesso al sito. Si può anche avviare un nuovo browser e provare ad accedere con un account personale per verificare che gli utenti esterni all'organizzazione non siano autorizzati ad accedere.

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'esercitazione è stata completata e l'app Web o le risorse associate non sono più necessarie, [pulire le risorse create](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Configurare l'autenticazione per l'app Web.
> * Limitare l'accesso all'app Web agli utenti dell'organizzazione.

> [!div class="nextstepaction"]
> [Il servizio app accede alle risorse di archiviazione](scenario-secure-app-access-storage.md)
