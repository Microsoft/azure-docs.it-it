---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Alibaba Cloud Service (Role-based SSO) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Alibaba Cloud Service (Role-based SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.openlocfilehash: 9e4e45cdefc9342ec5727bae8ea10fe133df1627
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318850"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Alibaba Cloud Service (Role-based SSO)

Questa esercitazione descrive come integrare Alibaba Cloud Service (Role-based SSO) con Azure Active Directory (Azure AD). Integrando Alibaba Cloud Service (Role-based SSO) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Alibaba Cloud Service (Role-based SSO).
* Abilitare gli utenti per l'accesso automatico a ad Alibaba Cloud Service (Role-based SSO) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Alibaba Cloud Service (Role-based SSO) abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Alibaba Cloud Service (Role-based SSO) supporta l'accesso SSO avviato da **IDP**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Aggiunta di Alibaba Cloud Service (Role-based SSO) dalla raccolta

Per configurare l'integrazione di Alibaba Cloud Service (Role-based SSO) in Azure AD, è necessario aggiungere Alibaba Cloud Service (Role-based SSO) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Alibaba Cloud Service (Role-based SSO)** nella casella di ricerca.
1. Selezionare **Alibaba Cloud Service (Role-based SSO)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.
5. Nella pagina **Alibaba Cloud Service (Role-based SSO)** fare clic su **Proprietà** nel riquadro di spostamento a sinistra, copiare l'**ID oggetto** e salvarlo nel computer per usarlo in seguito.

    ![Configurazione delle proprietà](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Alibaba Cloud Service (Role-based SSO)

Configurare e testare l'accesso SSO di Azure AD con Alibaba Cloud Service (Role-based SSO) usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Alibaba Cloud Service (Role-based SSO).

Per configurare e testare l'accesso SSO di Azure AD con Alibaba Cloud Service (Role-based SSO), completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On in base al ruolo in Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Configurare l'accesso Single Sign-On di Alibaba Cloud Service (Role-based SSO)](#configure-alibaba-cloud-service-role-based-sso-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Alibaba Cloud Service (Role-based SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** : per avere una controparte di Britta Simon in Alibaba Cloud Service (Role-based SSO) collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Alibaba Cloud Service (Role-based SSO)** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    >[!NOTE]
    >Si otterranno i metadati del provider di servizi da questo [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Fare clic su **Carica il file di metadati**.

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Al termine del caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nella casella di testo della sezione di Alibaba Cloud Service (Role-based SSO):

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

1. Con Alibaba Cloud Service (Role-based SSO) è necessario configurare i ruoli in Azure AD. Le attestazioni di ruolo sono preconfigurate, quindi non è necessario configurarle, ma è comunque necessario crearle in Azure AD usando le informazioni incluse in questo [articolo](../develop/active-directory-enterprise-app-role-management.md).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Alibaba Cloud Service (Role-based SSO)** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Alibaba Cloud Service (Role-based SSO).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Alibaba Cloud Service (Role-based SSO)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella scheda **Utenti e gruppi** selezionare u2 dall'elenco di utenti e fare clic su **Seleziona**. Fare quindi clic su **Assegna**.

    ![Screenshot che mostra il riquadro Aggiungi assegnazione per Alibaba senza alcun utente o gruppo selezionato.](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Visualizzare il ruolo assegnato e testare Alibaba Cloud Service (Role-based SSO).

    ![Screenshot che mostra il ruolo assegnato all'utente u2.](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Dopo aver assegnato l'utente (u2), il ruolo creato viene collegato automaticamente all'utente. Se sono stati creati più ruoli, è necessario collegare il ruolo appropriato all'utente in base alle esigenze. Per implementare l'accesso SSO in base al ruolo da Azure AD a più account Alibaba Cloud, ripetere i passaggi precedenti.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurare l'accesso Single Sign-On in base al ruolo in Alibaba Cloud Service

1. Accedere alla [console RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) di Alibaba Cloud usando Account1.

2. Nel pannello di navigazione a sinistra selezionare **SSO**.

3. Nella scheda **Role-based SSO** (SSO in base al ruolo) fare clic su **Create IdP** (Crea IdP).

4. Nella pagina visualizzata immettere `AAD` nel campo IdP Name (Nome IdP), immettere una descrizione nel campo **Note** (Nota), fare clic su **Upload** (Carica) per caricare il file di metadati federativi scaricato in precedenza e fare clic su **OK**.

5. Dopo aver creato il provider di identità, fare clic su **Create RAM Role** (Crea ruolo RAM).

6. Nel campo **RAM Role Name** (Nome del ruolo RAM) immettere `AADrole`, selezionare `AAD` dall'elenco a discesa **Select IdP** (Seleziona IdP) e fare clic su OK.

    >[!NOTE]
    >È possibile concedere l'autorizzazione al ruolo in base alle esigenze. Dopo aver creato il provider di identità e il ruolo corrispondente, si consiglia di salvare i numeri ARN del provider di identità e del ruolo per un uso successivo. È possibile ottenere i numeri ARN nella pagina delle informazioni sul provider di identità e nella pagina delle informazioni sul ruolo.

7. Associare il ruolo Alibaba Cloud RAM (AADrole) all'utente di Azure AD (u2): Per associare il ruolo RAM all'utente di Azure AD, è necessario creare un ruolo in Azure AD seguendo questa procedura:

    a. Accedere a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Fare clic su **autorizzazioni di modifica** per ottenere le autorizzazioni necessarie per creare un ruolo.

    ![Screenshot che mostra la schermata Authentication di Graph Explorer con un collegamento modify permissions.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selezionare le autorizzazioni seguenti dall'elenco e fare clic su **Autorizzazioni di modifica**, come illustrato nella figura seguente.

    ![Screenshot che mostra le autorizzazioni da selezionare: Directory.AccessAsUser.All, Directory.Read.All e Directory.ReadWrite.All.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Dopo che le autorizzazioni sono state concesse, ripetere l'accesso a Graph Explorer.

    d. Nella pagina di Graph Explorer selezionare **GET** dal primo elenco a discesa e **beta** dal secondo elenco a discesa. Immettere quindi `https://graph.microsoft.com/beta/servicePrincipals` nel campo accanto agli elenchi a discesa e fare clic su **Esegui query**.

    ![Screenshot che mostra Graph Explorer con le opzioni GET e beta selezionate e il pulsante Run Query evidenziato.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se si usano più directory, è possibile immettere `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` nel campo della query.

    e. Nella sezione **Response Preview** (Anteprima della risposta) estrarre la proprietà appRoles dall'entità servizio per un uso successivo.

    ![Screenshot che mostra plaintext nella sezione Response Preview in cui è possibile ottenere la proprietà appRoles.](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Per individuare la proprietà appRoles, immettere `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` nel campo della query. Si noti che `objectID` è l'ID dell'oggetto copiato dalla pagina **Proprietà** di Azure AD.

    f. Tornare a Graph Explorer, sostituire il metodo **GET** con **PATCH**, incollare il contenuto seguente nella sezione **Corpo della richiesta** e fare clic su **Esegui query**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` rappresenta i numeri ARN del provider di identità e del ruolo creati nella console RAM. È possibile aggiungere più ruoli in base alle esigenze. Azure AD invierà il valore di questi ruoli come valore attestazione nella risposta SAML. È tuttavia possibile aggiungere i nuovi ruoli solo dopo la parte `msiam_access` per l'operazione patch. Per semplificare il processo di creazione, è consigliabile usare un generatore di ID, ad esempio Generatore di GUID, per generare gli ID in tempo reale.

    g. Dopo che all'entità servizio è stata applicata la patch con il ruolo necessario, collegare il ruolo all'utente di Azure AD (u2) seguendo la procedura indicata nella sezione **Assegnare l'utente di test di Azure AD** dell'esercitazione.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Configurare l'accesso Single Sign-On di Alibaba Cloud Service (Role-based SSO)

Per configurare l'accesso Single Sign-On sul lato **Alibaba Cloud Service (Role-based SSO)** , è necessario inviare il file **XML di metadati della federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Creare l'utente di test di Alibaba Cloud Service (Role-based SSO)

In questa sezione viene creato un utente di nome Britta Simon in Alibaba Cloud Service (Role-based SSO). Collaborare con il [team di supporto di Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/) per aggiungere gli utenti nella piattaforma Alibaba Cloud Service (Role-based SSO). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

Dopo aver completato le configurazioni precedenti, testare Alibaba Cloud Service (Role-based SSO) seguendo questa procedura:

1. Nel portale di Azure passare alla pagina **Alibaba Cloud Service (Role-based SSO)** , selezionare **Single Sign-On** e fare clic su **Test**.

    ![Screenshot che mostra il riquadro Test single sign-on with Alibaba Cloud Service con un pulsante Test.](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Fare clic su **Accedi con l'account utente corrente**.

    ![Screenshot che mostra il collegamento Accedi con l'account utente corrente.](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Nella pagina di selezione dell'account selezionare u2.

    ![Screenshot che mostra la schermata per l'accesso SSO con l'utente u2 selezionato.](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Viene visualizzata la pagina seguente, che indica che l'accesso SSO in base al ruolo ha avuto esito positivo.

    ![Screenshot che mostra la pagina Prodotti e servizi in cui si indica che il test è riuscito. ](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Alibaba Cloud Service (Role-based SSO) con Azure AD](https://aad.portal.azure.com/)