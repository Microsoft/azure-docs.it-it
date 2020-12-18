---
title: 'Esercitazione: Configurare Clarizen One per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Clarizen One.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 3754a56d1520118937a9d4aad406ae657b4dd23b
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673665"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Esercitazione: Configurare Clarizen One per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Clarizen One che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [Clarizen One](https://www.clarizen.com/) tramite il servizio di provisioning di Azure AD. Per informazioni su questo servizio e su come funziona, insieme alle domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning degli utenti in applicazioni SaaS (software-as-a-service) con Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creare utenti in Clarizen One.
> * Rimuovere utenti da Clarizen One quando non richiedono più l'accesso.
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Clarizen One.
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in Clarizen One.
> * [Accesso Single Sign-On (SSO)](./clarizen-tutorial.md) a Clarizen One (scelta consigliata).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore di applicazioni, amministratore di applicazioni cloud, proprietario di applicazioni o amministratore globale.
* Un account utente in Clarizen One con le [autorizzazioni](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support) **Integration User** (Utente integrazione) e **Lite admin** (Amministratore lite).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determinare quali dati [mappare tra Azure AD e Clarizen One](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Clarizen One per supportare il provisioning con Azure AD

1. Selezionare uno dei quattro URL tenant seguenti in base all'ambiente e al data center di Clarizen One:
      * Data center di produzione USA: https://servicesapp2.clarizen.com/scim/v2
      * Data center di produzione EU: https://serviceseu1.clarizen.com/scim/v2
      * Data center sandbox USA: https://servicesapp.clarizentb.com/scim/v2
      * Data center sandbox EU: https://serviceseu.clarizentb.com/scim/v2

1. Creare una [chiave API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Questo valore verrà immesso nel campo **Token segreto** nella scheda **Provisioning** dell'applicazione Clarizen One nel portale di Azure.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Clarizen One dalla raccolta di applicazioni di Azure AD

Aggiungere Clarizen One dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Clarizen One. Se Clarizen One è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. Quando si testa inizialmente l'integrazione, creare un'app separata. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, vedere [Aggiungere un'applicazione al tenant di Azure AD](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Con il servizio di provisioning di Azure AD, è possibile definire l'ambito per gli utenti di cui verrà effettuato il provisioning in base all'assegnazione all'applicazione oppure in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning per l'app in base all'assegnazione, seguire la procedura descritta in [Gestire l'assegnazione di utenti per un'app in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, usare un filtro per la definizione dell'ambito come descritto in [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando si assegnano utenti e gruppi a Clarizen One, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli.
* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di procedere alla distribuzione generale. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile mantenere il controllo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di definizione dell'ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Passaggio 5. Configurare il provisioning utenti automatico in Clarizen One

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Clarizen One in base alle assegnazioni di utenti o gruppi in Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Configurare il provisioning utenti automatico per Clarizen One in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

      ![Screenshot che mostra il riquadro Applicazioni aziendali.](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Clarizen One**.

      ![Screenshot che mostra il collegamento a Clarizen One nell'elenco delle applicazioni.](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

      ![Screenshot che mostra la scheda Provisioning.](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

      ![Screenshot che mostra l'opzione Automatico nella scheda Provisioning.](common/provisioning-automatic.png)

1. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** e il **token segreto** di Clarizen One. Selezionare **Test connessione** per verificare che Azure AD possa connettersi a Clarizen One. Se la connessione non riesce, verificare che l'account Clarizen One abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la casella Token segreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot che mostra la casella Indirizzo di posta elettronica per le notifiche.](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Clarizen One** (Sincronizza utenti di Azure Active Directory con Clarizen One).

1. Esaminare gli attributi utente sincronizzati da Azure AD a Clarizen One nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Clarizen One per le operazioni di aggiornamento. Se si cambia l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Clarizen One supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |displayName|string|
   |active|Boolean|
   |title|string|
   |emails[type eq "work"].value|string|
   |emails[type eq "home"].value|string|
   |emails[type eq "other"].value|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |name.honorificPrefix|string|
   |name.honorificSuffix|string|
   |addresses[type eq "other"].formatted|string|
   |addresses[type eq "work"].formatted|string|
   |addresses[type eq "work"].country|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].streetAddress|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |phoneNumbers[type eq "home"].value|string|
   |phoneNumbers[type eq "other"].value|string|
   |phoneNumbers[type eq "pager"].value|string|
   |externalId|string|
   |nickName|string|
   |locale|string|
   |roles[primary eq"True".type]|string|
   |roles[primary eq"True".value]|string|
   |timezone|string|
   |userType|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|

1. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Clarizen One** (Sincronizza gruppi di Azure Active Directory con Clarizen One).

1. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Clarizen One nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Clarizen One per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|
      |---|---|
      |displayName|string|
      |externalId|string|
      |Membri di|Informazioni di riferimento|

1. Per configurare i filtri di definizione dell'ambito, vedere le istruzioni riportate in questa [esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning di Azure AD per Clarizen One, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

      ![Screenshot che mostra l'opzione Stato del provisioning impostata su Sì.](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui effettuare il provisioning in Clarizen One selezionando i valori appropriati in **Ambito** nella sezione **Impostazioni**.

      ![Screenshot che mostra l'ambito del provisioning.](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

      ![Screenshot che mostra il salvataggio della configurazione del provisioning.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione.

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
1. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
1. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [Provisioning delle applicazioni in stato di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

Quando si assegna un utente all'app Clarizen One della raccolta, selezionare solo il ruolo **Utente**. I ruoli seguenti non sono validi:

* Administrator (admin)
* Email Reporting user
* External user
* Financial user
* Social user
* Superuser
* Time & Expense user

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)