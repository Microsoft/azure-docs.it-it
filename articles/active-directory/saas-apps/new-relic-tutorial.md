---
title: 'Esercitazione: Integrazione di Azure Active Directory con New Relic by Account | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e New Relic by Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649963"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con New Relic by Account

Questa esercitazione descrive come integrare New Relic by Account con Azure Active Directory (Azure AD). Integrando New Relic by Account con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a New Relic by Account.
* Abilitare gli utenti per l'accesso automatico a New Relic by Account con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di New Relic by Account abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* New Relic by Account supporta l'accesso SSO avviato da **SP**

## <a name="add-new-relic-by-account-from-the-gallery"></a>Aggiungere New Relic per account dalla raccolta

Per configurare l'integrazione di New Relic by Account in Azure AD, è necessario aggiungere New Relic by Account dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **New Relic by Account** nella casella di ricerca.
1. Selezionare **New Relic by Account** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Configurare e testare Azure AD SSO per la nuova reliquia per account

Configurare e testare l'accesso SSO di Azure AD con New Relic by Account usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in New Relic by Account.

Per configurare e testare Azure AD SSO con New Relic by account, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di New Relic by Account](#configure-new-relic-by-account-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di New Relic by Account](#create-new-relic-by-account-test-user)** : per avere una controparte di B.Simon in New Relic by Account collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **New Relic by account** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)
   
1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: 

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize`: assicurarsi di sostituire `acc_id` con l'ID account di New Relic by Account.

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `rpm.newrelic.com`

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura New Relic by Account** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a New Relic by Account.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **New Relic by Account**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è &quot;Accesso predefinito&quot;.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name=&quot;configure-new-relic-by-account-sso&quot;></a>Configurare l'accesso Single Sign-On di New Relic by Account

1. In un'altra finestra del Web browser accedere al sito aziendale di **New Relic by Account** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Screenshot che mostra la pagina Welcome con l'opzione Account settings selezionata.](./media/new-relic-tutorial/settings.png &quot;Impostazioni account")

3. Fare clic sulla scheda **Security and authentication** (Sicurezza e autenticazione), quindi fare clic sulla scheda **Single sign on**.
   
    ![Single Sign-On](./media/new-relic-tutorial/single-sign-on-tab.png "Single Sign-On")

4. Nella pagina della finestra di dialogo SAML eseguire la procedura seguente:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Fare clic su **Choose File** per caricare il certificato di Azure Active Directory scaricato.

    b. Nella casella di testo **Remote login URL** (URL accesso remoto) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    c. Nella casella di testo **Logout landing URL** (URL di destinazione di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    d. Fare clic su **Save my changes**.

### <a name="create-new-relic-by-account-test-user"></a>Creare l'utente di test di New Relic by Account

1. Accedere al sito aziendale di **New Relic by Account** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Screenshot che mostra l'opzione Account settings selezionata nella pagina Welcome.](./media/new-relic-tutorial/account.png "Impostazioni account")

3. Nel riquadro **Account** a sinistra fare clic su **Summary** (Riepilogo), quindi su **Add user** (Aggiungi utente).
   
    ![Screenshot che mostra il riquadro Summary in cui è possibile selezionare Add user.](./media/new-relic-tutorial/add.png "Impostazioni account")

4. Nella finestra di dialogo **Active users** (Utenti attivi) seguire questa procedura:
   
    ![Active users](./media/new-relic-tutorial/user.png "Active users")
   
    a. Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si vuole effettuare il provisioning.

    b. Come **Role** (Ruolo) selezionare **User** (Utente).

    c. Fare clic su **Add this user**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da New Relic by Account per effettuare il provisioning degli account utente Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento a New Relic tramite l'URL di accesso dell'account in cui è possibile avviare il flusso di accesso. 

* Passare direttamente a New Relic tramite l'URL di accesso dell'account e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro New Relic by account in app personali, questo verrà reindirizzato a New Relic per URL di accesso dell'account. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato New Relic per account, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).