---
title: Problemi di configurazione dell'accesso SSO basato su password per Azure AD app della raccolta
description: Informazioni sui problemi comuni che si possono incontrare durante la configurazione dell'accesso Single Sign-On basato su password per applicazioni incluse nella raccolta delle applicazioni di Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fda2657a68fc8a3fd293c0c6001a71f5970548
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763517"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema nella configurazione dell'accesso Single Sign-On basato su password per un'applicazione nella raccolta di Azure AD

L'articolo descrive i problemi comuni che si possono riscontrare durante la configurazione dell'accesso **Single Sign-On basato su password** con un'applicazione della raccolta di Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Le credenziali vengono compilate, ma l'estensione non le invia

Questo problema si verifica in genere se il fornitore dell'applicazione ha modificato di recente la pagina di accesso per aggiungere un campo, ha modificato un identificatore usato per rilevare i campi nome utente e password o ha modificato l'esperienza di accesso alla propria applicazione. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni nelle integrazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Nel caso in cui una di queste integrazioni non funzioni correttamente, aprire un caso di supporto in modo che il problema possa essere risolto il più rapidamente possibile.

**Se l'utente è in contatto con il fornitore dell'applicazione, **può inviargli la soluzione offerta in modo che Microsoft possa lavorare con loro per integrare nativamente la loro applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Le credenziali vengono compilate e inoltrate, ma un messaggio nella pagina indica che le credenziali sono errate

Per risolvere questo problema, provare innanzitutto queste soluzioni:

- Chiedere all'utente di provare ad **accedere direttamente al sito Web dell'applicazione** con le credenziali archiviate.

  * Se riesce ad accedere, chiedergli di fare clic sul pulsante **Aggiorna credenziali** nell'**icona applicazione** della sezione **App** del [pannello di accesso dell'applicazione](https://myapps.microsoft.com/) per aggiornare le credenziali al nome utente e password funzionanti più recenti.

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante**Aggiorna credenziali**.

- Se è stato l'utente ad assegnarsi le credenziali, chiedergli di **verificare che la password dell'applicazione non sia scaduta** ed eventualmente **aggiornarla** accedendo all'applicazione direttamente.

  * Dopo che la password è stata aggiornata nell'applicazione, chiedere all'utente di fare clic sul pulsante **Aggiorna credenziali** nell'**icona applicazione** della sezione **App** del [pannello di accesso dell'applicazione](https://myapps.microsoft.com/) per aggiornare le credenziali al nome utente e password funzionanti più recenti.

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante**Aggiorna credenziali**.

- Chiedere all'utente di aggiornare l'estensione del browser del pannello di accesso seguendo la procedura indicata nella sezione [Come installare l'estensione del browser del pannello di accesso](#how-to-install-the-access-panel-browser-extension).

- Assicurarsi che l'estensione sia funzionante e abilitata nel browser dell'utente.

- Assicurarsi che gli utenti non stiano tentando di accedere all'applicazione dal pannello di accesso in **modalità privata o in incognito**. L'estensione del pannello di accesso non è supportata in queste modalità.

Se il problema persiste, è possibile che sia stata apportata una modifica sul lato applicazione che ha interrotto temporaneamente l'integrazione dell'applicazione con Azure AD. Il fornitore dell'applicazione potrebbe, ad esempio, aver introdotto nella pagina uno script con un comportamento che varia tra l'input manuale e l'input automatico e che causa l'interruzione nell'integrazione automatica. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>L'estensione funziona in Chrome e Firefox, ma non in Internet Explorer

Esistono due soluzioni principali a questo problema:

- A seconda delle impostazioni di sicurezza abilitate in Internet Explorer, se il sito Web non rientra in un'**area attendibile**, è possibile che allo script Microsoft venga impedito di eseguire l'applicazione.

  *  Per risolvere il problema, chiedere all'utente di **aggiungere il sito Web dell'applicazione** all'elenco **Siti attendibili** nelle **impostazioni di sicurezza di Internet Explorer**. Per le istruzioni dettagliate, rimandare l'utente all'articolo relativo a [come aggiungere un sito all'elenco dei siti attendibili](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5).

- La convalida di sicurezza di Internet Explorer può, in casi rari, fare sì che pagina venga caricata più lentamente rispetto all'esecuzione dello script Microsoft.

  * Questa situazione può variare a seconda della versione del browser in uso, della velocità del computer o del sito visitato. Si consiglia in questo caso di contattare il supporto Microsoft in modo da correggere l'integrazione per questa applicazione specifica.

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controllare se la pagina di accesso dell'applicazione è cambiata di recente o richiede un campo aggiuntivo

Se la pagina di accesso dell'applicazione è cambiata drasticamente, talvolta è possibile che si verifichi un'interruzione dell'integrazione. Un esempio è quando il fornitore dell'applicazione aggiunge un campo di accesso, un captcha o l'autenticazione a più fattori alle proprie esperienze. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Come installare l'estensione del browser per il pannello di accesso

Per installare l'estensione del browser per il pannello di accesso, seguire questa procedura:

1.  Aprire il [pannello di accesso](https://myapps.microsoft.com) in uno dei browser supportati e accedere come **utente** ad Azure AD.

2.  Fare clic su un'**applicazione con accesso SSO basato su password** nel pannello di accesso.

3.  Quando viene richiesto di installare il software, selezionare **Installa ora**.

4.  A seconda del browser in uso, si verrà indirizzati al collegamento per il download. **Aggiungere** l'estensione al browser.

5.  Se richiesto dal browser, scegliere se **abilitare** o **consentire** l'uso dell'estensione.

6.  Al termine dell'installazione, **riavviare** la sessione del browser.

7.  Accedere al pannello di accesso e verificare se è possibile **avviare** le applicazioni con accesso SSO basato su password

È anche possibile scaricare l'estensione per Chrome e Firefox dai collegamenti diretti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso per Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)

