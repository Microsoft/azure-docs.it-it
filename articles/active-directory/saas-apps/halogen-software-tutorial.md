---
title: 'Esercitazione: Integrazione di Azure Active Directory con Saba TalentSpace | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: edb94e8e1b4e814bfac4a1a2a90c5ec71bc48c77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Saba TalentSpace

Questa esercitazione descrive come integrare Saba TalentSpace con Azure Active Directory (Azure AD). Integrando Saba TalentSpace con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Saba TalentSpace.
* Abilitare gli utenti per l'accesso automatico a Saba TalentSpace con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Saba TalentSpace abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Saba TalentSpace supporta l'accesso SSO avviato da **SP**

## <a name="add-saba-talentspace-from-the-gallery"></a>Aggiungere Saba TalentSpace dalla raccolta

Per configurare l'integrazione di Saba TalentSpace in Azure AD, è necessario aggiungere Saba TalentSpace dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Saba TalentSpace** nella casella di ricerca.
1. Selezionare **Saba TalentSpace** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Configurare e testare Azure AD SSO per Saba TalentSpace

Configurare e testare l'accesso SSO di Azure AD con Saba TalentSpace usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Saba TalentSpace.

Per configurare e testare Azure AD SSO con Saba TalentSpace, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Saba TalentSpace](#configure-saba-talentspace-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Saba TalentSpace](#create-saba-talentspace-test-user)** : per avere una controparte di B.Simon in Saba TalentSpace collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **Saba TalentSpace** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Nella casella di testo **identificatore (ID entità)** Digitare l'URL usando il modello seguente: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Nella casella di testo **URL di risposta (URL del servizio consumer di asserzione)** Digitare l'URL usando il modello seguente: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Saba TalentSpace](https://support.saba.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Saba TalentSpace** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Saba TalentSpace.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Saba TalentSpace**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-saba-talentspace-sso"></a>Configurare l'accesso Single Sign-On di Saba TalentSpace

1. In una finestra del browser diversa accedere all'applicazione **Saba TalentSpace** come amministratore.

2. Scegliere la scheda **Options (Opzioni)** .
  
    ![Screenshot che mostra la home page di "saba TalentSpace" con la scheda "Options" selezionata.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. Nel riquadro di spostamento a sinistra fare clic su **SAML Configuration**.
  
    ![Screenshot che mostra il riquadro di spostamento sinistro "User Interface" con l'opzione "SAML Configuration" selezionata.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Nella pagina **Configurazione SAML** seguire la procedura seguente:

    ![Screenshot che mostra la pagina "SAML Configuration" con le opzioni di "Settings" evidenziate.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Come **Identificatore univoco**, selezionare **NameID**.

    b. Come **Unique Identifier Maps To** (L'identificatore univoco mappa verso) selezionare **Username** (Nome utente).
  
    c. Per caricare il file di metadati fare clic su **Browse** (Sfoglia) per selezionare il file e quindi fare clic su **Upload File** (Carica file).

    d. Per testare la configurazione, fare clic su **Run Test**.

    > [!NOTE]
    > È necessario attendere la visualizzazione del messaggio"*The SAML test is complete. Please close this window*". chiudere quindi la ginestra del browser aperta. La casella di controllo **Enable SAML** (Abilita SAML) è selezionata solo se il test è stato completato.

    e. Selezionare **Enable SAML**.

    f. Fare clic su **Salva modifiche**.

### <a name="create-saba-talentspace-test-user"></a>Creare l'utente di test di Saba TalentSpace

Questa sezione descrive come creare un utente di nome Britta Simon in Saba TalentSpace.

**Per creare un utente di nome Britta Simon in Saba TalentSpace, seguire questa procedura:**

1. Accedere all'applicazione **Saba TalentSpace** come amministratore.

2. Fare clic sulla scheda **User Center** (Centro utenti) e quindi su **Create User** (Crea utente).

    ![Screenshot che mostra la scheda "User Center" e l'opzione "Create User" selezionata.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Nella pagina **Nuovo utente** eseguire la procedura seguente:

    ![Cos'è Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Digitare il nome dell'utente, ad esempio **B**, nella casella di testo **First Name** (Nome).

    b. Digitare il cognome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome).

    c. Nella casella di testo **Username** (Nome utente) digitare **B.Simon**, il nome utente che appare nel portale di Azure.

    d. Nella casella di testo **Password** digitare una password per B.Simon.

    e. Fare clic su **Salva**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Saba TalentSpace, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a Saba TalentSpace e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro TalentSpace di Saba in app personali, si dovrebbe accedere automaticamente al TalentSpace di Saba per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

 Dopo la configurazione di Saba TalentSpace, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).