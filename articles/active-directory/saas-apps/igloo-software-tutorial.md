---
title: 'Esercitazione: Integrazione di Azure Active Directory con Igloo Software | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Igloo Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460392"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Esercitazione: Integrazione di Azure Active Directory con Igloo Software

Questa esercitazione descrive come integrare Igloo Software con Azure Active Directory (Azure AD).
L'integrazione di Igloo Software con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere a Igloo Software.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Igloo Software con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Igloo Software sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Igloo Software abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Igloo Software supporta l'accesso SSO avviato da **SP**
* Igloo Software supporta il provisioning utenti **JIT**

## <a name="adding-igloo-software-from-the-gallery"></a>Aggiunta di Igloo Software dalla raccolta

Per configurare l'integrazione di Igloo Software in Azure AD è necessario aggiungere Igloo Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Igloo Software dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Igloo Software**, selezionare **Igloo Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Igloo Software nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato Azure AD Single Sign-On con igloo software in base a un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Igloo Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Igloo Software è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Igloo Software](#configure-igloo-software-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Igloo Software](#create-igloo-software-test-user)**: per avere una controparte di Britta Simon in Igloo Software collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Igloo Software, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Igloo Software](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Igloo Software](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<company name>.igloocommmunities.com`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Igloo Software](https://www.igloosoftware.com/services/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Igloo Software** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-igloo-software-single-sign-on"></a>Configurare l'accesso Single Sign-On per Igloo Software

1. In un'altra finestra del Web browser accedere al sito aziendale di Igloo Software come amministratore.

2. Passare al **Control Panel**.

     ![Pannello di controllo](./media/igloo-software-tutorial/ic799949.png "Control panel")

3. Nella scheda **Membership** fare clic su **Sign In Settings**.

    ![Sign In Settings](./media/igloo-software-tutorial/ic783968.png "Sign In Settings")

4. Nella sezione relativa alla configurazione SAML fare clic su **Configure SAML Authentication**.

    ![SAML Configuration](./media/igloo-software-tutorial/ic783969.png "SAML Configuration")

5. Nella sezione **General Configuration** eseguire la procedura seguente:

    ![General Configuration](./media/igloo-software-tutorial/ic783970.png "General Configuration")

    a. Nella casella di testo **Connection Name** digitare un nome personalizzato per la configurazione.

    b. Nella casella di testo **IDP login URL (URL di accesso IDP** ) incollare il valore dell' **URL di accesso** copiato da portale di Azure.

    c. Nella casella di testo **IDP Logout URL (URL di disconnessione IDP** ) incollare il valore dell' **URL di disconnessione** copiato da portale di Azure.

    d. Selezionare **Logout Response and Request HTTP Type** (Risposta di disconnessione e tipo di richiesta HTTP) con l'impostazione **POST**.

    e. Aprire nel Blocco note il certificato con codifica **Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Public Certificate** (Certificato pubblico).

6. In **Response and Authentication Configuration** eseguire la procedura seguente:

    ![Configurazione risposta e autenticazione](./media/igloo-software-tutorial/IC783971.png "Configurazione risposta e autenticazione")
  
    a. In **Identity Provider** selezionare **Microsoft ADFS**.

    b. In **Identifier Type** selezionare **Email Address**. 

    c. Nella casella di testo **Email Attribute** digitare **emailaddress**.

    d. Nella casella di testo **First Name Attribute** digitare **givenname**.

    e. Nella casella di testo **Last Name Attribute** digitare **surname**.

7. Per completare la configurazione seguire questa procedura:

    ![User creation on Sign in](./media/igloo-software-tutorial/IC783972.png "User creation on Sign in") 

    a. In **User creation on Sign in** selezionare **Create a new user in your site when they sign in**.

    b. In **Sign in Settings** selezionare **Use SAML button on “Sign in” screen**.

    c. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Igloo Software.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Igloo Software**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Igloo Software**.

    ![Collegamento di Igloo Software nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-igloo-software-test-user"></a>Creare un utente di test di Igloo Software

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Igloo Software.  

Quando un utente assegnato prova ad accedere a Igloo Software usando il pannello di accesso, Igloo Software verifica se l'utente esiste.  Se l'account utente non è presente, Igloo Software lo crea automaticamente.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Igloo Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Igloo Software per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)