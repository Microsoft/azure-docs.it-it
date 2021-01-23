---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con RetrieverMediaDatabase | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RetrieverMediaDatabase.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 786925799d19bf01e8edebbf85de04d92213298b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732846"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retrievermediadatabase"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con RetrieverMediaDatabase

Questa esercitazione descrive come integrare RetrieverMediaDatabase con Azure Active Directory (Azure AD). Integrando RetrieverMediaDatabase con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a RetrieverMediaDatabase.
* Abilitare gli utenti per l'accesso automatico a RetrieverMediaDatabase con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di RetrieverMediaDatabase abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RetrieverMediaDatabase supporta l'accesso SSO avviato da **IDP**

## <a name="adding-retrievermediadatabase-from-the-gallery"></a>Aggiunta di RetrieverMediaDatabase dalla raccolta

Per configurare l'integrazione di RetrieverMediaDatabase in Azure AD, è necessario aggiungere RetrieverMediaDatabase dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **RetrieverMediaDatabase** nella casella di ricerca.
1. Selezionare **RetrieverMediaDatabase** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-retrievermediadatabase"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per RetrieverMediaDatabase

Configurare e testare l'accesso SSO di Azure AD con RetrieverMediaDatabase usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RetrieverMediaDatabase.

Per configurare e testare l'accesso SSO di Azure AD con RetrieverMediaDatabase, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di RetrieverMediaDatabase](#configure-retrievermediadatabase-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di RetrieverMediaDatabase](#create-retrievermediadatabase-test-user)** : per avere una controparte di B.Simon in RetrieverMediaDatabase collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **RetrieverMediaDatabase** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.


1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)
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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RetrieverMediaDatabase.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **RetrieverMediaDatabase**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-retrievermediadatabase-sso"></a>Configurare l'accesso Single Sign-On di RetrieverMediaDatabase

Per configurare l'accesso Single Sign-On sul lato **RetrieverMediaDatabase**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di RetrieverMediaDatabase](mailto:support@retriever.nl). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-retrievermediadatabase-test-user"></a>Creare l'utente di test di RetrieverMediaDatabase

In questa sezione viene creato un utente di nome Britta Simon in RetrieverMediaDatabase. Collaborare con il [team di supporto di RetrieverMediaDatabase](mailto:support@retriever.nl) per aggiungere gli utenti alla piattaforma RetrieverMediaDatabase. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

1. Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di RetrieverMediaDatabase per cui si è configurato l'accesso SSO

1. È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di RetrieverMediaDatabase in App personali, si dovrebbe accedere automaticamente all'applicazione RetrieverMediaDatabase per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato RetrieverMediaDatabase, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).