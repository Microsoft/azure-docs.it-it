---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Salesforce | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651431"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Salesforce

Questa esercitazione descrive come integrare Salesforce con Azure Active Directory (Azure AD). Integrando Salesforce con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Salesforce.
* Abilitare gli utenti per l'accesso automatico a Salesforce con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Salesforce abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Salesforce supporta SSO avviato da **SP** .

* Salesforce supporta il [provisioning e il deprovisioning **automatizzato** degli utenti](salesforce-provisioning-tutorial.md) (scelta consigliata).

* Salesforce supporta il provisioning degli utenti **just-in-Time** .

* È ora possibile configurare l'applicazione per dispositivi mobili Salesforce con Azure AD per abilitare l'accesso SSO. In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

## <a name="adding-salesforce-from-the-gallery"></a>Aggiunta di Salesforce dalla raccolta

Per configurare l'integrazione di Salesforce in Azure AD, è necessario aggiungere Salesforce dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Salesforce** nella casella di ricerca.
1. Selezionare **Salesforce** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Salesforce

Configurare e testare l'accesso SSO di Azure AD con Salesforce usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce.

Per configurare e testare l'accesso SSO di Azure AD con Salesforce, completare i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Salesforce](#configure-salesforce-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Salesforce](#create-salesforce-test-user)** : per avere una controparte di B.Simon in Salesforce collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Salesforce** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:
    
    a. Nella casella di testo **Identificatore** digitare il valore adottando il modello seguente:

    Account aziendale: `https://<subdomain>.my.salesforce.com`

    Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Nella casella di testo **URL di risposta** digitare il valore nel formato seguente:

    Account aziendale: `https://<subdomain>.my.salesforce.com`

    Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente:

    Account aziendale: `https://<subdomain>.my.salesforce.com`

    Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Salesforce** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Salesforce.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Salesforce**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-salesforce-sso"></a>Configurare l'accesso Single Sign-On di Salesforce

1. Per automatizzare la configurazione all'interno di Salesforce, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione App personali](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Salesforce** per passare direttamente all'applicazione Salesforce. Nell'applicazione specificare le credenziali di amministratore per accedere a Salesforce. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 13.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente Salesforce, aprire una nuova finestra del Web browser, accedere al sito aziendale di Salesforce come amministratore e seguire questa procedura:

1. Fare clic su **Setup** (Configura) sotto l'**icona Settings** (Impostazioni) nell'angolo superiore destro della pagina.

    ![Configurare l'accesso Single Sign-On - Icona delle impostazioni](./media/salesforce-tutorial/configure1.png)

1. Scorrere verso il basso fino alla voce **SETTINGS** (Impostazioni) nel riquadro di spostamento e fare clic su **Identity** (Identità) per espandere la sezione corrispondente. Fare quindi clic su **Single Sign-On Settings**.

    ![Configurare l'accesso Single Sign-On -Settings](./media/salesforce-tutorial/sf-admin-sso.png)

1. Nella pagina **Single Sign-On Settings** fare clic su **Edit**.

    ![Configurare l'accesso Single Sign-On - Edit](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se non si è in grado di abilitare le impostazioni dell'accesso Single Sign-On per l'account Salesforce, potrebbe essere necessario contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support).

1. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

    ![Configurare l'accesso Single Sign-On -SAML Enabled](./media/salesforce-tutorial/sf-enable-saml.png)

1. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New from Metadata File** (Nuovo da file di metadati).

    ![Configurare l'accesso Single Sign-On - New from Metadata File](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Fare clic su **Scegli file** per caricare il file XML di metadati scaricato dal portale di Azure e fare clic su **Crea**.

    ![Configurare l'accesso Single Sign-On - Choose File](./media/salesforce-tutorial/xmlchoose.png)

1. Nella pagina **Single Sign-On Settings di SAML** i campi vengono popolati automaticamente. Se si vuole usare il protocollo JIT SAML, selezionare il **provisioning dell'utente abilitato** e selezionare il **tipo di identità SAML** perché **l'asserzione contiene l'ID Federazione dell'oggetto utente** altrimenti, deselezionare il **provisioning utenti abilitato** e selezionare il **tipo di identità SAML** come **asserzione contiene il nome utente Salesforce dell'utente**. Fare clic su **Salva**.

    ![Configurare l'accesso Single Sign-On - User Provisioning Enabled](./media/salesforce-tutorial/salesforcexml.png)

1. Nel pannello di navigazione sinistro in Salesforce fare clic su **Company Settings** (Impostazioni aziendali) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale).

    ![Configurare l'accesso Single Sign-On - My Domain](./media/salesforce-tutorial/sf-my-domain.png)

1. Scorrere verso il basso fino alla sezione **Authentication Configuration**, quindi fare clic su **Edit**.

    ![Configurare l'accesso Single Sign-On - Authentication Configuration](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Nella sezione **Authentication Configuration (Configurazione autenticazione** ) controllare la **pagina di accesso** e  **AzureSSO** come **servizio di autenticazione** della configurazione SAML SSO e quindi fare clic su Save ( **Salva**).

    ![Configurare l'accesso Single Sign-On - Authentication Service](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Se vengono selezionati più servizi di autenticazione, agli utenti viene richiesta la selezione del servizio di autenticazione da usare per l'accesso quando si tenta di avviare l'accesso Single Sign-On all'ambiente di Salesforce. Se non si vuole visualizzare la richiesta, è consigliabile **lasciare deselezionati tutti gli altri servizi di autenticazione**.

### <a name="create-salesforce-test-user"></a>Creare l'utente di test di Salesforce

In questa sezione viene creato un utente di nome B.Simon in Salesforce. Salesforce supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Salesforce, ne viene creato uno nuovo quando si prova ad accedere a Salesforce. Salesforce supporta anche il provisioning utenti automatico; [qui](salesforce-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Salesforce, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Salesforce e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Salesforce in app personali, si dovrebbe accedere automaticamente a Salesforce per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-salesforce-mobile"></a>Testare l'accesso SSO per Salesforce (per dispositivi mobili)

1. Aprire l'applicazione per dispositivi mobili Salesforce. Nella pagina di accesso fare clic su **Use Custom Domain** (Usa dominio personalizzato).

    ![App per dispositivi mobili Salesforce - Use Custom Domain](media/salesforce-tutorial/mobile-app1.png)

1. Nella casella di testo **Custom Domain** (Dominio personalizzato) immettere il nome del dominio personalizzato registrato e fare clic su **Continue** (Continua).

    ![App per dispositivi mobili Salesforce - Custom Domain](media/salesforce-tutorial/mobile-app2.png)

1. Immettere le credenziali di Azure AD per accedere nell'applicazione Salesforce e fare clic su **Next** (Avanti).

    ![App per dispositivi mobili Salesforce - Credenziali di Azure AD](media/salesforce-tutorial/mobile-app3.png)

1. Nella pagina **Allow Access** (Consenti accesso) come illustrato di seguito, fare clic su **Allow** (Consenti) per concedere l'accesso all'applicazione Salesforce.

    ![App per dispositivi mobili Salesforce - Allow Access?](media/salesforce-tutorial/mobile-app4.png)

1. Al termine dell'accesso verrà infine visualizzata la home page dell'applicazione.

    ![Home page dell'app per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app5.png) ![App per dispositivi mobili Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Salesforce, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)