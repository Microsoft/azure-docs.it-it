---
title: 'Esercitazione: Integrazione di Azure Active Directory con Corptax | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Corptax.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 4209e609420e610b9f3456da26b0afe0bf839d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689080"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Esercitazione: Integrazione di Azure Active Directory con Corptax

Questa esercitazione descrive come integrare Corptax con Azure Active Directory (Azure AD).
L'integrazione di Corptax con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Corptax.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Corptax con i rispettivi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Corptax, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Corptax abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Corptax supporta l'accesso SSO avviato da **SP**

## <a name="adding-corptax-from-the-gallery"></a>Aggiunta di Corptax dalla raccolta

Per configurare l'integrazione di Corptax in Azure AD, è necessario aggiungere Corptax dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Corptax dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select_azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise_applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add_new_app.png)

4. Nella casella di ricerca digitare **Corptax**, selezionare **Corptax** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Corptax nell'elenco risultati](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Corptax usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Corptax.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Corptax, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Corptax](#configure-corptax-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Corptax](#create-corptax-test-user)** : per avere una controparte di Britta Simon in Corptax collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Corptax, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Corptax** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select_sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select_saml_option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit_urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Corptax](common/sp_intiated.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://asp.corptax.com`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **XML metadati federazione** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Configurare l'accesso Single Sign-On di Corptax

Per configurare l'accesso Single Sign-On in **Corptax**, è necessario inviare il file **XML metadati federazione** scaricato al [team di supporto di Corptax](https://connect.corptax.com/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new_user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user_properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.

    b. Nel campo **Nome utente** digitare `brittasimon@<yourcompanydomain.extension>`. Ad esempio: `brittasimon@contoso.com`.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Corptax.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Corptax**.

    ![Pannello delle applicazioni aziendali](common/enterprise_applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Corptax**.

    ![Collegamento relativo a Corptax nell'elenco delle applicazioni](common/all_applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users_groups_blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add_assign_user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-corptax-test-user"></a>Creare un utente di test di Corptax

In questa sezione viene creato un utente di nome Britta Simon in Corptax. Collaborare con il [team di supporto di Corptax](https://connect.corptax.com/) per aggiungere gli utenti alla piattaforma Corptax. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
Quando si fa clic sul riquadro di Corptax nel pannello di accesso, si viene reindirizzati alla pagina di Corptax seguente: 

![image](media/corptax-tutorial/corptaxlogin.png)

Nella casella di testo **Environment** (Ambiente) digitare l'ambiente appropriato. Si dovrebbe accedere automaticamente all'applicazione Corptax per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)