---
title: 'Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Brightspace by Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.openlocfilehash: 91d48e42dc1c92e05e963b4c8b53e44daa403378
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456696"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn

Questa esercitazione descrive come integrare Brightspace by Desire2Learn con Azure Active Directory (Azure AD).
L'integrazione di Brightspace by Desire2Learn con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Brightspace by Desire2Learn.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Brightspace by Desire2Learn con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Brightspace by Desire2Learn, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Brightspace by Desire2Learn abilitata per l'accesso Single Sign-On

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Brightspace by Desire2Learn supporta l'accesso SSO avviato da **IDP**

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Aggiunta di Brightspace by Desire2Learn dalla raccolta

Per configurare l'integrazione di Brightspace by Desire2Learn in Azure AD, è necessario aggiungere Brightspace by Desire2Learn dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Brightspace by Desire2Learn dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Brightspace by Desire2Learn** , selezionare **Brightspace by Desire2Learn** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Brightspace by Desire2Learn nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Brightspace by Desire2Learn usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Brightspace by Desire2Learn.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Brightspace by Desire2Learn, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Brightspace by Desire2Learn](#configure-brightspace-by-desire2learn-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Brightspace by Desire2Learn](#create-brightspace-by-desire2learn-test-user)** : per avere una controparte di Britta Simon in Brightspace by Desire2Learn collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Brightspace by Desire2Learn, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Brightspace by Desire2Learn](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:
    
    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Brightspace by Desire2Learn](https://www.d2l.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Brightspace by Desire2Learn** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione

### <a name="configure-brightspace-by-desire2learn-single-sign-on"></a>Configurare l'accesso Single Sign-On per Brightspace by Desire2Learn

Per configurare l'accesso Single Sign-On sul lato **Brightspace by Desire2Learn** , è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Brightspace by Desire2Learn](https://www.d2l.com/contact/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Brightspace by Desire2Learn.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **Brightspace by Desire2Learn**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Brightspace by Desire2Learn**.

    ![Collegamento di Brightspace by Desire2Learn nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Creare l'utente di test di Brightspace by Desire2Learn

In questa sezione viene creato un utente di nome Britta Simon in Brightspace by Desire2Learn. Collaborare con il [team di supporto di Brightspace by Desire2Learn](https://www.d2l.com/contact/) per aggiungere gli utenti alla piattaforma Brightspace by Desire2Learn. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Brightspace by Desire2Learn per eseguire il provisioning degli account utente di Azure Active Directory.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Brightspace by Desire2Learn nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Brightspace by Desire2Learn per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)