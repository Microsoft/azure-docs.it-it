---
title: Opzioni per l'accesso Single Sign-On in Azure AD
description: Informazioni sulle opzioni disponibili per l'accesso Single Sign-On (SSO) in Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 2bf84a22a384e6079c2d85c833b34ba37eecaa46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252356"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Opzioni per l'accesso Single Sign-On in Azure AD

L'accesso Single Sign-On offre molti vantaggi rispetto ai metodi di accesso tradizionali.

- **Con l'accesso Single Sign-On**, gli utenti eseguono l'accesso una sola volta con un account a tutti i dispositivi aggiunti a un dominio, alle risorse aziendali, alle applicazioni SaaS (software distribuito come servizio) e alle applicazioni Web. Dopo aver eseguito l'accesso, l'utente potrà avviare le applicazioni dal portale di Office 365 o da App personali. Gli amministratori possono centralizzare la gestione degli account utente e aggiungere o rimuovere automaticamente l'accesso utente alle applicazioni in base all'appartenenza a gruppi.

- **Senza l'accesso Single Sign-On**, gli utenti devono ricordare le password specifiche dell'applicazione e accedere a ognuna di esse. Il personale IT deve creare e aggiornare gli account utente per ogni applicazione, ad esempio Microsoft 365, Box e Salesforce. Gli utenti devono ricordare le password, oltre a impiegare il tempo necessario per accedere a ogni applicazione.

