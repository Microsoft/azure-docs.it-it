---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Concur Travel and Expense | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Concur Travel and Expense.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Concur Travel and Expense

Questa esercitazione descrive come integrare Concur Travel and Expense con Azure Active Directory (Azure AD). Integrando Concur Travel and Expense con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Concur Travel and Expense.
* Abilitare gli utenti per l'accesso automatico a Concur Travel and Expense con i rispettivi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Concur Travel and Expense.
* Un ruolo di amministratore aziendale nell'account utente Concur. Per verificare se l'accesso disponibile è corretto, passare allo [strumento self-service per l'accesso SSO di Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Se l'accesso non è disponibile, contattare il supporto tecnico o il project manager dell'implementazione. 

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD.

* Concur Travel and Expense supporta l'accesso SSO avviato da **IDP** e **SP**
* Concur Travel and Expense supporta il test dell'accesso SSO nell'ambiente di produzione e di implementazione 

> [!NOTE]
> L'identificatore di questa applicazione è un valore stringa fisso per ognuna delle tre aree seguenti: Stati Uniti, EMEA e Cina. In un tenant è quindi possibile configurare una sola istanza per ogni area. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Aggiunta di Concur Travel and Expense dalla raccolta

Per configurare l'integrazione di Concur Travel and Expense in Azure AD, è necessario aggiungere Concur Travel and Expense dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Concur Travel and Expense** nella casella di ricerca.
1. Selezionare **Concur Travel and Expense** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Concur Travel and Expense

Configurare e testare l'accesso SSO di Azure AD con Concur Travel and Expense usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Concur Travel and Expense.

Per configurare e testare l'accesso SSO di Azure AD con Concur Travel and Expense, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Concur Travel and Expense](#configure-concur-travel-and-expense-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Concur Travel and Expense](#create-concur-travel-and-expense-test-user)**: per avere in Concur Travel and Expense una controparte di B.Simon collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Concur Travel and Expense** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata in modalità avviata da **IDP** e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

    > [!NOTE]
    > I valori di Identificatore (ID entità) e URL di risposta (URL del servizio consumer di asserzione) sono specifici dell'area. Effettuare la selezione in base al data center dell'entità Concur. Se non si conosce il data center dell'entità Concur, contattare il supporto di Concur. 

5. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (penna) relativa a **Attributo utente** per modificare le impostazioni. Il valore di Identificatore univoco dell'utente deve corrispondere al valore di login_id dell'utente di Concur. In genere, è necessario modificare **user.userprincipalname** in **user.mail**.

    ![Modifica attributo utente](common/edit-attribute.png)

6. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare i metadati e salvarli nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Concur Travel and Expense.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Concur Travel and Expense**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-concur-travel-and-expense-sso"></a>Configurare l'accesso Single Sign-On in Concur Travel and Expense

1. Per automatizzare la configurazione all'interno di Concur Travel and Expense, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura Concur Travel and Expense** per passare direttamente all'applicazione Concur Travel and Expense. Specificare quindi le credenziali di amministratore per accedere a Concur Travel and Expense. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare Concur Travel and Expense manualmente, in un'altra finestra del Web browser, è necessario caricare il file **XML dei metadati di federazione** scaricato in [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) e accedere a Concur Travel and Expense come amministratore.

1. Scegliere **Aggiungi**.
1. Immettere un nome personalizzato per il provider di identità, ad esempio "Azure AD (US)". 
1. Fare clic su **Upload XML File** (Carica file XML) e allegare il file **XML dei metadati della federazione** scaricato in precedenza.
1. Fare clic su **Add Metadata** (Add metadati) per salvare la modifica.

    ![Screenshot dello strumento self-service per l'accesso SSO di Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Creare un utente di test in Concur Travel and Expense

In questa sezione viene creato un utente di nome B.Simon in Concur Travel and Expense. Collaborare con il team di supporto di Concur per aggiungere gli utenti alla piattaforma Concur Travel and Expense. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

> [!NOTE]
> L'ID di accesso Concur di B.Simon deve corrispondere all'identificatore univoco di B.Simon in Azure AD. Se, ad esempio, l'identificatore univoco Azure AD di B.Simon è `B.Simon@contoso.com`, anche l'ID accesso Concur di B.Simon deve essere `B.Simon@contoso.com`. 

## <a name="configure-concur-mobile-sso"></a>Configurare l'accesso Single Sign-On di Concur per dispositivi mobili
Per abilitare l'accesso Single Sign-On di Concur per dispositivi mobili, è necessario fornire al team di supporto il valore di **URL accesso utente**. Eseguire le operazioni seguenti per ottenere il valore di **URL accesso utente** da Azure AD:
1. Passare ad **Applicazioni aziendali**
1. Fare clic su **Concur Travel and Expense**
1. Fare clic su **Proprietà**
1. Copiare il valore di **URL di accesso utente** e fornirlo al supporto di Concur

> [!NOTE]
> L'opzione self-service per la configurazione dell'accesso SSO non è disponibile. Contattare il team di supporto di Concur per abilitare l'accesso SSO per dispositivi mobili. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Concur Travel and Expense, in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di Concur Travel and Expense e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Concur Travel and Expense per cui si è configurato l'accesso SSO

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Concur Travel and Expense in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Concur Travel and Expense per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Concur Travel and Expense, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).