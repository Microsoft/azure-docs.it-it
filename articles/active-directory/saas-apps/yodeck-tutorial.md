---
title: 'Esercitazione: Integrazione di Azure Active Directory con Yodeck | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Yodeck.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895107"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Esercitazione: Integrazione di Azure Active Directory con Yodeck

Questa esercitazione descrive come integrare Yodeck con Azure Active Directory (Azure AD).
L'integrazione di Yodeck con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Yodeck.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Yodeck con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Yodeck, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Yodeck abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Yodeck supporta l'accesso SSO avviato da **SP** e **IDP**

## <a name="adding-yodeck-from-the-gallery"></a>Aggiunta di Yodeck dalla raccolta

Per configurare l'integrazione di Yodeck in Azure AD, è necessario aggiungere Yodeck dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Yodeck dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Yodeck**, selezionare **Yodeck** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Yodeck nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Yodeck usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Yodeck.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Yodeck, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Yodeck](#configure-yodeck-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Yodeck](#create-yodeck-test-user)** : per avere una controparte di Britta Simon in Yodeck collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Yodeck, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Yodeck](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Yodeck](common/idp-identifier.png)

    Nella casella di testo **Identificatore** digitare un URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra la sezione Configurazione SAML di base con il collegamento Carica file di metadati.](common/both-preintegrated-signon.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.yodeck.com/login`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Configurare l'accesso Single Sign-On di Yodeck

1. Per automatizzare la configurazione all'interno di **Yodeck**, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Screenshot che mostra il pulsante Installa l'estensione.](./media/target-process-tutorial/install_extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Yodeck** per passare direttamente all'applicazione Yodeck. Nell'applicazione specificare le credenziali di amministratore per accedere a Yodeck. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 5.

    ![Eseguire la configurazione](common/setup-sso.png)

    **Per configurare l'applicazione manualmente, seguire questa procedura:**

1. In un'altra finestra del Web browser accedere al sito aziendale di Yodeck come amministratore.

1. Fare clic sull'opzione **User Settings** (Impostazioni utente) nell'angolo in alto a destra della pagina e selezionare **Account Settings** (Impostazioni account).

    ![Screenshot che mostra l'opzione Account Settings selezionata per l'utente.](./media/yodeck-tutorial/configure1.png)

1. Selezionare **SAML** e seguire questa procedura:

    ![Screenshot che mostra la scheda SAML in cui è possibile eseguire questi passaggi.](./media/yodeck-tutorial/configure2.png)

    a. Selezionare **Import from URL** (Importa dall'URL).

    b. Nella casella di testo **URL** incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic su **Import** (Importa).
    
    c. Dopo l'importazione dell'**URL dei metadati di federazione dell'app**, gli altri campi vengono automaticamente popolati.

    d. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare brittasimon@yourcompanydomain.extension. Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Yodeck.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Yodeck**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Yodeck**.

    ![Collegamento di Yodeck nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-yodeck-test-user"></a>Creare un utente di test di Yodeck

Per consentire agli utenti di Azure AD di accedere a Yodeck, è necessario effettuarne il provisioning in Yodeck. Nel caso di Yodeck, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Yodeck come amministratore.

2. Fare clic sull'opzione **User Settings** (Impostazioni utente) nell'angolo in alto a destra della pagina e selezionare **Users** (Utenti).

    ![Screenshot che mostra l'opzione Users selezionata per l'utente.](./media/yodeck-tutorial/user1.png)

3. Fare clic su **+User** (+Utenti) per aprire la scheda **User Details** (Dettagli utente).

    ![Screenshot che mostra il pulsante Users.](./media/yodeck-tutorial/user2.png)

4. Nella pagina **User Details** eseguire la procedura seguente:

    ![Screenshot che mostra la scheda User Details in cui è possibile eseguire questi passaggi.](./media/yodeck-tutorial/user3.png)

    a. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    d. Selezionare l'opzione **Account Permissions** (Autorizzazioni account) appropriata in base ai requisiti aziendali.
    
    e. Fare clic su **Salva**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro di Yodeck nel pannello di accesso si dovrebbe accedere automaticamente all'applicazione Yodeck per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)