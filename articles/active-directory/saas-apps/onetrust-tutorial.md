---
title: 'Esercitazione: Integrazione di Azure Active Directory con OneTrust Privacy Management Software | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OneTrust Privacy Management Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648442"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Esercitazione: Integrazione di Azure Active Directory con OneTrust Privacy Management Software

In questa esercitazione si apprenderà come integrare onetrust privacy Management software con Azure Active Directory (Azure AD). Quando si integra onetrust privacy Management software con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a onetrust privacy Management software.
* Consentire agli utenti di eseguire l'accesso automatico a onetrust privacy Management software con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OneTrust Privacy Management Software, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di onetrust privacy Management software Single Sign-On abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OneTrust Privacy Management Software supporta l'accesso SSO avviato da **SP** e **IDP**

* OneTrust Privacy Management Software supporta il provisioning utenti **JIT**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Aggiungere onetrust privacy Management software dalla raccolta

Per configurare l'integrazione di OneTrust Privacy Management Software in Azure AD, è necessario aggiungere OneTrust Privacy Management Software dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **Onetrust privacy Management software** nella casella di ricerca.
1. Selezionare **Onetrust privacy Management software** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Configurare e testare Azure AD SSO per onetrust privacy Management software

Configurare e testare Azure AD SSO con onetrust privacy Management software usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in onetrust privacy Management software.

Per configurare e testare Azure AD SSO con onetrust privacy Management software, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Onetrust privacy Management software SSO](#configure-onetrust-privacy-management-software-sso)** -per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Onetrust privacy Management software](#create-onetrust-privacy-management-software-test-user)** : per avere una controparte di B. Simon InOneTrust privacy Management software collegato alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure.
 
1. Nella portale di Azure della pagina di integrazione dell'applicazione **Onetrust privacy Management software** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://www.onetrust.com/saml2`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di OneTrust Privacy Management Software](mailto:support@onetrust.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura OneTrust Privacy Management Software** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.
1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi annotare la password.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione viene abilitato B. Simon per l'uso di Azure Single Sign-On concedendo l'accesso a onetrust privacy Management software.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **OneTrust Privacy Management Software**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Configurare onetrust privacy Management software SSO

Per configurare l'accesso Single Sign-On sul lato **OneTrust Privacy Management Software**, è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di OneTrust Privacy Management Software](mailto:support@onetrust.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Creare l'utente di test di OneTrust Privacy Management Software

In questa sezione viene creato un utente di nome Britta Simon in OneTrust Privacy Management Software. OneTrust Privacy Management Software supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in OneTrust Privacy Management Software, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare manualmente un utente, contattare [team di supporto di OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di onetrust privacy Management software in cui è possibile avviare il flusso di accesso.  
 
* Passare direttamente all'URL di accesso di onetrust privacy Management software e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al software di gestione della privacy di onetrust per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro onetrust privacy Management software in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente al software di gestione della privacy di onetrust per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato onetrust privacy Management software, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).