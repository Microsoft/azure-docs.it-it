---
title: 'Esercitazione: integrazione di Azure Active Directory con Adaptive Insights | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232129"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Esercitazione: Integrare Adaptive Insights con Azure Active Directory

Questa esercitazione descrive come integrare Adaptive Insights con Azure Active Directory (Azure AD). Integrando Adaptive Insights con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Adaptive Insights.
* Abilitare gli utenti per l'accesso automatico ad Adaptive Insights con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Adaptive Insights abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adaptive Insights supporta l'accesso SSO avviato da **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Aggiunta di Adaptive Insights dalla raccolta

Per configurare l'integrazione di Adaptive Insights in Azure AD, è necessario aggiungere Adaptive Insights dalla raccolta all'elenco delle applicazioni SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adaptive Insights** nella casella di ricerca.
1. Selezionare **Adaptive Insights** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Adaptive Insights usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adaptive Insights.

Per configurare e testare l'accesso SSO di Azure AD con Adaptive Insights, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Adaptive Insights](#configure-adaptive-insights-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Adaptive Insights](#create-adaptive-insights-test-user)** : per avere una controparte di B.Simon in Adaptive Insights collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Adaptive Insights** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > È possibile ottenere valori dell'identificatore (ID entità) e l'URL di risposta dalla pagina **Impostazioni SSO SAML** di Adaptive Insights.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Adaptive Insights** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Configurare l'accesso SSO di Adaptive Insights

1. In un'altra finestra del Web browser accedere al sito aziendale di Adaptive Insights come amministratore.

2. Passare a **Administration**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Amministrativi")

3. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **SAML SSO Settings** (Impostazioni SSO SAML).

    ![Gestire le impostazioni per l'accesso SSO SAML](./media/adaptivesuite-tutorial/ic805645.png "Manage SAML SSO Settings")

4. Nella pagina **SAML SSO Settings** eseguire queste operazioni:

    ![SAML SSO Settings](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO Settings")

    a. Nella casella di testo **Identity provider name** digitare un nome per la configurazione.

    b. Incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure nella casella di testo **Identity provider Entity ID** (ID entità del provider di identità).

    c. Incollare il valore di **URL di accesso** copiato dal portale di Azure nella casella di testo **Identity provider SSO URL** (URL SSO del provider di identità).

    d. Incollare il valore di **URL disconnessione** copiato dal portale di Azure nella casella di testo **Custom logout URL** (Personalizza URL di disconnessione).

    e. Per caricare il certificato scaricato, fare clic su **Scegli file**.

    f. Selezionare le opzioni seguenti per:

     * **SAML user id** (ID utente SAML), selezionare **User's Adaptive Insights user name** (Nome utente dell'utente di Adaptive Insights).

     * **SAML user id location** (Posizione ID utente SAML), selezionare **User id in NameID of Subject** (ID utente in NameID of Subject).

     * **SAML NameID format** (Formato NameID SAML), selezionare **Email address** (Indirizzo di posta elettronica).

     * **Abilita SAML** selezionare **Allow SAML SSO and direct Adaptive Insights login** (Consenti SSO SAML e accesso diretto ad Adaptive Insights).

    g. Copiare l'**URL SSO di Adaptive Insights** e incollarlo nelle caselle di testo **Identificatore (ID entità)** e **URL di risposta** della sezione **Configurazione SAML di base** nel portale di Azure.

    h. Fare clic su **Salva**.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Adaptive Insights.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Adaptive Insights**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-adaptive-insights-test-user"></a>Creare un utente di test di Adaptive Insights

Per consentire agli utenti di Azure AD di accedere ad Adaptive Insights, è necessario effettuarne il provisioning in Adaptive Insights. Nel caso di Adaptive Insights, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Adaptive Insights** come amministratore.

2. Passare a **Administration**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Amministrativi")

3. Nella sezione **Users and Roles** (Utenti e ruoli) fare clic su **Users** (Utenti).

   ![Add User](./media/adaptivesuite-tutorial/IC805648.png "Aggiunta di un utente")

4. Nella sezione **Nuovo utente** seguire questa procedura:

   ![Invia](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. Nelle caselle di testo corrispondenti **Name**, **Username**, **Email**, **Password** digitare il nome, il nome utente, l'indirizzo di posta elettronica e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning.

   b. Selezionare un valore in **Role**.

   c. Fare clic su **Submit** (Invia).

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Adaptive Insights per effettuare il provisioning degli account utente Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adaptive Insights nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adaptive Insights per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

