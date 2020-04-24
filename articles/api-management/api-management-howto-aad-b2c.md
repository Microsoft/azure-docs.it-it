---
title: Autorizzare gli account per sviluppatore usando Azure Active Directory B2C
titleSuffix: Azure API Management
description: Informazioni su come autorizzare gli utenti usando Azure Active Directory B2C in Gestione API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475494"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Come autorizzare gli account per sviluppatore usando Azure Active Directory B2C in Gestione API di Azure

## <a name="overview"></a>Panoramica

Azure Active Directory B2C è una soluzione di gestione delle identità cloud per applicazioni per dispositivi mobili e Web rivolte agli utenti. Può essere usato per gestire l'accesso a un portale per sviluppatori. Questa guida illustra la configurazione necessaria nel servizio Gestione API per l'integrazione con Azure Active Directory B2C. Per informazioni su come abilitare l'accesso al portale per sviluppatori con la versione classica di Azure Active Directory, vedere [Come autorizzare gli account per sviluppatore usando Azure Active Directory].

> [!NOTE]
> Per completare i passaggi di questa guida, è necessario un tenant di Azure Active Directory B2C in cui creare un'applicazione. È necessario anche che siano pronti i criteri di iscrizione e accesso. Per altre informazioni, vedere la [panoramica di Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizzare gli account per sviluppatore usando Azure Active Directory B2C

1. Per iniziare, accedere al [portale di Azure](https://portal.azure.com) e individuare l'istanza di Gestione API.

   > [!NOTE]
   > Se non è ancora stata creata un'istanza del servizio gestione API, vedere [creare un'istanza del servizio gestione API][Create an API Management service instance] nell' [esercitazione Introduzione a gestione API di Azure][Get started with Azure API Management].

1. In **identità**. Fare clic su **+Aggiungi** nella parte superiore.

   Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**. Scegliere **Azure Active Directory B2C**.
    
   ![Aggiungere AAD B2C come provider di identità][api-management-howto-add-b2c-identity-provider]

1. Copiare l' **URL di reindirizzamento**.

   ![URL di reindirizzamento del provider di identità AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. In una nuova scheda accedere al tenant di Azure Active Directory B2C nel portale di Azure e aprire il pannello **Applicazioni**.

   ![Registrare una nuova applicazione 1][api-management-howto-aad-b2c-portal-menu]

1. Fare clic sul pulsante **Aggiungi** per creare una nuova applicazione Azure Active Directory B2C.

   ![Registrare una nuova applicazione 2][api-management-howto-aad-b2c-add-button]

1. Nel pannello **Nuova applicazione** immettere un nome per l'applicazione. Scegliere **Sì** in **App Web/API Web** e quindi **Sì** in **Consenti flusso implicito**. Incollare quindi l'**URL di reindirizzamento** copiato nel passaggio 3 nella casella di testo **URL di risposta**.

   ![Registrare una nuova applicazione 3][api-management-howto-aad-b2c-app-details]

1. Se si usa il nuovo portale per sviluppatori (non il portale per sviluppatori Legacy), includere il nome, il **Cognome**e l' **ID oggetto dell'utente** **specificati**nelle attestazioni dell'applicazione.

    ![Attestazioni dell'applicazione](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Fare clic sul pulsante **Create** (Crea). Dopo che è stata creata, l'applicazione viene visualizzata nel pannello **Applicazioni**. Fare clic sul nome dell'applicazione per visualizzarne i dettagli.

   ![Registrare una nuova applicazione 4][api-management-howto-aad-b2c-app-created]

1. Dal pannello **Proprietà** copiare l'**ID applicazione** negli Appunti.

   ![ID applicazione 1][api-management-howto-aad-b2c-app-id]

1. Tornare al pannello **Aggiungi provider di identità** di Gestione API e incollare l'ID nella casella di testo **ID client**.
    
1.  Tornare alla registrazione dell'app B2C, fare clic sul pulsante **Chiavi** e quindi su **Genera chiave**. Fare clic su **Salva** per salvare la configurazione e visualizzare la **chiave dell'app**. Copiare la chiave negli Appunti.

    ![Chiave dell'app 1][api-management-howto-aad-b2c-app-key]

1.  Tornare al pannello **Aggiungi provider di identità** di Gestione API e incollare la chiave nella casella di testo **Segreto client**.
    
1.  Specificare il nome di dominio del tenant di Azure Active Directory B2C nel **tenant di accesso**.

1.  Il campo **Authority** consente di controllare l'URL di accesso Azure ad B2C da usare. Impostare il valore su **<your_b2c_tenant_name>. b2clogin.com**.

1. Come criteri del tenant B2C specificare **Criteri di iscrizione** e **Criteri di accesso**. Facoltativamente è possibile specificare anche **Criteri di modifica del profilo** e **Criteri di reimpostazione password**.

1. Dopo avere specificato la configurazione desiderata, fare clic su **Salva**.

    Dopo il salvataggio delle modifiche, gli sviluppatori potranno creare nuovi account e accedere al portale per sviluppatori con Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portale per sviluppatori: aggiungere Azure AD B2C autenticazione dell'account

Nel portale per sviluppatori è possibile accedere con AAD B2C con il **pulsante di accesso: OAuth** widget. Il widget è già incluso nella pagina di accesso del contenuto predefinito del portale per sviluppatori.

Sebbene venga creato automaticamente un nuovo account ogni volta che un nuovo utente accede con AAD B2C, è possibile aggiungere lo stesso widget alla pagina di iscrizione.

Il **modulo di iscrizione: OAuth** widget rappresenta un modulo usato per l'iscrizione a OAuth.

> [!IMPORTANT]
> Per rendere effettive le modifiche di AAD, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portale per sviluppatori Legacy: come iscriversi con Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Per eseguire l'iscrizione per un account per sviluppatore usando Azure Active Directory B2C, aprire una nuova finestra del browser e andare al portale per sviluppatori. Fare clic sul pulsante **Iscriviti**.

   ![Portale per sviluppatori 1][api-management-howto-aad-b2c-dev-portal]

2. Scegliere quindi di iscriversi con **Azure Active Directory B2C**.

   ![Portale per sviluppatori 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Si verrà reindirizzati ai criteri di iscrizione configurati nella sezione precedente. Scegliere di eseguire l'iscrizione usando l'indirizzo di posta elettronica o uno degli account di social networking esistenti.

   > [!NOTE]
   > Se Azure Active Directory B2C è l'unica opzione abilitata nella scheda **Identità** del portale di pubblicazione, si verrà reindirizzati direttamente ai criteri di iscrizione.

   ![Portale per sviluppatori][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Al termine dell'iscrizione si verrà reindirizzati al portale per sviluppatori. Si è ora connessi al portale per sviluppatori per l'istanza del servizio Gestione API.

    ![Registrazione completata][api-management-registration-complete]

## <a name="next-steps"></a>Passaggi successivi

*  [Panoramica di Azure Active Directory B2C]
*  [Azure Active Directory B2C: framework di criteri estendibile]
*  [Usare un account Microsoft come provider di identità in Azure Active Directory B2C]
*  [Usare un account Google come provider di identità in Azure Active Directory B2C]
*  [Usare un account LinkedIn come provider di identità in Azure Active Directory B2C]
*  [Usare un account Facebook come provider di identità in Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Panoramica di Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Come autorizzare gli account per sviluppatore usando Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: framework di criteri estendibile]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Usare un account Microsoft come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Usare un account Google come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Usare un account Facebook come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Usare un account LinkedIn come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
