---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: ebd07990767d91c88f245b4ed7f8a66f93de0b85
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631380"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

Questa esercitazione descrive come integrare Citrix ShareFile con Azure Active Directory (Azure AD).
L'integrazione di Citrix ShareFile con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Citrix ShareFile.
* È possibile consentire agli utenti l'accesso automatico a Citrix ShareFile (Single Sign-On) con i rispettivi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Citrix ShareFile, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Citrix ShareFile abilitata per la Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Citrix ShareFile supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Aggiunta di Citrix ShareFile dalla raccolta

Per configurare l'integrazione di Citrix ShareFile in Azure AD, è necessario aggiungere Citrix ShareFile dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Citrix ShareFile** nella casella di ricerca.
1. Selezionare **Citrix ShareFile** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Citrix ShareFile

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix ShareFile con un utente di test di nome **Britta Simon**.
Per il funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix ShareFile.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Citrix ShareFile](#configure-citrix-sharefile-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Citrix ShareFile](#create-citrix-sharefile-test-user)**: per avere una controparte di Britta Simon in Citrix ShareFile collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenant-name>.sharefile.com/saml/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Citrix ShareFile** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Citrix ShareFile.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **Citrix ShareFile**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-citrix-sharefile-sso"></a>Configurare l'accesso Single Sign-On di Citrix ShareFile

1. Per automatizzare la configurazione all'interno di **Citrix ShareFile**, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Citrix ShareFile** per passare direttamente all'applicazione Citrix ShareFile. Specificare quindi le credenziali di amministratore per accedere a Citrix ShareFile. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare Citrix ShareFile manualmente, in un'altra finestra del Web browser accedere al sito aziendale di Citrix ShareFile come amministratore.

1. Nel **Dashboard** fare clic su **Impostazioni** e selezionare **impostazioni di amministrazione**.

    ![Amministrazione](./media/sharefile-tutorial/settings.png)

1. In Admin Settings (Impostazioni di amministrazione) passare a **Security** -> **Login & Security Policy** (Sicurezza > Criteri di accesso e di sicurezza).
   
    ![Amministrazione account](./media/sharefile-tutorial/settings-security.png "Account Administration")

1. In **Basic Settings** nella pagina della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** eseguire la procedura seguente:
   
    ![Single Sign-On](./media/sharefile-tutorial/saml-configuration.png "Single Sign-On")
   
    a. Selezionare **YES** (SÌ) in **Enable SAML** (Abilita SAML).

    b. Copiare il valore di **ShareFile Issuer/ Entity ID** (ID autorità di certificazione/entità ShareFile) e incollarlo nella casella **Identifier URL** (URL identificatore) nella finestra di dialogo **Configurazione SAML di base** nel portale di Azure.
    
    c. Nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IdP/ID entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Fare clic su **Change** (Cambia) accanto al campo **X.509 Certificate** (Certificato X.509) e quindi caricare il certificato scaricato dal portale di Azure.
    
    e. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    
    f. Nella casella di testo **Logout URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Nelle **impostazioni facoltative** scegliere **contesto di autenticazione avviato da SP** come **nome utente e password** ed **esatto**.

5. Fare clic su **Salva**.

## <a name="create-citrix-sharefile-test-user"></a>Creare un utente di test di Citrix ShareFile

1. Accedere al tenant di **Citrix ShareFile**.

2. Fare clic su **People** -> **Manage Users Home** -> **Create New Users** -> **Create Employee** (Utenti > Home page di gestione utenti > Crea nuovi utenti > Crea dipendente).
   
    ![Creare il dipendente](./media/sharefile-tutorial/create-user.png "Create Employee")

3. Nella sezione **Informazioni di base** seguire questa procedura:
   
    ![Informazioni di base](./media/sharefile-tutorial/user-form.png "Basic Information")
   
    a. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente come **Britta**.
   
    b.  Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente come **Simon**.
   
    c. Digitare l'indirizzo e-mail di Britta Simon nella casella di testo **Indirizzo di posta elettronica** come **brittasimon\@contoso.com**.

4. Fare clic su **Add User**.
  
    >[!NOTE]
    >Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica e selezionerà un collegamento per confermare il proprio account prima che venga attivato. È possibile usare qualsiasi altro strumento di creazione di account utente Citrix ShareFile o qualsiasi API fornita da Citrix ShareFile per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Citrix ShareFile, in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di Citrix ShareFile e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Citrix ShareFile in App personali, si verrà reindirizzati all'URL di accesso di Citrix ShareFile. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Citrix ShareFile, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).