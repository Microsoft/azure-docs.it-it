---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con JIRA SAML SSO by Microsoft | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e JIRA SAML SSO by Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 4793cad25e34e2e318dd205ba5f474d4529d13d5
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183898"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con JIRA SAML SSO by Microsoft

Questa esercitazione descrive come integrare JIRA SAML SSO by Microsoft con Azure Active Directory, ovvero Azure AD. Integrando JIRA SAML SSO by Microsoft con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a JIRA SAML SSO by Microsoft.
* Abilitare gli utenti per l'accesso automatico a JIRA SAML SSO by Microsoft con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="description"></a>Descrizione

Usare l'account di Microsoft Azure Active Directory con il server Atlassian JIRA per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione JIRA. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con JIRA SAML SSO by Microsoft sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
- JIRA Core e JIRA Software (versioni dalla 6.4 alla 8.14.0) oppure JIRA Service Desk (versioni dalla 3.0 alla 4.11.1) devono essere installati e configurati in una versione di Windows a 64 bit
- Server JIRA abilitato per HTTPS
- Si noti che le versioni supportate per il plug-in JIRA sono indicate nella sezione seguente.
- Il server JIRA deve essere raggiungibile da Internet, in particolare per l'autenticazione nella pagina di accesso di Azure AD e deve essere in grado di ricevere il token da Azure AD
- Credenziali di amministratore configurate in JIRA
- WebSudo disabilitato in JIRA
- Utente di test creato nell'applicazione server JIRA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione JIRA per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

Per iniziare, sono necessari gli elementi seguenti:

* Non usare l'ambiente di produzione a meno che non sia necessario.
* Sottoscrizione di JIRA SAML SSO by Microsoft abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="supported-versions-of-jira"></a>Versioni di JIRA supportate

* JIRA Core e Software: dalla 6.4 alla 8.14.0
* JIRA Service Desk: dalla 3.0.0 alla 4.11.1
* JIRA supporta anche la versione 5.2. Per altre informazioni, fare clic su [Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Si noti che il plug-in JIRA funziona anche in Ubuntu versione 16.04 e Linux.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* JIRA SAML SSO by Microsoft supporta l'accesso SSO avviato da **SP**

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Aggiunta di JIRA SAML SSO by Microsoft dalla raccolta

Per configurare l'integrazione di JIRA SAML SSO by Microsoft in Azure AD è necessario aggiungere JIRA SAML SSO by Microsoft dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **JIRA SAML SSO by Microsoft** nella casella di ricerca.
1. Selezionare **JIRA SAML SSO by Microsoft** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Configurare e testare l'accesso SSO di Azure AD per JIRA SAML SSO by Microsoft

Configurare e testare l'accesso SSO di Azure AD con JIRA SAML SSO by Microsoft usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in JIRA SAML SSO by Microsoft.

Per configurare e testare l'accesso SSO di Azure AD con JIRA SAML SSO by Microsoft, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di JIRA SAML SSO by Microsoft](#configure-jira-saml-sso-by-microsoft-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di JIRA SAML SSO by Microsoft](#create-jira-saml-sso-by-microsoft-test-user)** : per avere una controparte di B.Simon in JIRA SAML SSO by Microsoft collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **JIRA SAML SSO by Microsoft** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug Jira descritta più avanti nell'esercitazione.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a JIRA SAML SSO by Microsoft.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **JIRA SAML SSO by Microsoft**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Configurare l'accesso Single Sign-On di JIRA SAML SSO by Microsoft

1. In un'altra finestra del Web browser accedere all'istanza di JIRA come amministratore.

2. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Screenshot che mostra l'opzione Add-ons selezionata nel menu Settings.](./media/jiramicrosoft-tutorial/addon1.png)

3. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56506). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Screenshot che mostra la scheda Manage add-ons con il collegamento Upload add-on evidenziato.](./media/jiramicrosoft-tutorial/addon12.png)

