---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Oktopost SAML | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: e71f991037c0d725a2a03ecd8c1ff4ebbd4d7810
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Oktopost SAML

Questa esercitazione descrive come integrare Oktopost SAML con Azure Active Directory (Azure AD). Integrando Oktopost SAML con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Oktopost SAML.
* Abilitare gli utenti per l'accesso automatico a Oktopost SAML con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Oktopost SAML abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Oktopost SAML supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Aggiungere Oktopost SAML dalla raccolta

Per configurare l'integrazione di Oktopost SAML in Azure AD, è necessario aggiungere Oktopost SAML dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Oktopost SAML** nella casella di ricerca.
1. Selezionare **Oktopost SAML** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Oktopost SAML

Configurare e testare l'accesso SSO di Azure AD con Oktopost SAML usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Oktopost SAML.

Per configurare e testare l'accesso SSO di Azure AD con Oktopost SAML, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Oktopost SAML](#configure-oktopost-saml-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Oktopost SAML](#create-oktopost-saml-test-user)** : per avere una controparte di B.Simon in Oktopost SAML collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Oktopost SAML** nel portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://app.oktopost.com/auth/login`


1. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Oktopost SAML** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Oktopost SAML.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Oktopost SAML**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-oktopost-saml-sso"></a>Configurare l'accesso Single Sign-On di Oktopost SAML

1. Accedere a Oktopost SAML come amministratore.

1. Fare clic sull'**icona User > Settings** (Utente > Impostazioni).

    ![Impostazioni di Oktopost SAML](./media/oktopost-saml-tutorial/settings.png)

1. In **Settings** (Impostazioni) passare alla pagina **Security > Single Sign-on** (Sicurezza > Single Sign-On) e seguire questa procedura.

    ![Configurazione di Oktopost SAML](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Impostare **Enable Single Sign-on** (Abilita Single Sign-On) su **Yes** (Sì).

    b. Nella casella di testo **SAML Endpoint** (Endpoint SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Aprire nel Blocco note il file **Certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato X.509**.
    
    e. Fare clic su **Salva**.

### <a name="create-oktopost-saml-test-user"></a>Creare l'utente di test di Oktopost SAML

1. Accedere a Oktopost SAML come amministratore.

1. Fare clic sull'**icona User > Settings** (Utente > Impostazioni).

    ![Utente di test di Oktopost SAML 1](./media/oktopost-saml-tutorial/settings.png)

1.  Passare a **User Management > Users > Add User** (Gestione utenti > Utenti > Aggiungi utente).

    ![Utente di test di Oktopost SAML 2](./media/oktopost-saml-tutorial/add-user-1.png)

1. Compilare i campi obbligatori nella finestra popup e fare clic su **Send** (Invia).

    ![Utente di test di Oktopost SAML 3](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Oktopost SAML, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Oktopost SAML e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Testa l'applicazione** nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Oktopost SAML per cui si è configurato l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Oktopost SAML in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Oktopost SAML per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Oktopost SAML, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).