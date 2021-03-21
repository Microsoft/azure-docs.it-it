---
title: 'Esercitazione: Integrazione di Azure Active Directory con TeamSeer | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TeamSeer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6085ba5091b2b9973354280175aeb01f93ad7e28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521170"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Esercitazione: Integrazione di Azure Active Directory con TeamSeer

Questa esercitazione descrive come integrare TeamSeer con Azure Active Directory (Azure AD).
L'integrazione di TeamSeer con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TeamSeer.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TeamSeer con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TeamSeer, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di TeamSeer abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TeamSeer supporta l'accesso SSO avviato da **SP**

## <a name="adding-teamseer-from-the-gallery"></a>Aggiunta di TeamSeer dalla raccolta

Per configurare l'integrazione di TeamSeer in Azure AD, è necessario aggiungere TeamSeer dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TeamSeer dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **TeamSeer**, selezionare **TeamSeer** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![TeamSeer nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TeamSeer usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TeamSeer.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TeamSeer, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di TeamSeer](#configure-teamseer-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di TeamSeer](#create-teamseer-test-user)** : per avere una controparte di Britta Simon in TeamSeer collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TeamSeer, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **TeamSeer** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TeamSeer](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura TeamSeer** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-teamseer-single-sign-on"></a>Configurare l'accesso Single Sign-On di TeamSeer

1. In un'altra finestra del Web browser accedere al sito aziendale di TeamSeer come amministratore.

1. Passare a **HR Admin** (Amministratore risorse umane).

    ![Screenshot che mostra l'opzione HR Admin selezionata nella finestra TeamSeer.](./media/teamseer-tutorial/ic789634.png "Amministratore risorse umane")

1. Fare clic su **Configura**.

    ![Configurazione](./media/teamseer-tutorial/ic789635.png "Configurazione")

1. Fare clic su **Configura dettagli del provider SAML**.

    ![Screenshot che mostra l'opzione shows Set up SAML provider details selezionata.](./media/teamseer-tutorial/ic789636.png "Impostazioni SAML")

1. Nella sezione dei dettagli del provider SAML, eseguire la procedura seguente:

    ![Screenshot che mostra la sezione SAML provider details in cui è possibile immettere i valori indicati.](./media/teamseer-tutorial/ic789637.png "Impostazioni SAML")

    a. Nella casella di testo **URL** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Aprire il certificato con codifica Base 64 in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **IdP Public Certificate** (Certificato pubblico IdP).

1. Per completare la configurazione del provider SAML eseguire la procedura seguente:

    ![Screenshot che mostra la sezione di configurazione del provider SAML in cui è possibile immettere i valori indicati.](./media/teamseer-tutorial/ic789638.png "Impostazioni SAML")

    a. In **Test indirizzi e-mail** digitare l'indirizzo di posta elettronica dell'utente di test.
  
    b. Nella casella di testo **Autorità di certificazione** digitare l'URL del provider di servizi.
  
    c. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TeamSeer.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **TeamSeer**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TeamSeer**.

    ![Collegamento di TeamSeer nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-teamseer-test-user"></a>Creare l'utente di test di TeamSeer

Per consentire agli utenti di Azure AD di accedere a TeamSeer, è necessario effettuarne il provisioning in TeamSeer. Nel caso di TeamSeer, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **TeamSeer** come amministratore.

1. Passare a **HR Admin \> Users** (Amministratore risorse umane > Utenti) e quindi fare clic su **Run the New User wizard** (Esegui la procedura guidata Nuovo utente).

    ![Screenshot che mostra la scheda HR Admin in cui è possibile selezionare una procedura guidata da eseguire.](./media/teamseer-tutorial/ic789640.png "Amministratore risorse umane")

1. Nella sezione **Dettagli utente** eseguire la procedura seguente:

    ![Dettagli utente](./media/teamseer-tutorial/ic789641.png "Dettagli utente")

    a. Nelle caselle di testo correlate digitare **nome**, **cognome**, **nome utente (indirizzo di posta elettronica)** di un account Azure AD di cui effettuare il provisioning.
  
    b. Fare clic su **Avanti**.

1. Seguire le istruzioni visualizzate per l'aggiunta di un nuovo utente e fare clic su **Fine**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TeamSeer per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TeamSeer nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di TeamSeer per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)