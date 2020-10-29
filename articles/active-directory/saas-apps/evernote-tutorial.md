---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Evernote | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: 86a314cd5255c06a70d0f9b28d06e3ac4156fdb6
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Evernote

Questa esercitazione descrive come integrare Evernote con Azure Active Directory (Azure AD). Integrando Evernote con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Evernote.
* Abilitare gli utenti per l'accesso automatico a Evernote con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Evernote abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Evernote supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-evernote-from-the-gallery"></a>Aggiunta di Evernote dalla raccolta

Per configurare l'integrazione di Evernote in Azure AD, è necessario aggiungere Evernote dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Evernote** nella casella di ricerca.
1. Selezionare **Evernote** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Evernote

Configurare e testare l'accesso SSO di Azure AD con Evernote usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Evernote.

Per configurare e testare l'accesso SSO di Azure AD con Evernote, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Evernote](#configure-evernote-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Evernote](#create-evernote-test-user)** : per avere una controparte di B.Simon in Evernote collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Evernote** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP** :

    Nella casella di testo **Identificatore** digitare un URL: `https://www.evernote.com/saml2`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL di accesso** digitare un URL: `https://www.evernote.com/Login.action`

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Per modificare le opzioni di **Firma** , fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Screenshot che mostra la finestra di dialogo "Certificato di firma SAML" con il pulsante "Modifica" selezionato.](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selezionare l'opzione **Firma asserzione e risposta SAML** in **Opzione di firma** .

    b. Fare clic su **Save** (Salva).

1. Nella sezione **Configura Evernote** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Evernote.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Evernote** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-evernote-sso"></a>Configurare l'accesso Single Sign-On di Evernote

1. Per automatizzare la configurazione all'interno di Evernote, è necessario installare l' **estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione** .

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Evernote** per passare direttamente all'applicazione Evernote. Nell'applicazione fornire le credenziali di amministratore per accedere a Evernote. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare manualmente Evernote, aprire una nuova finestra del Web browser, accedere al sito aziendale di Evernote come amministratore e seguire questa procedura:

4. Passare a **Admin Console** (Console di amministrazione)

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Da **Admin Console** (Console di amministrazione), andare a **Security** (Sicurezza) e selezionare **Single Sign-On** (Single Sign-On).

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configurare i valori seguenti:

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Enable SSO** (Abilita SSO): l'accesso SSO è abilitato per impostazione predefinita. Fare clic su **Disable Single Sign-on** (Disabilita Single Sign-on) per rimuovere il requisito SSO.

    b. Incollare il valore di **URL di accesso** copiato dal portale di Azure nella casella di testo **SAML HTTP Request URL** (URL di richiesta HTTP SAML).

    c. Aprire il certificato scaricato da Azure AD in blocco note e copiare il contenuto incluso tra "BEGIN CERTIFICATE" ed "END CERTIFICATE" nella casella di testo **X.509 Certificate** (Certificato x. 509). 

    Fare clic su **Save Changes** (Salva modifiche).

### <a name="create-evernote-test-user"></a>Creare l'utente di test di Evernote

Per consentire agli utenti di Azure AD di accedere a Evernote, è necessario effettuarne il provisioning in Evernote.  
Nel caso di Evernote, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Evernote come amministratore.

2. Fare clic su di **Admin Console** (Console di amministrazione).

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Da **Admin Console** (Console di amministrazione), andare a **Add users** (Aggiungi utenti).

    ![Screenshot che mostra il menu "Users" con l'opzione "Add Users" selezionata.](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Add team members** (Aggiungi membri del team): nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account utente e fare clic su **Invite** (Invita).

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Dopo l'invio dell'invito, il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica per accettare l'invito.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Evernote nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Evernote per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Evernote con Azure AD](https://aad.portal.azure.com/)