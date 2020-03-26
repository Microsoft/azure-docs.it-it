---
title: 'Esercitazione: Integrazione di Azure Active Directory con dmarcian | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823706"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Esercitazione: Integrare dmarcian con Azure Active Directory

Questa esercitazione descrive come integrare dmarcian con Azure Active Directory (Azure AD). Integrando dmarcian con Azure AD è possibile:

* Controllare in Azure AD chi può accedere a dmarcian.
* Consentire agli utenti l'accesso automatico a dmarcian con i rispettivi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di dmarcian abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* dmarcian supporta l'accesso SSO avviato da **provider di servizi e provider di identità**

## <a name="adding-dmarcian-from-the-gallery"></a>Aggiunta di dmarcian dalla raccolta

Per configurare l'integrazione di dmarcian in Azure AD, è necessario aggiungere dmarcian dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **dmarcian** nella casella di ricerca.
1. Selezionare **dmarcian** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con dmarcian usando un utente di test di nome **B.Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in dmarcian.

Per configurare e testare l'accesso SSO di Azure AD con dmarcian, completare le procedure di base seguenti.

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso SSO di dmarcian](#configure-dmarcian-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di dmarcian](#create-dmarcian-test-user)** : per avere una controparte di B.Simon in dmarcian collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **dmarcian** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, È necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Configurare l'accesso SSO di dmarcian

1. Per automatizzare la configurazione all'interno di dmarcian, è necessario installare l'**estensione del browser My Apps Secure Sign-in** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura dmarcian** per passare direttamente all'applicazione dmarcian. Specificare quindi le credenziali di amministratore per accedere a dmarcian. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente dmarcian, aprire una nuova finestra del Web browser, accedere al sito aziendale di dmarcian come amministratore e seguire questa procedura:

4. Fare clic su **Profile** (Profilo) in alto a destra e passare a **Preferences** (Preferenze).

    ![Preferenze](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Scorrere verso il basso e fare clic sulla sezione **Single Sign-On** e quindi fare clic su **Configure** (Configura).

    ![Single](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Nella sezione **SAML Single Sign-On** (Single Sign-On SAML) impostare **Status** (Stato) su **Enabled** (Abilitato) e seguire questa procedura:

    ![Autenticazione](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPY** (COPIA) per copiare il valore di **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) per l'istanza e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Nella sezione **Add dmarcian to your Identity Provider** (Aggiungi dmarcian al provider di identità) fare clic su **COPY** (COPIA) per copiare il valore di **Entity ID** (ID entità) per l'istanza e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    * Nella casella di testo **Identity Provider Metadata** (Metadati provider di identità) della sezione **Set up Authentication** (Configura autenticazione) incollare il valore di **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    * Nella casella di testo **Attribute Statements** (Dichiarazioni di attributo) della sezione **Set up Authentication** (Configura autenticazione) incollare l'URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Nella sezione **Set up Login URL** (Configura URL di accesso) copiare il valore dell'**URL di accesso** per l'istanza e incollarlo nella casella di testo **URL accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

        > [!Note]
        > È possibile modificare l'**URL di accesso** in base all'organizzazione.

    * Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a dmarcian.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **dmarcian**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-dmarcian-test-user"></a>Creare l'utente di test di dmarcian

Per consentire agli utenti di Azure AD di accedere a dmarcian, è necessario effettuarne il provisioning in dmarcian. Nel caso di dmarcian il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a dmarcian come amministratore della sicurezza.

2. Fare clic su **Profile** (Profilo) in alto a destra e passare a **Manage Users** (Gestisci utenti).

    ![Utente](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Sul lato destro della sezione **SSO Users** (Utenti SSO) fare clic su **Aggiungi nuovo utente**.

    ![Aggiungi utente](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Nella finestra popup **Aggiungi nuovo utente** seguire questa procedura:

    ![Nuovo utente](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Nella casella di testo **New User Email** (Posta elettronica nuovo utente) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    b. Se si vogliono assegnare diritti di amministratore all'utente, selezionare **Make User an Admin** (Imposta utente come amministratore).

    c. Fare clic su **Add User**.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di dmarcian nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione dmarcian per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

