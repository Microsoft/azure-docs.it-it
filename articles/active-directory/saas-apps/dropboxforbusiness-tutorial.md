---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dropbox Business | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dropbox Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c596a1477c773717e0a6170d8931aeaf40594f5
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85479964"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Esercitazione: Integrare Dropbox Business con Azure Active Directory

Questa esercitazione descrive come integrare Dropbox Business con Azure Active Directory (Azure AD). Integrando Dropbox Business con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Dropbox Business.
* Abilitare gli utenti per l'accesso automatico a Dropbox Business con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Dropbox Business abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

* In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Dropbox Business supporta l'accesso SSO avviato da **SP**

* Dropbox Business supporta [provisioning e deprovisioning utenti automatici](dropboxforbusiness-tutorial.md)
* Dopo aver configurato Dropbox, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Aggiunta di Dropbox Business dalla raccolta

Per configurare l'integrazione di Dropbox Business in Azure AD, è necessario aggiungere Dropbox Business dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Dropbox Business** nella casella di ricerca.
1. Selezionare **Dropbox Business** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Dropbox Business usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Dropbox Business.

Per configurare e testare l'accesso SSO di Azure AD con Dropbox Business, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.    
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Dropbox Business](#configure-dropbox-business-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Dropbox Business](#create-dropbox-business-test-user)** : per avere una controparte di Britta Simon in Dropbox Business collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Dropbox Business** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://www.dropbox.com/sso/<id>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un valore: `Dropbox`

    > [!NOTE]
    > Il valore precedente non è un valore reale. È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Dropbox Business** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di disconnessione


### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Dropbox Business.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Dropbox Business**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-dropbox-business-sso"></a>Configurare l'accesso Single Sign-On di Dropbox Business

1. Per automatizzare la configurazione all'interno di Dropbox Business, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Dropbox Business** (Configura Dropbox Business) per passare direttamente all'applicazione Dropbox Business. Da tale posizione, specificare le credenziali di amministratore per accedere a Dropbox Business. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 8.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Per configurare manualmente Dropbox Business, aprire una nuova finestra del Web browser, passare al tenant di Dropbox Business e accedere al proprio tenant di Dropbox Business. Seguire quindi questa procedura:

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/ic769509.png "Configura accesso Single Sign-On")

4. Fare clic su **Icona utente** e selezionare la scheda **Impostazioni**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure1.png "Configura accesso Single Sign-On")

5. Nel riquadro di spostamento a sinistra fare clic su **Console di amministrazione**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure2.png "Configura accesso Single Sign-On")

6. In **Console di amministrazione** fare clic su **Impostazioni** nel riquadro di spostamento a sinistra.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure3.png "Configura accesso Single Sign-On")

7. Selezionare l'opzione **Single sign-on** sotto la sezione **Autenticazione**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure4.png "Configura accesso Single Sign-On")

8. Nella sezione **Single sign-on**seguire questa procedura:  

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure5.png "Configura accesso Single Sign-On")

    a. Selezionare **Required** (Obbligatorio) come opzione nell'elenco a discesa **Single sign-on**.

    b. Fare clic su **Aggiungi URL di accesso** e quindi nella casella di testo **URL di accesso del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure e selezionare **Fine**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure6.png "Configura accesso Single Sign-On")

    c. Fare clic su **Carica certificato**e quindi andare al **file di certificato oBase64** scaricato dal portale di Azure.

    d. Fare clic su **Copy link** (Copia collegamento) e incollare il valore copiato nella casella di testo **Sign-on URL** (URL di accesso) della sezione **URL e dominio di Dropbox Business** nel portale di Azure.

    e. Fare clic su **Salva**.

### <a name="create-dropbox-business-test-user"></a>Creare l'utente di test di Dropbox Business

In questa sezione viene creato un utente di nome B.Simon in Dropbox Business. Dropbox Business supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Dropbox Business, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto clienti di Dropbox Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Dropbox Business nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Dropbox Business per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Provare Dropbox Business con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)