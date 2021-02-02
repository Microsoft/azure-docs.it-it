---
title: Problemi di accesso a un'applicazione Microsoft | Microsoft Docs
description: Risolvere i problemi comuni riscontrati durante l'accesso alle applicazioni Microsoft di terze parti usando Azure AD (ad esempio Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260012"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemi di accesso a un'applicazione Microsoft

Le applicazioni Microsoft (ad esempio Exchange, SharePoint, Yammer e così via) vengono assegnate e gestite in modo leggermente diverso rispetto alle applicazioni SaaS di terze parti o ad altre applicazioni che si integrano con Azure AD per Single Sign-on.

Vi sono tre principali modi con cui un utente può accedere a un'applicazione pubblicata da Microsoft.

-   Per le applicazioni nel Microsoft 365 o in altri gruppi a pagamento, agli utenti viene concesso l'accesso tramite l' **assegnazione delle licenze** direttamente al proprio account utente oppure tramite un gruppo che usa la funzionalità di assegnazione delle licenze basata sui gruppi.

-   Per le applicazioni che Microsoft o una terza parte pubblica per l'utilizzo gratuito da parte di chiunque, gli utenti possono ottenere l'accesso tramite **consenso dell'utente**. Ciò significa che gli utenti accedono all'applicazione con l'account Azure AD aziendale o dell'istituto di istruzione e consentono a tale applicazione di accedere a un set limitato di dati nell'account.

-   Per le applicazioni che Microsoft o una terza parte pubblica per l'utilizzo gratuito da parte di chiunque, gli utenti possono ottenere l'accesso tramite il **consenso dell'amministratore**. Ciò significa che un amministratore ha determinato che l'applicazione può essere usata da qualsiasi utente dell'organizzazione e, tale scopo, ha effettuato l'accesso all'applicazione con un account di amministratore globale e ha consentito l'accesso a tutti gli utenti dell'organizzazione.

