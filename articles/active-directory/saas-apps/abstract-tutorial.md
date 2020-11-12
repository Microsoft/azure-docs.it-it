---
title: 'Esercitazione: Integrazione di Azure Active Directory con Abstract | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Abstract.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: baae0f89165e292408150dd419c6de8dc41a320c
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335537"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Esercitazione: Integrare Abstract con Azure Active Directory

Questa esercitazione descrive come integrare Abstract con Azure Active Directory (Azure AD). Integrando Abstract con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Abstract.
* Abilitare gli utenti per l'accesso automatico a Abstract con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Abstract abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Abstract supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-abstract-from-the-gallery"></a>Aggiunta di Abstract dalla raccolta

Per configurare l'integrazione di Abstract in Azure AD, è necessario aggiungere Abstract dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Abstract** nella casella di ricerca.
1. Selezionare **Abstract** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Abstract usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Abstract.

Per configurare e testare l'accesso SSO di Azure AD con Abstract, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Abstract](#configure-abstract-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Abstract](#create-abstract-test-user)** : per avere una controparte di Britta Simon in Abstract collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Abstract** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata in modalità avviata da **IDP** e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.abstract.com/signin`

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic sul pulsante Copia per copiare l' **URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Configurare l'accesso SSO di Abstract

Assicurarsi di recuperare `App Federation Metadata Url` e `Azure AD Identifier` dal portale di Azure, dal momento che saranno necessari per configurare l'accesso Single Sign-On in Abstract.

Queste informazioni sono disponibili nella pagina **Configura l'accesso Single Sign-On con SAML** :

* `App Federation Metadata Url` si trova nella sezione **Certificato di firma SAML**.
* `Azure AD Identifier` si trova nella sezione **Configura Abstract**.


Ora è possibile configurare l'accesso Single Sign-On in Abstract:

>[!Note]
>Sarà necessario eseguire l'autenticazione con un account di amministratore dell'organizzazione per accedere alle impostazioni di accesso Single Sign-On in Abstract.

1. Aprire l'[app Web Abstract](https://app.abstract.com/).
2. Passare alla pagina **Permissions** (Autorizzazioni) nella barra laterale a sinistra.
3. Nella sezione **Configure SSO** (Configura accesso SSO) immettere l' **URL dei metadati** e l' **ID entità**.
4. Immettere tutte le eventuali eccezioni manuali. Gli indirizzi di posta elettronica elencati nella sezione delle eccezioni manuali ignoreranno l'accesso SSO e potranno eseguire l'accesso con l'indirizzo di posta elettronica e la password. 
5. Fare clic su **Salva modifiche**.

>[!Note] 
>Nell'elenco delle eccezioni manuali sarà necessario usare gli indirizzi di posta elettronica primari. L'attivazione dell'accesso SSO non riuscirà nel caso in cui l'indirizzo di posta elettronica elencato sia un indirizzo secondario dell'utente. In tale circostanza viene visualizzato un messaggio di errore in cui è indicato l'indirizzo di posta elettronica primario per l'account in cui si è verificato l'errore. Aggiungere l'indirizzo di posta elettronica primario all'elenco delle eccezioni manuali dopo aver verificato l'identità dell'utente.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Abstract.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Abstract**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-abstract-test-user"></a>Creare l'utente di test di Abstract

Per testare l'accesso SSO in Abstract:

1. Aprire l'[app Web Abstract](https://app.abstract.com/).
2. Passare alla pagina **Permissions** (Autorizzazioni) nella barra laterale a sinistra.
3. Fare clic su **Test with my Account** (Esegui il test con l'account personale). Se il test non viene superato, [contattare il team di supporto](https://help.abstract.com/hc/).

>[!Note]
>Sarà necessario eseguire l'autenticazione con un account di amministratore dell'organizzazione per accedere alle impostazioni di accesso Single Sign-On in Abstract.
Questo account di amministratore dell'organizzazione dovrà essere assegnato ad Abstract nel portale di Azure.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Abstract nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Abstract per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)