---
title: 'Esercitazione: Integrazione di Azure Active Directory con Ceridian Dayforce HCM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: bd7213f31eb737bedd64d627141c2576fdadb79a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456380"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Esercitazione: Integrazione di Azure Active Directory con Ceridian Dayforce HCM

Questa esercitazione descrive come integrare Ceridian Dayforce HCM con Azure Active Directory (Azure AD).
L'integrazione di Ceridian Dayforce HCM con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Ceridian Dayforce HCM.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Ceridian Dayforce HCM con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con Ceridian Dayforce HCM, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Ceridian Dayforce HCM abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Ceridian Dayforce HCM supporta l'accesso SSO avviato da **SP**

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Aggiunta di Ceridian Dayforce HCM dalla raccolta

Per configurare l'integrazione di Ceridian Dayforce HCM in Azure AD, è necessario aggiungere Ceridian Dayforce HCM dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Ceridian Dayforce HCM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **Ceridian Dayforce HCM** , selezionare **Ceridian Dayforce HCM** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Ceridian Dayforce HCM nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Ceridian Dayforce HCM usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Ceridian Dayforce HCM.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Ceridian Dayforce HCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** : per avere una controparte di Britta Simon in Ceridian Dayforce HCM collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Ceridian Dayforce HCM, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Ceridian Dayforce HCM** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Nella casella di testo **URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Ceridian Dayforce HCM.

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Per il test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Per il test | `https://fs-test.dayforcehcm.com/sp` |

    c. Nella casella di testo **URL di risposta** digitare l'URL utilizzato da Azure AD per inserire la risposta.

    | Environment | URL |
    | :-- | :-- |
    | Per la produzione | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Per il test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Ceridian Dayforce HCM](https://www.ceridian.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Ceridian Dayforce HCM prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Screenshot che mostra il riquadro Attributi utente con l'icona di modifica selezionata.](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | Nome | Attributo di origine|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Screenshot che mostra il riquadro Attestazioni utente con l'opzione per aggiungere una nuova attestazione.](common/new-save-attribute.png)

    ![Screenshot che mostra la finestra di dialogo Gestisci attestazioni utente in cui è possibile immettere i valori descritti.](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare l'attributo utente da usare per l'implementazione. Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML** , nella sezione **Certificato di firma SAML** , fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Ceridian Dayforce HCM** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Configurare l'accesso Single Sign-On per Ceridian Dayforce HCM

Per configurare l'accesso Single Sign-On sul lato **Ceridian Dayforce HCM** , è necessario inviare il file **XML dei metadati** scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di Ceridian Dayforce HCM](https://www.ceridian.com/support). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Ceridian Dayforce HCM.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **Ceridian Dayforce HCM**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Ceridian Dayforce HCM**.

    ![Il collegamento Ceridian Dayforce HCM nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Creare l'utente di test di Ceridian Dayforce HCM

In questa sezione viene creato un utente di nome Britta Simon in Ceridian Dayforce HCM. Collaborare con il [team di supporto di Ceridian Dayforce HCM](https://www.ceridian.com/support) per aggiungere gli utenti alla piattaforma Ceridian Dayforce HCM. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Ceridian Dayforce HCM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Ceridian Dayforce HCM per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)