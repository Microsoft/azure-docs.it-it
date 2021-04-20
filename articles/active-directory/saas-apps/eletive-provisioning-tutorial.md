---
title: 'Esercitazione: Configurare Eletive per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il de-provisioning degli account utente da Azure AD a Eletive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8a775422-e6d7-4cd5-b8d1-cc8a2db24c4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2021
ms.author: Zhchia
ms.openlocfilehash: 393629be5c30eb3d97dae1de1c06e2d4cf7af94d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741758"
---
# <a name="tutorial-configure-eletive-for-automatic-user-provisioning"></a>Esercitazione: Configurare Eletive per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Eletive e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurata, Azure AD esegue automaticamente il provisioning di utenti e gruppi in [Eletive](https://app.eletive.com/) usando il Azure AD Provisioning. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Eletive
> * Rimuovere gli utenti in Eletive quando non richiedono più l'accesso
> * Mantenere sincronizzati gli attributi utente tra Azure AD ed Eletive
> * Single Sign-On a Eletive (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account utente in Eletive con accesso amministrativo.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare i dati [di cui eseguire il mapping tra Azure AD ed Eletive.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-eletive-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Eletive per supportare il provisioning con Azure AD

1. Accedere a [Eletive.](https://app.eletive.com/) Passare a **Impostazioni**  ->  **Funzionalità**.

    ![Funzionalità](media/eletive-provisioning-tutorial/settings.png)

2.  Abilitare **le integrazioni** e **SCIM 2.0.**

    ![Integrazioni](media/eletive-provisioning-tutorial/scim.png)

3.  Passare a  ->  **Integrazioni impostazioni**.

4.  Fare clic su **Provisioning utenti**.

    ![Scheda](media/eletive-provisioning-tutorial/user.png)

5.  Fare clic su **Connetti**.

    ![Pulsante](media/eletive-provisioning-tutorial/connect.png)

6.  Copiare e salvare l'URL SCIM 2.0 e il token di bearer. Questi valori verranno immessi nel campo URL tenant e token segreto nella scheda Provisioning dell'applicazione Eletive nel portale di Azure.


## <a name="step-3-add-eletive-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Eletive dalla raccolta Azure AD applicazioni

Aggiungere Eletive dalla raccolta Azure AD di applicazioni per iniziare a gestire il provisioning in Eletive. Se Eletive è stata precedentemente impostata per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a Eletive, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile aggiornare [il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllare l'ambito assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-eletive"></a>Passaggio 5. Configurare il provisioning utenti automatico in Eletive 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-eletive-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Eletive in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Eletive.**

    ![Collegamento di Eletive nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore immettere** l'URL del tenant Eletive e il token segreto. Fare **clic su Test** connessione per Azure AD possibile connettersi a Eletive. Se la connessione non riesce, verificare che l'account Eletive abbia le autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Eletive (Sincronizza utenti con Eletive).**

9. Esaminare gli attributi utente sincronizzati da Azure AD a Eletive nella **sezione Mapping degli** attributi. Gli attributi selezionati come **proprietà corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Eletive per le operazioni di aggiornamento. Se si sceglie di modificare [l'attributo](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)di destinazione corrispondente, è necessario assicurarsi che l'API Eletive supporti il filtro degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |emails[type eq "work"].value|string|
   |externalId|string|
   |name.givenName|string|
   |name.familyName|string|
   |preferredLanguage|string|
   |userType|string|
   |urn:ietf:params:scim:schemas:extension:eletive:2.0:User:participateInSurvey|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il Azure AD di provisioning per Eletive, impostare **Stato del provisioning** su **Attivato** nella **sezione** Impostazioni.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Eletive scegliendo i valori desiderati in **Ambito** nella **sezione** Impostazioni.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="more-resources"></a>Altre risorse

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
