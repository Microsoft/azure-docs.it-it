---
title: Problemi di accesso al sito Web del pannello di accesso | Microsoft Docs
description: Linee guida per risolvere i possibili problemi durante il tentativo di accedere per usare il pannello di accesso
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760814"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problemi di accesso al sito Web del pannello di accesso

Il pannello di accesso è un portale basato sul Web che consente a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Un utente dotato di edizioni di Azure AD può anche usare le funzionalità di gestione self-service di gruppi e app tramite il pannello di accesso. Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione Azure.

Gli utenti possono accedere al pannello di accesso se hanno un account aziendale o dell'istituto di istruzione in Azure AD.

-   Gli utenti possono essere autenticati direttamente tramite Azure AD.

-   Gli utenti possono essere autenticati tramite Active Directory Federation Services (AD FS).

-   Gli utenti possono essere autenticati tramite Windows Server Active Directory.

Se un utente ha una sottoscrizione per Azure o un abbonamento a Office 365 e ha usato il portale di Azure o un'applicazione di Office 365, potrà usare il pannello di accesso in tutta semplicità senza dover eseguire di nuovo l'accesso. Agli utenti non autenticati verrà richiesto di eseguire l'accesso con il nome utente e la password del rispettivo account in Azure AD. Se l'organizzazione ha configurato la federazione, sarà sufficiente digitare il nome utente.

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare prima 

-   Assicurarsi che l'utente acceda all' **URL corretto**:<https://myapps.microsoft.com>

-   Verificare che il browser dell'utente abbia aggiunto l'URL ai **siti attendibili**

-   Verificare che l'account dell'utente sia **abilitato** per l'accesso.

-   Verificare che l'account dell'utente non sia **bloccato**.

-   Verificare che la **password dell'utente non sia scaduta o non sia stata dimenticata**.

-   Verificare che **Multi-Factor Authentication** non blocchi l'accesso utente.

-   Verificare che un criterio di **accesso condizionale** o di **protezione delle identità** non blocchi l'accesso utente.

-   Verificare che le **informazioni di contatto per l'autenticazione** di un utente siano aggiornate per consentire l'applicazione dei criteri di accesso condizionale o Multi-Factor Authentication.

-   Assicurarsi anche di cancellare i cookie del browser e riprovare ad accedere.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Applicazione dei requisiti del browser per il pannello di accesso

Per il pannello di accesso è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Per usare il servizio Single Sign-On (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

Per l'accesso Single Sign-On basato su password il browser dell'utente finale può essere uno dei seguenti:

-   Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive

-   Microsoft Edge su Windows 10 Anniversary Edition o versioni successive 

-   Chrome in Windows 7 o versione successiva e MacOS X o versione successiva

-   Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva


## <a name="problems-with-the-users-account"></a>Problemi relativi all'account dell'utente

L'accesso al pannello di accesso può essere bloccato a causa di un problema relativo all'account dell'utente. Di seguito sono riportati alcuni modi in cui è possibile risolvere i problemi relativi agli utenti e alle impostazioni degli account:

-   [Controllare se esiste un account utente in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Controllare lo stato dell'account di un utente](#check-a-users-account-status)

-   [Reimposta la password di un utente](#reset-a-users-password)

-   [Abilitare la reimpostazione self-service delle password](#enable-self-service-password-reset)

-   [Controllare lo stato di autenticazione a più fattori di un utente](#check-a-users-multi-factor-authentication-status)

-   [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info)

-   [Controllare le appartenenze a gruppi dell'utente](#check-a-users-group-memberships)

-   [Controllare le licenze assegnate di un utente](#check-a-users-assigned-licenses)

-   [Assegnare una licenza a un utente](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controllare se esiste un account utente in Azure Active Directory

Per controllare se l'account di un utente è presente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Controllare le proprietà dell'oggetto utente per assicurarsi che vengano specificate come previsto e che non manchi alcun dato.

### <a name="check-a-users-account-status"></a>Controllare lo stato dell'account di un utente

Per controllare lo stato dell'account di un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  fare clic su **profilo**.

8.  In **Impostazioni** assicurarsi che l'opzione **Blocca l'accesso** sia impostata su **No**.

### <a name="reset-a-users-password"></a>Reimpostare la password di un utente

Per reimpostare la password di un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic sul pulsante **Reimposta password** nella parte superiore del riquadro dell'utente.

8.  Fare clic sul pulsante **Reimposta password** nel riquadro **Reimposta password** visualizzato.

9.  Copiare la **password temporanea** o **immettere una nuova password** per l'utente.

10. Comunicare questa nuova password all'utente, che dovrà modificarla al successivo accesso ad Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Per abilitare la reimpostazione self-service delle password, seguire questa procedura di distribuzione:

-   [Consentire agli utenti di reimpostare le password di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Consentire agli utenti di reimpostare o modificare le password locali di Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Controllare lo stato di autenticazione a più fattori di un utente

Per controllare lo stato di autenticazione a più fattori di un utente, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fare clic su **utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti gli utenti**.

6. Fare clic sul pulsante **Multi-Factor Authentication** nella parte superiore del riquadro.

7. Una volta caricato il **portale di amministrazione di multi-factor authentication** , assicurarsi di essere nella scheda **utenti** .

8. Trovare l'utente nell'elenco degli utenti tramite una ricerca, l'applicazione di un filtro o l'ordinamento.

9. Selezionare l'utente nell'elenco di utenti e scegliere **Abilita**, **Disabilita** o **Applica** per l'autenticazione a più fattori nel modo desiderato.

   >[!NOTE]
   >Se lo stato di un utente è impostato su **Applicato**, è possibile impostarlo temporaneamente su **Disattivato** per permettere all'utente di accedere di nuovo al proprio account. A questo punto, è possibile modificare nuovamente lo stato in **Attivato** in modo che l'utente debba registrare di nuovo le informazioni di contatto al successivo accesso. In alternativa, è possibile eseguire la procedura descritta in [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info) per verificare o impostare questi dati per l'utente.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Controllare le informazioni di contatto per l'autenticazione di un utente

Per controllare le informazioni di contatto per l'autenticazione di un utente usate per l'autenticazione a più fattori, l'accesso condizionale, la protezione delle identità e la reimpostazione della password, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  fare clic su **profilo**.

8.  Scorrere fino a **Informazioni di contatto per l'autenticazione**.

9.  Fare clic su **Verifica** per controllare i dati registrati per l'utente e aggiornarli nel modo necessario.

### <a name="check-a-users-group-memberships"></a>Controllare le appartenenze a gruppi dell'utente

Per controllare l'appartenenza a gruppi di un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Gruppi** per visualizzare i gruppi di cui l'utente è membro.

### <a name="check-a-users-assigned-licenses"></a>Controllare le licenze assegnate di un utente

Per controllare le licenze assegnate a un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per verificare quali licenze sono assegnate all'utente.

### <a name="assign-a-user-a-license"></a>Assegnare una licenza a un utente 

Per assegnare una licenza a un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per verificare quali licenze sono assegnate all'utente.

8.  fare clic sul pulsante **assegna** .

9.  Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.

10. **Facoltativo**: fare clic sulla voce **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Fare clic su **OK** al termine.

11. Fare clic sul pulsante **Assegna** per assegnare queste licenze all'utente.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se questi passaggi per la risoluzione dei problemi non risolvono il problema

aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione

-   UPN (indirizzo di posta elettronica dell'utente)

-   ID tenant

-   Tipo di browser

-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore

-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
