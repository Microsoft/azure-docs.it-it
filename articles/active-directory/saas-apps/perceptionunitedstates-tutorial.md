---
title: 'Esercitazione: Integrazione di Azure Active Directory con Perception Stati Uniti (Non-UltiPro) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Perception Stati Uniti (Non-UltiPro).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515526"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Esercitazione: Integrazione di Azure Active Directory con Perception Stati Uniti (Non-UltiPro)

Questa esercitazione descrive come integrare Perception Stati Uniti (Non-UltiPro) con Azure Active Directory (Azure AD).
L'integrazione di Perception Stati Uniti (Non-UltiPro) con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Perception Stati Uniti (Non-UltiPro).
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Perception Stati Uniti (Non-UltiPro) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Perception Stati Uniti (Non-UltiPro), sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Perception Stati Uniti (Non-UltiPro) abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Perception Stati Uniti (Non-UltiPro) supporta l'accesso SSO avviato da **IDP**

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Aggiunta di Perception Stati Uniti (Non-UltiPro) dalla raccolta

Per configurare l'integrazione di Perception Stati Uniti (Non-UltiPro) in Azure AD, è necessario aggiungere Perception Stati Uniti (Non-UltiPro) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Perception Stati Uniti (Non-UltiPro) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Perception Stati Uniti (Non-UltiPro)** , selezionare **Perception Stati Uniti (Non-UltiPro)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Perception Stati Uniti (Non-UltiPro) nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Perception Stati Uniti (Non-UltiPro) usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Perception Stati Uniti (Non-UltiPro).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Perception Stati Uniti (Non-UltiPro), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Perception Stati Uniti (Non-UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Perception Stati Uniti (Non-UltiPro)](#create-perception-united-states-non-ultipro-test-user)** : per avere una controparte di Britta Simon in Perception Stati Uniti (Non-UltiPro) collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Perception Stati Uniti (Non-UltiPro), seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Perception Stati Uniti (Non-UltiPro)](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Perception Stati Uniti (Non-UltiPro)](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://perception.kanjoya.com/sp`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. L'applicazione **Perception United States (Non-UltiPro)** richiede che il valore **Identificatore di Azure AD** come <entity_id>, ottenuto nella sezione **Configurare Perception United States (Non-UltiPro)** , sia con codifica URI. Per ottenere il valore con codifica URI, usare il collegamento seguente: **http://www.url-encode-decode.com/** .

    d. Dopo aver ottenuto il valore con codifica URI, combinarlo con l'**URL di risposta** come indicato di seguito.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Incollare il valore precedente nella casella di testo **URL di risposta**.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configurare Perception Stati Uniti (Non-UltiPro)** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Configurare l'accesso Single Sign-On di Perception Stati Uniti (Non-UltiPro)

1. In un'altra finestra del browser accedere al sito aziendale di Perception Stati Uniti (Non-UltiPro) come amministratore.

2. Sulla barra degli strumenti principale fare clic su **Account Settings** (Impostazioni account).

    ![Screenshot che mostra l'opzione "Account Settings" selezionata sulla barra degli strumenti principale.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Nella pagina **Account Settings** (Impostazioni account) seguire questa procedura:

    ![Utente di Perception Stati Uniti (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Nella casella di testo **Company Name** (Nome società) digitare il nome della **società**.
    
    b. Nella casella di testo **Account Name** (Nome account) digitare il nome dell'**account**.

    c. Nella casella di testo **Default Reply-To Email** (Indirizzo di posta elettronica predefinito per le risposte) digitare un **indirizzo** valido.

    d. Per **SSO Identity Provider** (Provider di identità SSO) selezionare **SAML 2.0**.

4. Nella pagina **SSO Configuration** (Configurazione SSO) seguire questa procedura:

    ![Configurazione SSO di Perception Stati Uniti (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Per **SAML NameID Type** (Tipo ID nome SAML) selezionare **EMAIL** (POSTA ELETTRONICA).

    b. Nella casella di testo **SSO Configuration Name** (Nome configurazione SSO) digitare il nome della **configurazione**.
    
    c. Nella casella di testo **Identity Provider Name** (Nome provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure. 

    d. Nella casella di testo **SAML Domain** (Dominio SAML) immettere il dominio, ad esempio @contoso.com.

    e. Fare clic su **Upload Again** (Carica di nuovo) per caricare il file **XML metadati**.

    f. Fare clic su **Update**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Perception Stati Uniti (Non-UltiPro).

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Perception Stati Uniti (Non-UltiPro)** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Perception Stati Uniti (Non-UltiPro)** .

    ![Collegamento di Perception Stati Uniti (Non-UltiPro) nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Creare l'utente di test di Perception Stati Uniti (Non-UltiPro)

In questa sezione viene creato un utente di nome Britta Simon in Perception Stati Uniti (Non-UltiPro). Collaborare con il [team di supporto clienti di Perception Stati Uniti (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) per aggiungere gli utenti nella piattaforma Perception Stati Uniti (Non-UltiPro).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Perception Stati Uniti (Non-UltiPro) nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Perception Stati Uniti (Non-UltiPro) per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)