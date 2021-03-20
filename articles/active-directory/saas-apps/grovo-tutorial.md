---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Grovo | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Grovo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 84bbec783630aadc68a9632c90ee90f4a8cc98d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92446685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Grovo

Questa esercitazione descrive come integrare Grovo con Azure Active Directory (Azure AD). Integrando Grovo con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Grovo.
* Abilitare gli utenti per l'accesso automatico a Grovo con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Grovo abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Grovo supporta l'accesso SSO avviato da **SP e IDP**
* Grovo supporta il provisioning utenti **Just-In-Time** (JIT)

## <a name="adding-grovo-from-the-gallery"></a>Aggiunta di Grovo dalla raccolta

Per configurare l'integrazione di Grovo in Azure AD, è necessario aggiungere Grovo dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Grovo** nella casella di ricerca.
1. Selezionare **Grovo** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Grovo

Configurare e testare l'accesso SSO di Azure AD con Grovo usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Grovo.

Per configurare e testare l'accesso SSO di Azure AD con Grovo, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Grovo](#configure-grovo-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Grovo](#create-grovo-test-user)** : per avere una controparte di B.Simon in Grovo collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Grovo** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL nel formato seguente: `https://<subdomain>.grovo.com`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta, l'URL di accesso e lo stato dell'inoltro effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Grovo](https://www.grovo.com/contact-us). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Grovo** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Grovo.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Grovo**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-grovo-sso"></a>Configurare l'accesso Single Sign-On di Grovo

1. In un'altra finestra del Web browser accedere a Grovo come amministratore.

2. Andare ad **Admin** (Amministratore) > **Integrations** (Integrazioni).
 
    ![Screenshot che mostra il menu "Admin" con l'opzione "Integrations" selezionata.](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Fare clic su **SET UP** (CONFIGURA) nella sezione **SP Initiated SAML 2.0** (SAML 2.0 avviato da SP).

    ![Screenshot che mostra la sezione "S P initiated S A M L 2.0" con il pulsante "Set up" selezionato.](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. Nella finestra popup **SP Initiated SAML 2.0** (SAML 2.0 avviato da SP) seguire questa procedura:

    ![Configurazione di Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **Single sign-on service endpoint** (URL del servizio Single Sign-On) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. In **Single sign-on service endpoint binding** (Associazione endpoint servizio Single Sign-On) selezionare `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Aprire il **certificato con codifica Base64** scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Public key** (Chiave pubblica).

    e. Fare clic su **Avanti**.

### <a name="create-grovo-test-user"></a>Creare l'utente di test di Grovo

In questa sezione viene creato un utente di nome B.Simon in Grovo. Grovo supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Grovo, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Grovo](https://www.grovo.com/contact-us).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Grovo nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Grovo per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Grovo con Azure AD](https://aad.portal.azure.com/)