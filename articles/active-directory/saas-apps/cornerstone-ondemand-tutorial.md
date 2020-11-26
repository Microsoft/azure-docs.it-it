---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cornerstone OnDemand | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cornerstone OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 33c7b9b91067c39ea047242d207a4834c08b7e40
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179334"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cornerstone OnDemand

Questa esercitazione descrive come integrare Cornerstone OnDemand con Azure Active Directory (Azure AD). Integrando Cornerstone OnDemand con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Cornerstone OnDemand.
* Abilitare gli utenti per l'accesso automatico a Cornerstone OnDemand con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cornerstone OnDemand abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cornerstone OnDemand supporta l'accesso SSO avviato da **SP**
* Cornerstone OnDemand supporta il [provisioning utenti automatico](cornerstone-ondemand-provisioning-tutorial.md)
* Dopo aver configurato Cornerstone OnDemand, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Aggiunta di Cornerstone OnDemand dalla raccolta

Per configurare l'integrazione di Cornerstone OnDemand in Azure AD è necessario aggiungere Cornerstone OnDemand dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cornerstone OnDemand** nella casella di ricerca.
1. Selezionare **Cornerstone OnDemand** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Cornerstone OnDemand

Configurare e testare l'accesso SSO di Azure AD con Cornerstone OnDemand usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cornerstone OnDemand.

Per configurare e testare l'accesso SSO di Azure AD con Cornerstone OnDemand, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Cornerstone OnDemand](#configure-cornerstone-ondemand-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Cornerstone OnDemand](#create-cornerstone-ondemand-test-user)**: per avere una controparte di B.Simon in Cornerstone OnDemand collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cornerstone OnDemand** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<company>.csod.com/samldefault.aspx?ouid=2`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<company>.csod.com/samldefault.aspx?ouid=2`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Cornerstone OnDemand](mailto:moreinfo@csod.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Cornerstone OnDemand** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cornerstone OnDemand.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cornerstone OnDemand**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cornerstone-ondemand-sso"></a>Configurare l'accesso Single Sign-On di Cornerstone OnDemand

Per configurare l'accesso Single Sign-On sul lato **Cornerstone OnDemand** è necessario inviare il file **Certificato (Base64)** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Cornerstone OnDemand](mailto:moreinfo@csod.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-cornerstone-ondemand-test-user"></a>Creare un utente di test di Cornerstone OnDemand

L'obiettivo di questa sezione consiste nel creare un utente di nome B.Simon in Cornerstone OnDemand. Cornerstone OnDemand supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](./cornerstone-ondemand-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

Per configurare il provisioning dell'utente inviare le informazioni, ad esempio nome e indirizzo di posta elettronica dell'utente di Azure AD, al [team di supporto di Cornerstone OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Cornerstone OnDemand per effettuare il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Cornerstone OnDemand nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cornerstone OnDemand per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Cornerstone OnDemand con Azure AD](https://aad.portal.azure.com)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
