---
title: 'Esercitazione: Integrazione di Azure Active Directory con Procore SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Procore SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e0a8e6786dbce1015d587cf8015e3624c97a5817
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553615"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Procore SSO

Questa esercitazione descrive come integrare Procore SSO con Azure Active Directory (Azure AD).
L'integrazione di Procore SSO con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Procore SSO.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Procore SSO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Procore SSO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Procore SSO abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Procore SSO supporta l'accesso SSO avviato da **IDP**

## <a name="adding-procore-sso-from-the-gallery"></a>Aggiungere Procore SSO dalla raccolta

Per configurare l'integrazione di Procore SSO in Azure AD, è necessario aggiungere Procore SSO dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Procore SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Procore SSO**, selezionare **Procore SSO** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Procore SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Procore SSO usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Procore SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Procore SSO, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Procore SSO](#configure-procore-sso-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Procore SSO](#create-procore-sso-test-user)** : per avere una controparte di Britta Simon in Procore SSO collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Procore SSO, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Procore SSO** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni sull'accesso Single Sign-On per l'URL e il dominio di Procore SSO](common/preintegrated.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Procore SSO** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-procore-sso-single-sign-on"></a>Configurare l'accesso Single Sign-On di Procore SSO

1. Per configurare l'accesso Single Sign-On sul lato **Procore SSO**, accedere al sito aziendale di Procore come amministratore.

2. Nell'elenco della casella degli strumenti scegliere **Admin** per aprire la pagina delle impostazioni SSO.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_tool_admin.png)

3. Incollare i valori nelle caselle come descritto di seguito.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Nella casella di testo **Single Sign On Issuer URL** (URL autorità di certificazione Single Sign-On) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella **SAML Sign On Target URL** (URL di destinazione accesso SAML). incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il file **XML metadati federazione** scaricato in precedenza dal portale di Azure e copiare il certificato nel tag denominato **X509Certificate**. Incollare il valore copiato nel **Single Sign On x509 Certificate** (Certificato x509 Single Sign-On).

4. Fare clic su **Save Changes** (Salva modifiche).

5. Dopo queste impostazioni, è necessario inviare il **nome di dominio**, ad esempio **contoso.com**, con cui si effettua l'accesso a Procore al [team di supporto Procore](https://support.procore.com/), che attiverà l'accesso Single Sign-On federato per quel dominio.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Procore SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Procore SSO**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Procore SSO**.

    ![Collegamento di Procore SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-procore-sso-test-user"></a>Creare l'utente di test di Procore SSO

Eseguire la procedura seguente per creare un utente di test di Procore sul lato Procore SSO.

1. Accedere al sito aziendale di Procore come amministratore.    

2. Nell'elenco a discesa della casella degli strumenti fare clic su **Directory** per aprire la pagina della directory aziendale.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Fare clic su **Add a Person** (Aggiungi una persona) per aprire il modulo e immettere le opzioni seguenti.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_add.png)

    a. Digitare il nome di Britta Simon nella casella di testo **First Name** (Nome), ad esempio **Britta**.

    b. Digitare il cognome di Britta Simon nella casella di testo **Last Name** (Cognome), ad esempio **Simon**.

    c. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio BrittaSimon@contoso.com.

    d. Selezionare **Permission Template** (Modello di autorizzazione) per **Apply Permission Template Later** (Applica modello di autorizzazione più tardi).

    e. Fare clic su **Crea**.

4. Verificare e aggiornare i dettagli del contatto appena aggiunto.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_check.png)

5. Se è richiesto un invito tramite posta elettronica, fare clic su **Save and Send Invitation** (Salva e invia invito) oppure scegliere **Save** (Salva) per salvare direttamente e completare la registrazione dell'utente.
    
    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Procore SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Procore SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

