---
title: 'Esercitazione: Integrazione di Azure Active Directory con MobileIron | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4e997c6f2d0826c8914c671d625cc1c49bb018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160454"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Esercitazione: integrazione di Azure Active Directory con MobileIron

Questa esercitazione descrive come integrare MobileIron con Azure Active Directory (Azure AD).
L'integrazione di MobileIron con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a MobileIron.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a MobileIron con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con MobileIron, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di MobileIron abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MobileIron supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-mobileiron-from-the-gallery"></a>Aggiunta di MobileIron dalla raccolta

Per configurare l'integrazione di MobileIron in Azure AD, è necessario aggiungere MobileIron dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere MobileIron dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **MobileIron**, selezionare **MobileIron** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![MobileIron nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MobileIron usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MobileIron.

Per configurare e testare l'accesso Single Sign-On di Azure AD con MobileIron, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di MobileIron](#configure-mobileiron-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di MobileIron](#create-mobileiron-test-user)** : per avere una controparte di Britta Simon in MobileIron collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con MobileIron, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [MobileIron](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio MobileIron](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.mobileiron.com/<key>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio MobileIron](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterranno i valori di chiave e host dal portale di amministrazione di MobileIron illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Configurare l'accesso Single Sign-On per MobileIron

1. In un'altra finestra del Web browser accedere al sito aziendale di MobileIron come amministratore.

2. Passare a **Admin** > **Identity** (Amministratore > Identità) e selezionare l'opzione **AAD** nel campo **Info on Cloud IDP Setup** (Informazioni sulla configurazione del provider di identità cloud).

    ![Pulsante Admin nella configurazione dell'accesso Single Sign-On](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Copiare i valori di **Chiave** e **Host** e incollarli per completare gli URL nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Pulsante Admin nella configurazione dell'accesso Single Sign-On](./media/mobileiron-tutorial/key.png)

4. Nel campo **Export metadata file from AAD and import to MobileIron Cloud** (Esporta file di metadati da AAD e importa nel cloud di MobileIron) fare clic su **Scegli file** per caricare i metadati scaricati dal portale di Azure. Dopo il caricamento fare clic su **Fatto**.

    ![Pulsante per i metadati amministratore nella configurazione dell'accesso Single Sign-On](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MobileIron.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **MobileIron**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **MobileIron**.

    ![Collegamento MobileIron nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mobileiron-test-user"></a>Creare l'utente di test di MobileIron

Per consentire agli utenti di Azure AD di accedere a MobileIron, è necessario eseguire il provisioning degli utenti in MobileIron.  
Nel caso di MobileIron, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di MobileIron come amministratore.

1. Passare a **Utenti** e fare clic su **Aggiungi** > **Utente singolo**.

    ![Pulsante Utenti nella configurazione dell'accesso Single Sign-On](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Nella pagina **"Utente singolo"** seguire questa procedura:

    ![Pulsante per l'aggiunta di utenti nella configurazione dell'accesso Single Sign-On](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio Simon.

    d. Fare clic su **Done**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di MobileIron nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione MobileIron per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

