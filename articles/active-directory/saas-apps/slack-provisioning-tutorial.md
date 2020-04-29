---
title: 'Esercitazione: provisioning utenti per Slack-Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062830"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Esercitazione: Configurare Slack per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Slack e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Slack.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Tenant di Slack con [piano Plus](https://aadsyncfabric.slack.com/pricing) o superiore abilitato
* Account utente in Slack con autorizzazioni di amministratore di team

Nota: l'integrazione del provisioning Azure AD si basa sull' [API Slack scim](https://api.slack.com/scim), disponibile per i team Slack nel piano Plus o superiore.

## <a name="assigning-users-to-slack"></a>Assegnazione di utenti a Slack

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Slack. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Slack seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Suggerimenti importanti per l'assegnazione di utenti a Slack

* È consigliabile assegnare un singolo utente di Azure AD a Slack per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Slack, è necessario selezionare il ruolo **Utente** o "Gruppo" nella finestra di dialogo di assegnazione. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="configuring-user-provisioning-to-slack"></a>Configurazione del provisioning utenti in Slack 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Slack e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Slack in base all'assegnazione di utenti e gruppi in Azure AD.

**Suggerimento:** per Slack è anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Slack con Azure AD:

1. Nella [portale di Azure](https://portal.azure.com)passare alla sezione **Azure Active Directory > app aziendali > tutte le applicazioni** .

2. Se si è già configurato Slack per l'accesso Single Sign-On, cercare l'istanza di Slack usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Slack** nella raccolta di applicazioni. Selezionare Slack nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Slack e quindi la scheda **Provisioning**.

4. Impostare la **modalità di provisioning** su **automatico**.

   ![Provisioning in Slack](./media/slack-provisioning-tutorial/slack1.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Slack in una nuova finestra del browser.

6. Nella nuova finestra accedere a Slack con l'account di amministratore di team. Nella finestra di dialogo di autorizzazione risultante selezionare il team Slack per cui si vuole abilitare il provisioning e quindi **Authorize** (Autorizza). Al termine, tornare al portale di Azure per completare la configurazione del provisioning.

    ![Finestra di dialogo di autorizzazione](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Slack. Se la connessione non riesce, verificare che l'account Slack abbia autorizzazioni di amministratore di team e ripetere il passaggio "Authorize" (Autorizza).

8. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

9. Fare clic su **Save**.

10. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Slack** (Sincronizza utenti di Azure Active Directory in Slack).

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che verranno sincronizzati da Azure AD a Slack. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente in Slack per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

12. Per abilitare il servizio di provisioning di Azure AD per Slack, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

13. Fare clic su **Save**.

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Slack nella sezione Utenti e gruppi. Si noti che la sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, eseguite circa ogni 10 minuti fintanto che è in esecuzione il servizio. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Facoltativo] Configurazione del provisioning di oggetti gruppo in Slack

Facoltativamente, è possibile abilitare il provisioning di oggetti gruppo da Azure AD a Slack. La differenza rispetto all'assegnazione di gruppi di utenti risiede nel fatto che verrà eseguita la replica da Azure AD a Slack dell'oggetto gruppo effettivo, oltre che dei relativi membri. Se si ha un gruppo denominato "Gruppo personale" in Azure AD, ad esempio, in Slack verrà creato un identico gruppo denominato "Gruppo personale".

### <a name="to-enable-provisioning-of-group-objects"></a>Per abilitare il provisioning di oggetti gruppo:

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Groups to Slack** (Sincronizza gruppi di Azure Active Directory in Slack).

2. Nel pannello Mapping attributi impostare Abilitato su Sì.

3. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che verranno sincronizzati da Azure AD a Slack. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con i gruppi in Slack per le operazioni di aggiornamento. 

4. Fare clic su **Save**.

Verrà così eseguita la sincronizzazione completa da Azure AD a Slack di tutti gli oggetti gruppo assegnati a Slack nella sezione **Utenti e gruppi**. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Slack.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Durante la configurazione dell'attributo **displayName** di Slack, tenere presente i seguenti comportamenti:

  * I valori non sono completamente univoci (2 utenti, ad esempio, possono avere lo stesso nome visualizzato)

  * Supporta caratteri non inglesi, spazi, maiuscole/minuscole. 
  
  * I segni di punteggiature consentiti sono punti, caratteri di sottolineatura, trattini, apostrofi, parentesi (ad esempio **( [ { } ] )**) e separatori (ad esempio **,/;**).
  
  * Si aggiorna solo se queste due impostazioni sono configurate nell'area di lavoro/organizzazione di Slack: **è abilitata la sincronizzazione del profilo** e **gli utenti non possono modificare il nome visualizzato**.
  
* L'attributo **userName** di Slack deve essere inferiore a 21 caratteri e avere un valore univoco.

* Slack consente solo la corrispondenza con gli attributi **username** e **email**.  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
