---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con SurveyMonkey Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SurveyMonkey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 8c14d7166beb3fd94a0cf20c94038c8e973a4942
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con SurveyMonkey Enterprise

Questa esercitazione descrive come integrare SurveyMonkey Enterprise con Azure Active Directory (Azure AD). Integrando SurveyMonkey Enterprise con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SurveyMonkey Enterprise.
* Abilitare gli utenti per l'accesso automatico a SurveyMonkey Enterprise con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SurveyMonkey Enterprise abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SurveyMonkey Enterprise supporta l'accesso SSO avviato da **IDP** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-surveymonkey-enterprise-from-the-gallery"></a>Aggiungere SurveyMonkey Enterprise dalla raccolta

Per configurare l'integrazione di SurveyMonkey Enterprise in Azure AD, è necessario aggiungere SurveyMonkey Enterprise dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SurveyMonkey Enterprise** nella casella di ricerca.
1. Selezionare **SurveyMonkey Enterprise** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-surveymonkey-enterprise"></a>Configurare e testare Azure AD SSO per SurveyMonkey Enterprise

Configurare e testare l'accesso SSO di Azure AD con SurveyMonkey Enterprise usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SurveyMonkey Enterprise.

Per configurare e testare Azure AD SSO con SurveyMonkey Enterprise, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO per SurveyMonkey Enterprise](#configure-surveymonkey-enterprise-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SurveyMonkey Enterprise](#create-surveymonkey-enterprise-test-user)**: per avere una controparte di B.Simon in SurveyMonkey Enterprise collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SurveyMonkey Enterprise** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. L'applicazione SurveyMonkey Enterprise prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione SurveyMonkey Enterprise prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura SurveyMonkey Enterprise** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SurveyMonkey Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SurveyMonkey Enterprise**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-surveymonkey-enterprise-sso"></a>Configurare l'accesso SSO per SurveyMonkey Enterprise

Per configurare l'accesso Single Sign-On sul lato **SurveyMonkey Enterprise**, è necessario inviare il file **XML metadati federazione** scaricato e gli URL copiati appropriati dal portale di Azure al [team di supporto di SurveyMonkey Enterprise](mailto:support@selerix.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-surveymonkey-enterprise-test-user"></a>Creare un utente di test di SurveyMonkey Enterprise

Non è necessario creare un utente di test in SurveyMonkey Enterprise. Se l'utente sceglie di creare un nuovo account, verrà effettuato il provisioning degli utenti in base all'asserzione SAML. Il Customer Success Manager di SurveyMonkey Enterprise fornirà i passaggi necessari per completare questo processo dopo che i metadati di Azure sono stati aggiunti alla configurazione di SurveyMonkey Enterprise e sono pronti per la convalida.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente a SurveyMonkey Enterprise per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro SurveyMonkey Enterprise in app personali, si dovrebbe accedere automaticamente a SurveyMonkey Enterprise per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SurveyMonkey Enterprise è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).