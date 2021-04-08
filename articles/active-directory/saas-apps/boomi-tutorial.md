---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Boomi | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: c58566c628eedd1dbc3d86ae6a142156cbf31211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Boomi

Questa esercitazione descrive come integrare Boomi con Azure Active Directory (Azure AD). Integrando Boomi con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Boomi.
* Abilitare gli utenti per l'accesso automatico a Boomi con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Boomi abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Boomi supporta l'accesso SSO avviato da **IDP** .

## <a name="add-boomi-from-the-gallery"></a>Aggiungere Boomi dalla raccolta

Per configurare l'integrazione di Boomi in Azure AD, è necessario aggiungere Boomi dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Boomi** nella casella di ricerca.
1. Selezionare **Boomi** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Configurare e testare Azure AD SSO per Boomi

Configurare e testare l'accesso SSO di Azure AD con Boomi usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Boomi.

Per configurare e testare Azure AD SSO con Boomi, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Boomi](#configure-boomi-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Boomi](#create-boomi-test-user)** : per avere una controparte di B.Simon in Boomi collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **Boomi** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si dispone del **file di metadati del provider di servizi** e si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Fare clic su **Carica il file di metadati**.

    ![Caricare file di metadati](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Scegliere file di metadati](common/browse-upload-metadata.png)

    c. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Configurazione SAML di base.

    d. Immettere l'**URL di accesso**, ad esempio `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}`.

    > [!Note]
    > Si otterrà il **file di metadati del provider di servizi** in base alla procedura descritta più avanti nella sezione **Configurare l'accesso Single Sign-On di Boomi** dell'esercitazione. Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

1. L'applicazione Boomi prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![Screenshot che mostra la schermata Attributi utente e attestazioni con i valori predefiniti, quali Givenname user.givenname e Emailaddress user.mail.](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Boomi prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome |  Attributo di origine|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Boomi** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Boomi.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Boomi**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-boomi-sso"></a>Configurare l'accesso Single Sign-On di Boomi

1. In un'altra finestra del Web browser accedere al sito aziendale di Boomi come amministratore.

1. Passare a **Nome società** e scegliere **Configurazione**.

1. Fare clic sulla scheda **Opzioni SSO** e seguire questa procedura.

    ![Configurazione accesso Single Sign-On sul lato app](./media/boomi-tutorial/import.png)

    a. Selezionare la casella di controllo **Abilita Single Sign-On SAML**.

    b. Fare clic su **Importa** per caricare il certificato scaricato da Azure AD in **Identity Provider Certificate** (Certificato del Provider di identità).

    c. Nella casella di testo **URL di accesso provider di identità** inserire il valore di **URL di accesso** dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Per **Federation Id Location** (Percorso ID federazione) selezionare il pulsante di opzione **Federation Id is in FEDERATION_ID Attribute element** (L'ID federazione si trova nell'elemento Attribute di FEDERATION_ID).

    e. Copiare l'**URL dei metadati di AtomSphere**, passare all'**URL dei metadati** tramite il browser preferito e salvare l'output in un file. Caricare l'**URL dei metadati** nella sezione **Configurazione SAML di base** del portale di Azure.

    f. Fare clic sul pulsante **Salva** .

### <a name="create-boomi-test-user&quot;></a>Creare un utente test di Boomi

Per consentire agli utenti di Azure AD di accedere a Boomi, è necessario effettuarne il provisioning in Boomi. Nel caso di Boomi, il provisioning è un'attività manuale.

### <a name=&quot;to-provision-a-user-account-perform-the-following-steps&quot;></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Boomi come amministratore.

1. Dopo aver effettuato l'accesso, passare a **Gestione utenti** e scegliere **Utenti**.

    ![Screenshot che mostra la pagina User Management con l'opzione Users selezionata.](./media/boomi-tutorial/user.png &quot;Utenti")

1. Fare clic sull'icona **+** . Si aprirà la finestra di dialogo **Add/Maintain User Roles** (Aggiungi/Gestisci ruoli utente).

    ![Screenshot che mostra l'icona + selezionata.](./media/boomi-tutorial/add.png "Utenti")

    ![Screenshot che mostra la schermata Add / Maintain User Roles in cui configurare un utente.](./media/boomi-tutorial/roles.png "Utenti")

    a. Nella casella di testo **Indirizzo di posta elettronica utente** digitare l'indirizzo di posta elettronica dell'utente, ad esempio B.Simon@contoso.com.

    b. Digitare il nome dell'utente, ad esempio B., nella casella di testo **First Name** (Nome).

    c. Digitare il nome dell'utente, ad esempio Simon, nella casella di testo **Cognome**.

    d. Immettere l'**ID federazione** dell'utente. Ogni utente deve avere un ID federazione che lo identifichi in modo univoco all'interno dell'account.

    e. Assegnare il ruolo **Utente standard** all'utente. Non assegnare il ruolo Amministratore perché altrimenti l'utente avrebbe l'accesso normale ad AtomSphere, nonché l'accesso Single Sign-on.

    f. Fare clic su **OK**.

    > [!NOTE]
    > L'utente non riceverà un messaggio di notifica di benvenuto contenente una password che può essere utilizzata per accedere all'account di AtomSphere perché la password viene gestita tramite il provider di identità. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Boomi per eseguire il provisioning degli account utente di AAD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente al Boomi per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Boomi in app personali, si dovrebbe accedere automaticamente al Boomi per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurato Boomi, è possibile imporre il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).