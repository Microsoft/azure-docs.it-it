---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lifesize Cloud | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Lifesize Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 483377d0376a5a98d822392af1fa2d84ad8498e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458592"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Lifesize Cloud

Questa esercitazione descrive come integrare Lifesize Cloud con Azure Active Directory (Azure AD).
L'integrazione di Lifesize Cloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Lifesize Cloud.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Lifesize Cloud con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Lifesize Cloud, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Lifesize Cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Lifesize Cloud supporta l'accesso SSO avviato da **SP**

* Lifesize Cloud supporta il provisioning utenti **automatico**

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Aggiunta di Lifesize Cloud dalla raccolta

Per configurare l'integrazione di Lifesize Cloud in Azure AD, è necessario aggiungere Lifesize Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Lifesize Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Lifesize Cloud**, selezionare **Lifesize Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Lifesize Cloud nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Lifesize Cloud usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lifesize Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Lifesize Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Lifesize Cloud](#configure-lifesize-cloud-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Lifesize Cloud](#create-lifesize-cloud-test-user)** : per avere una controparte di Britta Simon in Lifesize Cloud collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Lifesize Cloud, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Lifesize Cloud** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Lifesize Cloud](common/sp-identifier-relay.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://login.lifesizecloud.com/ls/?acs`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://login.lifesizecloud.com/<companyname>`

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL nel formato seguente: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'identificatore, l'URL di accesso e lo stato dell'inoltro effettivi. Contattare il [team di supporto clienti di Lifesize Cloud](https://www.lifesize.com/en/support) per ottenere i valori di URL di accesso e identificatore in modo da ricavare il valore dello stato dell'inoltro dalla configurazione SSO illustrata più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Lifesize Cloud** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione

### <a name="configure-lifesize-cloud-single-sign-on"></a>Configurare l'accesso Single Sign-On per Lifesize Cloud

1. Al fine di configurare l'accesso Single Sign-On per l'applicazione, effettuare l'accesso all'applicazione Lifesize Cloud con privilegi di amministratore.

2. Nell'angolo superiore destro fare clic sul proprio nome e quindi su **Impostazioni avanzate**.

    ![Screenshot che mostra la voce di menu Impostazioni avanzate.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Nelle impostazioni avanzate fare clic sul collegamento **SSO Configuration** (Configurazione SSO). Verrà aperta la pagina della configurazione SSO per l'istanza.

    ![Screenshot che mostra la sezione Impostazioni avanzate in cui è possibile selezionare il collegamento S S O Configuration.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Ora configurare i valori seguenti nell'interfaccia utente di configurazione SSO.

    ![Screenshot che mostra la pagina S S O Configuration in cui è possibile immettere i valori descritti.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione del provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b.  Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**.
  
    d. Nei mapping dell'attributo SAML per la casella di testo Name (Nome) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Nei mapping dell'attributo SAML per la casella di testo **Last Name** (Cognome) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Nei mapping dell'attributo SAML per la casella di testo **Email** (Indirizzo di posta elettronica) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Per verificare la configurazione è possibile fare clic sul pulsante **Prova**.

    >[!NOTE]
    >Perché la prova abbia esito positivo, è necessario completare la configurazione guidata di Azure AD e anche fornire l'accesso a utenti o gruppi che possano eseguire il test.

6. Abilitare l'accesso SSO con il pulsante **Enable SSO** (Abilita SSO).

7. Fare clic sul pulsante **Aggiorna** in modo che tutte le impostazioni vengano salvate. Verrà generato il valore RelayState. Copiare il valore RelayState, che viene generato nella casella di testo, incollarlo nella casella di testo **Stato dell'inoltro** della sezione **URL e dominio Lifesize Cloud**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lifesize Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Lifesize Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Lifesize Cloud**.

    ![Collegamento di Lifesize Cloud nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-lifesize-cloud-test-user"></a>Creare l'utente di test di Lifesize Cloud

In questa sezione viene creato un utente chiamato Britta Simon in Lifesize Cloud. Lifesize Cloud supporta il provisioning utenti automatico. Dopo l'autenticazione in Azure AD sarà eseguito il provisioning automatico dell'utente nell'applicazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Lifesize Cloud nel pannello di accesso, dovrebbe automaticamente apparire la pagina di accesso dell'applicazione Lifesize Cloud. Dopo aver immesso il nome utente in questa pagina, si verrà reindirizzati alla home page dell'applicazione.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)