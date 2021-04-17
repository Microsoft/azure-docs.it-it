---
title: "Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con CylancePROTECT | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con CylancePROTECT

Questa esercitazione descrive come integrare CylancePROTECT con Azure Active Directory (Azure AD). Integrando CylancePROTECT with Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a CylancePROTECT.
* Abilitare gli utenti per l'accesso automatico a CylancePROTECT con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di CylancePROTECT abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* CylancePROTECT supporta l'accesso SSO avviato da **IDP.**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-cylanceprotect-from-the-gallery"></a>Aggiungere CylancePROTECT dalla raccolta

Per configurare l'integrazione di CylancePROTECT in Azure AD, è necessario aggiungere CylancePROTECT dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **CylancePROTECT** nella casella di ricerca.
1. Selezionare **CylancePROTECT** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Configurare e testare Azure AD SSO per CylancePROTECT

Configurare e testare l'accesso SSO di Azure AD con CylancePROTECT usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CylancePROTECT.

Per configurare e testare Azure AD SSO con CylancePROTECT, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di CylancePROTECT](#configure-cylanceprotect-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di CylancePROTECT](#create-cylanceprotect-test-user)**: per avere una controparte di B.Simon in CylancePROTECT collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure'applicazione **CylancePROTECT** individuare la sezione  Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** immettere i valori per i campi seguenti:

    a. Nella casella **di** testo Identificatore digitare uno degli URL seguenti:
    
    | Region | Valore URL |
    |----------|---------|
    | Asia-Pacifico nord-orientale (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacifico sud-orientale (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa centrale (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | America del Nord|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | America del Sud (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Nella casella **di testo URL** di risposta digitare uno degli URL seguenti:
    
    | Region | Valore URL |
    |----------|---------|
    | Asia-Pacifico nord-orientale (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacifico sud-orientale (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa centrale (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | America del Nord|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | America del Sud (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. L'applicazione CylancePROTECT prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione CylancePROTECT prevede che **nameidentifier** sia associato a **user.mail** e che tutte le altre attestazioni rimanenti vengano rimosse, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica**.

    ![image](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura CylancePROTECT** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

> [!NOTE]
> Aprire il certificato scaricato con codifica Base64 in un editor di testo e copiare SOLO il testo compreso tra i tag **START** e **END** da incollare nel portale di amministrazione di Cylance.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CylancePROTECT.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **CylancePROTECT**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cylanceprotect-sso"></a>Configurare l'accesso Single Sign-On di CylancePROTECT

Per configurare l'accesso Single Sign-On sul lato **CylancePROTECT**, è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di CylancePROTECT](https://www.cylance.com/en-us/resources/support/support-overview.html). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati. Per altre informazioni, usare la documentazione di Cylance: [https://support.cylance.com/s/](https://support.cylance.com/s/) .

### <a name="create-cylanceprotect-test-user"></a>Creare l'utente di test di CylancePROTECT

In questa sezione viene creato un utente di nome Britta Simon in CylancePROTECT. Collaborare con l'amministratore della console per aggiungere gli utenti nella piattaforma CylancePROTECT. Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su Test this application in portale di Azure per accedere automaticamente all'applicazione CylancePROTECT per cui si è configurato l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro CylancePROTECT nel App personali, si dovrebbe accedere automaticamente all'applicazione CylancePROTECT per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato CylancePROTECT, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
