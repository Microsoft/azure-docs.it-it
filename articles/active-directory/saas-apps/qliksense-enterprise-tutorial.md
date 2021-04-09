---
title: 'Esercitazione: Integrazione di Azure Active Directory con Qlik Sense Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Qlik Sense Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2d046f5f039555e58d9ce4c028e750ce083fd5f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733690"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Esercitazione: Integrare Qlik Sense Enterprise con Azure Active Directory

Questa esercitazione descrive come integrare Qlik Sense Enterprise con Azure Active Directory (Azure AD). Integrando Qlik Sense Enterprise con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Qlik Sense Enterprise.
* Abilitare gli utenti per l'accesso automatico ad Qlik Sense Enterprise con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Qlik Sense Enterprise abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 
* Qlik Sense Enterprise supporta l'accesso SSO avviato da **SP**.
* Qlik Sense Enterprise supporta il **provisioning JIT**

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Aggiunta di Qlik Sense Enterprise dalla raccolta

Per configurare l'integrazione di Qlik Sense Enterprise in Azure AD, è necessario aggiungere Qlik Sense Enterprise dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Qlik Sense Enterprise** nella casella di ricerca.
1. Selezionare **Qlik Sense Enterprise** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-qlik-sense-enterprise"></a>Configurare e testare l'accesso SSO di Azure AD per Qlik Sense Enterprise

Configurare e testare l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise usando un utente di test di nome **Britta Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Qlik Sense Enterprise.

