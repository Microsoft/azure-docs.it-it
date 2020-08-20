---
title: 'Esercitazione: Integrazione di Azure Active Directory con Ziflow | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Ziflow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9a9e2298b6707304df96a2e954015459534abfa9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546082"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Esercitazione: Integrazione di Azure Active Directory con Ziflow

Questa esercitazione descrive come integrare Ziflow con Azure Active Directory (Azure AD).
L'integrazione di Ziflow con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Ziflow.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Ziflow con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Ziflow, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Ziflow abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Ziflow supporta l'accesso Single Sign-On avviato da **provider di servizi**

## <a name="adding-ziflow-from-the-gallery"></a>Aggiunta di Ziflow dalla raccolta

Per configurare l'integrazione di Ziflow in Azure AD, è necessario aggiungere Ziflow dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Ziflow dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Ziflow**, selezionare **Ziflow** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Ziflow nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Ziflow usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Ziflow.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Ziflow, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Ziflow](#configure-ziflow-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Ziflow](#create-ziflow-test-user)** : per avere una controparte di Britta Simon in Ziflow collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Ziflow, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Ziflow** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Ziflow](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > I valori precedenti non sono valori reali. È necessario aggiornare il valore dell'ID univoco nell'identificatore e nell'URL di accesso con il valore effettivo, come descritto più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Ziflow** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-ziflow-single-sign-on"></a>Configurare l'accesso Single Sign-On di Ziflow

1. In un'altra finestra del Web browser accedere a Ziflow come amministratore della sicurezza.

2. Fare clic su Avatar nell'angolo in alto a destra e quindi fare clic su **Manage account** (Gestisci account).

    ![Gestione della configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. In alto a sinistra fare clic su **Single Sign-On**.

    ![Single Sign-On nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Nella pagina **Single Sign-On** seguire questa procedura:

    ![Single Sign-On nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. In **Type** (Tipo) selezionare **SAML2.0**.

    b. Nella casella di testo **Sign In URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Caricare nel **certificato di firma X509** il certificato codificato in base 64 scaricato dal portale di Azure.

    d. Nella casella di testo **Sign Out URL** (URL di disconnessione) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    e. Nella sezione **Configuration Settings for your Identifier Provider** (Impostazioni di configurazione per il provider di identità) copiare il valore dell'ID univoco evidenziato e aggiungerlo all'identificatore e all'URL di accesso nella sezione **Configurazione SAML di base** del portale di Azure.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Ziflow.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Ziflow**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Ziflow**.

    ![Collegamento di Ziflow nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ziflow-test-user"></a>Creare l'utente di test di Ziflow

Per consentire agli utenti di Azure AD di accedere a Ziflow, è necessario effettuarne il provisioning in Ziflow. Nel caso di Ziflow il provisioning è un'attività manuale.

Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere a Ziflow come amministratore della sicurezza.

2. Passare a **People** (Persone) nella parte superiore.

    ![People (Persone) nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Fare clic su **Add** (Aggiungi) e quindi su **Add user** (Aggiungi utente).

    ![Aggiunta di un utente nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Nella sezione **Add a user** (Aggiungi un utente) seguire questa procedura:

    ![Aggiunta di un utente nella configurazione di Ziflow](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last name** (Nome) immettere il cognome dell'utente, ad esempio Simon.

    d. Selezionare il ruolo di Ziflow.

    e. Fare clic su **Add 1 user** (Aggiungi 1 utente).

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Ziflow nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Ziflow per cui si è configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

