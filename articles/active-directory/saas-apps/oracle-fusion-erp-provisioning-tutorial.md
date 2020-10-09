---
title: 'Esercitazione: configurare Oracle Fusion ERP per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 45a1ae281a8a1acb1cfd12efe500f5a623033ef3
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850041"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Esercitazione: configurare Oracle Fusion ERP per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Oracle Fusion ERP e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Oracle Fusion ERP.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* Un [tenant Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Un account utente in Oracle Fusion ERP con autorizzazioni di amministratore.

## <a name="assign-users-to-oracle-fusion-erp"></a>Assegnare gli utenti a Oracle Fusion ERP 
Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Oracle Fusion ERP. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Oracle Fusion ERP seguendo le istruzioni riportate qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Suggerimenti importanti per l'assegnazione di utenti a Oracle Fusion ERP 

 * È consigliabile assegnare un singolo utente Azure AD a Oracle Fusion ERP per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Oracle Fusion ERP, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configurare Oracle Fusion ERP per il provisioning

Prima di configurare Oracle Fusion ERP per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM su Oracle Fusion ERP.

1. Accedere alla console di [amministrazione di Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Fare clic sullo strumento di spostamento nell'angolo in alto a sinistra. In **strumenti**selezionare **console di sicurezza**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Screenshot della pagina strumento di navigazione nella console di amministrazione di Oracle Fusion E R P. Gli strumenti e la console di sicurezza sono evidenziati." border="false":::

3. Passare a **Utenti**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Screenshot della pagina strumento di navigazione nella console di amministrazione di Oracle Fusion E R P. Gli strumenti e la console di sicurezza sono evidenziati." border="false":::

4. Salvare il nome utente e la password per l'account utente amministratore che si userà per accedere alla console di amministrazione di Oracle Fusion ERP. Questi valori devono essere immessi nei campi **nome utente** e **password** amministratore nella scheda provisioning dell'applicazione Oracle Fusion ERP nell'portale di Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Aggiungere Oracle Fusion ERP dalla raccolta

Per configurare Oracle Fusion ERP per il provisioning utenti automatico con Azure AD, è necessario aggiungere Oracle Fusion ERP dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Oracle Fusion ERP dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Oracle Fusion ERP**e selezionare **Oracle Fusion ERP** nel pannello dei risultati.

    ![Oracle Fusion ERP nell'elenco dei risultati](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configurare il provisioning utenti automatico per Oracle Fusion ERP 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Oracle Fusion ERP in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Oracle Fusion ERP seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Oracle Fusion ERP, vedere [API REST per le funzionalità comuni in Oracle Applications cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Per configurare il provisioning utenti automatico per il fusibile in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Oracle Fusion ERP**.

    ![Collegamento di Oracle Fusion ERP nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` in **URL tenant**. Immettere il nome utente e la password dell'amministratore recuperati in precedenza nei campi nome utente e **password** dell' **amministratore** . Fare clic su **Test connessione** tra Azure ad e Oracle Fusion ERP. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Screenshot della pagina strumento di navigazione nella console di amministrazione di Oracle Fusion E R P. Gli strumenti e la console di sicurezza sono evidenziati." border="false":::

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Screenshot della pagina strumento di navigazione nella console di amministrazione di Oracle Fusion E R P. Gli strumenti e la console di sicurezza sono evidenziati." border="false":::

9. Esaminare gli attributi utente sincronizzati da Azure AD a Oracle Fusion ERP nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Oracle Fusion ERP per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Screenshot della pagina strumento di navigazione nella console di amministrazione di Oracle Fusion E R P. Gli strumenti e la console di sicurezza sono evidenziati." border="false":::

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Oracle Fusion ERP**.

    ![Mapping dei gruppi ERP Oracle Fusion](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Oracle Fusion ERP nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Oracle Fusion ERP per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Oracle Fusion ERP, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Oracle Fusion ERP selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in Oracle Fusion ERP.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Oracle Fusion ERP supporta solo l'autenticazione di base per l'endpoint SCIM.
* Oracle Fusion ERP non supporta il provisioning del gruppo.
* I ruoli in Oracle Fusion ERP vengono mappati ai gruppi in Azure AD. Per assegnare ruoli agli utenti in Oracle Fusion ERP da Azure AD, è necessario assegnare gli utenti ai gruppi di Azure AD desiderati, denominati dopo i ruoli in Oracle Fusion ERP.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
