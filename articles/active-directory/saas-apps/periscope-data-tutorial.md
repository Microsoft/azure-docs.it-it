---
title: 'Esercitazione: Integrazione di Azure Active Directory con Periscope Data | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Periscope Data.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520858"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Esercitazione: Integrazione di Azure Active Directory con Periscope Data

Questa esercitazione descrive come integrare Periscope Data con Azure Active Directory (Azure AD).
L'integrazione di Periscope Data con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Periscope Data.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Periscope Data con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Periscope Data, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Periscope Data abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Periscope Data supporta l'accesso SSO avviato da **SP**

## <a name="adding-periscope-data-from-the-gallery"></a>Aggiunta di Periscope Data dalla raccolta

Per configurare l'integrazione di Periscope Data in Azure AD, è necessario aggiungere Periscope Data dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Periscope Data dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Periscope Data**, selezionare **Periscope Data** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Periscope Data nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Periscope Data usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Periscope Data.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Periscope Data, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Periscope Data](#configure-periscope-data-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Periscope Data](#create-periscope-data-test-user)** : per avere una controparte di Britta Simon in Periscope Data collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Periscope Data, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Periscope Data** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Periscope Data](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare uno degli URL seguenti:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Il valore dell'URL di accesso non è reale. Aggiornarlo con quello effettivo. Contattare il [team di supporto clienti di Periscope Data](mailto:support@periscopedata.com) per ottenere questo valore e il valore dell'identificatore ottenuto nella sezione **Configurare l'accesso Single Sign-On per Periscope Data** , come spiegato più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Configurare l'accesso Single Sign-On per Periscope Data

1. In un'altra finestra del Web browser accedere a Periscope Data come amministratore.

2. Aprire il menu con l'icona dell'ingranaggio in basso a sinistra e aprire il menu **Billing** > **Security** (Fatturazione > Sicurezza), quindi seguire questa procedura. Solo gli amministratori hanno accesso a queste impostazioni.

    ![Screenshot che mostra la finestra di dialogo "Security" con le impostazioni selezionate.](./media/periscope-data-tutorial/configure01.png)

    a. Copiare l'**URL dei metadati di federazione dell'app** del passaggio 5 **Certificato di firma SAML** e aprirlo in un browser. Verrà aperto un documento XML.

    b. Nella casella di testo **Single Sign-On** selezionare **Azure Active Directory**.

    c. Individuare il tag **SingleSignOnService** e incollare il valore di **Location** (Località) nella casella di testo **SSO URL** (URL SSO).

    d. Individuare il tag **SingleLogoutService** e incollare il valore di **Location** (Località) nella casella di testo **SLO URL** (URL SLO).

    e. Copiare il valore di **Identifier** (Identificatore) per l'istanza e incollarlo nella casella di testo **Identificatore (ID entità)** della sezione **Configurazione SAML di base** del portale di Azure.

    f. Individuare il primo tag del file XML, copiare il valore di **entityID** e incollarlo nella casella di testo **Issuer** (Emittente).

    g. Individuare il tag **IDPSSODescriptor** con protocollo SAML. In tale sezione individuare il tag **KeyDescriptor** con **use=signing**. Copiare il valore di **X509Certificate** e incollarlo nella casella di testo **Certificate** (Certificato).

    h. I siti con più spazi possono scegliere quello predefinito nell'elenco a discesa **Default Space** (Spazio predefinito). Questo sarà lo spazio a cui verranno aggiunti i nuovi utenti quando accedono per la prima volta a Periscope Data e di cui viene effettuato il provisioning tramite Accesso Single Sign-On Active Directory.

    i. Fare infine clic su **Save** (Salva) e **confermare** la modifica delle impostazioni SSO digitando **Logout**.

    ![Screenshot che mostra la finestra di dialogo "Update S S O Configuration" con "logout" digitato nella casella di testo e il pulsante "Confirm" selezionato.](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Periscope Data.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Periscope Data**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Periscope Data**.

    ![Collegamento di Periscope Data nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-periscope-data-test-user"></a>Creare l'utente di test di Periscope Data

Per consentire agli utenti di Azure AD di accedere a Periscope Data, è necessario effettuarne il provisioning in Periscope Data. Nel caso di Periscope Data il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Periscope Data come amministratore.

2. Fare clic sull'icona **Settings** (Impostazioni) in basso a sinistra nel menu e passare a **Permissions** (Autorizzazioni).

    ![Screenshot che mostra il menu "Settings" con l'opzione "Permissions" selezionata.](./media/periscope-data-tutorial/configure03.png)

3. Fare clic su **ADD USER** (AGGIUNGI UTENTE) e seguire questa procedura:

      ![Informazioni per la configurazione di Periscope Data](./media/periscope-data-tutorial/configure04.png)

    a. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    d. Fare clic su **ADD** (AGGIUNGI).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Periscope Data nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Periscope Data per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)