---
title: Domande frequenti (FAQ) per Azure Active Directory B2C
description: Risposte alle domande più frequenti su Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30b7788419695db7ee5d4b81714bf55248a41b4a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108723"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: domande frequenti

In questa pagina vengono fornite le risposte alle domande frequenti sulla Azure Active Directory B2C (Azure AD B2C). Controllarla costantemente per eventuali aggiornamenti.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Perché non riesco ad accedere all'estensione Azure AD B2C nel portale di Azure?

Esistono due motivi comuni per cui l'estensione di Azure AD non funziona per l'utente. Azure AD B2C richiede che il ruolo utente nella directory sia quello di amministratore globale. Se si ritiene di dover disporre dell'accesso, contattare l'amministratore. Se si dispone di privilegi di amministratore globale, assicurarsi di trovarsi in una directory di Azure AD B2C e non in una directory di Azure Active Directory. È possibile visualizzare le istruzioni per la [creazione di un tenant di Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>È possibile usare le funzionalità di Azure AD B2C nel tenant di Azure AD esistente per dipendenti aziendali?

Azure AD e Azure AD B2C vengono offerti come prodotti separati e non possono coesistere nello stesso tenant. Un tenant di Azure AD rappresenta un'organizzazione. Un tenant di Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni relying party. Con l'aggiunta di un **nuovo provider OpenID Connect** in **Azure ad B2C > provider di identità** o con criteri personalizzati, Azure AD B2C possibile federare Azure ad consentire l'autenticazione dei dipendenti in un'organizzazione.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>È possibile usare Azure AD B2C per fornire l'accesso ai social networking (Facebook e Google +) in Microsoft 365?

Non è possibile usare Azure AD B2C per autenticare gli utenti per Microsoft 365. Azure AD è la soluzione Microsoft per la gestione dell'accesso dei dipendenti alle app SaaS e include funzionalità progettate per questo scopo, ad esempio le licenze e l'accesso condizionale. Azure AD B2C offre una piattaforma di gestione di identità e accessi per la compilazione di applicazioni web e per dispositivi mobili. Quando Azure AD B2C è configurato per eseguire la federazione di un tenant di Azure AD, il tenant di Azure AD gestisce l'accesso dei dipendenti alle applicazioni che si basano su Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Che cosa sono gli account locali in Azure AD B2C? In che cosa differiscono dagli account aziendali o dell'istituto di istruzione in Azure AD?

In un tenant di Azure AD, gli utenti che vi appartengono effettuano l'accesso con un indirizzo di posta elettronica del modulo `<xyz>@<tenant domain>`. `<tenant domain>` è uno dei domini verificati nel tenant o nel dominio `<...>.onmicrosoft.com` iniziale. Questo tipo di account è un account aziendale o dell'istituto di istruzione.

In un tenant di Azure AD B2C, la maggior parte delle app richiede l'accesso dell'utente con un indirizzo di posta elettronica arbitrario, ad esempio joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com. Questo tipo di account è un account locale. Sono supportati anche nomi utente arbitrari come account locali (ad esempio, joe, bob, sarah o jim). È possibile scegliere uno di questi due tipi di account locale durante la configurazione dei provider di identità per Azure AD B2C nel portale di Azure. Nel tenant di Azure AD B2C selezionare **provider di identità**, selezionare **account locale** e quindi fare clic su **nome utente**.

Gli account utente per le applicazioni possono essere creati tramite un flusso utente di iscrizione, un flusso utente di iscrizione o accesso, l'API Microsoft Graph o nel portale di Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quali provider di identità di social networking sono attualmente supportati? Per quali provider è previsto il supporto in futuro?

Attualmente sono supportati diversi provider di identità basati su social network, tra cui Amazon, Facebook, GitHub (Preview), Google, LinkedIn, account Microsoft (MSA), QQ (anteprima), Twitter, WeChat (anteprima) e Weibo (anteprima). Viene valutato l'aggiunta del supporto per altri provider di identità di social networking comuni in base alla domanda dei clienti.

Azure AD B2C supporta anche [criteri personalizzati](custom-policy-overview.md). I criteri personalizzati consentono di creare criteri personalizzati per qualsiasi provider di identità che supporta [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) o SAML. Per un'introduzione ai criteri personalizzati, vedere lo [starter pack sui criteri personalizzati](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>È possibile configurare ambiti per raccogliere altre informazioni sugli utenti da diversi provider di identità di social networking?

No. Gli ambiti predefiniti usati per il gruppo di provider di identità di social networking supportato sono:

* Facebook: email
* Google+: email
* Account Microsoft: profilo di posta elettronica openid
* Amazon: profile
* LinkedIn: r_emailaddress e r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Per essere compatibile con Azure AD B2C, un'applicazione deve essere eseguita in Azure?

No, l'applicazione può essere ospitata ovunque (nel cloud o in locale). Per interagire con Azure AD B2C deve avere la possibilità di inviare e ricevere richieste HTTP su endpoint accessibili pubblicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Nel caso di più tenant Azure AD B2C, come è possibile gestirli nel portale di Azure?

Prima di aprire "Azure AD B2C" nel menu sul lato sinistro del portale di Azure, è necessario passare alla directory che si vuole gestire. Per cambiare directory, fare clic sulla propria identità in alto a destra nel portale di Azure e quindi scegliere una directory nell'elenco a discesa che viene visualizzato.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Come si personalizzano i messaggi di posta elettronica di verifica (il contenuto e il campo "Da:") inviati da Azure AD B2C?

È possibile usare la [funzionalità di personalizzazione della società](../active-directory/fundamentals/customize-branding.md) per personalizzare il contenuto dei messaggi di posta elettronica di verifica. In particolare, è possibile personalizzare i due elementi di posta elettronica seguenti:

* **Logo banner**: visualizzato in basso a destra.
* **Colore di sfondo**: in alto.

    ![Screenshot di un messaggio di posta elettronica di verifica personalizzato](./media/faq/company-branded-verification-email.png)

La firma della posta elettronica contiene il nome del tenant di Azure AD B2C fornito al momento della creazione del tenant di Azure AD B2C. È possibile modificare il nome seguendo queste istruzioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale.
1. Aprire il pannello **Azure Active Directory** .
1. Fare clic sulla scheda **Proprietà**.
1. Modificare il valore del campo **Nome**.
1. Fare clic su **Salva** nella parte superiore della pagina.

Attualmente non è possibile modificare il campo "Da:" del messaggio di posta elettronica.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Come si esegue la migrazione di nomi utente, password e profili esistenti dal database personale in Azure AD B2C?

È possibile usare l'API Microsoft Graph per scrivere lo strumento di migrazione. Vedere il [Guida alla migrazione degli utenti](user-migration.md) per informazioni dettagliate.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual è il flusso di password utente usato per gli account locali in Azure AD B2C?

Il flusso di password utente di AAD B2C per gli account locali si basa sui criteri di Azure AD. L'iscrizione ad Azure AD B2C, i flussi di iscrizione o di accesso e di reset delle password utenti usano un livello di complessità sicuro e le password non scadono. Per informazioni dettagliate, vedere [criteri e restrizioni per le password in Azure Active Directory](../active-directory/authentication/concept-sspr-policy.md).

Per informazioni su blocchi automatici e password degli account, vedere [Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>È possibile usare Azure AD Connect per eseguire la migrazione in Azure AD B2C delle identità utente archiviate in Active Directory locale?

No, Azure AD Connect non è progettato per funzionare con Azure AD B2C. Prendere in considerazione l'uso dell' [API Microsoft Graph](manage-user-accounts-graph-api.md) per la migrazione degli utenti. Vedere il [Guida alla migrazione degli utenti](user-migration.md) per informazioni dettagliate.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>L'applicazione può aprire le pagine di Azure AD B2C all'interno di un iFrame?

No. Per motivi di sicurezza, le pagine di Azure AD B2C non possono essere aperte in un iFrame. Il servizio comunica con il browser per impedire iFrames. L'uso di iFrames per le esperienze di gestione delle identità è sconsigliato dalla community della sicurezza in generale e dalla specifica OAUTH2 a causa del rischio di clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C è compatibile con i sistemi CRM come Microsoft Dynamics?

È disponibile l'integrazione con il portale di Microsoft Dynamics 365. Vedere l'articolo relativo alla [configurazione del portale di Dynamics 365 per usare Azure AD B2C per l'autenticazione](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C è compatibile con SharePoint 2016 locale o versione precedente?

Azure AD B2C non è concepito per lo scenario di condivisione esterna con i partner di SharePoint. A questo scopo, vedere invece il blog su [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>È necessario usare Azure AD B2C o B2B per gestire le identità esterne?

Per altre informazioni sull'applicazione delle funzionalità appropriate agli scenari di identità esterni, vedere [confrontare collaborazione B2B e B2C in Azure ad](../active-directory/external-identities/compare-with-b2c.md) .

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quali funzionalità di reporting e controllo offre Azure AD B2C? Offre le stesse funzionalità di Azure AD Premium?

No, Azure AD B2C non supporta lo stesso set di report di Azure AD Premium. Esistono tuttavia molte caratteristiche comuni:

* I **report degli accessi** contengono un record per ogni accesso con dettagli ridotti.
* I **report di controllo** includono sia le attività amministrative che quelle delle applicazioni.
* I **report sull'uso** includono il numero di utenti, il numero di accessi e il volume di autenticazioni a più fattori.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>È possibile localizzare l'interfaccia utente delle pagine servite da Azure AD B2C? Quali lingue sono supportate?

Sì, vedere [personalizzazione della lingua](language-customization.md). Offriamo traduzioni per 36 lingue ed è possibile eseguire l'override di qualsiasi stringa in base alle esigenze.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>È possibile usare l'URL personale nelle pagine di iscrizione e accesso servite da Azure AD B2C? Ad esempio, è possibile modificare l'URL da contoso.b2clogin.com a login.contoso.com?

No, per il momento. Questa funzionalità verrà implementata in futuro. Questo non si ottiene verificando il dominio nella scheda **Domini** nel portale di Azure. Tuttavia, con b2clogin.com, viene offerto un [dominio di livello superiore neutro](b2clogin.md)e, di conseguenza, l'aspetto esterno può essere implementato senza il riferimento a Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Come si elimina il tenant di Azure AD B2C?

Per eliminare il tenant di Azure AD B2C, seguire questa procedura.

È possibile usare la nuova esperienza Unified **registrazioni app** o  **le applicazioni legacy (legacy)** . [Altre informazioni sulla nuova esperienza](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com/) come *amministratore della sottoscrizione*. Usare lo stesso account aziendale o dell'Istituto di istruzione o lo stesso account Microsoft usato per iscriversi ad Azure.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Eliminare tutti i **flussi utente (criteri)** nel tenant del Azure ad B2C.
1. Selezionare **registrazioni app**, quindi selezionare la scheda **tutte le applicazioni** .
1. Eliminare tutte le applicazioni registrate.
1. Eliminare **B2C-Extensions-app**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare a sua volta ciascun utente (escludere l'utente *amministratore della sottoscrizione* con cui si è attualmente connessi). Selezionare **Elimina** nella parte inferiore della pagina e selezionare **Sì** quando richiesto.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. In **Gestisci** selezionare **impostazioni utente**.
1. In **Gestisci** selezionare **Proprietà**
1. In **Gestione degli accessi per le risorse di Azure** selezionare **Sì** e quindi selezionare **Salva**.
1. Disconnettersi dal portale di Azure e quindi accedere di nuovo per aggiornare l'accesso.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Nella pagina **Panoramica** selezionare **Elimina tenant**. Seguire le istruzioni visualizzate per completare il processo.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com/) come *amministratore della sottoscrizione*. Usare lo stesso account aziendale o dell'Istituto di istruzione o lo stesso account Microsoft usato per iscriversi ad Azure.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Eliminare tutti i **flussi utente (criteri)** nel tenant del Azure ad B2C.
1. Eliminare tutte le **applicazioni (legacy)** registrate nel tenant del Azure ad B2C.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare a sua volta ciascun utente (escludere l'utente *amministratore della sottoscrizione* con cui si è attualmente connessi). Selezionare **Elimina** nella parte inferiore della pagina e selezionare **Sì** quando richiesto.
1. In **Gestione** selezionare **Registrazioni per l'app**.
1. Selezionare **Visualizza tutte le applicazioni**
1. Selezionare l'applicazione denominata **B2C-Extensions-app**, selezionare **Elimina** e quindi fare clic su **Sì** quando richiesto.
1. In **Gestisci** selezionare **impostazioni utente**.
1. Se presente, in **connessioni account LinkedIn** selezionare **No**, quindi selezionare **Salva**.
1. In **Gestisci** selezionare **Proprietà**
1. In **Gestione degli accessi per le risorse di Azure** selezionare **Sì** e quindi selezionare **Salva**.
1. Disconnettersi dal portale di Azure e quindi accedere di nuovo per aggiornare l'accesso.
1. Selezionare **Azure Active Directory** dal menu a sinistra.
1. Nella pagina **Overview (panoramica** ) selezionare **Delete directory (Elimina directory**). Seguire le istruzioni visualizzate per completare il processo.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>È possibile ottenere Azure AD B2C come parte di Enterprise Mobility Suite?

No, Azure AD B2C è un servizio di Azure con pagamento in base al consumo e non fa parte di Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Come è possibile segnalare problemi relativi ad Azure AD B2C?

Vedere [Azure Active Directory B2C: Inviare richieste di supporto](support-options.md).