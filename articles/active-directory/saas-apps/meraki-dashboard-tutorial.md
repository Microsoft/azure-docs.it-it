---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Meraki Dashboard | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Meraki Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 0d5b6b7b3cf74ab740dbaeb8d86ab8bbb98e8531
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357027"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Meraki Dashboard

Questa esercitazione descrive come integrare Meraki Dashboard con Azure Active Directory (Azure AD). Integrando Meraki Dashboard con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Meraki Dashboard.
* Abilitare gli utenti per l'accesso automatico a Meraki Dashboard con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Meraki Dashboard abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Meraki Dashboard supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Aggiunta di Meraki Dashboard dalla raccolta

Per configurare l'integrazione di Meraki Dashboard in Azure AD, è necessario aggiungere Meraki Dashboard dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Meraki Dashboard** nella casella di ricerca.
1. Selezionare **Meraki Dashboard** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Meraki Dashboard

Configurare e testare l'accesso SSO di Azure AD con Meraki Dashboard usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Meraki Dashboard.

Per configurare e testare l'accesso SSO di Azure AD con Meraki Dashboard, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Meraki Dashboard](#configure-meraki-dashboard-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Meraki Dashboard](#create-meraki-dashboard-test-user)** : per avere una controparte di B.Simon in Meraki Dashboard collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Meraki Dashboard** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:
     
    Nella casella di testo **URL di risposta** digitare un URL usando il modello seguente: `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornarlo con quello dell'URL di risposta effettivo, come descritto più avanti nell'esercitazione.

1. Fare clic sul pulsante **Salva**.

1. L'applicazione Meraki Dashboard prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Meraki Dashboard prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome | Attributo di origine|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

1. Nella sezione **Certificato di firma SAML** copiare il valore **Valore di identificazione personale** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

1. Nella sezione **Configura Meraki Dashboard** copiare il valore di URL di disconnessione e salvarlo nel computer.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Meraki Dashboard.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Meraki Dashboard**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

    ![ruolo utente](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > L'opzione **Selezionare un ruolo** verrà disabilitata e il ruolo predefinito è UTENTE per l'utente selezionato.

1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-meraki-dashboard-sso"></a>Configurare l'accesso Single Sign-On di Meraki Dashboard

1. In un'altra finestra del Web browser accedere al sito Web di Meraki Dashboard come amministratore.

1. Passare a **Organization** -> **Settings** (Organizzazione -> Impostazioni).

    ![Scheda Settings di Meraki Dashboard](./media/meraki-dashboard-tutorial/configure-1.png)

1. In Authentication (Autenticazione) modificare **SAML SSO** (SSO SAML) in **SAML SSO enabled** (SSO SAML abilitato).

    ![Opzione Authentication di Meraki Dashboard](./media/meraki-dashboard-tutorial/configure-2.png)

1. Fare clic su **Add a SAML IdP** (Aggiungi un provider di identità SAML).

    ![Opzione Add a SAML IdP di Meraki Dashboard](./media/meraki-dashboard-tutorial/configure-3.png)

1. Nella casella di testo **X.590 cert SHA1 fingerprint** (Identificazione personale SHA1 del certificato X.590) incollare il valore di **Identificazione personale** copiato dal portale di Azure. Fare quindi clic su **Salva**. Dopo il salvataggio, viene visualizzato l'URL del consumer. Copiare il valore di dell'URL del consumer e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configurazione di Meraki Dashboard](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Creare l'utente di test di Meraki Dashboard

1. In un'altra finestra del Web browser accedere al sito Web di Meraki Dashboard come amministratore.

1. Passare a **Organization** -> **Administrators** (Organizzazione -> Amministratori).

    ![Opzione Administrators di Meraki Dashboard](./media/meraki-dashboard-tutorial/user-1.png)

1. Nella sezione SAML administrator roles (Ruoli di amministratore SAML) fare clic sul pulsante **Add SAML role** (Aggiungi ruolo SAML).

    ![Pulsante Add SAML role di Meraki Dashboard](./media/meraki-dashboard-tutorial/user-2.png)

1. Immettere il ruolo **meraki_full_admin**, contrassegnare **Organization access** (Accesso organizzazione) come **Full** (Completo) e fare clic su **Create role** (Crea ruolo). Ripetere il processo per **meraki_readonly_admin**, ma questa volta contrassegnare **Organization access** (Accesso organizzazione) come **Read-only** (Di sola lettura).
 
    ![Creazione dell'utente in Meraki Dashboard](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Meraki Dashboard per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Meraki Dashboard in App personali, si dovrebbe accedere automaticamente all'istanza di Meraki Dashboard per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Meraki Dashboard, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).