Per configurare e testare l'accesso SSO di Azure AD con Qlik Sense Enterprise, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Qlik Sense Enterprise](#configure-qlik-sense-enterprise-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** : per avere una controparte di Britta Simon in Qlik Sense Enterprise collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Qlik Sense Enterprise** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Identificatore |
    |-------------|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com` |
    | `https://<Fully Qualified Domain Name>.qliksense.com` |
    |
   

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con identificatore, URL di accesso e URL di risposta effettivi, illustrati più avanti in questa esercitazione, oppure contattare il [team di supporto clienti di Qlik Sense Enterprise](https://www.qlik.com/us/services/support) per ottenere questi valori. La porta predefinita per gli URL è la 443, ma è possibile personalizzarla in base alle esigenze dell'organizzazione.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** nella sezione **Certificato di firma SAML** trovare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `Britta Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `BrittaSimon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Qlik Sense Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Qlik Sense Enterprise**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-qlik-sense-enterprise-sso"></a>Configurare l'accesso SSO di Qlik Sense Enterprise

1. Preparare il file XML di metadati della federazione per il caricamento sul server Qlik Sense.

    > [!NOTE]
    > Prima di caricare i metadati IdP sul server Qlik Sense, è necessario modificare il file rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense.

    ![Screenshot che mostra una finestra di Visual Studio Code con il file FederationMetaData.xml.][qs24]

    a. Aprire il file FederationMetaData.xml scaricato dal portale di Azure in un editor di testo.

    b. Cercare il valore **RoleDescriptor**.  Ci sono quattro voci (due coppie di tag di elementi di apertura e di chiusura).

    c. Eliminare dal file i tag RoleDescriptor e tutte le informazioni in essi racchiuse.

    d. Salvare il file e tenerlo a disposizione per usarlo più avanti in questo documento.

2. Passare a Qlik Sense Qlik Management Console (QMC) come utente autorizzato a creare configurazioni di proxy virtuali.

3. In QMC fare clic sulla voce di menu **Virtual Proxies** (Proxy virtuali).

    ![Screenshot che mostra la voce di menu Virtual proxies del menu CONFIGURE SYSTEM.][qs6]

4. Nella parte inferiore dello schermo fare clic sul pulsante **Create new** (Crea nuovo).

    ![Screenshot che mostra l'opzione Create new.][qs7]

5. Viene visualizzata la schermata per la modifica del proxy virtuale.  Sul lato destro della schermata è disponibile un menu per la visualizzazione delle opzioni di configurazione.

    ![Screenshot che mostra la voce di menu Identification selezionata nel menu Properties.][qs9]

6. Dopo aver selezionato l'opzione di menu Identification (Identificazione), immettere le informazioni di identificazione per la configurazione del proxy virtuale di Azure.

    ![Screenshot che mostra la sezione Identification di Edit virtual proxy in cui è possibile immettere i valori descritti.][qs8]  

    a. Il campo **Description** (Descrizione) contiene un nome descrittivo per la configurazione del proxy virtuale.  Immettere un valore per la descrizione.

    b. Il campo **Prefix** (Prefisso) identifica l'endpoint proxy virtuale per la connessione a Qlik Sense con l'accesso Single Sign-On di Azure AD.  Immettere un nome di prefisso univoco per il proxy virtuale.

    c. **Session inactivity timeout (minutes)** (Timeout di inattività sessione - minuti) indica il timeout per le connessioni tramite il proxy virtuale.

    d. **Session cookie header name** (Nome intestazione cookie sessione) è il nome del cookie in cui è archiviato l'identificatore della sessione di Qlik Sense ricevuto da un utente dopo il completamento dell'autenticazione.  Il nome deve essere univoco.

7. Fare clic sull'opzione di menu Authentication (Autenticazione) per renderla visibile.  Verrà visualizzata la schermata Authentication (Autenticazione).

    ![Screenshot che mostra la sezione Authentication di Edit virtual proxy in cui è possibile immettere i valori descritti.][qs10]

    a. L'elenco a discesa **Anonymous access mode** (Modalità accesso anonimo) determina se gli utenti anonimi potranno accedere a Qlik Sense tramite il proxy virtuale.  L'opzione predefinita è No anonymous user (Nessun utente anonimo).

    b. L'elenco a discesa **Authentication method** (Metodo di autenticazione) determina lo schema di autenticazione che verrà usato dal proxy virtuale.  Selezionare SAML nell'elenco a discesa.  Verranno di conseguenza visualizzate altre opzioni.

    c. Nel campo **SAML host URI** (URI host SAML) specificare il nome host che verrà immesso dagli utenti per accedere a Qlik Sense tramite questo proxy virtuale SAML.  Il nome host è l'URI del server Qlik Sense.

    d. In **SAML entity ID**(ID entità SAML) immettere lo stesso valore inserito nel campo SAML host URI (URI host SAML).

    e. **SAML IdP metadata** (Metadati IdP SAML) è il campo modificato nella precedente sezione relativa alla **modifica dei metadati della federazione della configurazione di Azure AD**.  **Prima di caricare i metadati IdP, è necessario modificare il file** rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense.  **Se il file non è ancora stato modificato, vedere le istruzioni riportate sopra.**  Se il file è stato modificato, fare clic sul pulsante Browse (Sfoglia) e selezionare il file di metadati modificato per caricarlo nella configurazione del proxy virtuale.

    f. Immettere il nome dell'attributo o il riferimento dello schema per l'attributo SAML che rappresenta l'**ID utente** inviato da Azure AD al server Qlik Sense.  Le informazioni relative al riferimento dello schema sono disponibili nelle schermate dell'app in Azure in seguito alla configurazione.  Per usare il nome di attributo, immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Immettere il valore della **directory utente** che verrà associata agli utenti quando eseguiranno l'autenticazione al server Qlik Sense tramite Azure AD.  I valori hardcoded devono essere racchiusi tra **parentesi quadre []** .  Per usare un attributo inviato nell'asserzione SAML di Azure AD, immettere il nome dell'attributo in questa casella di testo **senza** parentesi quadre.

    h. **SAML signing algorithm** (Algoritmo di firma SAML) imposta la firma del certificato del provider di servizi (in questo caso, il server Qlik Sense) per la configurazione del proxy virtuale.  Se il server Qlik Sense usa un certificato attendibile generato con Microsoft Enhanced RSA and AES Cryptographic Provider, modificare l'algoritmo di firma SAML impostandolo su **SHA-256**.

    i. La sezione SAML attribute mapping (Mapping attributi SAML) consente di inviare a Qlik Sense attributi aggiuntivi, come i gruppi, da usare nelle regole di sicurezza.

8. Fare clic sull'opzione di menu **LOAD BALANCING** (BILANCIAMENTO DEL CARICO) per renderla visibile.  Verrà visualizzata la schermata Load balancing (Bilanciamento del carico).

    ![Screenshot che mostra la sezione LOAD BALANCING della schermata Virtual proxy edit in cui è possibile selezionare Add new server node.][qs11]

9. Fare clic sul pulsante **Add new server node** (Aggiungi nuovo nodo server), selezionare uno o più nodi motore a cui Qlik Sense invierà le sessioni ai fini del bilanciamento del carico e fare clic su **Add** (Aggiungi).

    ![Screenshot che mostra il pulsante Add della finestra di dialogo Add server nodes to load balance on che consente di aggiungere server.][qs12]

10. Fare clic sull'opzione di menu Advanced (Avanzate) per renderla visibile. Verrà visualizzata la schermata Advanced (Avanzate).

    ![Screenshot che mostra la sezione Advanced della schermata Edit virtual proxy.][qs13]

    L'elenco degli elementi consentiti host identifica i nomi host accettati per la connessione al server di Qlik Sense.  **Immettere il nome host che gli utenti specificheranno per la connessione al server Qlik Sense.** Il nome host è lo stesso valore specificato in SAML host URI (URI host SAML) senza https://.

11. Fare clic sul pulsante **Applica**.

    ![Screenshot che mostra il pulsante Apply.][qs14]

12. Fare clic su OK per accettare il messaggio di avviso che indica che i proxy collegati al proxy virtuale verranno riavviati.

    ![Screenshot che mostra il messaggio di conferma dell'applicazione delle modifiche al proxy virtuale.][qs15]

13. Sul lato destro della schermata verrà visualizzato il menu Associated items (Elementi associati).  Fare clic sull'opzione di menu **Proxies** (Proxy).

    ![Screenshot che mostra l'opzione Proxies selezionata in Associated items.][qs16]

14. Verrà visualizza la schermata relativa ai proxy.  Fare clic sul pulsante **Link** (Collega) in basso per collegare un proxy al proxy virtuale.

    ![Screenshot che mostra il pulsante Link.][qs17]

15. Selezionare il nodo proxy che supporterà la connessione proxy virtuale e fare clic sul pulsante **Link** (Collega).  Al termine del collegamento, il proxy verrà incluso nell'elenco dei proxy associati.

    ![Screenshot che mostra la schermata Select proxy services.][qs18]
  
    ![Screenshot che mostra il numero di proxy associati nella finestra di dialogo Virtual proxy associated items.][qs19]

16. Dopo circa 5-10 secondi verrà visualizzato il messaggio per l'aggiornamento di QMC.  Fare clic sul pulsante **Refresh QMC** (Aggiorna QMC).

    ![Screenshot che mostra il messaggio Your session has ended che avvisa che la sessione è terminata.][qs20]

17. Dopo l'aggiornamento di QMC, fare clic sulla voce di menu **Virtual proxies** (Proxy virtuali). La nuova voce del proxy virtuale SAML sarà elencata nella tabella visualizzata.  Fare clic sulla voce del proxy virtuale.

    ![Screenshot che mostra la sezione Virtual proxies con una singola voce.][qs51]

18. Nella parte inferiore della schermata verrà attivato il pulsante Download SP metadata (Scarica metadati SP).  Fare clic sul pulsante **Download SP metadata** (Scarica metadati SP) per salvare i metadati in un file.

    ![Screenshot che mostra il pulsante Download SP metadata.][qs52]

19. Aprire il file di metadati SP.  Osservare le voci **entityID** e **AssertionConsumerService**.  Questi valori sono uguali ai valori **Identificatore**, **URL di accesso** e **URL di risposta** nella configurazione dell'applicazione di Azure AD. Incollare questi valori nella sezione **URL e dominio Qlik Sense Enterprise** nella configurazione dell'applicazione di Azure AD se i valori non corrispondono. Sarà quindi necessario sostituire i valori nella configurazione guidata dell'app Azure AD.

    ![Screenshot che mostra un editor di testo normale con un EntityDescriptor e le voci entityID e AssertionConsumerService evidenziate.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Creare l'utente di test di Qlik Sense Enterprise

Qlik Sense Enterprise supporta il **provisioning JIT**. Gli utenti vengono aggiunti automaticamente al repository 'USERS' di Qlik Sense Enterprise quando usano la funzionalità SSO. Inoltre, i client possono usare QMC e creare un UDC (User Directory Connector) per la precompilazione degli utenti in Qlik Sense Enterprise dal proprio LDAP predefinito, tra cui Active Directory.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Qlik Sense Enterprise, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Qlik Sense Enterprise e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di Qlik Sense Enterprise in App personali, si verrà reindirizzati all'URL di accesso di Qlik Sense Enterprise. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Qlik Sense Enterprise, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png