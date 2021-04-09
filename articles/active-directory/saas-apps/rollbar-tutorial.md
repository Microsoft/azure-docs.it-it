---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rollbar | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rollbar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 2d5aedf24034c9ba5ee865dd0d2289169ea5f859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520658"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Esercitazione: Integrazione di Azure Active Directory con Rollbar

Questa esercitazione descrive come integrare Rollbar con Azure Active Directory (Azure AD).
L'integrazione di Rollbar con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Rollbar.
* È possibile abilitare gli utenti per l'accesso automatico a Rollbar (Single Sign-On) con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Rollbar, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Rollbar abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Rollbar supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-rollbar-from-the-gallery"></a>Aggiunta di Rollbar dalla raccolta

Per configurare l'integrazione di Rollbar in Azure AD, è necessario aggiungere Rollbar dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Rollbar dalla raccolta, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Rollbar**, nel pannello dei risultati selezionare **Rollbar** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Rollbar nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Rollbar in base a un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Rollbar.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rollbar, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Rollbar](#configure-rollbar-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Rollbar](#create-rollbar-test-user)** per avere una controparte di Britta Simon in Rollbar collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Rollbar, eseguire questa procedura:

1. Nella pagina di integrazione dell'**applicazione Rollbar** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://saml.rollbar.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Rollbar](mailto:support@rollbar.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Set up Rollbar** (Configura Rollbar) copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-rollbar-single-sign-on"></a>Configurare Rollbar Single Sign-On

1. In un'altra finestra del Web browser accedere al sito aziendale Rollbar come amministratore.

1. Fare clic sulle **impostazioni del profilo** nell'angolo superiore destro e quindi fare clic sulle **impostazioni del nome account**.

    ![Screenshot che mostra le impostazioni di un nome account selezionate nelle impostazioni del profilo.](./media/rollbar-tutorial/general.png)

1. Fare clic su **Identity Provider** (Provider di identità) in SECURITY (SICUREZZA).

    ![Screenshot che mostra la voce Identity Provider evidenziata in SECURITY.](./media/rollbar-tutorial/configure1.png)

1. Nella sezione **SAML Identity Provider** (Provider di identità SAML) eseguire questa procedura:

    ![Screenshot che mostra la sezione SAML Identity Provider in cui è possibile immettere i valori descritti.](./media/rollbar-tutorial/configure2.png)

    a. Selezionare **AZURE** dall'elenco a discesa **SAML Identity Provider** (Provider di identità SAML).

    b. Aprire il certificato scaricato in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML Metadata** (Metadati SAML).

    c. Fare clic su **Salva**.

1. Dopo che si è fatto clic su Save (Salva), la schermata avrà l'aspetto seguente:

    ![Screenshot che mostra i risultati nella pagina SAML Identity Provider.](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Per completare il passaggio seguente, è necessario prima aggiungere se stessi come utente all'app Rollbar in Azure.
    >

    a. Se si vuole che tutti gli utenti eseguano l'autenticazione tramite Azure, fare clic su **log in via your identity provider** (accedere tramite il provider di identità) per ripetere l'autenticazione tramite Azure.  

    b.  Quando si ritorna alla schermata, selezionare la casella di controllo **Require login via SAML Identity Provider** (Richiedi accesso tramite provider di identità SAML).

    b. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite concessione dell'accesso a Rollbar.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Rollbar**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Rollbar**.

    ![Collegamento a Rollbar nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-rollbar-test-user"></a>Creare utente di test Rollbar

Per consentire agli utenti di Azure AD di accedere a Rollbar, è necessario eseguirne il provisioning in Rollbar. Nel caso di Rollbar, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale Rollbar come amministratore.

1. Fare clic sulle **impostazioni del profilo** nell'angolo superiore destro e quindi fare clic sulle **impostazioni del nome account**.

    ![Utente](./media/rollbar-tutorial/general.png)

1. Fare clic su **Users**.

    ![Aggiungere un dipendente](./media/rollbar-tutorial/user1.png)

1. Fare clic su **Invite Team Members** (Invita membri del team).

    ![Screenshot che mostra l'opzione Invite Team Members selezionata.](./media/rollbar-tutorial/user2.png)

1. Nella casella di testo immettere il nome dell'utente, ad esempio **brittasimon\@contoso.com** e fare clic su **Add/Invite** (Aggiungi/Invita).

    ![Screenshot che mostra la sezione Add/Invite Members con un indirizzo specificato.](./media/rollbar-tutorial/user3.png)

1. L'utente riceve un invito. Dopo che questo è stato accettato, l'utente viene creato nel sistema.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Rollbar nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Rollbar per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)