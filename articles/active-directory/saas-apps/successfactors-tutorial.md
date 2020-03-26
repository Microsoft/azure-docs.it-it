---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SuccessFactors | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SuccessFactors

Questa esercitazione descrive come integrare SuccessFactors con Azure Active Directory (Azure AD). Integrando SuccessFactors con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SuccessFactors.
* Abilitare gli utenti per l'accesso automatico a SuccessFactors con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SuccessFactors abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SuccessFactors supporta l'accesso SSO avviato da **SP**.
* Dopo aver configurato SuccessFactors, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Aggiunta di SuccessFactors dalla raccolta

Per configurare l'integrazione di SuccessFactors in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SuccessFactors** nella casella di ricerca.
1. Selezionare **SuccessFactors** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SuccessFactors

Configurare e testare l'accesso SSO di Azure AD con SuccessFactors usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SuccessFactors.

Per configurare e testare l'accesso SSO di Azure AD con SuccessFactors, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di SuccessFactors](#configure-successfactors-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SuccessFactors](#create-successfactors-test-user)** : per avere una controparte di B. Simon in SuccessFactors collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SuccessFactors** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto clienti di SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) per ottenere questi valori.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura SuccessFactors** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SuccessFactors.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni, selezionare **SuccessFactors**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-successfactors-sso"></a>Configurare l'accesso Single Sign-On di SuccessFactors

1. In un'altra finestra del Web browser accedere al **portale di amministrazione di SuccessFactors** come amministratore.

2. Vedere **Sicurezza applicazione** e **Single Sign On Feature** (Funzionalità di accesso Single Sign-On).

3. Inserire un valore qualsiasi in **Reset Token** (Reimposta token) e fare clic su **Save Token** (Salva token) per abilitare SSO SAML.

    ![Configurazione dell'accesso Single Sign-On sul lato app][11]

    > [!NOTE]
    > Questo valore viene usato come opzione di attivazione o disattivazione. Se viene salvato un valore, SSO SAML è ON. Se viene salvato un valore vuoto, SSO SAML è OFF.

4. Nella schermata sottostante eseguire le azioni seguenti:

    ![Configurazione dell'accesso Single Sign-On sul lato app][12]
  
    a. Selezionare il pulsante di opzione **SAML v2 SSO** (SSO SAML v2)
  
    b. Impostare **SAML Asserting Party Name** (Nome dell'entità di asserzione SAML), ad esempio Autorità di certificazione SAML + Nome società.

    c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Selezionare **Asserzione** per **Require Mandatory Signature** (Richiedi firma obbligatoria).

    e. Selezionare **Abilitato** per **Enable SAML Flag** (Abilita flag SAML).

    f. Selezionare **No** per **Login Request Signature(SF Generated/SP/RP)** (Firma richiesta di accesso - Generata da SF/SP/RP).

    g. Selezionare **Browser/Post Profile** (Profilo browser/post) per **SAML Profile** (Profilo SAML).

    h. Selezionare **No** per **Enforce Certificate Valid Period** (Applicare periodo di validità certificato).

    i. Copiare il contenuto del file del certificato scaricato dal portale di Azure, copiarlo e incollarlo nella casella di testo **SAML Verifying Certificate** (Verifica certificato SAML).

    > [!NOTE] 
    > Il contenuto del certificato deve avere i tag di inizio e fine certificato.

5. Passare a SAML V2 e seguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On sul lato app][13]

    a. Selezionare **Sì** per **Support SP-initiated Global Logout** (Supporto disconnessione globale avviata da SP).

    b. Nella casella di testo **Global Logout Service URL (LogoutRequest destination)** (URL del servizio disconnessione globale - Destinazione LogoutRequest) incollare il valore **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    c. Selezionare **No** per **Require sp must encrypt all NameID element** (Richiesta a sp di crittografare tutti gli elementi NameID).

    d. Selezionare **non specificato** per **NameID Format** (Formato NameID).

    e. Selezionare **Sì** per **Enable sp initiated login (AuthnRequest)** (Abilita accesso avviato da sp - AuthnRequest).

    f. Nella casella di testo **Send request as Company-Wide issuer** (Invia richiesta come autorità di certificazione a livello di azienda) incollare il valore di **URL di accesso** copiato dal portale di Azure.

6. Eseguire questi passaggi se si vuole che i nomi utente di account di accesso utente non facciano distinzione tra maiuscole e minuscole.

    ![Configure Single Sign-On][29]

    a. Vedere **Impostazioni società** in basso.

    b. Selezionare la casella di controllo accanto alla **Enable Non-Case-Sensitive Username** (Abilita nome utente senza distinzione maiuscole/minuscole).

    c. Fare clic su **Salva**.

    > [!NOTE]
    > Se si prova ad abilitare questa opzione, il sistema controlla se viene creato un nome di account di accesso SAML duplicato. Ad esempio, se il cliente ha nomi utente Utente1 e utente1. L'annullamento della distinzione maiuscole/minuscole crea questi duplicati. Il sistema visualizza un messaggio di errore e non abilita la funzionalità. Il cliente deve modificare uno dei nomi utente in modo che sia scritto diversamente.

### <a name="create-successfactors-test-user"></a>Creare l'utente di test di SuccessFactors

Per consentire agli utenti di Azure AD di accedere a SuccessFactors, è necessario effettuarne il provisioning in SuccessFactors. Nel caso di SuccessFactors, il provisioning è un'attività manuale.

Per creare utenti in SuccessFactors, è necessario contattare il [team di supporto di SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SuccessFactors nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SuccessFactors per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare SuccessFactors con Azure AD](https://aad.portal.azure.com)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere SuccessFactors con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
