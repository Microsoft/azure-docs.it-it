---
title: 'Esercitazione: Configurare GoToMeeting per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e GoToMeeting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358900"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Esercitazione: Configurare GoToMeeting per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in GoToMeeting e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a GoToMeeting.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di GoToMeeting abilitata per l'accesso Single Sign-On.
*   Account utente in GoToMeeting con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-gotomeeting"></a>Assegnazione di utenti a GoToMeeting

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app GoToMeeting. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app GoToMeeting seguendo le istruzioni riportate in questo articolo:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Suggerimenti importanti per l'assegnazione di utenti a GoToMeeting

*   È consigliabile assegnare un singolo utente di Azure AD a GoToMeeting per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a GoToMeeting, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatizzato

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di GoToMeeting e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in GoToMeeting in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per GoToMeeting, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning"></a>Per configurare il provisioning automatico degli account utente:

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se si è già configurato GoToMeeting per l'accesso Single Sign-On, cercare l'istanza di GoToMeeting usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **GoToMeeting** nella raccolta di applicazioni. Selezionare GoToMeeting nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di GoToMeeting e quindi la scheda **Provisioning**.

1. Impostare **Modalità di provisioning** su **Automatico**. 

    ![Screenshot della scheda Provisioning per GoToMeeting nel portale di Azure. L'opzione Modalità di provisioning è impostata su Automatico e le opzioni Nome utente amministratore, Password amministratore e Test connessione sono evidenziate.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Nella sezione Credenziali amministratore, seguire questa procedura:
   
    a. Nella casella di testo **Nome utente amministratore GoToMeeting** digitare il nome utente di un amministratore.

    b. Nella casella di testo **Password amministratore GoToMeeting** digitare la password dell'amministratore.

1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app GoToMeeting. Se la connessione non riesce, verificare che l'account GoToMeeting abbia autorizzazioni di amministratore di team e ripetere il passaggio **Credenziali amministratore**.

1. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

1. Fare clic su **Salva**.

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to GoToMeeting** (Sincronizza utenti di Azure Active Directory in GoToMeeting).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a GoToMeeting. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in GoToMeeting per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per GoToMeeting, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni.

1. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a GoToMeeting nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app GoToMeeting.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configurare l'accesso Single Sign-On](./citrix-gotomeeting-tutorial.md)