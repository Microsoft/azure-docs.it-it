---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Confluence SAML SSO by Microsoft | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Confluence SAML SSO by Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 12f6e66140b3654f1283cad45c97325e2bfb1a28
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775667"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Confluence SAML SSO by Microsoft

Questa esercitazione descrive come integrare Confluence SAML SSO by Microsoft con Azure Active Directory (Azure AD). Integrando Confluence SAML SSO by Microsoft con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Confluence SAML SSO by Microsoft.
* Abilitare gli utenti per l'accesso automatico a Confluence SAML SSO by Microsoft con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Descrizione:

Usare l'account di Microsoft Azure Active Directory con il server Atlassian Confluence per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione Confluence. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Confluence SAML SSO by Microsoft sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Applicazione server Confluence installata in un server di Windows a 64 bit (in locale o nell'infrastruttura IaaS cloud)
- Server Confluence abilitato per HTTPS
- Si noti che le versioni supportate per il plug-in Confluence sono indicate nella sezione seguente.
- Il server Confluence deve essere raggiungibile da Internet, in particolare per la pagina di accesso di Azure AD per l'autenticazione e deve essere in grado di ricevere il token da Azure AD
- Credenziali di amministratore configurate in Confluence
- WebSudo disabilitato in Confluence
- Utente di test creato nell'applicazione server Confluence

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione di Confluence per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

Per iniziare, sono necessari gli elementi seguenti:

* Non usare l'ambiente di produzione a meno che non sia necessario.
* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per l'accesso Single Sign-On (SSO) di Confluence SAML SSO by Microsoft.

## <a name="supported-versions-of-confluence"></a>Versioni di Confluence supportate

A tutt'oggi sono supportate le versioni seguenti di Confluence:

- Confluence: da 5.0 a 5.10
- Confluence: da 6.0.1 a 6.15.9
- Confluence: da 7.0.1 a 7.7.2

> [!NOTE]
> Si noti che il plug-in Confluence funziona anche in Ubuntu versione 16.04

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Confluence SAML SSO by Microsoft supporta l'accesso SSO avviato da **SP**

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Aggiunta di Confluence SAML SSO by Microsoft dalla raccolta

Per configurare l'integrazione di Confluence SAML SSO by Microsoft in Azure AD è necessario aggiungere Confluence SAML SSO by Microsoft dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Confluence SAML SSO by Microsoft** nella casella di ricerca.
1. Selezionare **Confluence SAML SSO by Microsoft** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Confluence SAML SSO by Microsoft

Configurare e testare l'accesso SSO di Azure AD con Confluence SAML SSO by Microsoft usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Confluence SAML SSO by Microsoft.

Per configurare e testare l'accesso SSO di Azure AD con Confluence SAML SSO by Microsoft, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO di Confluence SAML SSO by Microsoft](#configure-confluence-saml-sso-by-microsoft-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Confluence SAML SSO by Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)** : per avere una controparte di B.Simon in Confluence SAML SSO by Microsoft collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Confluence SAML SSO by Microsoft** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug-in Confluence, illustrata più avanti nell'esercitazione.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Confluence SAML SSO by Microsoft.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Confluence SAML SSO by Microsoft**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Configurare l'accesso SSO di Confluence SAML SSO by Microsoft

1. In un'altra finestra del Web browser accedere all'istanza di Confluence come amministratore.

1. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Screenshot che mostra l'icona dell'ingranaggio e l'opzione "Add-ons" evidenziata nel menu a discesa.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Screenshot che mostra la pagina "Manage add-ons" con l'azione "Upload add-on" selezionata.](./media/confluencemicrosoft-tutorial/addon12.png)

