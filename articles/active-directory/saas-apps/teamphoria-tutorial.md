---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Teamphoria | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 30971c9c5280bfba37f46aaab0aaf2c2506502c0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542344"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Teamphoria

Questa esercitazione descrive come integrare Teamphoria con Azure Active Directory (Azure AD). Integrando Teamphoria con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Teamphoria.
* Abilitare gli utenti per l'accesso automatico a Teamphoria con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Teamphoria abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Teamphoria supporta l'accesso SSO avviato da **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Aggiungere Teamphoria dalla raccolta

Per configurare l'integrazione di Teamphoria in Azure AD, è necessario aggiungere Teamphoria dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Teamphoria** nella casella di ricerca.
1. Selezionare **Teamphoria** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Teamphoria

Configurare e testare l'accesso SSO di Azure AD con Teamphoria usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Teamphoria.

Per configurare e testare l'accesso SSO di Azure AD con Teamphoria, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Teamphoria](#configure-teamphoria-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Teamphoria](#create-teamphoria-test-user)** : per avere una controparte di B.Simon in Teamphoria collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Teamphoria** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Teamphoria](https://www.teamphoria.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Teamphoria** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Teamphoria.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Teamphoria**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-teamphoria-sso"></a>Configurare l'accesso Single Sign-On di Teamphoria

1. Per automatizzare la configurazione all'interno di Teamphoria, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Teamphoria** per passare direttamente all'applicazione Teamphoria. Nell'applicazione specificare le credenziali di amministratore per accedere a Teamphoria. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Teamphoria, aprire una nuova finestra del Web browser, accedere al sito aziendale di Teamphoria come amministratore e seguire questa procedura:

4. Passare all'opzione **ADMIN SETTINGS** (Impostazioni di amministrazione) nella barra degli strumenti di sinistra e scegliere **SINGLE SIGN-ON** nella scheda di configurazione per aprire la finestra di configurazione SSO.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Fare clic sull'opzione **ADD NEW IDENTITY PROVIDER** (Aggiungi nuovo provider di identità) nell'angolo superiore destro per aprire il modulo e aggiungere le impostazioni per l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Immettere i dettagli nei campi come descritto di seguito.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME VISUALIZZATO**: immettere il nome del plug-in visualizzato nella pagina di amministrazione.

    b. **NOME PULSANTE**: il nome della scheda che verrà visualizzata nella pagina di accesso per l'accesso Single Sign-On.

    c. **CERTIFICATO**: aprire il certificato scaricato in precedenza dal portale di Azure nel Blocco note, copiare il contenuto dello stesso e incollarlo qui nella casella.

    d. **PUNTO DI INGRESSO**: incollare l'**URL di accesso** copiato in precedenza dal portale di Azure.

    e. Posizionare il selettore dell'opzione su **ON** e fare clic su **SAVE** (Salva).

### <a name="create-teamphoria-test-user"></a>Creare l'utente di test di Teamphoria

Per consentire agli utenti di Azure AD di accedere a Teamphoria, è necessario effettuarne il provisioning in Teamphoria. Nel caso di Teamphoria, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Teamphoria come amministratore.

1. Fare clic sulle impostazioni **ADMIN** nella barra degli strumenti di sinistra e scegliere **USERS** (Utenti) nella scheda **MANAGE** (Gestisci) per aprire la pagina di amministrazione per gli utenti.

    ![Aggiungere un dipendente](./media/teamphoria-tutorial/admin_manage_users.png)

1. Fare clic sull'opzione **MANUAL INVITE** (Invito manuale).

    ![Invita persone](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. In questa pagina effettuare l'operazione seguente.

    ![Invita persone](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Nella casella di testo **EMAIL ADDRESS** (INDIRIZZO DI POSTA ELETTRONICA) immettere l'**indirizzo di posta elettronica** dell'utente, ad esempio B.Simon.

    b. Nella casella di testo **FIRST NAME** (NOME) immettere il nome dell'utente, ad esempio **B**.

    c. Nella casella di testo **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Fare clic su **INVITE 1 USER** (Invita 1 utente). L'utente deve accettare l'invito per essere creato nel sistema.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Teamphoria nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Teamphoria per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Teamphoria con Azure AD](https://aad.portal.azure.com/)

