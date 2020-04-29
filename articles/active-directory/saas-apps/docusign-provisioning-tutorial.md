---
title: 'Esercitazione: Configurare DocuSign per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88b65c8e8962ad8420ded47da1a343672123c589
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058179"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Esercitazione: Configurare DocuSign per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in DocuSign e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a DocuSign.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Una sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On.
*   Un account utente in DocuSign con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-docusign"></a>Assegnazione di utenti a DocuSign

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app DocuSign. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app DocuSign seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Suggerimenti importanti per l'assegnazione di utenti a DocuSign

*   È consigliabile assegnare un singolo utente di Azure AD a DocuSign per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

*   Quando si assegna un utente a DocuSign, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

> [!NOTE]
> Azure Active Directory non supporta il provisioning di gruppi con l'applicazione Docusign. È possibile eseguire il provisioning solo degli utenti.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di DocuSign e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in DocuSign in base all'assegnazione di utenti e gruppi in Azure AD.

> [!Tip]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per DocuSign, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in DocuSign.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se si è già configurato DocuSign per l'accesso Single Sign-On, cercare l'istanza di DocuSign usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **DocuSign** nella raccolta di applicazioni. Selezionare DocuSign nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di DocuSign e quindi la scheda **Provisioning**.

1. Impostare la **modalità di provisioning** su **automatico**. 

    ![provisioning](./media/docusign-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome di account DocuSign che abbia il profilo **Amministratore di sistema** assegnato in DocuSign.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.

1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app DocuSign.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva.**

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to DocuSign** (Sincronizza utenti di Azure Active Directory in DocuSign).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a DocuSign. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in DocuSign per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per DocuSign, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni.

1. Fare clic su **Salva.**

Viene avviata la sincronizzazione iniziale di tutti gli utenti assegnati a DocuSign nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app DocuSign.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-on](docusign-tutorial.md)
