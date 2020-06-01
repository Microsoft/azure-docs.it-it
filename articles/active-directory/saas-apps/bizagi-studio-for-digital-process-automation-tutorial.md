---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Bizagi for Digital Process Automation | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Bizagi for Digital Process Automation.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a374ef8c6605aef8dfb54379017781507339306e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740598"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Bizagi for Digital Process Automation

Questa esercitazione descrive come integrare servizi o server Bizagi for Digital Process Automation con Azure Active Directory (Azure AD). Integrando Bizagi for Digital Process Automation con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a un progetto di servizi o server Bizagi for Digital Process Automation.
* Abilitare gli utenti per l'accesso automatico a un progetto di servizi o server Bizagi for Digital Process Automation con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Progetto Bizagi che usa il server o i servizi di automazione. 
* Certificati personalizzati per le firme di asserzione SAML. Questi certificati devono essere generati in formato P12 o PFX.
* File di metadati in formato XML generato dal progetto Bizagi. 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On (SSO) di Azure AD in un progetto Bizagi che usa il server o i servizi di automazione.

* Bizagi for Digital Process Automation supporta l'accesso SSO avviato da **SP**
* Dopo aver configurato Bizagi for Digital Process Automation, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Aggiunta di Bizagi for Digital Process Automation dalla raccolta

Per configurare l'integrazione di Bizagi for Digital Process Automation in Azure AD, è necessario aggiungere Bizagi for Digital Process Automation dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Bizagi for Digital Process Automation** nella casella di ricerca.
1. Selezionare **Bizagi for Digital Process Automation** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Bizagi for Digital Process Automation

Configurare e testare l'accesso SSO di Azure AD con Bizagi for Digital Process Automation usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato nel progetto Bizagi.

Per configurare e testare l'accesso SSO di Azure AD con Bizagi for Digital Process Automation, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Bizagi for Digital Process Automation](#configure-bizagi-for-digital-process-automation-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Bizagi for Digital Process Automation](#create-bizagi-for-digital-process-automation-test-user)** : per avere una controparte di B.Simon in Bizagi for Digital Process Automation collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Bizagi for Digital Process Automation** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Caricare il file di metadati di Bizagi nell'opzione **Carica file di metadati**.
1. Rivedere la configurazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL del progetto Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l' URL del progetto Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)
    
    Questo URL dei metadati deve essere registrato nelle opzioni di autenticazione del progetto Bizagi.
    
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona di modifica (penna) relativa a **Attributi utente e attestazioni** per modificare l'identificatore utente univoco.
    
    Impostare l'identificatore utente univoco su user.mail.

### <a name="create-an-azure-ad-test"></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Bizagi for Digital Process Automation.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Bizagi for Digital Process Automation**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configurare l'accesso Single Sign-On di Bizagi for Digital Process Automation

Per configurare l'accesso Single Sign-On sul lato **Bizagi for Digital Process Automation**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Creare l'utente di test di Bizagi for Digital Process Automation

In questa sezione viene creato un utente di nome Britta Simon in Bizagi for Digital Process Automation. Collaborare con il [team di supporto di Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) per aggiungere gli utenti alla piattaforma Bizagi for Digital Process Automation. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Bizagi for Digital Process Automation nel pannello di accesso, si dovrebbe accedere automaticamente al portale di Bizagi for Digital Process Automation per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Bizagi for Digital Process Automation con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
