---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Discovery Benefits SSO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Discovery Benefits SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0e5d67e00ee56b5c4006a8422c713e3cabb32bfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Discovery Benefits SSO

Questa esercitazione descrive come integrare Discovery Benefits SSO con Azure Active Directory (Azure AD). Integrando Discovery Benefits SSO con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Discovery Benefits SSO.
* Abilitare gli utenti per l'accesso automatico a Discovery Benefits SSO con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Discovery Benefits SSO abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Discovery Benefits SSO supporta l'accesso SSO avviato da **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Aggiunta di Discovery Benefits SSO dalla raccolta

Per configurare l'integrazione di Discovery Benefits SSO in Azure AD, è necessario aggiungere Discovery Benefits SSO dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Discovery Benefits SSO** nella casella di ricerca.
1. Selezionare **Discovery Benefits SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Discovery Benefits SSO

Configurare e testare l'accesso SSO di Azure AD con Discovery Benefits SSO usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Discovery Benefits SSO.

Per configurare e testare l'accesso SSO di Azure AD con Discovery Benefits SSO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Discovery Benefits SSO](#configure-discovery-benefits-sso-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Discovery Benefits SSO](#create-discovery-benefits-sso-test-user)** : per avere una controparte di B.Simon in Discovery Benefits SSO collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Discovery Benefits SSO** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata in modalità avviata da **IDP** e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. L'applicazione Discovery Benefits SSO prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

    a. Fare clic sull'icona **Edit** (Modifica) per aprire la finestra di dialogo **Unique User Identifier (Name ID)** (Identificatore utente univoco (ID nome)).

    ![Screenshot che mostra la sezione "Attributi utente e attestazioni" con l'opzione "Attestazione obbligatoria" sul lato destro selezionata.](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Configurazione di Discovery Benefits SSO](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Fare clic sull'icona **Edit** (Modifica) per aprire la finestra di dialogo the **Manage transformation** (Gestisci trasformazione).

    c. Nella casella di testo **Transformation** (Trasformazione) digitare la trasformazione **ToUppercase()** visualizzata per tale riga.

    d. Nella casella di testo **Parameter 1** (Parametro 1) digitare il valore del parametro: `<Name Identifier value>`.

    e. Scegliere **Aggiungi**.

    > [!NOTE]
    > Per consentire l'integrazione, in Discovery Benefits SSO è necessario passare un valore stringa fisso nel campo **Unique User Identifier (Name ID)** (Identificatore utente univoco (ID nome)). Azure AD attualmente non supporta questa funzionalità, quindi è possibile usare la trasformazione **ToUpper** o **ToLower** di NameID per impostare un valore stringa fisso, come illustrato in precedenza nello screenshot.

    f. Le attestazioni aggiuntive necessarie per la configurazione dell'accesso SSO (`SSOInstance` e `SSOID`) sono state popolate automaticamente. Usare l'icona **Edit** (Modifica) per eseguire il mapping dei valori in base alle indicazioni dell'organizzazione.

    ![Screenshot che mostra la schermata "Attributi utente e attestazioni" con i valori di "SSOInstance" e "SSOID" evidenziati.](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Discovery Benefits SSO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Discovery Benefits SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Discovery Benefits SSO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-discovery-benefits-sso-sso"></a>Configurare l'accesso Single Sign-On di Discovery Benefits SSO

Per configurare l'accesso Single Sign-On sul lato **Discovery Benefits SSO** è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-discovery-benefits-sso-test-user"></a>Creare l'utente di test di Discovery Benefits SSO

In questa sezione viene creato un utente di nome Britta Simon in Discovery Benefits SSO. Collaborare con il [team di supporto di Discovery Benefits SSO](mailto:Jsimpson@DiscoveryBenefits.com) per aggiungere gli utenti alla piattaforma Discovery Benefits SSO. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Discovery Benefits SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Discovery Benefits SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Discovery Benefits SSO con Azure AD](https://aad.portal.azure.com/)