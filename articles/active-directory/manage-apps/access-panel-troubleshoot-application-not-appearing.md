---
title: Un'applicazione assegnata non viene visualizzata nel pannello di accesso | Microsoft Docs
description: Risolvere il problema relativo alla mancata visualizzazione di un'applicazione nel pannello di accesso
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272755"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Un'applicazione assegnata non viene visualizzata nel pannello di accesso

Il pannello di accesso è un portale basato sul Web e consente a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Queste applicazioni sono configurate per conto dell'utente nel portale di Azure AD. Perché l'applicazione sia visibile nel pannello di accesso, è necessario che sia configurata correttamente e assegnata all'utente o a un gruppo di cui l'utente è membro.

Il tipo di app che l'utente può visualizzare rientra nelle categorie seguenti:

-   Applicazioni di Office 365

-   Applicazioni Microsoft e di terze parti configurate con il servizio Single Sign-On basato su federazione

-   Applicazioni SSO basate su password

-   Applicazioni con soluzioni SSO esistenti

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare prima

-   Se un'applicazione è stata appena aggiunta a un utente, provare ad accedere, a disconnettersi e ad accedere nuovamente al pannello di accesso dell'utente dopo alcuni minuti per vedere se l'applicazione è stata aggiunta.

-   Se è stata appena rimossa una licenza da un utente o da un gruppo di cui l'utente è membro, l'aggiornamento della visualizzazione potrebbe richiedere molto tempo a seconda delle dimensioni e della complessità del gruppo. Attendere un tempo più lungo prima di accedere al pannello di accesso.

## <a name="problems-related-to-application-configuration"></a>Problemi relativi alla configurazione dell'applicazione

È possibile che un'applicazione non venga visualizzata nel pannello di accesso dell'utente a causa di un'errata configurazione della stessa. L'articolo illustra alcuni modi per risolvere i problemi correlati alla configurazione dell'applicazione:

