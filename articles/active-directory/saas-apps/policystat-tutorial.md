---
title: 'Esercitazione: Integrazione di Azure Active Directory con PolicyStat | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893069"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Esercitazione: Integrazione di Azure Active Directory con PolicyStat

Questa esercitazione descrive come integrare PolicyStat con Azure Active Directory (Azure AD).
L'integrazione di PolicyStat con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a PolicyStat.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a PolicyStat con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PolicyStat, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di PolicyStat abilitata per l'accesso Single Sign-On

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* PolicyStat supporta l'accesso SSO avviato da **SP**

* PolicyStat supporta il provisioning utenti **JIT**

## <a name="adding-policystat-from-the-gallery"></a>Aggiunta di PolicyStat dalla raccolta

Per configurare l'integrazione di PolicyStat in Azure AD, è necessario aggiungere PolicyStat dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere PolicyStat dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **PolicyStat**, selezionare **PolicyStat** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![PolicyStat nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PolicyStat usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PolicyStat.

Per configurare e testare l'accesso Single Sign-On di Azure AD con PolicyStat, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di PolicyStat](#configure-policystat-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
4. **[Creare l'utente di test di PolicyStat](#create-policystat-test-user)** : per avere una controparte di Britta Simon in PolicyStat collegata alla rappresentazione dell'utente in Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con PolicyStat, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **PolicyStat** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di PolicyStat](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.policystat.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di PolicyStat](http://www.policystat.com/support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

5. L'applicazione PolicyStat prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra la finestra dialogo "User Attributes" con l'icona "Edit" selezionata.](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione PolicyStat prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.
    
    ![Screenshot che mostra la sezione "Attestazioni utente" con le azioni "Aggiungi nuova attestazione" e "Salva" evidenziate.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo "Manage user claims" con le caselle di testo "Name", "Transformation" e "Parameter" evidenziate e il pulsante "Save" selezionato.](./media/policystat-tutorial/attribute01.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Trasformazione**.

    e. Nell'elenco **Trasformazione** digitare il valore dell'attributo indicato per la riga.
    
    f. Nell'elenco **Parametro 1** digitare il valore dell'attributo indicato per la riga.

    g. Fare clic su **Salva**.

7. Nella sezione **Configura PolicyStat** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-policystat-single-sign-on"></a>Configurare l'accesso Single Sign-On di PolicyStat

1. In un'altra finestra del Web browser accedere al sito aziendale di PolicyStat come amministratore.

2. Scegliere la scheda **Admin**, quindi fare clic su **Configurazione di Single Sign-On** nel riquadro di spostamento a sinistra.
   
    ![Administrator Menu](./media/policystat-tutorial/ic808633.png "Menu Amministratore")

3. Fare clic su **Metadati del provider di identità** quindi, nella sezione **Metadati del provider di identità**, eseguire la procedura seguente:
   
    ![Screenshot che mostra l'azione "Your IDP Metadata" selezionata.](./media/policystat-tutorial/ic808636.png "Configurazione di Single Sign-On")
   
    a. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Your Identity Provider Metadata** (Metadati provider di identità).

    b. Fare clic su **Salva modifiche**.

4. Fare clic su **Configura attributi** e nella sezione **Configura attributi** eseguire la procedura seguente:
   
    a. Nella casella di testo **Attributo nome utente** digitare **uid**.

    b. Nella casella di testo **First Name Attribute** (Attributo nome) digitare il nome dell'attestazione dell'attributo nome da **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** di Azure.

    c. Nella casella di testo **Last Name Attribute** (Attributo cognome) digitare il nome dell'attestazione dell'attributo cognome da **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** di Azure.

    d. Nella casella di testo **Email Attribute** (Attributo posta elettronica) digitare il nome dell'attestazione dell'attributo posta elettronica da **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** di Azure.

    e. Fare clic su **Salva modifiche**.

5. Nella sezione **Configurazione** selezionare **Abilitare integrazione di Single Sign-On**.
   
    ![Single Sign-On Configuration](./media/policystat-tutorial/ic808634.png "Configurazione di Single Sign-On")


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita il proprio account per l'uso dell'accesso Single Sign-On di Azure concedendogli l'accesso a PolicyStat.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **PolicyStat**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **PolicyStat**.

    ![Collegamento di PolicyStat nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare l'account nell'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-policystat-test-user"></a>Creare l'utente di test di PolicyStat

In questa sezione viene creato un utente di nome Britta Simon in PolicyStat. PolicyStat supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in PolicyStat, ne viene creato uno nuovo dopo l'autenticazione.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da PolicyStat per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di PolicyStat nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione PolicyStat per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)