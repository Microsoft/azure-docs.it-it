---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Mapbox | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mapbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: fcee52d585d465b06e7b0dc8d70dc35fb66d2615
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916168"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Mapbox

Questa esercitazione descrive come integrare Mapbox con Azure Active Directory (Azure AD). Integrando Mapbox con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Mapbox.
* Abilitare gli utenti per l'accesso automatico a Mapbox con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Mapbox abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mapbox supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-mapbox-from-the-gallery"></a>Aggiunta di Mapbox dalla raccolta

Per configurare l'integrazione di Mapbox in Azure AD, è necessario aggiungere Mapbox dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Mapbox** nella casella di ricerca.
1. Selezionare **Mapbox** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Configurare e testare l'accesso SSO di Azure AD per Mapbox

Configurare e testare l'accesso SSO di Azure AD con Mapbox usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mapbox.

Per configurare e testare l'accesso SSO di Azure AD con Mapbox, completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Mapbox](#configure-mapbox-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Mapbox](#create-mapbox-test-user)** : per avere una controparte di B.Simon in Mapbox collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Mapbox** nel portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona a forma di matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. L'applicazione Mapbox prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Mapbox prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome   |  Attributo di origine|
    | -----|--------- |
    | ruolo | user.assignedroles |
    | | |

    > [!NOTE]
    > Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura Mapbox** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mapbox.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Mapbox**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se i ruoli sono stati configurati come illustrato sopra, è possibile selezionarlo dall'elenco a discesa **Selezionare un ruolo**.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-mapbox-sso"></a>Configurare l'accesso Single Sign-On di Mapbox

1. In un'altra finestra del Web browser accedere a Mapbox come amministratore.

1. Fare clic sulla scheda **Impostazioni**.

    ![Scheda Settings di Mapbox](./media/mapbox-tutorial/configure1.png)

1. Fare clic sulla scheda **Security** (Sicurezza) nel riquadro di spostamento sinistro.

    ![Scheda Security di Mapbox](./media/mapbox-tutorial/configure2.png)

1. Fare clic su **Edit single sign-on** (Modifica accesso Single Sign-On).

    ![Opzione Edit single sign-on di Mapbox](./media/mapbox-tutorial/configure3.png)

1. Scorrere verso il basso fino a **Step 3: Setup SAML single sign-on for Mapbox** (Passaggio 3: Configurare l'accesso Single Sign-On per Mapbox) e seguire questa procedura:

    ![Configurazione di Mapbox](./media/mapbox-tutorial/configure4.png)

    1. Nella casella di testo **IdP Sign-on URL** (URL di accesso IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella di testo **Issuer ID** (ID autorità di certificazione) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    1. Aprire nel Blocco note il file del **certificato (base)** scaricato dal portale di Azure, copiare il contenuto del file di certificato e incollarlo nella casella **X.509 certificate** (Certificato X.509).

    1. Fare clic su **Save single sign-on settings** (Salva impostazione dell'accesso Single Sign-On).

### <a name="create-mapbox-test-user"></a>Creare l'utente di test di Mapbox

In questa sezione viene creato un utente di nome Britta Simon in Mapbox. Collaborare con il [team di supporto di Mapbox](mailto:help@mapbox.com) per aggiungere gli utenti alla piattaforma Mapbox. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Dopo aver fatto clic su Testa l'applicazione nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Mapbox per cui si è configurato l'accesso SSO

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Mapbox in App personali, si dovrebbe accedere automaticamente all'istanza di Mapbox per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Mapbox, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).