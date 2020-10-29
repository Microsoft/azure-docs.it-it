---
title: Esercitazione Integrazione di Azure Active Directory con Flatter Files | Documentazione Microsoft
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Flatter Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453223"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Esercitazione: Integrazione di Azure Active Directory con Flatter Files

Questa esercitazione descrive come integrare Flatter Files con Azure Active Directory (Azure AD).
L'integrazione di Flatter Files con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Flatter Files.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Flatter Files con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Flatter Files, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Flatter Files abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Flatter Files supporta l'accesso SSO avviato da **IDP**

## <a name="adding-flatter-files-from-the-gallery"></a>Aggiunta di Flatter Files dalla raccolta

Per configurare l'integrazione di Flatter Files in Azure AD, è necessario aggiungere Flatter Files dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Flatter Files dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni** .

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Flatter Files** , selezionare **Flatter Files** nel pannello dei risultati e fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Flatter Files nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Flatter Files usando un utente di test di nome **Britta Simon** .
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Flatter Files.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Flatter Files, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Flatter Files](#configure-flatter-files-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Flatter Files](#create-flatter-files-test-user)** : per avere una controparte di Britta Simon in Flatter Files collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Flatter Files, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Flatter Files** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On** .

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base** .

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Flatter Files](common/preintegrated.png)

5. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Flatter Files** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-flatter-files-single-sign-on"></a>Configurare l'accesso Single Sign-On per Flatter Files

1. Accedere all'applicazione Flatter Files come amministratore.

2. Fare clic su **DASHBOARD** . 
   
    ![Screenshot che mostra l'opzione "DASHBOARD" selezionata nell'app "Flatter Files".](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Fare clic su **Settings** (Impostazioni) e quindi nella scheda **Company** (Azienda) seguire questa procedura: 
   
    ![Screenshot che mostra la scheda "Company" con l'opzione "Use SAML 2.0 for Authentication" e il pulsante "Configure SAML" selezionati.](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selezionare **Use SAML 2.0 for Authentication** .
    
    b. Fare clic su **Configure SAML** .

4. Nella finestra di dialogo **SAML Configuration** (Configurazione SAML) seguire questa procedura: 
   
    ![Configure Single Sign-On](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Nella casella di testo **Domain** (Dominio) digitare il dominio registrato.
   
   > [!NOTE]
   > Se ancora non si ha un dominio registrato, contattare il team di supporto di Flatter Files all'indirizzo [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Nella casella di testo **Identity Provider URL** (URL provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    c.  Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato provider di identità** .

    d. Fare clic su **Update** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon** .
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Flatter Files.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **Flatter Files** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Flatter Files** .

    ![Collegamento di Flatter Files nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

### <a name="create-flatter-files-test-user"></a>Creare l'utente di test di Flatter Files

Questa sezione descrive come creare un utente chiamato Britta Simon in Flatter Files.

**Per creare un utente denominato Britta Simon in Flatter Files, seguire questa procedura:**

1. Accedere al sito aziendale di **Flatter Files** come amministratore.

2. Nel riquadro di spostamento a sinistra fare clic su **Settings** (Impostazioni) e quindi fare clic sulla scheda **Users** (Utenti).
   
    ![Screenshot che mostra la pagina "Settings" con la scheda "Users" selezionata.](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Fare clic su **Add User** . 

4. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
   
    ![Creare un utente in Flatter Files](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Nella casella di testo **Nome** digitare **Britta** .
   
    b. Nella casella di testo **Cognome** digitare **Simon** . 
   
    c. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di Britta Simon nel portale di Azure.
   
    d. Fare clic su **Submit** (Invia).   


### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Flatter Files nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Flatter Files per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)