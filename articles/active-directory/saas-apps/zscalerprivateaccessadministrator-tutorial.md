---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access Administrator | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Private Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104583522c7041d89d9c63ba67a5633a3bd383f3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016468"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Private Access Administrator

Questa esercitazione descrive come integrare Zscaler Private Access Administrator con Azure Active Directory (Azure AD).
L'integrazione di Zscaler Private Access Administrator con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Zscaler Private Access Administrator.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Zscaler Private Access Administrator con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Zscaler Private Access Administrator sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Zscaler Private Access Administrator abilitata per l'accesso Single Sign-On.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler Private Access Administrator supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Aggiunta di Zscaler Private Access Administrator dalla raccolta

Per configurare l'integrazione di Zscaler Private Access Administrator in Azure AD è necessario aggiungere Zscaler Private Access Administrator dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Zscaler Private Access Administrator dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **Zscaler Private Access Administrator**, selezionare **Zscaler Private Access Administrator** dal pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Private Access Administrator nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator usando un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Private Access Administrator.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Zscaler Private Access Administrator](#configure-zscaler-private-access-administrator-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** : per avere una controparte di Britta Simon in Zscaler Private Access Administrator collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zscaler Private Access Administrator, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Zscaler Private Access Administrator** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per domini e URL Zscaler Private Access Administrator](common/idp-relay.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL: `idpadminsso`

5.  Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per domini e URL Zscaler Private Access Administrator](common/both-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Zscaler Private Access Administrator** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Configurare l'accesso Single Sign-On di Zscaler Private Access Administrator

1. In un'altra finestra del Web browser accedere all'istanza di Zscaler Private Access Administrator come amministratore.

2. Nella parte superiore, fare clic su **Amministrazione**, passare alla sezione **AUTENTICAZIONE**, quindi fare clic su **Configurazione IdP**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Nell'angolo superiore destro, fare clic su **Aggiungi configurazione IdP**. 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Nella pagina **Aggiungi configurazione IdP**, eseguire la procedura seguente:
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Fare clic su **Seleziona file** per caricare il file di metadati scaricati da Azure AD nel campo **Caricamento file di metadati IdP**.

    b. Vengono letti i **metadati IdP** da Azure AD e vengono compilate tutte le informazioni dei campi, come mostrato di seguito.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selezionare **Single Sign-On** come **Amministratore**.

    d. Selezionare il dominio dal campo **Domini**.
    
    e. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Zscaler Private Access Administrator.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler Private Access Administrator**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Zscaler Private Access Administrator**.

    ![Collegamento a Zscaler Private Access Administrator nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Creare un utente di test di Zscaler Private Access Administrator

Per consentire agli utenti di Azure AD di accedere a Zscaler Private Access Administrator, è necessario effettuarne il provisioning in Zscaler Private Access Administrator. Nel caso di Zscaler Private Access Administrator, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito della società di Zscaler Private Access Administrator come amministratore.

2. Nella parte superiore, fare clic su **Amministrazione**, passare alla sezione **AUTENTICAZIONE**, quindi fare clic su **Configurazione IdP**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Fare clic su **Amministratori** sul lato sinistro del menu.

    ![Amministratore di Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Nell'angolo superiore destro, fare clic su **Aggiungi amministratore**:

    ![Aggiunta di un amministratore in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Nella pagina **Aggiungi amministratore** eseguire la procedura seguente:

    ![Amministratore utente in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Nella casella di testo **Username** (Nome utente) immettere l'indirizzo di posta elettronica dell'utente, ad esempio BrittaSimon@contoso.com.

    b. Nella casella di testo **Password**, digitare la password.

    c. Nella casella di testo **Conferma password**, digitare di nuovo la password.

    d. Per **Ruolo**, selezionare **Zscaler Private Access Administrator**.

    e. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio BrittaSimon@contoso.com.

    f. Nella casella di testo **Telefono**, digitare il numero di telefono.

    g. Nella casella di testo **Fuso orario**, selezionare il fuso orario.

    h. Fare clic su **Salva**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Zscaler Private Access Administrator nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler Private Access Administrator per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

