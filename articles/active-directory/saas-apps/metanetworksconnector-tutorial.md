---
title: 'Esercitazione: Integrazione di Azure Active Directory con Meta Networks Connector | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Meta Networks Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 941218f9a35a0a242612ae1d6230c642129c8259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856892"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Esercitazione: Integrazione di Azure Active Directory con Meta Networks Connector

Questa esercitazione descrive come integrare Meta Networks Connector con Azure Active Directory (Azure AD).
L'integrazione di Meta Networks Connector con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Meta Networks Connector.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Meta Networks Connector con i loro account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Meta Networks Connector, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Meta Networks Connector abilitata per Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Meta Networks Connector supporta l'accesso SSO avviato da **SP** e **IDP**
 
* Meta Networks Connector supporta il provisioning utenti **JIT**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Aggiunta di Meta Networks Connector dalla raccolta

Per configurare l'integrazione di Meta Networks Connector in Azure AD, è necessario aggiungere Meta Networks Connector dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Meta Networks Connector dalla raccolta, completare i passaggi seguenti:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Meta Networks Connector**, selezionare **Meta Networks Connector** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Meta Networks Connector nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Meta Networks Connector usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Meta Networks Connector.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Meta Networks Connector, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Meta Networks Connector](#create-meta-networks-connector-test-user)** : per avere una controparte di Britta Simon in Meta Networks Connector collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Meta Networks Connector, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Meta Networks Connector**, fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/both-advanced-urls.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornare tali valori con i valori reali di identificatore, URL di risposta e URL di accesso. La procedura viene descritta più avanti nell'esercitazione.

6. L'applicazione Meta Networks Connector prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione di attributi token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)
    
7. Oltre quelli elencati in precedenza, l'applicazione Meta Networks Connector prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | Nome | Attributo di origine | Spazio dei nomi|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra il riquadro Attestazioni utente con l'opzione per aggiungere una nuova attestazione.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti.](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

9. Nella sezione **Configura Meta Networks Connector** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configurare Single Sign-On per Meta Networks Connector

1. Aprire una nuova scheda del browser e accedere all'account amministratore di Meta Networks Connector.
    
    > [!NOTE]
    > Meta Networks Connector è un sistema sicuro. Di conseguenza, prima di accedere al portale, è necessario aggiungere l'indirizzo IP in un elenco di indirizzi consentiti sul lato del portale. Per ottenere l'indirizzo IP pubblico, seguire il collegamento specificato [qui](https://whatismyipaddress.com/). Inviare l'indirizzo IP al [team di supporto clienti di Meta Networks Connector](mailto:support@metanetworks.com) perché venga inserito nell'elenco degli indirizzi IP consentiti.
    
2. Passare ad **Amministratore** e selezionare **Impostazioni**.
    
    ![Screenshot che mostra l'opzione Impostazioni dal menu Administrator.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Assicurarsi che le opzioni **Log Internet Traffic** (Registra traffico Internet) e **Force VPN MFA** (Forza MFA VPN) siano disattivate.
    
    ![Screenshot che mostra la disattivazione di queste impostazioni.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Passare ad **Amministratore** e selezionare **SAML**.
    
    ![Screenshot che mostra l'opzione SAML nel menu Administrator.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Nella pagina **DETTAGLI** completare i passaggi seguenti:
    
    ![Screenshot che mostra la pagina DETAILS in cui è possibile immettere i valori descritti.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Copiare il valore del campo **SSO URL** (URL di Single Sign-On) nella casella di testo **URL di accesso** della sezione **URL e dominio Meta Networks Connector**.
    
    b. Copiare il valore del campo **Recipient URL** (URL destinatario) nella casella di testo **URL di risposta** della sezione **URL e dominio Meta Networks Connector**.
    
    c. Copiare il valore del campo **Audience URI (SP Entity ID)** (URI destinatario - ID entità provider di servizi) nella casella di testo **Identificatore (ID entità)** della sezione **URL e dominio Meta Networks Connector**.
    
    d. Abilitare SAML
    
6. Nella scheda **GENERALE** completare i passaggi seguenti:

    ![Screenshot che mostra la pagina GENERALE in cui è possibile immettere i valori descritti.](./media/metanetworksconnector-tutorial/configure5.png)

    a. Nella casella di testo **Identity Provider Single Sign-On URL** (URL di accesso Single Sign-On del provider di identità) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Identity Provider Issuer** (Autorità di certificazione provider di identità) incollare il valore dell'**identificatore di Azure AD** copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure nel Blocco note e incollarlo nella casella di testo **Certificato X.509**.

    d. Abilitare il **provisioning JIT (Just-in-Time)** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Meta Networks Connector.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Meta Networks Connector**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Meta Networks Connector**.

    ![Collegamento a Meta Networks Connector nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-meta-networks-connector-test-user"></a>Creare un utente di test di Meta Networks Connector

In questa sezione viene creato un utente di nome Britta Simon in Meta Networks Connector. Meta Networks Connector supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Meta Networks Connector, ne viene creato uno nuovo quando si prova ad accedere a Meta Networks Connector.

>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Meta Networks Connector nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Meta Networks Connector per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

