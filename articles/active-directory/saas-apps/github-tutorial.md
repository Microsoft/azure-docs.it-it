---
title: "Esercitazione: Integrazione di Azure Active Directory con un'istanza di GitHub Enterprise Cloud - Organization | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e un'istanza di GitHub Enterprise Cloud - Organization.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 68de75f25c70db9e2f5fcdb524e6b55fb6939e9a
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186287"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con un'istanza di GitHub Enterprise Cloud - Organization

Questa esercitazione descrive come integrare un'**organizzazione** GitHub Enterprise Cloud con Azure Active Directory (Azure AD). Integrando un'istanza di GitHub Enterprise Cloud - Organization con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere all'organizzazione GitHub Enterprise Cloud.
* Gestire l'accesso all'organizzazione GitHub Enterprise Cloud in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con un'istanza di GitHub Enterprise Cloud - Organization, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Un'organizzazione GitHub creata in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), che richiede il [piano di fatturazione GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* GitHub supporta l'accesso SSO avviato da **SP**

* GitHub supporta il provisioning utenti [**automatico** (inviti a organizzazioni)](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>Aggiunta di GitHub dalla raccolta

Per configurare l'integrazione di GitHub in Azure AD, è necessario aggiungere GitHub dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **GitHub** nella casella di ricerca.
1. Selezionare **GitHub Enterprise Cloud - Organization** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Configurare e testare l'accesso SSO di Azure AD per GitHub

Configurare e testare l'accesso SSO di Azure AD con GitHub usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in GitHub.

Per configurare e testare l'accesso SSO di Azure AD con GitHub, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di GitHub](#configure-github-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di GitHub](#create-github-test-user)** : per avere una controparte di B.Simon in GitHub collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **GitHub** nel portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

   a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://github.com/orgs/<Organization ID>/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://github.com/orgs/<Organization ID>`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. In questo caso, è consigliabile di usare il valore univoco della stringa nell'identificatore. Passare alla sezione Admin di GitHub per recuperare questi valori.

5. L'applicazione GitHub prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti in cui **Identificatore utente univoco (ID nome)** è associato a **user.userprincipalname**. L'applicazione GitHub prevede che **Identificatore utente univoco (ID nome)** sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping dell'attributo.

    ![Screenshot che mostra la sezione "Attributi utente" con l'icona "Modifica" selezionata.](common/edit-attribute.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura GitHub** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a GitHub.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **GitHub**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".

    ![ruolo utente](./media/github-tutorial/user-role.png)

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-github-sso"></a>Configurare l'accesso Single Sign-On di GitHub

1. In un'altra finestra del Web browser accedere al sito aziendale di GitHub come amministratore.

2. Passare a **Settings** (Impostazioni) e fare clic su **Security** (Sicurezza).

    ![Screenshot che mostra il menu "Organization settings" di GitHub con l'opzione "Security" selezionata.](./media/github-tutorial/security.png)

3. Selezionare la casella **Enable SAML authentication** (Abilita autenticazione SAML) per visualizzare i campi di configurazione dell'accesso Single Sign-On, quindi seguire questa procedura:

    ![Screenshot che mostra la sezione "SAML single sign-on" con "Enable SAML authentication" e le caselle di testo degli URL evidenziate.](./media/github-tutorial/saml-sso.png)

    a. Copiare il valore di **Single sign-on URL** (URL di accesso SSO) e incollarlo nella casella di testo **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.
    
    b. Copiare il valore di **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

4. Configurare i campi seguenti:

    ![Screenshot che mostra le caselle di testo "Sign on URL", "Issuer" e "Public certificate".](./media/github-tutorial/configure.png)

    a. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Aprire il certificato scaricato nel Blocco note dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato pubblico**.

    d. Fare clic sull'icona **Modifica** icona per modificare **Metodo di firma** e **Metodo digest** da **RSA-SHA1** e **SHA1** alla **RSA-SHA256** e **SHA256** come illustrato di seguito.
    
    e. Aggiornare l'**URL del servizio consumer di asserzione (URL di risposta)** dall'URL predefinito in modo che l'URL in GitHub corrisponda all'URL nella registrazione dell'app di Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Fare clic su **Test SAML configuration** (Test configurazione SAML) per confermare l'assenza di errori di convalida o errori durante l'accesso SSO.

    ![Impostazioni](./media/github-tutorial/test.png)

6. Fare clic su **Save** (Salva).

> [!NOTE]
> L'accesso Single Sign-On in GitHub esegue l'autenticazione in un'organizzazione specifica in GitHub e non sostituisce l'autenticazione di GitHub. Se quindi la sessione github.com dell'utente è scaduta, potrebbe essere richiesto di eseguire l'autenticazione con l'ID/la password GitHub durante il processo di accesso Single Sign-On.

### <a name="create-github-test-user"></a>Creare l'utente di test di GitHub

Questa sezione descrive come creare un utente chiamato Britta Simon in GitHub. GitHub supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](github-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di GitHub come amministratore.

2. Fare clic su **People** (Persone).

    ![Screenshot che mostra il sito GitHub con l'opzione People selezionata.](./media/github-tutorial/people.png "Persone")

3. Fare clic su **Invite member** (Invita membro).

    ![Invitare gli utenti](./media/github-tutorial/invite-member.png "Invitare gli utenti")

4. Nella finestra di dialogo **Invite member** (Invita membro) seguire questa procedura:

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    ![Invitare persone](./media/github-tutorial/email-box.png "Invitare persone")

    b. Fare clic su **Send Invitation** (Invia invito).

    ![Screenshot che mostra la pagina "Invite member" con l'opzione "Member" e il pulsante "Send invitation" selezionati.](./media/github-tutorial/send-invitation.png "Invitare persone")

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di GitHub, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di GitHub e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di GitHub in App personali, verrà eseguito il reindirizzamento all'URL di accesso di GitHub. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato GitHub, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