Per altre informazioni sull'accesso Single Sign-On, vedere [Informazioni sull'accesso Single Sign-On](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Scelta di un metodo di accesso Single Sign-On

È possibile configurare un'applicazione per l'accesso Single Sign-On in vari modi. La scelta di un metodo di accesso Single Sign-On dipende dal modo in cui è configurata per l'autenticazione.

- Le applicazioni cloud possono usare il metodo OpenID Connect, OAuth, SAML, basato su password, collegato o disabilitato per l'accesso Single Sign-On. 
- Le applicazioni locali possono usare il metodo basato su password o intestazione, collegato, disabilitato oppure l'autenticazione integrata di Windows per l'accesso Single Sign-On. Le scelte locali sono valide se le applicazioni sono configurate per Application Proxy.

Questo diagramma di flusso aiuta a decidere quale metodo di accesso Single Sign-On è più idoneo al proprio scenario.

![Diagramma di flusso decisionale per il metodo Single Sign-On](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

La tabella seguente include un riepilogo dei metodi di accesso Single Sign-On e collegamenti per maggiori dettagli.

| Metodo di accesso Single Sign-On | Tipi di applicazioni | Utilizzo |
| :------ | :------- | :----- |
| [OpenID Connect e OAuth](#openid-connect-and-oauth) | Cloud e locale | Usare OAuth e OpenID Connect quando si sviluppa una nuova applicazione. Questo protocollo semplifica la configurazione dell'applicazione, ha SDK facili da usare e consente all'applicazione di usare MS Graph.
| [SAML](#saml-sso) | Cloud e locale | Scegliere SAML laddove possibile per le applicazioni esistenti che non usano OAuth o OpenID Connect. SAML funziona per le applicazioni che eseguono l'autenticazione usando uno dei protocolli SAML.|
| [Basato su password](#password-based-sso) | Cloud e locale | Scegliere il metodo basato su password quando l'applicazione esegue l'autenticazione con nome utente e password. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questo metodo usa il processo di accesso esistente fornito dall'applicazione, ma consente all'amministratore di gestire le password. |
| [Collegato](#linked-sign-on) | Cloud e locale | Scegliere l'accesso collegato quando l'applicazione è configurata per il Single Sign-On in un altro servizio di provider di identità. Questa opzione non aggiunge l'accesso Single Sign-On all'applicazione. L'applicazione potrebbe comunque avere già implementato l'accesso Single Sign-On usando un altro servizio, ad esempio Active Directory Federation Services.|
| [Disabilitato](#disabled-sso) | Cloud e locale | Scegliere l'accesso Single Sign-On disabilitato se l'app non è pronta per essere configurata per Single Sign-On. Questa modalità è quella predefinita quando si crea l'app.|
| [Autenticazione integrata di Windows](#integrated-windows-authentication-iwa-sso) | Solo in locale | Scegliere l'accesso Single Sign-On dell'autenticazione integrata di Windows per le applicazioni che usano l'[autenticazione integrata di Windows](/aspnet/web-api/overview/security/integrated-windows-authentication) o le applicazioni che riescono a riconoscere le attestazioni. Per l'autenticazione integrata di Windows, i connettori Application Proxy usano la delega vincolata Kerberos per autenticare gli utenti con l'applicazione. |
| [Basato su intestazione](#header-based-sso) | Solo in locale | Usare l'accesso Single Sign-On basato su intestazione quando l'applicazione usa le intestazioni per l'autenticazione. Application Proxy usa Azure AD per autenticare l'utente e quindi passa il traffico attraverso il servizio del connettore.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect e OAuth

Quando si sviluppano nuove applicazioni, usare protocolli moderni quali OAuth e OpenID Connect per ottenere la migliore esperienza Single Sign-On per l'app su più piattaforme di dispositivo. OAuth consente agli utenti o agli amministratori di [concedere il consenso](configure-user-consent.md) per l'accesso a risorse protette come [Microsoft Graph](/graph/overview). Sono disponibili [SDK](../develop/reference-v2-libraries.md) facili da usare per l'app e, inoltre, l'app sarà pronta per usare [Microsoft Graph](/graph/overview).

Per altre informazioni, vedere:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guida per sviluppatori di Microsoft Identity Platform](../develop/index.yml)

## <a name="saml-sso"></a>SAML SSO

Con l'**accesso Single Sign-On SAML**, Azure AD esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. Con l'accesso Single Sign-On basato su SAML è possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML.

Scegliere l'accesso Single Sign-On basato su SAML quando l'applicazione lo supporta.

L'accesso Single Sign-On basato su SAML è supportato per le applicazioni che usano uno di questi protocolli:

- SAML 2.0
- WS-Federation

Per configurare un'applicazione SaaS per l'accesso Single Sign-On basato su SAML, vedere [Configurare l'accesso Single Sign-On basato su SAML](configure-saml-single-sign-on.md). Inoltre, molte applicazioni SaaS (software come un servizio) hanno un'[esercitazione specifica dell'applicazione](../saas-apps/tutorial-list.md) che illustra la configurazione per l'accesso Single Sign-On basato su SAML.

Per configurare un'applicazione per WS-Federation, seguire le stesse indicazioni per configurare l'applicazione per il Single Sign-On basato su SAML. Nel passaggio relativo alla configurazione dell'applicazione per l'uso di Azure AD è necessario sostituire l'URL di accesso ad Azure AD per l'endpoint di WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Per configurare un'applicazione locale per l'accesso Single Sign-On basato su SAML, vedere [Accesso Single Sign-On basato su SAML per le applicazioni locali con Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Per altre informazioni sul protocollo SAML, vedere [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO basato su password

Con l'accesso basato su password, gli utenti eseguono la procedura di accesso all'applicazione con nome utente e password solo al primo accesso. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione.

L'accesso Single Sign-On basato su password usa il processo di autenticazione esistente specificato dall'applicazione. Quando si abilita l'accesso Single Sign-On basato su password per un'applicazione, Azure AD raccoglie e archivia in modo sicuro i nomi utente e le password per l'applicazione. Le credenziali utente vengono archiviate in formato crittografato nella directory.

Scegliere l'accesso Single Sign-On basato su password quando:

- Un'applicazione non supporta il protocollo di accesso Single Sign-On SAML.
- Un'applicazione esegue l'autenticazione con un nome utente e una password anziché usando intestazioni e token di accesso.

>[!NOTE]
>Non è possibile applicare i criteri di accesso condizionale o l'autenticazione a più fattori per l'accesso Single Sign-On basato su password.

L'accesso Single Sign-On basato su password è supportato per qualunque applicazione basata su cloud con pagina di accesso basata su HTML. L'utente può usare uno dei browser seguenti:

- Internet Explorer 11 su Windows 7 o versioni successive
   > [!NOTE]
   > Internet Explorer è coperto da supporto limitato e non riceve più nuovi aggiornamenti software. Il browser consigliato è Microsoft Edge.

- Microsoft Edge su Windows 10 Anniversary Edition o versioni successive
- Microsoft Edge per iOS e Android
- Intune Managed Browser
- Chrome in Windows 7 o versioni successive e in MacOS X o versioni successive
- Firefox 26.0 o versioni successive in Windows XP SP2 o versioni successive e in Mac OS X 10.6 o versioni successive

Per configurare un'applicazione cloud per l'accesso Single Sign-On basato su password, vedere [Configurare l'accesso Single Sign-On tramite password](configure-password-single-sign-on-non-gallery-applications.md).

Per configurare un'applicazione in locale per l'accesso Single Sign-On tramite Application Proxy, vedere [Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy dell'applicazione](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Come funziona l'autenticazione per l'accesso SSO basato su password

Per l'autenticazione di un utente a un'applicazione, Azure AD recupera le credenziali dell'utente dalla directory e le immette nella pagina di accesso dell'applicazione.  Azure AD passa in modo sicuro le credenziali dell'utente tramite un'estensione del Web browser o un'app per dispositivi mobili. Questo processo consente agli amministratori di gestire le credenziali dell'utente senza che quest'ultimo debba ricordare le password.

> [!IMPORTANT]
> Le credenziali vengono offuscate all'utente durante il processo di accesso automatico, ma sono individuabili usando strumenti di debug Web. Gli utenti e gli amministratori devono seguire gli stessi criteri di sicurezza come se le credenziali fossero immesse direttamente dall'utente.

### <a name="managing-credentials-for-password-based-sso"></a>Gestione delle credenziali per SSO basato su password

Le password per ogni applicazione possono essere gestite dall'amministratore di Azure AD o dagli utenti.

Quando l'amministratore di Azure AD gestisce le credenziali:  

- L'utente non deve reimpostare o ricordare il nome utente e la password. L'utente può accedere all'applicazione selezionandola in App personali oppure tramite un collegamento fornito.
- L'amministratore può eseguire attività di gestione sulle credenziali. Può ad esempio aggiornare l'accesso alle applicazioni in base alle appartenenze a gruppi di utenti e allo stato dei dipendenti.
- L'amministratore può usare credenziali amministrative per specificare l'accesso alle applicazioni condivise tra più utenti. Può ad esempio consentire a tutti gli utenti che possono accedere a un'applicazione di accedere a un social network o a un documento che condivide l'applicazione.

Quando l'utente finale gestisce le credenziali:

- Gli utenti possono gestire le password aggiornandole o eliminandole in base alle proprie esigenze.
- Gli amministratori sono comunque in grado di impostare nuove credenziali per l'applicazione.

## <a name="linked-sign-on"></a>Linked sign-on (Accesso collegato)
L'accesso Single Sign-On collegato consente ad Azure AD di fornire l'accesso Single Sign-On a un'applicazione già configurata per l'SSO in un altro servizio. L'applicazione collegata può essere visualizzata dagli utenti finali nel portale di Office 365 o nel portale MyApps di Azure AD. Un utente può ad esempio avviare un'applicazione configurata per l'accesso Single Sign-On in Active Directory Federation Services 2.0 (AD FS) dal portale di Office 365. Sono anche disponibili report aggiuntivi per le applicazioni collegate che vengono avviate dal portale di Office 365 o dal portale MyApps di Azure AD. Per configurare un'applicazione per l'accesso collegato, vedere [Configurare l'accesso collegato](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Accesso collegato per la migrazione dell'applicazione

L'accesso collegato può garantire un'esperienza utente coerente mentre si esegue la migrazione delle applicazioni in un periodo di tempo. Se si esegue la migrazione di applicazioni ad Azure Active Directory, è possibile usare l'accesso collegato per pubblicare rapidamente i collegamenti a tutte le applicazioni di cui eseguire la migrazione.  Gli utenti possono trovare tutti i collegamenti nel [portale MyApps](../user-help/my-apps-portal-end-user-access.md) o nel [launcher di applicazioni di Microsoft 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Gli utenti non si accorgeranno di accedere a un'applicazione collegata o sottoposta migrazione.  

Dopo che un utente ha eseguito l'autenticazione con un'applicazione collegata, dovrà essere creato un record di account prima che all'utente finale venga concesso l'accesso Single Sign-On. Il provisioning di questo record di account può verificarsi automaticamente o può essere effettuato manualmente da un amministratore.

>[!NOTE]
>Non è possibile applicare i criteri di accesso condizionale o l'autenticazione a più fattori a un'applicazione collegata. Un'applicazione collegata non fornisce infatti funzionalità di Single Sign-On tramite Azure AD. La configurazione di un'applicazione collegata consiste semplicemente nell'aggiunta di un collegamento che verrà visualizzato nell'utilità di avvio delle app o nel portale App personali. 

## <a name="disabled-sso"></a>SSO disabilitato

Con la modalità disabilitata, l'accesso Single Sign-On non viene usato per l'applicazione. Quando l'accesso Single Sign-On è disabilitato, gli utenti potrebbero doversi autenticare due volte. In primo luogo, gli utenti eseguono l'autenticazione ad Azure AD e quindi accedono all'applicazione.

Usare la modalità di accesso Single Sign-On disabilitato:

- Se non si è pronti a integrare questa applicazione con l'accesso Single Sign-On di Azure AD
- In fase di test di altri aspetti dell'applicazione
- Come livello di sicurezza per un'applicazione locale che non richiede l'autenticazione degli utenti. Con la modalità disabilitata, l'utente deve eseguire l'autenticazione.

Si noti che se l'applicazione è stata configurata per l'accesso Single Sign-On basato su SAML avviato da SP e si imposta la modalità di accesso Single Sign-On su Disabilitato, agli utenti non sarà impedito l'accesso all'applicazione all'esterno del portale App personali. Per ottenere questo risultato, è necessario [disabilitare l'accesso degli utenti](disable-user-sign-in-portal.md).

## <a name="integrated-windows-authentication-iwa-sso"></a>SSO con autenticazione integrata di Windows

[Application Proxy](application-proxy.md) offre l'accesso Single Sign-On (SSO) alle applicazioni che usano l'[autenticazione integrata di Windows](/aspnet/web-api/overview/security/integrated-windows-authentication) o alle applicazioni che riescono a riconoscere le attestazioni. Se l'applicazione usa l'autenticazione integrata di Windows, Application Proxy esegue l'autenticazione dell'utente all'applicazione mediante la delega vincolata Kerberos. Per le applicazioni con riconoscimento delle attestazione che considerano attendibile Azure Active Directory, l'accesso Single Sign-On funziona perché l'utente è già stato autenticato da Azure AD.

Scegliere la modalità di accesso Single Sign-On basato sull'autenticazione integrata di Windows per fornire l'accesso Single Sign-On a un'app locale che viene autenticata tramite l'autenticazione integrata di Windows.

Per configurare un'app locale per l'autenticazione integrata di Windows, vedere [Kerberos Constrained Delegation for single sign-on to your applications with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) (Delega vincolata Kerberos per l'accesso Single Sign-On alle applicazioni con Application Proxy).

### <a name="how-single-sign-on-with-kcd-works"></a>Funzionamento di Single Sign-On con KCD
Questo diagramma illustra il flusso quando un utente accede a un'applicazione locale che usa l'autenticazione integrata di Windows.

![Diagramma del flusso di autenticazione di Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. L'utente immette l'URL per accedere all'applicazione locale tramite Application Proxy.
1. Il proxy di applicazione reindirizza la richiesta ai servizi di autenticazione di Azure AD per la preautenticazione. A questo punto, Azure AD applica gli eventuali criteri di autenticazione e autorizzazione appropriati, ad esempio l'autenticazione a più fattori. Se l'utente viene convalidato, Azure AD crea un token e lo invia all'utente.
1. L'utente passa il token al proxy di applicazione.
1. Application Proxy convalida il token e recupera il nome dell'entità utente (UPN) dal token. Invia poi la richiesta, l'UPN e il nome dell'entità servizio (SPN) al connettore tramite un canale protetto doppiamente autenticato.
1. Il connettore usa la negoziazione della delega vincolata Kerberos con l'istanza di AD locale, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.
1. Active Directory invia il token Kerberos per l'applicazione al connettore.
1. Il connettore invia la richiesta originale al server dell'applicazione, usando il token Kerberos ricevuto da Active Directory.
1. L'applicazione invia la risposta al connettore, che viene quindi restituita al servizio Application Proxy e infine all'utente.

## <a name="header-based-sso"></a>SSO basato su intestazione

L'accesso Single Sign-On basato su intestazione è valido per le applicazioni che usano le intestazioni HTTP per l'autenticazione.

Scegliere l'accesso Single Sign-On basato su intestazione quando per l'applicazione locale è configurata la funzionalità Application Proxy.

Per altre informazioni sull'autenticazione basata su intestazione, vedere [Accesso SSO basato su intestazione](application-proxy-configure-single-sign-on-with-headers.md).


## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Pianificare una distribuzione di Single Sign-On](plan-sso-deployment.md)
* [Accesso Single Sign-On con app locali](application-proxy-config-sso-how-to.md)