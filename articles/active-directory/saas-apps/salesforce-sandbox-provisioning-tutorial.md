---
title: 'Esercitazione: Configurare Salesforce Sandbox per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063272"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Esercitazione: Configurare Salesforce Sandbox per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Salesforce Sandbox e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Salesforce Sandbox.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Un tenant valido per Salesforce Sandbox for Work o Salesforce Sandbox for Education. È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.
*   Account utente in Salesforce Sandbox con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-salesforce-sandbox"></a>Assegnazione di utenti a Salesforce Sandbox

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti o gruppi in Azure AD devono accedere all'app Salesforce Sandbox. Dopo aver deciso, è possibile assegnare questi utenti all'app Salesforce Sandbox seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Suggerimenti importanti per l'assegnazione di utenti a Salesforce Sandbox

* È consigliabile assegnare un singolo utente di Azure AD a Salesforce Sandbox per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Salesforce Sandbox, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

> [!NOTE]
> Questa app importa ruoli personalizzati da Salesforce Sandbox come parte del processo di provisioning che il cliente può decidere di selezionare durante l'assegnazione di utenti.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Salesforce Sandbox e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Salesforce Sandbox in base all'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Salesforce Sandbox, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

In questa sezione viene descritto come abilitare il provisioning utente degli account utente di Active Directory in Salesforce Sandbox.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se è già stato configurato Salesforce Sandbox per l'accesso Single Sign-On, cercare l'istanza di Salesforce Sandbox usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Salesforce Sandbox** nella raccolta di applicazioni. Selezionare Salesforce Sandbox nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di Salesforce Sandbox e quindi la scheda **Provisioning**.

1. Impostare la **modalità di provisioning** su **automatico**.

    ![provisioning](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome account di Salesforce Sandbox con il profilo **Amministratore di sistema** assegnato in Salesforce.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.

1. Per ottenere il token di sicurezza di Salesforce Sandbox, aprire una nuova scheda e accedere allo stesso account amministratore di Salesforce Sandbox. Nell'angolo superiore destro della pagina fare clic sul proprio nome e quindi su **Impostazioni**.

     ![Abilita provisioning utenti automatico](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Abilita provisioning utenti automatico")

1. Nel pannello di navigazione sinistro fare clic su **My Personal Information** (Informazioni personali) per espandere la sezione corrispondente e quindi fare clic su **Reset My Security Token** (Reimposta token di sicurezza personale).
  
    ![Abilita provisioning utenti automatico](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Abilita provisioning utenti automatico")

1. Nella pagina **Reset Security Token** (Reimposta token di sicurezza) fare clic sul pulsante **Reset Security Token** (Reimposta token di sicurezza).

    ![Abilita provisioning utenti automatico](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Abilita provisioning utenti automatico")

1. Controllare la casella di posta elettronica associata a questo account di amministratore. Cercare un messaggio di posta elettronica da Salesforce Sandbox.com contenente il nuovo token di sicurezza.

1. Copiare il token, passare alla finestra di Azure AD e incollarlo nel campo **Token segreto**.

1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Salesforce Sandbox.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva.**  
    
1.  Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Salesforce Sandbox** (Sincronizza utenti di Azure Active Directory in Salesforce Sandbox).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Salesforce Sandbox. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Salesforce Sandbox per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per Salesforce Sandbox, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

1. Fare clic su **Salva.**

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Salesforce Sandbox nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Salesforce Sandbox.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
