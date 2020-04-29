---
title: Configurare l'autenticazione di Facebook
description: Informazioni su come configurare l'autenticazione di Facebook come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519967"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Facebook con un indirizzo di posta elettronica verificato e un numero di telefono cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrare l'applicazione con Facebook

1. Visitare il sito Web [Facebook Developers] e accedere con le credenziali dell'account Facebook.

   Se non si dispone di un account Facebook for Developers **, selezionare inizia** e seguire la procedura di registrazione.
1. Selezionare **app** > personali**Aggiungi nuova app**.
1. Nel campo **nome visualizzato** :
   1. Digitare un nome univoco per l'app.
   1. Fornire il proprio **indirizzo di posta elettronica di contatto**.
   1. Selezionare **Crea ID app**.
   1. Completare il controllo di sicurezza.

   Verrà visualizzato il dashboard per sviluppatori per la nuova app Facebook.
1. Selezionare **Dashboard** > **Facebook login** > **set up** > **Web**.
1. Nel percorso di spostamento a sinistra in **account di accesso di Facebook**selezionare **Impostazioni**.
1. Nel campo **validi URI di reindirizzamento OAuth** immettere `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Ricordarsi di `<app-name>` sostituire con il nome dell'app di servizio app Azure.
1. Selezionare **Save changes** (Salva modifiche).
1. Nel riquadro sinistro selezionare **Impostazioni** > di**base**. 
1. Nel campo **Secret app** selezionare **show**. Copiare i valori di **ID app** e **segreto app**. Vengono usati in un secondo momento per configurare l'app del servizio app in Azure.

   > [!IMPORTANT]
   > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
   >

1. L'account Facebook usato per registrare l'applicazione è un amministratore dell'app. A questo punto, solo gli amministratori possono accedere a questa applicazione.

   Per autenticare altri account Facebook, selezionare **Verifica app** e abilitare **make \<your-app-name> public** per consentire al pubblico generale di accedere all'app usando l'autenticazione di Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Facebook all'applicazione

1. Accedere al [portale di Azure] e passare all'app del servizio app.
1. Selezionare **Impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. Selezionare **Facebook**e quindi incollare i valori di ID app e segreto app ottenuti in precedenza. Abilitare gli ambiti necessari per l'applicazione.
1. Selezionare **OK**.

   ![Screenshot delle impostazioni di Facebook per app per dispositivi mobili][0]

    Per impostazione predefinita, il servizio app fornisce l'autenticazione, ma non limita l'accesso autorizzato al contenuto del sito e alle API. È necessario autorizzare gli utenti nel codice dell'app.
1. Opzionale Per limitare l'accesso solo agli utenti autenticati da Facebook, impostare **azione da eseguire quando la richiesta non è autenticata** su **Facebook**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate a Facebook per l'autenticazione.

   > [!CAUTION]
   > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

A questo punto si è pronti per usare Facebook per l'autenticazione nell'app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
