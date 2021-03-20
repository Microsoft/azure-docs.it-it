---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rightscale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rightscale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013eadedc00dee23a09eff89147406cc14f017ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516583"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Esercitazione: Integrazione di Azure Active Directory con Rightscale

Questa esercitazione descrive come integrare Rightscale con Azure Active Directory (Azure AD).
L'integrazione di Rightscale con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a RightScale.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a RightScale con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Rightscale, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di RightScale abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RightScale supporta l'accesso SSO avviato da **provider di servizio e IdP**

## <a name="adding-rightscale-from-the-gallery"></a>Aggiunta di Rightscale dalla raccolta

Per configurare l'integrazione di Rightscale in Azure AD, è necessario aggiungere Rightscale dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Rightscale dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **RightScale**, selezionare **RightScale** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![RightScale nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RightScale usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RightScale.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rightscale, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di RightScale](#configure-rightscale-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di RightScale](#create-rightscale-test-user)** : per avere una controparte di Britta Simon in RightScale collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con RightScale, completare i passaggi seguenti:

1. Nella pagina di integrazione dell'applicazione [RightScale](https://portal.azure.com/) del **portale di Azure** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Screenshot che mostra la pagina Configurazione SAML di base.](common/preintegrated.png)

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://login.rightscale.com/`

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura RightScale** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-rightscale-single-sign-on"></a>Configurare l'accesso Single Sign-On di RightScale

1. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di RightScale come amministratore.

2. Nel menu in alto fare clic sulla scheda **Settings** (Impostazioni) e selezionare **Single Sign-On**.

    ![Screenshot che mostra l'opzione Single Sign-On selezionata nel menu Settings.](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Fare clic sul pulsante **Nuovo** per aggiungere i **provider di identità SAML**.

    ![Screenshot mostra il pulsante New selezionato per aggiungere un provider di identità SAML.](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Nella casella **Nome visualizzato** digitare il nome dell'azienda.

    ![Screenshot che mostra dove immettere un nome visualizzato.](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Selezionare **Allow RightScale-initiated SSO using a discovery hint** (Consenti accesso SSO avviato da RightScale tramite un hint di individuazione) e digitare il **nome di dominio** nella casella di testo sottostante.

    ![Screenshot che mostra dove è possibile specificare un metodo di accesso.](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Nella casella di testo **SAML SSO Endpoint** (Endpoint SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    ![Screenshot che mostra dove è possibile immettere un endpoint SSO SAML.](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure in **SAML Entity ID** (ID entità SAML) in RightScale.

    ![Screenshot che mostra dove è possibile immettere un ID entità SAML.](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Fare clic sul pulsante **Browser** per caricare il certificato scaricato dal portale di Azure.

    ![Screenshot che mostra dove è possibile specificare il certificato di firma SAML.](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Fare clic su **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Rightscale.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **RightScale**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Rightscale**.

    ![Collegamento RightScale nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-rightscale-test-user"></a>Creare un utente di test di RightScale

In questa sezione viene creato un utente di nome Britta Simon in RightScale. Collaborare con il [team di supporto clienti di Rightscale](mailto:support@rightscale.com) per aggiungere gli utenti alla piattaforma Rightscale. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RightScale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RightScale per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)