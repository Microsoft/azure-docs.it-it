---
title: 'Esercitazione: Integrazione di Azure Active Directory con Trisotech Digital Enterprise Server | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trisotech Digital Enterprise Server.
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
ms.openlocfilehash: 05978b7300c401a0658d6c3316f37735fcb70c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521935"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Esercitazione: Integrazione di Azure Active Directory con Trisotech Digital Enterprise Server

Questa esercitazione descrive come integrare Trisotech Digital Enterprise Server con Azure Active Directory (Azure AD).
L'integrazione di Trisotech Digital Enterprise Server con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Trisotech Digital Enterprise Server.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Trisotech Digital Enterprise Server con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Trisotech Digital Enterprise Server, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Trisotech Digital Enterprise Server abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Trisotech Digital Enterprise Server supporta l'accesso SSO avviato da **SP**

* Trisotech Digital Enterprise Server supporta il provisioning utenti **JIT**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Aggiunta di Trisotech Digital Enterprise Server dalla raccolta

Per configurare l'integrazione di Trisotech Digital Enterprise Server in Azure AD, è necessario aggiungere Trisotech Digital Enterprise Server dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Trisotech Digital Enterprise Server dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Trisotech Digital Enterprise Server**, selezionare **Trisotech Digital Enterprise Server** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Trisotech Digital Enterprise Server nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trisotech Digital Enterprise Server.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Trisotech Digital Enterprise Server](#configure-trisotech-digital-enterprise-server-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Trisotech Digital Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)** : per avere una controparte di Britta Simon in Trisotech Digital Enterprise Server collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Trisotech Digital Enterprise Server** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio di Trisotech Digital Enterprise Server per l'accesso Single Sign-On](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.trisotech.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Trisotech Digital Enterprise Server](mailto:support@trisotech.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Configurare l'accesso Single Sign-On di Trisotech Digital Enterprise Server

1. In un'altra finestra del Web browser accedere al sito aziendale di Trisotech Digital Enterprise Server come amministratore.

2. Fare clic sull'**icona Menu** e quindi selezionare **Administration** (Amministrazione).

    ![Screenshot che mostra l'icona Administration in Microsoft Digital Enterprise Server.](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selezionare **User Provider** (Provider utenti).

    ![Screenshot che mostra l'opzione User Provider selezionata nel menu.](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Nella sezione **User Provider Configurations** (Configurazioni provider utenti) eseguire queste operazioni:

    ![Screenshot che mostra la sezione User Provider Configuration in cui è possibile immettere i valori indicati.](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selezionare **Secured Assertion Markup Language 2 (SAML 2)** nell'elenco a discesa **Authentication Method** (Metodo di autenticazione).

    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Nella casella di testo **Application ID** (ID applicazione) digitare l'URL usando il modello seguente: `https://<companyname>.trisotech.com`.

    d. Fare clic su **Save** (Salva).

    e. Immettere il nome di dominio nella casella di testo **Allowed Domains (empty means everyone)** (Domini consentiti - Se vuoto significa tutti i domini). Vengono automaticamente assegnate le licenze per gli utenti che corrispondono ai domini consentiti

    f. Fare clic su **Save** (Salva).

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trisotech Digital Enterprise Server.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Trisotech Digital Enterprise Server**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Trisotech Digital Enterprise Server**.

    ![Collegamento Trisotech Digital Enterprise Server nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Creare l'utente di test di Trisotech Digital Enterprise Server

In questa sezione viene creato un utente di nome Britta Simon in Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Trisotech Digital Enterprise Server, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Trisotech Digital Enterprise Server nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Trisotech Digital Enterprise Server per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)