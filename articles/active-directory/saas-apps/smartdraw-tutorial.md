---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SmartDraw | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 46b4387252be7e0b76f6e0733d6fd4f95d39e2f0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125953"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con SmartDraw

Questa esercitazione descrive come integrare SmartDraw con Azure Active Directory (Azure AD). Integrando SmartDraw con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SmartDraw.
* Abilitare gli utenti per l'accesso automatico a SmartDraw con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SmartDraw abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SmartDraw supporta l'accesso SSO avviato da **SP e IDP**
* SmartDraw supporta il provisioning utenti **JIT**

## <a name="adding-smartdraw-from-the-gallery"></a>Aggiunta di SmartDraw dalla raccolta

Per configurare l'integrazione di SmartDraw in Azure AD, è necessario aggiungere SmartDraw dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **SmartDraw** nella casella di ricerca.
1. Selezionare **SmartDraw** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per SmartDraw

Configurare e testare l'accesso SSO di Azure AD con SmartDraw usando un utente di test di nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SmartDraw.

Per configurare e testare l'accesso SSO di Azure AD con SmartDraw, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di SmartDraw](#configure-smartdraw-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di SmartDraw](#create-smartdraw-test-user)** : per avere una controparte di B.Simon in SmartDraw collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SmartDraw** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Fare clic su **Salva** .

1. L'applicazione SmartDraw prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione SmartDraw prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Gruppi | user.groups |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura SmartDraw** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SmartDraw.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco di applicazioni selezionare **SmartDraw** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-smartdraw-sso"></a>Configurare l'accesso Single Sign-On di SmartDraw

1. Per automatizzare la configurazione all'interno di SmartDraw, è necessario installare l' **estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione** .

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura SmartDraw** per passare direttamente all'applicazione SmartDraw. Nell'applicazione specificare le credenziali di amministratore per accedere a SmartDraw. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare manualmente SmartDraw, aprire una nuova finestra del Web browser, accedere al sito aziendale di SmartDraw come amministratore e seguire questa procedura:

1. Fare clic su **Single Sign-On** nella sezione Gestisci licenza SmartDraw.

    ![Screenshot che mostra la finestra di dialogo Manage your SmartDraw License in cui è possibile selezionare Single Sign-On.](./media/smartdraw-tutorial/configure01.png)

1. Nella pagina Configurazione seguire questa procedura:

    ![Screenshot che mostra la pagina Configuration in cui è possibile immettere i valori descritti.](./media/smartdraw-tutorial/configure02.png)

    a. Nella casella di testo **Your Domain (ad esempio acme.com)** , digitare il dominio.

    b. Copiare il valore di **Your SP Initiated Login Url will be** (L'URL di accesso avviato dall'SP sarà) e incollarlo nella casella di testo URL di accesso in **Configurazione SAML di base** nel portale di Azure.

    c. Nella casella di testo dei **gruppi di sicurezza per consentire l'accesso a SmartDraw** , digitare **Everyone** (tutti gli utenti).

    d. Nella casella di testo **SAML Issuer URL** (URL autorità di certificazione SAML) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. Nel Blocco note aprire il file XML di metadati scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **Metadati SAML** .

    f. Fare clic su **Salva configurazione** 

### <a name="create-smartdraw-test-user"></a>Creare l'utente di test di SmartDraw

In questa sezione viene creato un utente di nome B.Simon in SmartDraw. SmartDraw supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in SmartDraw, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SmartDraw nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SmartDraw per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare SmartDraw con Azure AD](https://aad.portal.azure.com/)