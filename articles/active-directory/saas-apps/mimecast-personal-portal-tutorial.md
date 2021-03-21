---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mimecast Personal Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mimecast Personal Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: cc12afb9d4a889d53689421a98b522a8e3264da6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644613"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Mimecast Personal Portal

Questa esercitazione descrive come integrare Mimecast Personal Portal con Azure Active Directory (Azure AD). Integrando Mimecast Personal Portal con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Mimecast Personal Portal.
* Abilitare gli utenti per l'accesso automatico a Mimecast Personal Portal con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Mimecast Personal Portal abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mimecast Personal Portal supporta l'accesso SSO avviato da **SP e IDP**
 
## <a name="add-mimecast-personal-portal-from-the-gallery"></a>Aggiungere Mimecast Personal Portal dalla raccolta

Per configurare l'integrazione di Mimecast Personal Portal in Azure AD, è necessario aggiungere Mimecast Personal Portal dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Mimecast Personal Portal** nella casella di ricerca.
1. Selezionare **Mimecast Personal Portal** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-personal-portal"></a>Configurare e testare Azure AD SSO per Mimecast Personal Portal

Configurare e testare l'accesso SSO di Azure AD con Mimecast Personal Portal usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mimecast Personal Portal.

Per configurare e testare Azure AD SSO con Mimecast Personal Portal, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Mimecast Personal Portal](#configure-mimecast-personal-portal-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Mimecast Personal Portal](#create-mimecast-personal-portal-test-user)** : per avere una controparte di B.Simon in Mimecast Personal Portal collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Mimecast Personal Portal** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da IDP:

    a. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: 

    | Area  |  Valore | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stati Uniti   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Sud Africa    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Estero        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Il valore di `accountcode` è indicato in Mimecast Personal Portal in **Account** > **Settings** > **Account Code** (Account > Impostazioni > Codice account). Aggiungere `accountcode` all'identificatore.

    b. Nella casella di testo **URL di risposta** digitare l'URL: 

    | Area  |  Valore |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stati Uniti   | `https://us-api.mimecast.com/login/saml`|
    | Sud Africa    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Estero        | `https://jer-api.mimecast.com/login/saml`|

1. se si desidera configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare l'URL:

    | Area  |  Valore |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stati Uniti   | `https://us-api.mimecast.com/login/saml`|
    | Sud Africa    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Estero        | `https://jer-api.mimecast.com/login/saml`|

1. Fare clic su **Salva**.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Mimecast Personal Portal.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Mimecast Personal Portal**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-mimecast-personal-portal-sso"></a>Configurare l'accesso Single Sign-On di Mimecast Personal Portal

1. In un'altra finestra del Web browser accedere alla console di amministrazione di Mimecast.

1. Passare ad **Administration** > **Services** > **Applications** (Amministrazione > Servizi > Applicazioni).

    ![Screenshot che mostra la finestra di Mimecast con l'opzione Applications selezionata.](./media/mimecast-personal-portal-tutorial/services.png)

1. Fare clic sulla scheda **Authentication Profiles** (Profili di autenticazione).
    
    ![Screenshot che mostra la scheda Applications con l'opzione Authentication Profiles selezionata.](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Fare clic sulla scheda **New Authentication Profile** (Nuovo profilo di autenticazione).

    ![Screenshot che mostra l'opzione New Authentication Profile selezionata.](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Specificare una descrizione valida nella casella di testo **Description** (Descrizione) e selezionare **Enforce SAML Authentication for Mimecast Personal Portal** (Applica autenticazione SAML per Mimecast Personal Portal).

    ![Screenshot che mostra l'opzione New Authentication Profile selezionata.](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Nella pagina **SAML Configuration for Mimecast Personal Portal** (Configurazione SAML per Mimecast Personal Portal) seguire questa procedura:

    ![Screenshot che mostra dove selezionare l'opzione Enforce SAML Authentication for Administration Console.](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. Per **Provider** selezionare **Azure Active Directory** nell'elenco a discesa.

    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Importa**. Dopo aver importato l'URL dei metadati, i campi verranno popolati automaticamente e non è necessario eseguire alcuna operazione su questi campi.

    d. Assicurarsi di deselezionare le caselle di controllo **Use Password protected Context** (Usa contesto protetto da password) e **Use Integrated Authentication Context** (Usa contesto di autenticazione integrata).

    e. Fare clic su **Salva**.

### <a name="create-mimecast-personal-portal-test-user"></a>Creare l'utente di test di Mimecast Personal Portal

1. In un'altra finestra del Web browser accedere alla console di amministrazione di Mimecast.

1. Passare ad **Administration** > **Directories** > **Internal Directories** (Amministrazione > Directory > Directory interne).

    ![Screenshot che mostra la sezione SAML Configuration for Mimecast Personal Portal in cui è possibile immettere i valori descritti.](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Selezionare il dominio, se è indicato di seguito. In caso contrario, creare un nuovo dominio facendo clic su **New Domain** (Nuovo dominio).

    ![Screenshot che mostra la finestra di Mimecast con l'opzione Internal Directories selezionata.](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Fare clic sulla scheda **New Address** (Nuovo indirizzo).

    ![Screenshot che mostra il dominio selezionato.](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Specificare le informazioni sull'utente richieste nella pagina seguente:

    ![Screenshot che mostra la pagina in cui è possibile immettere i valori descritti.](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@yourdomainname.com`.

    b. Nella casella di testo **nome globale** immettere il **nome completo** dell'utente.

    c. Nelle caselle di testo **Password** **Confirm Password** (Conferma password) immettere la password dell'utente.

    d. Selezionare la casella di controllo **Force Change at Login** (Forza modifica all'accesso).

    e. Fare clic su **Salva**.

    f. Per assegnare ruoli all'utente, fare clic su **Role Edit** (Modifica ruolo) e assegnare il ruolo richiesto all'utente in base ai requisiti dell'organizzazione.

    ![Screenshot che mostra la sezione Address Settings in cui è possibile selezionare l'opzione Role Edit.](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Testare l'accesso SSO 
In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Mimecast Personal Portal in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Mimecast Personal Portal e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a Mimecast Personal Portal per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Mimecast Personal Portal in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al portale personale di Mimecast per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Mimecast Personal Portal, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)