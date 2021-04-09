---
title: 'Esercitazione: Configurare PureCloud by Genesys per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a PureCloud by Genesys.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181663"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Esercitazione: Configurare PureCloud by Genesys per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in PureCloud by Genesys che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi per [PureCloud by Genesys](https://www.genesys.com) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in PureCloud by Genesys
> * Rimuovere utenti da PureCloud by Genesys quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e PureCloud by Genesys
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in PureCloud by Genesys
> * [Accesso Single Sign-On](./purecloud-by-genesys-tutorial.md) a PureCloud by Genesys (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un'[organizzazione](https://help.mypurecloud.com/?p=81984) PureCloud.
* Un utente con [autorizzazioni](https://help.mypurecloud.com/?p=24360) per creare un client OAuth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati [mappare tra Azure AD e PureCloud by Genesys](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare PureCloud by Genesys per supportare il provisioning con Azure AD

1. Creare un [client OAuth](https://help.mypurecloud.com/?p=188023) configurato nell'organizzazione PureCloud.
2. Generare un token [con il client OAuth](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Per effettuare automaticamente il provisioning dell'appartenenza al gruppo in PureCloud, è necessario [creare gruppi](https://help.mypurecloud.com/?p=52397) in PureCloud con un nome identico al gruppo in Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere PureCloud by Genesys dalla raccolta di applicazioni di Azure AD

Aggiungere PureCloud by Genesys dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in PureCloud by Genesys. Se PureCloud by Genesys è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a PureCloud by Genesys, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Passaggio 5. Configurare il provisioning utenti automatico in PureCloud by Genesys 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Per configurare PureCloud by Genesys per il provisioning utenti automatico in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **PureCloud by Genesys**.

    ![Collegamento di PureCloud by Genesys nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL dell'API PureCloud by Genesys API e il token OAuth rispettivamente nei campi **URL tenant** e **Token segreto**. L'URL dell'API verrà strutturato come `{{API Url}}/api/v2/scim/v2`, usando il valore per l'area di PureCloud derivato da [PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html). Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a PureCloud by Genesys. Se la connessione non riesce, verificare che l'account PureCloud by Genesys abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la finestra di dialogo Credenziali amministratore, in cui è possibile immettere l'URL del tenant e il token segreto.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to PureCloud by Genesys** (Sincronizza utenti di Azure Active Directory con PureCloud by Genesys).

9. Esaminare gli attributi utente sincronizzati tra Azure AD e PureCloud by Genesys nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in PureCloud by Genesys per le operazioni di aggiornamento. Se si sceglie di cambiare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API PureCloud by Genesys supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

     |Attributo|Type|
     |---|---|
     |userName|string|
     |active|Boolean|
     |displayName|string|
     |emails[type eq "work"].value|string|
     |title|string|
     |phoneNumbers[type eq "mobile"].value|string|
     |phoneNumbers[type eq "work"].value|string|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
     

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to PureCloud by Genesys** (Sincronizza gruppi di Azure Active Directory con PureCloud by Genesys).

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e PureCloud by Genesys nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in PureCloud by Genesys per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche. PureCloud by Genesys non supporta la creazione o l'eliminazione di gruppi, ma ne supporta solo l'aggiornamento.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per PureCloud by Genesys, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in PureCloud by Genesys selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Registro delle modifiche

10/09 - Aggiunto il supporto per l'attributo aziendale "employeeNumber".

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)