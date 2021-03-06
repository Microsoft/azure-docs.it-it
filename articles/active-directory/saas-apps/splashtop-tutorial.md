---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Splashtop | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520558"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Splashtop

Questa esercitazione descrive come integrare Splashtop con Azure Active Directory (Azure AD). Integrando Splashtop con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Splashtop.
* Abilitare gli utenti per l'accesso automatico a Splashtop con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Splashtop abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Splashtop supporta l'accesso Single Sign-On avviato da **SP**

* Dopo aver configurato Splashtop, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Aggiunta di Splashtop dalla raccolta

Per configurare l'integrazione di Splashtop in Azure AD è necessario aggiungere Splashtop dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Splashtop** nella casella di ricerca.
1. Selezionare **Splashtop** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Splashtop

Configurare e testare l'accesso SSO di Azure AD con Splashtop usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Splashtop.

Per configurare e testare l'accesso SSO di Azure AD con Splashtop, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Splashtop](#configure-splashtop-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Splashtop](#create-splashtop-test-user)** : per avere una controparte di B.Simon in Splashtop collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Splashtop** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://my.splashtop.com/login/sso`

1. L'applicazione Splashtop prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione TicketManager prevede che **nameidentifier** sia associato a **user.mail**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Splashtop** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Splashtop.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Splashtop**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-splashtop-sso"></a>Configurare l'accesso Single Sign-On di Splashtop

In questa sezione è necessario richiedere un nuovo metodo per l'accesso Single Sign-On nel [portale Web di Splashtop](https://my.splashtop.com/login).
1. Nel portale Web di Splashtop passare alla scheda **Account info** / **Team** (Informazioni account/Team) e quindi scorrere fino alla sezione **Single Sign On**. Fare quindi clic su **Apply for new SSO method** (Richiedi nuovo metodo per l'accesso Single Sign-On).

    ![Screenshot che mostra la pagina Single Sign-On in cui è possibile selezionare Apply for new SSO method.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Nella finestra della richiesta assegnare un **nome all'accesso Single Sign-On**, ad esempio Nuovo Azure, quindi selezionare **Azure** come tipo IDP e inserire i valori di **Login URL** (URL di accesso) e **Azure AD Identifier** (Identificatore Azure AD) copiati dall'applicazione Splashtop nel portale di Azure.

    ![Screenshot che mostra la pagina Apply for SSO method in cui è possibile immettere un nome e altre informazioni.](media/splashtop-tutorial/azure-sso-1.png)

1. Per le informazioni sul certificato, fare clic con il pulsante destro del mouse sul file di certificato scaricato dall'applicazione Splashtop nel portale di Azure, modificarlo nel Blocco note, quindi copiarne il contenuto e incollarlo nel campo **Download Certificate (Base64)** (Scarica certificato (Base64)).

    ![Screenshot che mostra la selezione di un file di certificato e la sua apertura con Blocco note.](media/splashtop-tutorial/cert-1.png)
    ![Screenshot che mostra il contenuto del file di certificato.](media/splashtop-tutorial/cert-2.png)
    ![Screenshot che mostra la casella di testo Download Certificate.](media/splashtop-tutorial/azure-sso-2.png)

1. L'operazione è terminata. Fare clic su **Salva**. Si verrà contattati dal team di convalida SSO di Splashtop per le informazioni di verifica, quindi sarà possibile attivare il metodo per l'accesso Single Sign-On.

### <a name="create-splashtop-test-user"></a>Creare l'utente di test di Splashtop

1. Dopo l'attivazione del metodo per l'accesso Single Sign-On, selezionare il metodo appena creato per abilitarlo nella sezione **Single Sign-on**.

    ![Screenshot che mostra la pagina Single Sign-On in cui è possibile abilitare il nuovo metodo.](media/splashtop-tutorial/enable.png)

1. Invitare l'utente di test, ad esempio `B.Simon@contoso.com`, al team di Splashtop con il metodo per l'accesso Single Sign-On appena creato.

    ![Screenshot che mostra la pagina Invita utenti in cui è possibile selezionare il nuovo metodo.](media/splashtop-tutorial/invite.png)

1. È anche possibile modificare un account Splashtop esistente in un account SSO, come descritto in queste [istruzioni](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. L'operazione è terminata. È possibile usare l'account SSO per accedere al portale Web di Splashtop o all'app aziendale Splashtop.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Splashtop nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Splashtop per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Splashtop con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Splashtop con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)