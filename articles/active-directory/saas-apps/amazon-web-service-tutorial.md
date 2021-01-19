---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Amazon Web Services (AWS) | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Web Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 3db6fd2e6df96590d7d405157cbb33900c7d8531
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Amazon Web Services (AWS)

Questa esercitazione descrive come integrare Amazon Web Services (AWS) con Azure Active Directory (Azure AD). Integrando Amazon Web Services (AWS) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Amazon Web Services (AWS).
* Abilitare gli utenti per l'accesso automatico ad Amazon Web Services (AWS) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

> [!Note]
> Azure AD non supporta l'integrazione Single Sign-On con AWS SSO. Si tratta di un prodotto diverso da AWS. Anche se menzionato in AWS [qui](https://docs.aws.amazon.com/singlesignon/latest/userguide/azure-ad-idp.html), Azure AD consiglia ai clienti di usare l'integrazione di AWS IAM, in modo che sia possibile ottenere controlli di sicurezza migliori usando i criteri di accesso condizionale per i singoli account ed eseguire inoltre una migliore governance di tali applicazioni.

![Diagramma della relazione tra Azure AD e AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

È possibile configurare più identificatori per più istanze. Ad esempio:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Con questi valori Azure AD rimuove il valore di **#** e invia il valore corretto `https://signin.aws.amazon.com/saml` come URL destinatario nel token SAML.

È consigliabile usare questo approccio per i motivi seguenti:

- Ogni applicazione fornisce un certificato X509 unico. Ogni istanza di un'app AWS può quindi avere una diversa data di scadenza del certificato, che può essere gestita su una base dell'account AWS singolo. In questo caso il rollover generale dei certificati è più semplice.

- È possibile abilitare il provisioning utenti con un'app AWS in Azure AD, in modo che il servizio recuperi tutti i ruoli dall'account AWS. Non è necessario aggiungere o aggiornare manualmente i ruoli AWS nell'app.

- È possibile assegnare individualmente il proprietario dell'app per l'app. Questa persona può gestire l'applicazione direttamente in Azure AD.

> [!Note]
> Assicurarsi di usare solo un'applicazione della raccolta.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di AWS abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Amazon Web Services (AWS) supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta

Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con l'account Microsoft personale.
1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. Nel menu di panoramica di Azure Active Directory scegliere **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Amazon Web Services (AWS)** nella casella di ricerca.
1. Selezionare **Amazon Web Service (AWS)** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>Configurare e testare l'accesso SSO di Azure AD per Amazon Web Services (AWS)

Configurare e testare l'accesso SSO di Azure AD con Amazon Web Services (AWS) usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Amazon Web Services (AWS).

Per configurare e testare l'accesso Single Sign-On di Azure AD con AWS (Amazon Web Services), seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Amazon Web Services (AWS)](#configure-amazon-web-services-aws-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** : per avere una controparte di B.Simon in Amazon Web Services (AWS) collegata alla rappresentazione dell'utente in Azure AD.
    1. **[Come configurare il provisioning dei ruoli in Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** aggiornare **Identificatore (ID entità)** e **URL di risposta** con lo stesso valore predefinito: `https://signin.aws.amazon.com/saml`. Per salvare le modifiche alla configurazione, è necessario selezionare **Salva**.

1. Quando si configura più di un'istanza, specificare un valore per l'identificatore. Dalla seconda istanza in poi, usare il formato seguente, che include una firma **#** per specificare un valore SPN univoco.

    `https://signin.aws.amazon.com/saml#2`

1. L'applicazione AWS prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione AWS prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome  | Attributo di origine  | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Ruolo | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | "inserire un valore compreso tra 900 secondi (15 minuti) e 43200 secondi (12 ore)" |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS prevede ruoli per gli utenti assegnati all'applicazione. Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni su come configurare i ruoli in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** nella finestra di dialogo **Certificato di firma SAML** (Passaggio 3) selezionare **Aggiungi un certificato**.

    ![Creare un nuovo certificato SAML](common/add-saml-certificate.png)

1. Generare un nuovo certificato di firma SAML e quindi selezionare **Nuovo certificato**. Immettere un indirizzo di posta elettronica per le notifiche relative ai certificati.
   
    ![Nuovo certificato SAML](common/new-saml-certificate.png) 

1. Nella sezione **Certificato di firma SAML** trovare **XML metadati federazione** e quindi selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](./media/amazon-web-service-tutorial/certificate.png)

1. Nella sezione **Configura Amazon Web Services (AWS)** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. Nel menu di panoramica di Azure Active Directory scegliere **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Amazon Web Services (AWS).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-amazon-web-services-aws-sso"></a>Configurare l'accesso Single Sign-On di Amazon Web Services (AWS)

1. In un'altra finestra del browser accedere al sito aziendale di AWS come amministratore.

2. Selezionare **AWS Home** (Home page AWS).

    ![Screenshot del sito aziendale AWS con l'icona AWS Home (Home page AWS) evidenziata][11]

3. Selezionare **Identity and Access Management** (Gestione delle identità e degli accessi).

    ![Screenshot della pagina dei servizi AWS con l'opzione Identity and Access Management (Gestione delle identità e degli accessi) evidenziata][12]

4. Selezionare **Identity Providers** (Provider di identità)  > **Create Provider** (Crea provider).

    ![Screenshot della pagina IAM con Identity Providers (Provider di identità) e Create Provider (Crea provider) evidenziati][13]

5. Nella pagina **Configure Provider** (Configura provider) seguire questa procedura:

    ![Screenshot di Configure Provider (Configura provider)][14]

    a. In **Provider Type** (Tipo provider) selezionare **SAML**.

    b. In **Provider Name** (Nome provider) digitare un nome di provider, ad esempio: *WAAD*.

    c. Per caricare il **file di metadati** scaricato dal portale di Azure, selezionare **Choose File** (Scegli file).

    d. Selezionare **Next Step** (Passaggio successivo).

6. Nella pagina **Verify Provider Information** (Verifica informazioni provider) selezionare **Create** (Crea).

    ![Screenshot di Verify Provider Information (Verifica informazioni provider) con Create (Crea) evidenziato][15]

7. Selezionare **Roles** (Ruoli)  > **Create role** (Crea ruolo).

    ![Screenshot della pagina Roles (Ruoli)][16]

8. Nella pagina **Create role** (Crea ruolo) seguire questa procedura:  

    ![Screenshot della pagina Create role (Crea ruolo)][19]

    a. In **Select type of trusted entity** (Seleziona il tipo di entità attendibile) selezionare **SAML 2.0 federation** (Federazione SAML 2.0).

    b. In **Choose a SAML 2.0 Provider** (Scegli un provider SAML 2.0) selezionare il **provider SAML** creato in precedenza, ad esempio: *WAAD*.

    c. Selezionare **Allow programmatic and AWS Management Console access** (Consenti accesso a livello di codice e tramite AWS Management Console).
  
    d. Selezionare **Avanti: Permissions** (Avanti: Autorizzazioni).

9. Nella finestra di dialogo **Attach Permissions Policies** (Collega i criteri di autorizzazione) collegare i criteri appropriati in base all'organizzazione. Selezionare quindi **Next: Review** (Avanti: Rivedi).  

    ![Screenshot della finestra di dialogo Attach permissions policy (Collega i criteri di autorizzazione)][33]

10. Nella finestra di dialogo **Review** (Revisione) seguire questa procedura:

    ![Screenshot della finestra di dialogo Review (Revisione)][34]

    a. In **Role name** (Nome ruolo) immettere il nome del ruolo.

    b. In **Role description** (Descrizione ruolo) immettere la descrizione.

    c. Selezionare **Create role** (Crea ruolo).

    d. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping al provider di identità.

11. Usare le credenziali dell'account del servizio AWS per recuperare i ruoli dall'account AWS nel provisioning utenti di Azure AD. A tale scopo, aprire la pagina home della console AWS.

12. Selezionare **Services** (Servizi). In **Security, Identity & Compliance** (Sicurezza, identità e conformità) selezionare **IAM**.

    ![Screenshot della home page della console AWS con Services (Servizi) e IAM evidenziati](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Nella sezione IAM selezionare **Policies** (Criteri).

    ![Screenshot della sezione IAM con Policies (Criteri) evidenziato](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Creare un nuovo criterio selezionando **Create policy** (Crea criterio) per recuperare i ruoli dell'account AWS nel servizio di provisioning utenti di Azure AD.

    ![Screenshot della pagina Create role (Crea ruolo) con Create policy (Crea criterio) evidenziato](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Creare un criterio personalizzato per recuperare tutti i ruoli dagli account AWS.

    ![Screenshot della pagina Create policy (Crea criterio) con JSON evidenziato](./media/amazon-web-service-tutorial/policy1.png)

    a. In **Create policy** (Crea criterio) selezionare la scheda **JSON**.

    b. Nel documento del criterio aggiungere il codice JSON seguente:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selezionare **Review policy** (Rivedi criterio) per convalidare il criterio.

    ![Screenshot della pagina Create policy (Crea criterio)](./media/amazon-web-service-tutorial/policy5.png)

16. Definire il nuovo criterio.

    ![Screenshot della pagina Create policy (Crea criterio) con i campi Name (Nome) e Description (Descrizione) evidenziati](./media/amazon-web-service-tutorial/policy2.png)

    a. In **Name** (Nome) immettere **AzureAD_SSOUserRole_Policy**.

    b. In **Description** (Descrizione) immettere **This policy will allow to fetch the roles from AWS accounts** (Questo criterio consente di recuperare i ruoli dagli account AWS).

    c. Selezionare **Create policy** (Crea criterio).

17. Creare un nuovo account utente nel servizio AWS IAM.

    a. Nella console AWS IAM selezionare **Users** (Utenti).

    ![Screenshot della console AWS IAM con Users (Utenti) evidenziato](./media/amazon-web-service-tutorial/policy3.png)

    b. Per creare un nuovo utente, selezionare **Add user** (Aggiungi utente).

    ![Screenshot del pulsante Add user (Aggiungi utente)](./media/amazon-web-service-tutorial/policy4.png)

    c. Nella sezione **Add user** (Aggiungi utente):

    ![Screenshot della pagina Add user (Aggiungi utente) con User name (Nome utente) e Access type (Tipo di accesso) evidenziati](./media/amazon-web-service-tutorial/adduser1.png)

    * In Nome utente immettere **AzureADRoleManager**.

    * Per il tipo di accesso selezionare **Programmatic access** (Accesso a livello di programmazione). In questo modo l'utente può chiamare le API e recuperare i ruoli dall'account AWS.

    * Selezionare **Next Permissions** (Autorizzazioni successive).

18. Creare un nuovo criterio per questo utente.

    ![Screenshot che mostra la pagina Add user page in cui è possibile creare un criterio per l'utente.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selezionare **Attach existing policies directly** (Collega direttamente i criteri esistenti).

    b. Cercare il nuovo criterio creato nella sezione filtro, **AzureAD_SSOUserRole_Policy**.

    c. Selezionare il criterio e quindi selezionare **Next: Review** (Avanti: Rivedi).

19. Rivedere il criterio per l'utente collegato.

    ![Screenshot della pagina Add user (Aggiungi utente) con Create user (Crea utente) evidenziato](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controllare il nome utente, il tipo di accesso e il criterio associati all'utente.

    b. Selezionare **Create user** (Crea utente).

20. Scaricare le credenziali utente di un utente.

    ![Screenshot che mostra la pagina Add user con il pulsante Download .csv per ottenere le credenziali utente.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiare l'**ID chiave di accesso** e la **chiave di accesso segreta** dell'utente.

    b. Immettere queste credenziali nella sezione del provisioning utenti di Azure AD per recuperare i ruoli dalla console AWS.

    c. Selezionare **Chiudi**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Come configurare il provisioning dei ruoli in Amazon Web Services (AWS)

1. Nel portale di gestione di Azure AD nell'app AWS passare a **Provisioning**.

    ![Screenshot dell'app AWS con Provisioning evidenziato](./media/amazon-web-service-tutorial/provisioning.png)

2. Immettere la chiave di accesso e il segreto rispettivamente nei campi **Segreto client** e **Token segreto**.

    ![Screenshot della finestra di dialogo Credenziali amministratore](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Immettere la chiave di accesso utente AWS nel campo **Segreto client**.

    b. Immettere il segreto utente AWS nel campo **Token segreto**.

    c. Selezionare **Test connessione**.

    d. Salvare l'impostazione selezionando **Salva**.

3. Nella sezione **Impostazioni** per **Stato del provisioning** selezionare **Attiva**. Selezionare quindi **Salva**.

    ![Screenshot della sezione Impostazioni con Attiva evidenziato](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Il servizio di provisioning importa i ruoli solo da AWS ad Azure AD. Non effettua il provisioning di utenti e gruppi da Azure AD ad AWS.

> [!NOTE]
> Dopo aver salvato le credenziali di provisioning, è necessario attendere l'esecuzione del ciclo di sincronizzazione iniziale. Il completamento della sincronizzazione richiede in genere circa 40 minuti. È possibile visualizzare lo stato dell'operazione nella parte inferiore della pagina **Provisioning** in **Stato corrente**.

### <a name="create-amazon-web-services-aws-test-user"></a>Creare l'utente di test di Amazon Web Services (AWS)

Questa sezione descrive come creare un utente chiamato B.Simon in Amazon Web Service (AWS). Per Amazon Web Services (AWS) non è necessario creare un utente nel sistema per ottenere l'accesso SSO, pertanto non è necessario eseguire nessuna di queste operazioni.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Amazon Web Services (AWS), in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Amazon Web Services (AWS) e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Amazon Web Services (AWS) per cui si è configurato l'accesso SSO 

È anche possibile usare il pannello di accesso Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di Amazon Web Services (AWS) nel pannello di accesso, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di Amazon Web Services (AWS) per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="known-issues"></a>Problemi noti

 * Nella sottosezione **Mapping** della sezione **Provisioning** viene visualizzato il messaggio "Caricamento..." e i mapping degli attributi non vengono mai visualizzati. L'unico flusso di lavoro di provisioning supportato al momento è l'importazione di ruoli da AWS in Azure AD per la selezione durante l'assegnazione di un utente o di un gruppo. I mapping di attributi per questo flusso di lavoro sono predeterminati e non configurabili.

 * Nella sezione **Provisioning** è possibile immettere un solo set di credenziali per un unico tenant AWS alla volta. Tutti i ruoli importati vengono scritti nella proprietà `appRoles` dell'oggetto [`servicePrincipal` di Azure AD](/graph/api/resources/serviceprincipal?view=graph-rest-beta) per il tenant AWS.

   È possibile aggiungere ad Azure più tenant AWS, rappresentati da oggetti `servicePrincipals`, dalla raccolta per il provisioning. È però presente un problema noto che impedisce di scrivere automaticamente tutti i ruoli importati da più oggetti `servicePrincipals` AWS usati per il provisioning nell'unico oggetto `servicePrincipal` usato per l'accesso SSO.

   Per ovviare al problema, è possibile usare l'[API Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta) per estrarre tutti gli oggetti `appRoles` importati in ogni oggetto `servicePrincipal` AWS in cui è configurato il provisioning. È quindi possibile aggiungere queste stringhe di ruolo all'oggetto `servicePrincipal` AWS in cui è configurato l'accesso SSO.

* Per essere idonei all'importazione da AWS ad Azure AD, i ruoli devono rispettare i requisiti seguenti:

  * In AWS deve essere definito un solo provider SAML per i ruoli
  * La lunghezza combinata dell'ARN (Amazon Resource Name) per il ruolo e l'ARN per il provider SAML associato non deve superare 240 caratteri.

## <a name="change-log"></a>Registro delle modifiche

* 01/12/2020: il limite di lunghezza per il ruolo è stato aumentato da 119 a 239 caratteri. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Amazon Web Services (AWS), è possibile applicare il controllo sessione che protegge in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
