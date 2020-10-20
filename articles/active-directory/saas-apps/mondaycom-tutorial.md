---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con monday.com | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e monday.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ed3d5de27a2d9ce5060ff4bfe71c595512658e58
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con monday.com

Questa esercitazione descrive come integrare monday.com con Azure Active Directory (Azure AD). Integrando monday.com con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a monday.com.
* Abilitare gli utenti per l'accesso automatico a monday.com con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di monday.com abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* monday.com supporta l'accesso SSO avviato da **SP e IDP**
* monday.com supporta il provisioning utenti **JIT**

## <a name="adding-mondaycom-from-the-gallery"></a>Aggiunta di monday.com dalla raccolta

Per configurare l'integrazione di monday.com in Azure AD, è necessario aggiungere monday.com dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **monday.com** nella casella di ricerca.
1. Selezionare **monday.com** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per monday.com

Configurare e testare l'accesso SSO di Azure AD con monday.com usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in monday.com.

Per configurare e testare l'accesso SSO di Azure AD con monday.com, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di monday.com](#configure-mondaycom-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di monday.com](#create-mondaycom-test-user)** : per avere una controparte di B.Simon in monday.com collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **monday.com** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si dispone del **file di metadati del provider di servizi** e si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base.

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente. **Identificatore** e **URL di risposta** sono uguali e il valore è nel formato seguente: `https://<your-domain>.monday.com/saml/saml_callback`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di monday.com](https://monday.com/contact-us/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione monday.com prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot che mostra la schermata Attributi utente e attestazioni con i valori predefiniti, quali Givenname user.givenname e Emailaddress user.mail.](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione monday.com prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine |
    |--|--|
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura monday.com** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a monday.com.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **monday.com**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-mondaycom-sso"></a>Configurare l'accesso Single Sign-On di monday.com

1. Per automatizzare la configurazione all'interno di monday.com, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura monday.com** per passare direttamente all'applicazione monday.com. Specificare quindi le credenziali di amministratore per accedere a monday.com. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente monday.com, aprire una nuova finestra del Web browser, accedere a monday.com come amministratore e seguire questa procedura:

1. Passare a **Profile** (Profilo) nell'angolo in alto a destra della pagina e fare clic su **Admin** (Amministratore).

    ![Screenshot che mostra il profilo Admin selezionato.](./media/mondaycom-tutorial/configuration01.png)

1. Selezionare **Security** (Sicurezza) e assicurarsi di fare clic su **Open** (Apri) accanto a SAML.

    ![Screenshot che mostra la scheda Security con l'opzione Open accanto a SAML.](./media/mondaycom-tutorial/configuration02.png)

1. Specificare i dettagli seguenti dell'IDP.

    ![Screenshot che mostra la sezione SAML provider in cui è possibile immettere i dettagli dell'I D P.](./media/mondaycom-tutorial/configuration03.png)

    > [!NOTE]
    > Per altri dettagli, vedere [questo](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) articolo

### <a name="create-mondaycom-test-user"></a>Creare l'utente di test di monday.com

In questa sezione viene creato un utente di nome B. Simon in monday.com. monday.com supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in monday.com, ne viene creato uno nuovo quando si prova ad accedere a monday.com.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di monday.com nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione monday.com per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare monday.com con Azure AD](https://aad.portal.azure.com/)
