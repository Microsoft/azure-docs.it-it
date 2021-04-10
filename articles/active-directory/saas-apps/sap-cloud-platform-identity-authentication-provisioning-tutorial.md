---
title: 'Esercitazione: Configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in SAP Cloud Platform Identity Authentication.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96342738"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Esercitazione: Configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in SAP Cloud Platform Identity Authentication e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in SAP Cloud Platform Identity Authentication.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/pricing.html)
* Un account utente in SAP Cloud Platform Identity Authentication con autorizzazioni di amministratore.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Assegnazione di utenti a SAP Cloud Platform Identity Authentication Service

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a SAP Cloud Platform Identity Authentication. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a SAP Cloud Platform Identity Authentication seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Suggerimenti importanti per l'assegnazione di utenti a SAP Cloud Platform Identity Authentication

* È consigliabile assegnare un singolo utente di Azure AD a SAP Cloud Platform Identity Authentication per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SAP Cloud Platform Identity Authentication, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configurare SAP Cloud Platform Identity Authentication per il provisioning

1. Accedere alla [console di amministrazione di SAP Cloud Platform Identity Authentication](https://sapmsftintegration.accounts.ondemand.com/admin). Passare a **Users & Authorizations > Administrators** (Utenti e autorizzazioni > Amministratori).

    ![Console di amministrazione di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Selezionare il pulsante **+Add** (Aggiungi) nel pannello sinistro per aggiungere un nuovo amministratore all'elenco. Scegliere **Add System** (Aggiungi sistema) e immettere il nome del sistema.   

> [!NOTE]
> L'utente amministratore in SAP Cloud Platform Identity Authentication deve essere di tipo **System**. Se si crea un utente amministratore normale, potrebbero verificarsi errori di *utente non autorizzato* durante il provisioning.   

3.  In Configure Authorizations (Configura autorizzazioni) attivare gli interruttori relativi a **Manage Users** (Gestisci utenti) e **Manage Groups** (Gestisci gruppi).

    ![Aggiunta di SCIM in SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Si riceverà un messaggio di posta elettronica per l'attivazione dell'account e l'impostazione di una password per **SAP Cloud Platform Identity Authentication**.

4.  Copiare i valori di **User ID** (ID utente) e **Password**. Questi valori dovranno essere immessi rispettivamente nei campi Nome utente amministratore e Password amministratore nella scheda Provisioning dell'applicazione SAP Cloud Platform Identity Authentication nel portale di Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiungere SAP Cloud Platform Identity Authentication dalla raccolta

Prima di configurare SAP Cloud Platform Identity Authentication per il provisioning utenti automatico con Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta di applicazioni di Azure AD, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SAP Cloud Platform Identity Authentication**, selezionare **SAP Cloud Platform Identity Authentication** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurazione del provisioning utenti automatico per SAP Cloud Platform Identity Authentication 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SAP Cloud Platform Identity Authentication in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per SAP Cloud Platform Identity Authentication, seguendo le istruzioni fornite nell'[esercitazione sulll'accesso Single Sign-On per SAP Cloud Platform Identity Authentication](./sap-hana-cloud-platform-identity-authentication-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SAP Cloud Platform Identity Authentication in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://<tenantID>.accounts.ondemand.com/service/scim ` in **URL tenant**. Immettere i valori di **User ID** e **Password** recuperati in precedenza rispettivamente nei campi **Nome utente amministratore** e **Password amministratore**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a SAP Cloud Platform Identity Authentication. Se la connessione non riesce, verificare che l'account SAP Cloud Platform Identity Authentication abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to SAP Cloud Platform Identity Authentication** (Sincronizza utenti di Azure Active Directory in SAP Cloud Platform Identity Authentication).

    ![Mapping utente di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SAP Cloud Platform Identity Authentication nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in SAP Cloud Platform Identity Authentication per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per SAP Cloud Platform Identity Authentication, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in SAP Cloud Platform Identity Authentication selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in SAP Cloud Platform Identity Authentication.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* L'endpoint SCIM di SAP Cloud Platform Identity Authentication richiede che determinati attributi siano di un formato specifico. Altre informazioni su questi attributi e sul loro formato specifico sono disponibili [qui](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)