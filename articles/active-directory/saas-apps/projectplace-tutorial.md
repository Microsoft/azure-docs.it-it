---
title: 'Esercitazione: Integrazione di Azure Active Directory con Projectplace | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Projectplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: cbc65e64dfd18cdc16873a2d82aecadabeec4c28
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553513"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Esercitazione: Integrare Projectplace con Azure Active Directory

Questa esercitazione descrive come integrare Projectplace con Azure Active Directory (Azure AD). Integrando Projectplace con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Projectplace.
* Abilitare gli utenti per l'accesso automatico a Projectplace con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.
* Il provisioning degli utenti in Projectplace può essere effettuato automaticamente.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Projectplace abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Projectplace supporta l'accesso SSO avviato da **SP e IDP** e il provisioning utenti **JIT**.

## <a name="adding-projectplace-from-the-gallery"></a>Aggiunta di Projectplace dalla raccolta

Per configurare l'integrazione di Projectplace in Azure AD, è necessario aggiungere Projectplace dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Projectplace** nella casella di ricerca.
1. Selezionare **Projectplace** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Projectplace usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Projectplace.

Per configurare e testare l'accesso SSO di Azure AD con Projectplace, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Projectplace](#configure-projectplace)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Projectplace](#create-projectplace-test-user)** : per avere una controparte di B.Simon in Projectplace collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Projectplace** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://service.projectplace.com`

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sull'icona **Copia** per copiare l'**URL dei metadati di federazione dell'app** in base ai propri requisiti e salvarlo nel Blocco note.

   ![Collegamento di download del certificato](common/copy-metadataurl.png)

1. Nella sezione **Configura Projectplace** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configurare Projectplace

Per configurare l'accesso Single Sign-On sul lato **Projectplace**, è necessario inviare l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure al [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Il team verificherà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

>[!NOTE]
>La configurazione dell'accesso Single Sign-On deve essere eseguita dal [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Al termine della configurazione, viene ricevuta una notifica. 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B. Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Projectplace.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Projectplace**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-projectplace-test-user"></a>Creare l'utente di test di Projectplace

>[!NOTE]
>Se il provisioning in Projectplace è abilitato, è possibile ignorare questo passaggio. Si può chiedere di abilitare il provisioning al [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Fatto questo, gli utenti verranno creati in Projectplace al primo accesso.

Per consentire agli utenti di Azure AD di accedere ad Projectplace, aggiungerli ad Projectplace. È necessario aggiungerli manualmente.

**Per creare un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Projectplace** come amministratore.

2. Passare a **People** (Persone) e quindi selezionare **Members** (Membri):
   
    ![Passare a People e quindi selezionare Members](./media/projectplace-tutorial/ic790228.png "Persone")

3. Selezionare **Add Member** (Aggiungi membro):
   
    ![Selezionare Add Member](./media/projectplace-tutorial/ic790232.png "Aggiungi membri")

4. Nella sezione **Add Member** (Aggiungi membro) seguire questa procedura.
   
    ![Sezione Add Member](./media/projectplace-tutorial/ic790233.png "Nuovi membri")
   
    1. Nella casella **Nuovi membri** immettere l'indirizzo e-mail di un account Azure AD valido che si desidera aggiungere.
   
    1. Selezionare **Send** (Invia).

   Il titolare dell'account Azure AD riceve un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.

>[!NOTE]
>È inoltre possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Projectplace per aggiungere account utente di Azure AD.


### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Projectplace nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Projectplace per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)