4. Per l'esecuzione dello scenario di proxy inverso JIRA o dello scenario di bilanciamento del carico, seguire questa procedura:

    > [!NOTE]
    > Configurare prima di tutto il server con le istruzioni seguenti e quindi installare il plug-in.

    a. Aggiungere l'attributo seguente in **connector port** nel file **server.xml** dell'applicazione server JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Screenshot che mostra il file server.xml in un editor con una nuova riga aggiunta.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Modificare il valore di **Base URL** (URL di base) in **System Settings** (Impostazioni di sistema) in base al proxy/bilanciamento del carico.

    ![Screenshot che mostra la sezione Administration Settings in cui è possibile modificare il valore di Base URL.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Screenshot che mostra la sezione Azure AD SAML Single Sign-on for Jira con l'opzione Configure selezionata.](./media/jiramicrosoft-tutorial/addon14.png)

6. Seguire questa procedura nella pagina di configurazione:

    ![Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On di Microsoft Azure Active Directory per Jira.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, durante la risoluzione dei metadati, l'amministratore riceve un errore.

    a. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    b. Copiare i valori di **identificatore, URL di risposta e URL di accesso** e incollarli rispettivamente nelle caselle **Identificatore, URL di risposta e URL di accesso** nella sezione **JIRA SAML SSO by Microsoft Domain and URLs** (URL e dominio di JIRA SAML SSO by Microsoft) nel portale di Azure.

    c. In **Login Button Name** (Nome pulsante di accesso) digitare il nome del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.
    
    d. In **Login Button Description** (Descrizione pulsante di accesso) digitare la descrizione del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.

    e. In **SAML User ID Locations** (Posizioni ID utente SAML) selezionare **User ID is in the NameIdentifier element of the Subject statement** (ID utente nell'elemento NameIdentifier dell'istruzione Subject) oppure **User ID is in an Attribute element** (ID utente in un elemento Attribute).  Questo ID deve essere l'ID utente di JIRA. Se non viene trovata una corrispondenza per l'ID utente, il sistema non consentirà agli utenti di accedere.

    > [!Note]
    > La posizione predefinita dell'ID utente SAML è nell'identificatore del nome. È possibile sostituirlo con un attributo e immettere il nome dell'attributo appropriato.

    f. Se si seleziona l'opzione **User ID is in an Attribute element** (ID utente in un elemento Attribute), nella casella di testo **Attribute name** (Nome attributo) digitare il nome dell'attributo per cui è previsto l'ID utente.

    g. Se si usa un dominio federato, come AD FS o altri, con Azure AD fare clic sull'opzione **Enable Home Realm Discovery** (Abilita individuazione area di autenticazione principale) e configurare **Domain Name** (Nome dominio).

    h. In **Domain Name** (Nome dominio) digitare il nome del dominio in caso di accesso basato su AD FS.

    i. Selezionare **Enable Single Sign out** (Abilita Single Sign-Out) per impostare la disconnessione da Azure AD quando un utente si disconnette da JIRA.
    
    j. Abilitare la casella di controllo **Force Azure Login** (Forza accesso ad Azure) se si vuole accedere solo con le credenziali di Azure AD.
    
    > [!Note]
    > Per abilitare il modulo di accesso predefinito per gli amministratori nella pagina di accesso quando è abilitata l'impostazione per forzare l'accesso ad Azure, aggiungere il parametro query nell'URL del browser.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    > [!NOTE]
    > Per altre informazioni sull'installazione e la risoluzione dei problemi, vedere la [guida all'amministrazione dei connettori Microsoft per SSO in JIRA](./ms-confluence-jira-plugin-adminguide.md). Per altre informazioni, è possibile consultare anche il documento delle [domande frequenti](./ms-confluence-jira-plugin-adminguide.md).

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Creare l'utente di test di JIRA SAML SSO by Microsoft

Per consentire agli utenti di Azure AD di accedere a un server JIRA locale, è necessario effettuarne il provisioning in JIRA SAML SSO by Microsoft. In JIRA SAML SSO by Microsoft il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale JIRA come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Screenshot che mostra l'opzione User management selezionata nel menu Settings.](./media/jiramicrosoft-tutorial/user1.png)

3. Si verrà reindirizzati alla pagina Administrator Access (Accesso amministratore) per inserire la **Password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Screenshot che mostra la pagina Administrator Access in cui immettere le credenziali.](./media/jiramicrosoft-tutorial/user2.png)

4. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Screenshot che mostra la scheda User management in cui è possibile creare un utente.](./media/jiramicrosoft-tutorial/user3.png) 

5. Nella pagina della finestra di dialogo **"Create New User"** (Crea nuovo utente) effettuare le operazioni seguenti:

    ![Screenshot che mostra la finestra di dialogo Create New User in cui è possibile immettere le informazioni di questo passaggio.](./media/jiramicrosoft-tutorial/user4.png) 

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio B.simon@contoso.com.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio B.Simon.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio B.simon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.

    e. Fare clic su **Create User** (Crea utente).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di JIRA SAML SSO by Microsoft, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di JIRA SAML SSO by Microsoft e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di JIRA SAML SSO by Microsoft in App personali, si verrà reindirizzati all'URL di accesso di JIRA SAML SSO by Microsoft. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato JIRA SAML SSO by Microsoft, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
