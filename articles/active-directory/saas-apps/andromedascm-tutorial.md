---
title: 'Esercitazione: Integrazione di Azure Active Directory con Andromeda | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 31fc1877dd0edd2fd670f0f9466692ac7bc8ad15
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531103"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Esercitazione: Integrazione di Azure Active Directory con Andromeda

Questa esercitazione descrive come integrare Andromeda con Azure Active Directory (Azure AD).
L'integrazione di Andromeda con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Andromeda.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Andromeda con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Andromeda, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Andromeda abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Andromeda supporta l'accesso SSO avviato da **SP e IDP**
* Andromeda supporta il provisioning utenti **JIT**

## <a name="adding-andromeda-from-the-gallery"></a>Aggiunta di Andromeda dalla raccolta

Per configurare l'integrazione di Andromeda in Azure AD, è necessario aggiungere Andromeda dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Andromeda dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Andromeda**, selezionare **Andromeda** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Andromeda nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Andromeda usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Andromeda.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Andromeda, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Andromeda](#configure-andromeda-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Andromeda](#create-andromeda-test-user)** : per definire una controparte di Britta Simon in Andromeda collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Andromeda, eseguire i seguenti passaggi:

1. Nella pagina di integrazione dell'applicazione **Andromeda** del [portale di Azure](https://portal.azure.com/) selezionare **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On per Andromeda](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On per Andromeda](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Poiché questi non sono i valori reali, È necessario aggiornare tale valore con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura è descritta più avanti nell'esercitazione.

6. L'applicazione Andromeda si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Cancellare le definizioni dello spazio dei nomi durante la configurazione.

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | Nome | Attributo di origine|
    | ------ | -----------|
    | ruolo        | Ruolo specifico dell'app |
    | type        | Tipo di app |
    | company       | CompanyName |

    > [!NOTE]
    > Non sono valori reali. Questi valori sono solo a scopo dimostrativo. Usare i ruoli dell'organizzazione.

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Andromeda** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-andromeda-single-sign-on"></a>Configurare Single Sign-On per Andromeda

1. Accedere al sito aziendale di Andromeda come amministratore.

2. Nella parte superiore della barra dei menu fare clic su **Admin** (Amministratore) e passare ad **Administration** (Amministrazione).

    ![Amministratore di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Sul lato sinistro della barra degli strumenti in **Interfaces** (Interfacce) fare clic su **SAML Configuration** (Configurazione SAML).

    ![SAML per Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Nella sezione **SAML Configuration** (Configurazione SAML) seguire la procedura seguente:

    ![Configurazione di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Selezionare **Enable SSO with SAML** (Abilita SSO con SAML).

    b. Nella sezione **Andromeda Information** (Informazioni su Andromeda) copiare il valore del campo **SP Identity** (Identità SP) e incollarlo nella casella di testo **Identificatore** della sezione **Configurazione SAML di base**.

    c. Copiare il valore del campo **Consumer URL** (URL consumer) nella casella di testo **URL di risposta** della sezione **Configurazione SAML di base**.

    d. Copiare il valore del campo **URL di accesso** nella casella di testo **URL di accesso** della sezione **Configurazione SAML di base**.

    e. Nella sezione **Provider di identità SAML** digitare il nome del provider di identità.

    f. Nella casella di testo **Single Sign On End Point** (Endpoint Single Sign-On) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    g. Aprire il **certificato con codifica Base64** scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Certificato X.509**.
    
    h. Eseguire il mapping degli attributi seguenti con il rispettivo valore per implementare l'accesso SSO da Azure AD. L'attributo **ID utente** è richiesto per l'accesso. Ai fini del provisioning, gli attributi relativi a **posta elettronica**, **società**, **tipo di utente** e **ruolo** sono obbligatori. In questa sezione viene definito il mapping degli attributi (nome e valori) associati agli attributi definiti nel portale di Azure

    ![Mapping degli attributi di Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Andromeda.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Andromeda**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Andromeda**.

    ![Collegamento Andromeda nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-andromeda-test-user"></a>Creare un utente di test di Andromeda

In questa sezione si crea un utente di nome Britta Simon in Andromeda. Andromeda supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Andromeda, ne viene creato uno nuovo dopo l'autenticazione. Per creare un utente manualmente, contattare il [team del supporto clienti di Andromeda](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Andromeda nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Andromeda per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)