---
title: 'Esercitazione: configurare GroupTalk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735759"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Esercitazione: configurare GroupTalk per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in GroupTalk che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [GroupTalk](https://www.grouptalk.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in GroupTalk
> * Rimuovere gli utenti in GroupTalk quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e GroupTalk
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in GroupTalk

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account utente in GroupTalk con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare GroupTalk per supportare il provisioning con Azure AD

1. Rivolgersi al supporto di GroupTalk all'indirizzo support@grouptalk.com con il **nome** e l' **ID** del tenant che si vuole integrare con Azure ad.
2. Una volta ricevuta la notifica che la configurazione necessaria per l'integrazione di Azure AD è pronta, accedere a GroupTalk admin e passare alla visualizzazione dell'organizzazione. 
3. Dovrebbe essere visibile un elemento di configurazione di Azure AD Integration. Fare clic su di esso per verificare il nome e l' **ID** del **tenant** per ottenere un **JWT (token segreto)**. 
4. L'URL del tenant di GroupTalk è `https://api.grouptalk.com/api/scim/` . L' **URL del tenant** e il **token Secret** recuperato nel passaggio precedente verranno immessi nella scheda provisioning dell'applicazione GroupTalk nel portale di Azure. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere GroupTalk dalla raccolta di applicazioni Azure AD

Aggiungere **GroupTalk** dalla raccolta di applicazioni Azure ad per iniziare a gestire il provisioning in GroupTalk.

1. Fare clic sul pulsante **Iscriviti a GroupTalk** , che consente di indirizzare l'utente all'applicazione amministrativa GroupTalk.
2. Se si è già connessi a GroupTalk, disconnettersi per accedere alla schermata di accesso. Selezionare la scheda Azure AD, quindi fare clic sul pulsante **Accedi** .

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Accedere con l'account di amministratore di Active Directory e accettare i diritti di accesso dell'applicazione GroupTalk. Al termine di questa operazione verrà visualizzato un messaggio di errore che indica che l'utente non è presente. Questa operazione è prevista perché non è ancora stato effettuato il provisioning dell'utente in GroupTalk, ma è stato aggiunto GroupTalk al tenant.
4. Tornare alla portale di Azure e verificare che **GroupTalk** sia ora aggiunto alle applicazioni aziendali.

Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a GroupTalk, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Passaggio 5. Configurare il provisioning utenti automatico in GroupTalk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per GroupTalk in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **GroupTalk**.

    ![Collegamento di GroupTalk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant di GroupTalk e il token del segreto recuperati in precedenza dal passaggio 2. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a GroupTalk. Se la connessione non riesce, verificare che l'account GroupTalk disponga delle autorizzazioni di amministratore e riprovare. È sempre possibile ottenere un nuovo token segreto come descritto nel passaggio 2.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a GroupTalk**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a GroupTalk nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in GroupTalk per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API GroupTalk supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |phoneNumbers[type eq "mobile"].value|string|&check;|
   |emails[type eq "work"].value|string|&check;|
   |active|Boolean|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: utente: Label1|string|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: utente: Label2|string|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: utente: Label3|string|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: utente: Label4|string|
   |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: utente: Label5|string|


10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to GroupTalk**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a GroupTalk nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in GroupTalk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;|
      |Membri di|Informazioni di riferimento|
      |externalId|string|      
      |urn: IETF: params: SCIM: schemas: Extension: grouptalk: 2.0: Group: Description|string|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per GroupTalk, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in GroupTalk selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).
4. È possibile contattare il supporto di GroupTalk per i log di provisioning aggiuntivi configurati come report personalizzati all'interno di GroupTalk admin. Questi possono fornire suggerimenti aggiuntivi perché gli utenti e i gruppi non eseguono correttamente il provisioning.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)