---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con TrueChoice | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e TrueChoice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: jeedes
ms.openlocfilehash: 7d9ae4e92c81e7a4aff2ae14c3775c18637c5b8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103202677"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-truechoice"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con TrueChoice

Questa esercitazione illustra come integrare TrueChoice con Azure Active Directory (Azure AD). Quando si integra TrueChoice con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TrueChoice.
* Abilitare gli utenti per l'accesso automatico a TrueChoice con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per TrueChoice Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TrueChoice supporta SSO avviato da **SP** .

* TrueChoice supporta il provisioning degli utenti **just-in-Time** .

## <a name="adding-truechoice-from-the-gallery"></a>Aggiunta di TrueChoice dalla raccolta

Per configurare l'integrazione di TrueChoice in Azure AD, è necessario aggiungere TrueChoice dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **TrueChoice** nella casella di ricerca.
1. Selezionare **TrueChoice** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-truechoice"></a>Configurare e testare Azure AD SSO per TrueChoice

Configurare e testare Azure AD SSO con TrueChoice usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in TrueChoice.

Per configurare e testare Azure AD SSO con TrueChoice, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare TRUECHOICE SSO](#configure-truechoice-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di TrueChoice](#create-truechoice-test-user)** : per avere una controparte di B. Simon in TrueChoice collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **TrueChoice** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:


    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `urn:amazon:cognito:sp:<TRUECHOICE_APPID>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<APP>.auth.us-east-2.amazoncognito.com/saml2/idpresponse`

    c. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<APP>.auth.us-east-2.amazoncognito.com/login?response_type=code&client_id=<ID>&redirect_uri=https://<APP_ID>.amplifyapp.com/auth/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di TrueChoice](mailto:helpdesk@truechoice.io) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione TrueChoice prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre a quanto sopra, l'applicazione TrueChoice prevede che venga restituito un numero maggiore di attributi nella risposta SAML, indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome |  Attributo di origine|
    | ----------------- | --------- |
    | country | user.country |
    | name | user.displayname |
    |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)
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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a TrueChoice.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **TrueChoice**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-truechoice-sso"></a>Configurare TrueChoice SSO

Per configurare Single Sign-On sul lato **TrueChoice** , è necessario inviare l' **URL dei metadati di Federazione dell'app** al team di supporto di [TrueChoice](mailto:helpdesk@truechoice.io). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-truechoice-test-user"></a>Creare un utente di test di TrueChoice

In questa sezione viene creato un utente di nome Britta Simon in TrueChoice. TrueChoice supporta il provisioning utente just-in-time, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in TrueChoice, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di TrueChoice, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di TrueChoice e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro TrueChoice in app personali, questo verrà reindirizzato all'URL di accesso di TrueChoice. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato TrueChoice, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