Per risolvere il problema, iniziare dalla sezione [Aree generali da considerare per i problemi di accesso alle applicazioni](#general-problem-areas-with-application-access-to-consider) e quindi leggere la Procedura dettagliata: passaggi per risolvere i problemi di accesso alle applicazioni Microsoft per maggiori dettagli.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Aree generali da considerare per i problemi di accesso alle applicazioni

Di seguito è riportato un elenco delle aree problematiche generali che è possibile esaminare se si ha un'idea del punto da cui iniziare. Per procedere rapidamente è tuttavia consigliabile vedere la procedura dettagliata Passaggi per risolvere i problemi di accesso alle applicazioni Microsoft.

-   [Problemi relativi all'account dell'utente](#problems-with-the-users-account)

-   [Problemi relativi ai gruppi](#problems-with-groups)

-   [Problemi relativi ai criteri di accesso condizionale](#problems-with-conditional-access-policies)

-   [Problemi relativi al consenso dell'applicazione](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Passaggi per risolvere i problemi di accesso alle applicazioni Microsoft

Di seguito sono riportati alcuni problemi comuni che vengono riscontrati quando gli utenti non riescono ad accedere a un'applicazione Microsoft.

- Problemi generali da verificare prima

  * Verificare che l'utente acceda all'**URL corretto** e non a un URL dell'applicazione locale.

  * Verificare che l'account dell'utente non sia **bloccato**.

  * Verificare che l'**account dell'utente sia presente** in Azure Active Directory. [Controllare se esiste un account utente in Azure Active Directory](#problems-with-the-users-account)

  * Assicurarsi che l'account dell'utente sia **abilitato** per gli accessi. [Controllare lo stato dell'account di un utente](#problems-with-the-users-account)

  * Verificare che la **password dell'utente non sia scaduta o non sia stata dimenticata**. [Reimpostare la password di un utente](#reset-a-users-password) o [Abilitare la reimpostazione self-service delle password](../authentication/tutorial-enable-sspr.md)

  * Verificare che **Multi-Factor Authentication** non blocchi l'accesso utente. [Controllare lo stato di autenticazione a più fattori di un utente](#check-a-users-multi-factor-authentication-status) o [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info)

  * Verificare che un criterio di **accesso condizionale** o di **protezione delle identità** non blocchi l'accesso utente. [Controllare uno specifico criterio di accesso condizionale](#problems-with-conditional-access-policies) o [controllare i criteri di accesso condizionale di un'applicazione specifica](#check-a-specific-applications-conditional-access-policy) o [disabilitare uno specifico criterio di accesso condizionale](#disable-a-specific-conditional-access-policy)

  * Verificare che le **informazioni di contatto per l'autenticazione** di un utente siano aggiornate per permettere l'applicazione di Multi-Factor Authentication o di criteri di accesso condizionale. [Controllare lo stato di autenticazione a più fattori di un utente](#check-a-users-multi-factor-authentication-status) o [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info)

- Per le applicazioni **Microsoft** **che richiedono una licenza** (ad esempio Office365), di seguito sono riportati alcuni problemi specifici da verificare dopo aver escluso i problemi generali sopra elencati:

  * Assicurarsi che all'utente sia **assegnata una licenza**. [Controllare le licenze assegnate a un utente](#check-a-users-assigned-licenses) o [Controllare le licenze assegnate a un gruppo](#check-a-groups-assigned-licenses)

  * Se la licenza è **assegnata a un** **gruppo statico**, assicurarsi che l'**utente sia membro** di tale gruppo. [Controllare le appartenenze a gruppi dell'utente](#check-a-users-group-memberships)

  * Se la licenza è **assegnata a un** **gruppo dinamico**, assicurarsi che **la regola di gruppo dinamico sia impostata correttamente**. [Controllare i criteri di appartenenza di un gruppo dinamico](#check-a-dynamic-groups-membership-criteria)

  * Se la licenza è **assegnata a un** **gruppo dinamico**, assicurarsi che il gruppo dinamico abbia **completato l'elaborazione** della propria appartenenza e che l'**utente sia un membro** (questa operazione può richiedere un certo tempo). [Controllare le appartenenze a gruppi dell'utente](#check-a-users-group-memberships)

  *  Dopo aver verificato che la licenza è assegnata, assicurarsi che **non sia scaduta**.

  *  Accertarsi che la licenza sia **relativa all'applicazione** a cui gli utenti stanno accedendo.

- Per le applicazioni **Microsoft** **che non richiedono una licenza**, di seguito sono riportati altri aspetti da controllare:

  * Se l'applicazione richiede autorizzazioni a **livello di utente** , ad esempio "accedi alla cassetta postale di questo utente", assicurarsi che l'utente abbia eseguito l'accesso all'applicazione e abbia eseguito un' **operazione di consenso a livello di utente** per consentire all'applicazione di accedere ai dati.

  * Se l'applicazione richiede **autorizzazioni a livello di amministratore** (ad esempio "Accesso alle cassette postali di tutti gli utenti"), assicurarsi che un amministratore globale abbia eseguito un'**operazione di consenso a livello di amministratore per conto di tutti gli utenti** dell'organizzazione.

## <a name="problems-with-the-users-account"></a>Problemi relativi all'account dell'utente

L'accesso all'applicazione può essere bloccato a causa di un problema relativo a un utente assegnato all'applicazione. Di seguito sono riportati alcuni modi in cui è possibile risolvere i problemi relativi agli utenti e alle impostazioni degli account:

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

10. Comunicare questa nuova password all'utente, che potrebbe doverla modificare durante il successivo accesso ad Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Per abilitare la reimpostazione self-service delle password, eseguire questa procedura di distribuzione:

-   [Consentire agli utenti di reimpostare le password di Azure AD](../authentication/tutorial-enable-sspr.md)

-   [Consentire agli utenti di reimpostare o modificare le password locali di Active Directory](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Controllare lo stato di autenticazione a più fattori di un utente

Per controllare lo stato di autenticazione a più fattori di un utente, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fare clic su **utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti gli utenti**.

6. Fare clic sul pulsante **Multi-Factor Authentication** nella parte superiore del riquadro.

7. Quando viene caricato il **portale di amministrazione di Multi-Factor Authentication**, assicurarsi di visualizzare la scheda **Utenti**.

8. Trovare l'utente nell'elenco degli utenti tramite una ricerca, l'applicazione di un filtro o l'ordinamento.

9. Selezionare l'utente nell'elenco di utenti e scegliere **Abilita**, **Disabilita** o **Applica** per l'autenticazione a più fattori nel modo desiderato.

   * **Nota**: Se lo stato di un utente è impostato su **Applicato**, è possibile impostarlo temporaneamente su **Disattivato** per permettere all'utente di accedere di nuovo al proprio account. Quando l'utente è connesso, è possibile modificarne di nuovo lo stato in **Attivato** per chiedergli di registrare di nuovo le informazioni di contatto durante il successivo accesso. In alternativa, è possibile eseguire la procedura descritta in [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info) per verificare o impostare questi dati per l'utente.

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

## <a name="problems-with-groups"></a>Problemi relativi ai gruppi

L'accesso all'applicazione può essere bloccato a causa di un problema relativo a un gruppo assegnato all'applicazione. Di seguito sono riportati alcuni modi in cui è possibile risolvere i problemi relativi ai gruppi e all'appartenenza a gruppi:

-   [Controllare l'appartenenza di un gruppo](#check-a-groups-membership)

-   [Controllare i criteri di appartenenza di un gruppo dinamico](#check-a-dynamic-groups-membership-criteria)

-   [Controllare le licenze assegnate di un gruppo](#check-a-groups-assigned-licenses)

-   [Rielaborare le licenze di un gruppo](#reprocess-a-groups-licenses)

-   [Assegnare una licenza a un gruppo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Controllare l'appartenenza di un gruppo

Per controllare l'appartenenza a un gruppo, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti i gruppi**.

6.  **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **Membri** per esaminare l'elenco di utenti assegnati a questo gruppo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Controllare i criteri di appartenenza di un gruppo dinamico 

Per controllare i criteri di appartenenza di un gruppo dinamico, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti i gruppi**.

6.  **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **Regole di appartenenza dinamica**.

8.  Verificare la regola **semplice** o **avanzata** definita per questo gruppo e assicurarsi che l'utente che deve appartenere al gruppo soddisfi i criteri relativi.

### <a name="check-a-groups-assigned-licenses"></a>Controllare le licenze assegnate di un gruppo

Per controllare le licenze assegnate a un gruppo, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti i gruppi**.

6.  **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate al gruppo.

### <a name="reprocess-a-groups-licenses"></a>Rielaborare le licenze di un gruppo

Per rielaborare le licenze assegnate a un gruppo, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fare clic su **utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti i gruppi**.

6. **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7. Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate al gruppo.

8. Fare clic sul pulsante **Rielabora** per assicurarsi che le licenze assegnate ai membri di questo gruppo siano aggiornate. L'operazione potrebbe richiedere molto tempo a seconda della dimensione e della complessità del gruppo.

   >[!NOTE]
   >Per eseguire l'operazione più velocemente, è consigliabile assegnare temporaneamente una licenza direttamente all'utente. [Assegnare una licenza a un utente](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Assegnare una licenza a un gruppo

Per assegnare una licenza a un gruppo, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fare clic su **utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti i gruppi**.

6. **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7. Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate al gruppo.

8. fare clic sul pulsante **assegna** .

9. Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.

10. **Facoltativo**: fare clic sulla voce **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Fare clic su **OK** al termine.

11. Fare clic sul pulsante **Assegna** per assegnare queste licenze al gruppo. L'operazione potrebbe richiedere molto tempo a seconda della dimensione e della complessità del gruppo.

    >[!NOTE]
    >Per eseguire l'operazione più velocemente, è consigliabile assegnare temporaneamente una licenza direttamente all'utente. [Assegnare una licenza a un utente](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemi relativi ai criteri di accesso condizionale

### <a name="check-a-specific-conditional-access-policy"></a>Verifica di un criterio di accesso condizionale specifico

Per verificare o convalidare un singolo criterio di accesso condizionale:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fare clic su **applicazioni aziendali** nel menu di navigazione.

5. fare clic sull'elemento di navigazione **accesso condizionale** .

6. Fare clic sul criterio che si desidera controllare.

7. Verificare che non siano presenti condizioni specifiche, assegnazioni o altre impostazioni che potrebbero bloccare l'accesso degli utenti.

   >[!NOTE]
   >È possibile disabilitare temporaneamente questo criterio per assicurarsi che non influisca sugli accessi. A tale scopo, impostare l'opzione **Abilita criterio** su **No** e fare clic sul pulsante **Salva** .
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Verificare i criteri di accesso condizionale di un'applicazione specifica

Per verificare o convalidare i criteri di accesso condizionale attualmente configurati di un'applicazione:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **applicazioni aziendali** nel menu di navigazione.

5.  Fare clic su **Tutte le applicazioni**.

6.  Cercare l'applicazione a cui si è interessati o che l'utente stia tentando di accedere a per nome visualizzato dell'applicazione o ID applicazione.

     >[!NOTE]
     >Se l'applicazione che si sta cercando non è visualizzata, fare clic sul pulsante **Filtro** ed espandere l'ambito dell'elenco a **Tutte le applicazioni**. Se si desidera visualizzare più colonne, fare clic sul pulsante **Colonne** per aggiungere ulteriori dettagli per le applicazioni.
     >
     >

7.  fare clic sull'elemento di navigazione **accesso condizionale** .

8.  Fare clic sul criterio che si desidera controllare.

9.  Verificare che non vi siano condizioni specifiche, assegnazioni o altre impostazioni che possano bloccare l'accesso dell'utente.

     >[!NOTE]
     >È possibile disabilitare temporaneamente questo criterio per assicurarsi che non influisca sugli accessi. A tale scopo, impostare l'opzione **Abilita criterio** su **No** e fare clic sul pulsante **Salva** .
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Disabilitare un criterio di accesso condizionale specifico

Per verificare o convalidare un singolo criterio di accesso condizionale:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fare clic su **applicazioni aziendali** nel menu di navigazione.

5.  fare clic sull'elemento di navigazione **accesso condizionale** .

6.  Fare clic sul criterio che si desidera controllare.

7.  Disabilitare il criterio impostando **Abilita criterio** su **No** e facendo clic sul pulsante **Salva**.

## <a name="problems-with-application-consent"></a>Problemi relativi al consenso dell'applicazione

L'accesso all'applicazione può essere bloccato poiché non è stata eseguita l'operazione appropriata per consenso delle autorizzazioni. Di seguito sono riportati alcuni modi in cui è possibile risolvere i problemi relativi al consenso per le applicazioni:

-   [Eseguire un'operazione di consenso a livello di utente](#perform-a-user-level-consent-operation)

-   [Eseguire un'operazione di consenso a livello di amministratore per qualsiasi applicazione](#perform-administrator-level-consent-operation-for-any-application)

-   [Eseguire un'operazione di consenso a livello di amministratore per un'applicazione a tenant singolo](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Eseguire un'operazione di consenso a livello di amministratore per un'applicazione multi-tenant](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Eseguire un'operazione di consenso a livello di utente

-   Per qualsiasi applicazione abilitata a OpenID Connect che richiede le autorizzazioni, lo spostamento sulla schermata di accesso rappresenta un consenso a livello di utente all'applicazione per l'utente connesso.

-   Se si desidera eseguire questa operazione a livello di codice, vedere [Richiesta di consenso per un singolo utente](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Eseguire un'operazione di consenso a livello di amministratore per qualsiasi applicazione

-   **Solo per le applicazioni sviluppate usando il modello di applicazione V1**, è possibile imporre questo consenso a livello di amministratore aggiungendo "**?prompt=admin\_consent**" alla fine dell'URL di accesso di un'applicazione.

-   Per **qualsiasi applicazione sviluppata usando il modello di applicazione V2**, è possibile applicare questo consenso a livello di amministratore attenendosi alle istruzioni riportate nella sezione **Richiedere le autorizzazioni da un amministratore di directory** di [Uso dell'endpoint di consenso dell'amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Eseguire un'operazione di consenso a livello di amministratore per un'applicazione a tenant singolo

-   Per le **applicazioni a tenant singolo** che richiedono autorizzazioni (ad esempio quelle sviluppate internamente o di proprietà dell'organizzazione), è possibile eseguire un'operazione di **consenso a livello di amministratore** per conto di tutti gli utenti accedendo come amministratore globale e facendo clic sul pulsante **Concedi autorizzazioni** nella parte superiore del riquadro **Registro applicazioni -&gt; Tutte le applicazioni -&gt; Seleziona un'app -&gt; Autorizzazioni necessarie**.

-   Per **qualsiasi applicazione sviluppata usando il modello di applicazione V1 o V2**, è possibile applicare questo consenso a livello di amministratore attenendosi alle istruzioni riportate nella sezione **Richiedere le autorizzazioni da un amministratore di directory** di [Uso dell'endpoint di consenso dell'amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Eseguire un'operazione di consenso a livello di amministratore per un'applicazione multi-tenant

-   Per le **applicazioni multi-tenant** che richiedono autorizzazioni (ad esempio un'applicazione sviluppata da terza parte o da Microsoft), è possibile eseguire un'operazione di **consenso a livello di amministratore**. Accedere come amministratore globale e fare clic sul pulsante **Concedi autorizzazioni** nel riquadro **Applicazioni aziendali -&gt; Tutte le applicazioni -&gt; Seleziona un'app -&gt; Autorizzazioni** (presto disponibile).

-   È anche possibile applicare questo consenso a livello di amministratore attenendosi alle istruzioni riportate nella sezione **Richiedere le autorizzazioni da un amministratore di directory** di [Uso dell'endpoint di consenso dell'amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Passaggi successivi
[Uso dell'endpoint di consenso dell'amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)