-   [Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Come configurare l'accesso Single Sign-On federato per un'applicazione non inclusa nella raccolta](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Come configurare un'applicazione Single Sign-On basato su password per un'applicazione della raccolta di Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Come configurare un'applicazione Single Sign-On basato password per un'applicazione non inclusa nella raccolta](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD

Per tutte le applicazioni nella raccolta di Azure AD con funzionalità Enterprise Single Sign-On abilitata è disponibile un'esercitazione dettagliata. Per le istruzioni dettagliate, è possibile accedere all'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperare il certificato e i metadati di Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurare i valori dei metadati di Azure AD nell'applicazione (URL di accesso, autorità emittente, URL di disconnessione e certificato)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il portale di Azure e accedere come amministratore globale o coamministratoreOpen the [Azure portal](https://portal.azure.com) and sign in as **global Administrator** or **Co-admin**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6.  Nella casella di testo **Immettere un nome** della sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione.

7.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome usando la casella di testo **Nome**.

9.  Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, sarà possibile visualizzare il riquadro di configurazione dell'applicazione.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurare l'accesso Single Sign-On per un'applicazione della raccolta di Azure AD

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Selezionare **Accesso basato su SAML** dal menu a discesa **Modalità**.

9. Immettere i valori necessari in **URL e dominio**. È necessario ottenere questi valori dal fornitore dell'applicazione.

   1. Per configurare l'applicazione come SSO avviato da provider di servizi, l'URL di accesso è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

   2. Per configurare l'applicazione come SSO avviato da IdP, l'URL di risposta è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

10. **Facoltativo:** fare clic su **Mostra impostazioni URL avanzate** se si vuole vedere i valori non obbligatori.

11. In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

12. **Facoltativo:** fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

    Per aggiungere un attributo:

    1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

    2. fare clic su **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

13. Fare clic su **Configura &lt;nome applicazione&gt;** per accedere alla documentazione che illustra come configurare l'accesso Single Sign-On nell'applicazione. Sono inoltre disponibili il certificato e gli URL dei metadati necessari per configurare l'accesso SSO con l'applicazione.

14. fare clic su **Salva** per salvare la configurazione.

15. Assegnare gli utenti all'applicazione.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione

Per selezionare l'identificatore utente o aggiungere gli attributi dell'utente, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Nella sezione **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**. L'opzione selezionata deve corrispondere al valore previsto nell'applicazione per autenticare l'utente.

   >[!NOTE] 
   >Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'elemento AuthRequest SAML. Per ulteriori informazioni, vedere l'articolo [Protocollo SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) nella sezione NameIDPolicy.
   >
   >

9. Per aggiungere gli attributi utente, fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

   Per aggiungere un attributo:

   1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   2. fare clic su **Salva**. Il nuovo attributo è visualizzato nella tabella.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Scaricare il certificato o i metadati di Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

   Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Come configurare l'accesso Single Sign-On federato per un'applicazione non inclusa nella raccolta

Per configurare un'applicazione non inclusa nella raccolta, è necessario disporre di Azure AD Premium e l'applicazione deve supportare SAML 2.0. Per altre informazioni sulle versioni di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperare il certificato e i metadati di Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurare i valori dei metadati di Azure AD nell'applicazione (URL di accesso, autorità emittente, URL di disconnessione e certificato)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)

Per configurare l'accesso Single Sign-On per un'applicazione non inclusa nella raccolta di Azure AD, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6. Fare clic su **Applicazione non nella raccolta** nella sezione **Aggiungi app personalizzata**.

7. Immettere il nome dell'applicazione nella casella di testo **Nome**.

8. Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

9. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

10. Selezionare **Accesso basato su SAML** nell'elenco a discesa **Modalità.**

11. Immettere i valori necessari in **URL e dominio**. È necessario ottenere questi valori dal fornitore dell'applicazione.

    1. Per configurare l'applicazione come SSO avviato da IdP, immettere l'URL di risposta e l'identificatore.

    2.  **Facoltativo:** per configurare l'applicazione come SSO avviato da provider di servizi, l'URL di accesso è un valore obbligatorio.

12. In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

13. **Facoltativo:** fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

    Per aggiungere un attributo:

    1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

    2. Fare clic su **Salva.** Il nuovo attributo verrà visualizzato nella tabella.

14. Fare clic su **Configura &lt;nome applicazione&gt;** per accedere alla documentazione che illustra come configurare l'accesso Single Sign-On nell'applicazione. Sono inoltre disponibili i certificati e gli URL di Azure AD necessari per l'applicazione.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione

Per selezionare l'identificatore utente o aggiungere gli attributi dell'utente, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Nella sezione **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**. L'opzione selezionata deve corrispondere al valore previsto nell'applicazione per autenticare l'utente.

   >[!NOTE] 
   >Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'elemento AuthRequest SAML. Per ulteriori informazioni, vedere l'articolo [Protocollo SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) nella sezione NameIDPolicy.
   >
   >

9. Per aggiungere gli attributi utente, fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

   Per aggiungere un attributo:

   1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   2. Fare clic su **Salva.** Il nuovo attributo verrà visualizzato nella tabella.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Scaricare il certificato o i metadati di Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare l'accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il portale di Azure e accedere come amministratore globale o coamministratoreOpen the [Azure portal](https://portal.azure.com) and sign in as **global Administrator** or **Co-admin**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6.  Nella casella di testo **Immettere un nome** della sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione.

7.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome usando la casella di testo **Nome**.

9.  Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, sarà possibile visualizzare il riquadro di configurazione dell'applicazione.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Configurare l'applicazione per un accesso Single Sign-On basato su password

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Selezionare la modalità **Accesso basato su password**.

9. [Assegnare utenti all'applicazione](#how-to-assign-a-user-to-an-application-directly).

10. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali**, quindi immettendo il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Come configurare l'accesso Single Sign-On basato su password per un'applicazione non inclusa nella raccolta

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione non inclusa nella raccolta](#add-a-non-gallery-application)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Aggiungere un'applicazione non inclusa nella raccolta

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il portale di [Azure](https://portal.azure.com) e accedere come **amministratore globale** o **coamministratore.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6.  Fare clic su**Applicazione non nella raccolta**.

7.  Immettere il nome dell'applicazione nella casella di testo **Nome**. Fare clic su **Aggiungi**.

Dopo un breve periodo di tempo, sarà possibile visualizzare il riquadro di configurazione dell'applicazione.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Configurare l'applicazione per l'accesso Single Sign-On con password

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1.  Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

    1.  Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  Immettere l'**URL di accesso**. Questo è l'URL in cui gli utenti immettono il nome utente e la password per accedere. Verificare che i campi di accesso siano visibili nell'URL.

10. [Assegnare utenti all'applicazione](#how-to-assign-a-user-to-an-application-directly).

11. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali**, quindi immettendo il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemi relativi all'assegnazione di applicazioni agli utenti

Un utente potrebbe non vedere un'applicazione nel pannello di accesso perché non è stato assegnato all'applicazione. Di seguito sono elencate alcuni modi per verificare questa situazione:

-   [Controllare se un utente è assegnato all'applicazione](#check-if-a-user-is-assigned-to-the-application)

-   [Come assegnare un utente direttamente a un'applicazione](#how-to-assign-a-user-to-an-application-directly)

-   [Controllare se a un utente è stata assegnata una licenza relativa all'applicazione](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Come assegnare una licenza a un utente](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controllare se un utente è assegnato all'applicazione

Per controllare se un utente è assegnato all'applicazione, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

6. **Cercare** il nome dell'applicazione in questione.

7. Fare clic su **Utenti e gruppi**.

8. Controllare se un utente è assegnato all'applicazione.

   * In caso contrario, seguire la procedura descritta in "Come assegnare un utente direttamente a un'applicazione".

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Come assegnare un utente direttamente a un'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo ** o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il mouse **sull'utente** nell'elenco per visualizzare una **casella di controllo.** Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati saranno in grado di avviare queste applicazioni nel pannello di accesso.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controllare se un utente è incluso nella licenza relativa all'applicazione

Per controllare le licenze assegnate a un utente, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fai clic su **Utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti gli utenti**.

6. **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7. Fare clic su **Licenze** per verificare quali licenze sono assegnate all'utente.

   * Se l'utente è assegnato a una licenza Office, le applicazioni Office di prima entità verranno visualizzate nel pannello di accesso dell'utente.

### <a name="how-to-assign-a-user-a-license"></a>Come assegnare una licenza a un utente 

Per assegnare una licenza a un utente, eseguire questa procedura:

1.  Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fai clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per verificare quali licenze sono assegnate all'utente.

8.  fare clic sul pulsante **Assegna.**

9.  Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.

10. **Facoltativo**: fare clic sulla voce **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Fare clic su **OK** al termine.

11. Fare clic sul pulsante **Assegna** per assegnare queste licenze all'utente.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemi relativi all'assegnazione di applicazioni a gruppi

Un utente può visualizzare un'applicazione nel pannello di accesso perché è membro di un gruppo assegnato all'applicazione. Di seguito sono elencate alcuni modi per verificare questa situazione:

-   [Controllare le appartenenze a gruppi dell'utente](#check-a-users-group-memberships)

-   [Come assegnare un'applicazione direttamente a un gruppo](#how-to-assign-an-application-to-a-group-directly)

-   [Controllare se un utente è membro di un gruppo assegnato a una licenza](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Come assegnare una licenza a un gruppo](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Controllare le appartenenze a gruppi dell'utente

Per controllare l'appartenenza a un gruppo, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fai clic su **Utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti gli utenti**.

6. **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7. Fare clic su **Gruppi**.

8. Controllare se un utente è membro di un gruppo assegnato all'applicazione.

   * Se si vuole rimuovere l'utente dal gruppo, **fare clic sulla riga** del gruppo e selezionare Elimina.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Come assegnare un'applicazione direttamente a un gruppo

Per assegnare uno o più gruppi direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo del gruppo** a cui si vuole eseguire l'assegnazione.

11. Posizionare il puntatore sul **gruppo** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo del gruppo per aggiungere il gruppo all'elenco **selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un gruppo**, digitare un altro **nome completo di gruppo** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere il gruppo all'elenco **selezionato**.

13. Dopo avere selezionato i gruppi, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare ai gruppi selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione ai gruppi selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati saranno in grado di avviare queste applicazioni nel pannello di accesso.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Controllare se un utente è membro di un gruppo assegnato a una licenza

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. fai clic su **Utenti e gruppi** nel menu di navigazione.

5. Fare clic su **Tutti gli utenti**.

6. **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7. Fare clic su **Gruppi**.

8. Fare clic sulla riga di un gruppo specifico.

9. Fare clic su **Licenze** per vedere le licenze assegnate al gruppo.

   * Se il gruppo è assegnato a una licenza Office, ciò potrebbe abilitare alcune applicazioni Office di prima entità ad essere visualizzate nel pannello di accesso dell'utente.

### <a name="how-to-assign-a-license-to-a-group"></a>Come assegnare una licenza a un gruppo

Per assegnare una licenza a un gruppo, seguire questa procedura:

1.  Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  fai clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti i gruppi**.

6.  **Cercare** il gruppo desiderato e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate al gruppo.

8.  fare clic sul pulsante **Assegna.**

9.  Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.

10. **Facoltativo**: fare clic sulla voce **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Fare clic su **OK** al termine.

11. Fare clic sul pulsante **Assegna** per assegnare queste licenze al gruppo. L'operazione potrebbe richiedere molto tempo a seconda della dimensione e della complessità del gruppo.

>[!NOTE]
>Per eseguire l'operazione più velocemente, è consigliabile assegnare temporaneamente una licenza direttamente all'utente. 
>
>

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere nuovi utenti ad Azure Active DirectoryAdd new users to Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