1. Per l'esecuzione dello scenario di proxy inverso Confluence o dello scenario di bilanciamento del carico, seguire questa procedura:

    > [!NOTE]
    > Configurare prima di tutto il server con le istruzioni seguenti e quindi installare il plug-in.

    a. Aggiungere l'attributo seguente in **connector port** nel file **server.xml** dell'applicazione server JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Screenshot che mostra il file "server.xml" con l'attributo aggiunto alla porta "connector".](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Modificare il valore di **Base URL** (URL di base) in **System Settings** (Impostazioni di sistema) in base al proxy/bilanciamento del carico.

    ![Screenshot che mostra la pagina "Administration - Settings" con l'opzione "Base URL" evidenziata.](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Dopo aver installato il plug-in, questo comparirà nella sezione dei componenti aggiuntivi **User Installed** (Installati dall'utente) della sezione **Manage Add-on** (Gestisci componente aggiuntivo). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Screenshot che mostra la sezione "User Installed" con il pulsante "Configure" evidenziato.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Seguire questa procedura nella pagina di configurazione:

    ![Screenshot che mostra la pagina di configurazione dell'accesso Single Sign-On.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, l'amministratore riceve un errore durante la risoluzione dei metadati.

    1. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    1. Copiare i valori di **identificatore, URL di risposta e URL di accesso** e incollarli rispettivamente nelle caselle di testo **Identificatore, URL di risposta e URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

    1. In **Login Button Name** (Nome pulsante di accesso) digitare il nome del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.
    
    1. In **Login Button Description** (Descrizione pulsante di accesso) digitare la descrizione del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.

    1. In **SAML User ID Locations** (Posizioni ID utente SAML) selezionare **User ID is in the NameIdentifier element of the Subject statement** (ID utente nell'elemento NameIdentifier dell'istruzione Subject) oppure **User ID is in an Attribute element** (ID utente in un elemento Attribute).  Questo ID deve essere l'ID utente di Confluence. Se non viene trovata una corrispondenza per l'ID utente, il sistema non consentirà agli utenti di accedere. 

       > [!Note]
       > La posizione predefinita dell'ID utente SAML è nell'identificatore del nome. È possibile sostituirlo con un attributo e immettere il nome dell'attributo appropriato.

    1. Se si seleziona l'opzione **User ID is in an Attribute element** (ID utente in un elemento Attribute), nella casella di testo **Attribute name** (Nome attributo) digitare il nome dell'attributo per cui è previsto l'ID utente. 

    1. Se si usa un dominio federato, come AD FS o altri, con Azure AD fare clic sull'opzione **Enable Home Realm Discovery** (Abilita individuazione area di autenticazione principale) e configurare **Domain Name** (Nome dominio).

    1. In **Domain Name** (Nome dominio) digitare il nome del dominio in caso di accesso basato su AD FS.

    1. Selezionare **Enable Single Sign out** (Abilita Single Sign-Out) per impostare la disconnessione da Azure AD quando un utente si disconnette da Confluence. 

    1. Abilitare la casella di controllo **Force Azure Login** (Forza accesso ad Azure) se si vuole accedere solo con le credenziali di Azure AD.

       > [!Note]
       > Per abilitare il modulo di accesso predefinito per gli amministratori nella pagina di accesso quando è abilitata l'impostazione per forzare l'accesso ad Azure, aggiungere il parametro query nell'URL del browser.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

       > [!NOTE]
       > Per altre informazioni sull'installazione e la risoluzione dei problemi, vedere la [guida all'amministrazione dei connettori Microsoft per SSO in Confluence](../ms-confluence-jira-plugin-adminguide.md). Per altre informazioni, è possibile consultare anche il documento delle [domande frequenti](../ms-confluence-jira-plugin-faq.md).

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Creare l'utente di test di Confluence SAML SSO by Microsoft

Per consentire agli utenti di Azure AD di accedere a un server Confluence locale, è necessario effettuarne il provisioning in Confluence SAML SSO by Microsoft. In Confluence SAML SSO by Microsoft il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server Confluence locale come amministratore.

1. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](./media/confluencemicrosoft-tutorial/user1.png)

1. Nella sezione Users (Utenti) fare clic sula scheda **Add users** (Aggiungi utenti). Nella pagina della finestra di dialogo **Add a User** (Aggiungi un utente) eseguire la procedura seguente:

    ![Screenshot che mostra la schermata "Confluence administration" con la scheda "Add Users" selezionata e le informazioni immesse in "Add a User".](./media/confluencemicrosoft-tutorial/user2.png)

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio B.Simon.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio B.Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio B.Simon@contoso.com.

    d. Nella casella di testo **Password** digitare la password di B.Simon.

    e. Fare clic sul pulsante **Confirm** (Conferma) e immettere di nuovo la password.

    f. Fare clic sul pulsante **Aggiungi**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Confluence SAML SSO by Microsoft nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Confluence SAML SSO by Microsoft per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Confluence SAML SSO by Microsoft con Azure AD](https://aad.portal.azure.com/)
