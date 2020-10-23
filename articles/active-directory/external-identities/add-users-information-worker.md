---
title: Aggiungere utenti di collaborazione B2B come Information Worker-Azure AD
description: Collaborazione B2B permette agli Information Worker e ai proprietari di app di aggiungere utenti guest ad Azure AD per l'accesso | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba9a227956e7879a7c0f49e0534146fa01a49759
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442219"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Informazioni sul modo in cui utenti nell'organizzazione possono invitare utenti guest a un'app

Dopo che un utente guest è stato aggiunto alla directory in Azure AD, un proprietario dell'applicazione può inviare all'utente guest un collegamento diretto all'app che vuole condividere. Gli amministratori di Azure AD possono anche configurare la gestione self-service per app basate su SAML o raccolte nel tenant di Azure AD. In questo modo, i proprietari di applicazioni possono gestire i propri utenti guest, anche se gli utenti guest non sono stati ancora aggiunti alla directory. Quando un'app è configurata per la gestione self-service, il proprietario dell'applicazione usa il proprio pannello di accesso per invitare un utente guest a un'app o aggiungere un utente guest a un gruppo che ha accesso all'app. Per la gestione delle app self-service per la raccolta e per le app basate su SAML è richiesta una configurazione iniziale da parte di un amministratore. Di seguito è riportato un riepilogo dei passaggi di installazione. per istruzioni più dettagliate, vedere [prerequisiti](#prerequisites) più avanti in questa pagina:

 - Abilitare la gestione gruppi self-service per il tenant
 - Creare un gruppo da assegnare all'app e impostare l'utente come proprietario
 - Configurare l'app per la gestione self-service e assegnare il gruppo all'app

> [!NOTE]
> Questo articolo descrive come impostare la gestione self-service per le app basate su SAML e raccolte che sono state aggiunte al tenant di Azure AD. È anche possibile [configurare gruppi di Microsoft 365 self-service](../users-groups-roles/groups-self-service-management.md) in modo che gli utenti possano gestire l'accesso ai propri gruppi di Microsoft 365. Per altri modi in cui gli utenti possono condividere i file di Office e le app con gli utenti guest, vedere [accesso guest in gruppi di Microsoft 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [condividere file o cartelle di SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Invitare un utente guest a un'app dal pannello di accesso

Dopo che un'app è stata configurata per la gestione self-service, i proprietari di applicazioni possono usare il proprio pannello di accesso per invitare un utente guest all'app che vogliono condividere. L'utente guest non deve necessariamente essere aggiunto ad Azure AD in anticipo. 

1. Aprire il pannello di accesso dall'indirizzo `https://myapps.microsoft.com`.
2. Passare all'app, selezionare i puntini di sospensione (**...**) e quindi selezionare **Gestisci app**.
 
   ![Screenshot che illustra il sottomenu app Gestisci per l'app Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. Nella parte superiore dell'elenco utenti selezionare **+** .
   
   ![Screenshot che mostra il simbolo più per aggiungere membri all'app](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Nella casella di ricerca **Aggiungi membri** digitare l'indirizzo di posta elettronica per l'utente guest. Se si vuole, includere un messaggio di benvenuto.
   
   ![Screenshot che mostra la finestra Aggiungi membri per aggiungere un Guest](media/add-users-iw/access-panel-invitation.png)
   
5. Selezionare **Aggiungi** per inviare un invito all'utente guest. Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Invitare un utente a far parte di un gruppo che ha accesso all'app
Dopo che un'app è stata configurata per la gestione self-service, i proprietari di applicazioni possono invitare utenti guest ai gruppi che gestiscono e che hanno accesso alle app che vogliono condividere. Gli utenti guest nono devono essere già presenti nella directory. Il proprietario dell'applicazione completa questi passaggi per invitare un utente guest nel gruppo in modo che possa accedere all'app.

1. Assicurarsi di essere il proprietario del gruppo self-service che ha accesso all'app che si vuole condividere.
2. Aprire il pannello di accesso dall'indirizzo `https://myapps.microsoft.com`.
3. Selezionare l'app **Gruppi**.
   
   ![Screenshot che mostra l'app gruppi nel pannello di accesso](media/add-users-iw/access-panel-groups.png)
   
4. In **Gruppi di cui si è proprietari** selezionare il gruppo che ha accesso all'app che si vuole condividere.
   
   ![Screenshot che mostra dove selezionare un gruppo sotto i gruppi di cui si è proprietari](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Nella parte superiore dell'elenco dei membri del gruppo selezionare **+** .
   
   ![Screenshot che mostra il simbolo più per l'aggiunta di membri al gruppo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Nella casella di ricerca **Aggiungi membri** digitare l'indirizzo di posta elettronica per l'utente guest. Se si vuole, includere un messaggio di benvenuto.
   
   ![Screenshot che mostra la finestra Aggiungi membri per aggiungere un Guest](media/add-users-iw/access-panel-invitation.png)
   
7. Selezionare **Aggiungi** per inviare automaticamente l'invito all'utente guest. Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.


## <a name="prerequisites"></a>Prerequisiti

La gestione delle app self-service richiede alcuni passaggi di configurazione iniziali, che devono essere eseguiti da un amministratore globale e da un amministratore di Azure AD. Come parte di questa configurazione, è necessario configurare l'app per la gestione self-service e assegnare un gruppo all'app che il proprietario dell'applicazione possa gestire. È anche possibile configurare il gruppo in modo da permettere a chiunque di richiedere l'appartenenza, ma per questa operazione è necessaria l'approvazione di un proprietario del gruppo. Per altre informazioni, vedere [Gestione gruppi self-service](../users-groups-roles/groups-self-service-management.md). 

> [!NOTE]
> Non è possibile aggiungere utenti guest a un gruppo dinamico o a un gruppo sincronizzato con Active Directory locale.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Abilitare la gestione gruppi self-service per il tenant
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. Selezionare **Gruppi**.
4. In **Impostazioni** selezionare **Generale**.
5. In **Gestione dei gruppi self-service**, accanto a **I proprietari possono gestire le richieste di appartenenza ai gruppi nel Pannello di accesso**, selezionare **Sì**.
6. Selezionare **Salva**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Creare un gruppo da assegnare all'app e impostare l'utente come proprietario
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD o come amministratore globale.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. Selezionare **Gruppi**.
4. Selezionare **Nuovo gruppo**.
5. In **Tipo di gruppo** selezionare **Sicurezza**.
6. Immettere un **Nome gruppo** e una **Descrizione gruppo**.
7. In **Tipo di appartenenza** selezionare **Assegnato**.
8. Selezionare **Crea** e chiudere la pagina **Gruppo**.
9. Nella pagina **Gruppi - Tutti i gruppi** aprire il gruppo. 
10. In **Gestisci** selezionare **Proprietari** > **Aggiungi proprietari**. Cercare l'utente che deve gestire l'accesso all'applicazione. Selezionare l'utente e quindi fare clic su **Seleziona**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configurare l'app per la gestione self-service e assegnare il gruppo all'app
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD o come amministratore globale.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
4. Nell'elenco delle applicazioni individuare e aprire l'app.
5. In **Gestisci** selezionare **Single Sign-On** e configurare l'applicazione per l'accesso Single Sign-On. Per informazioni, vedere [Gestire l'accesso Single Sign-On per le app aziendali](../manage-apps/view-applications-portal.md).
6. In **Gestisci** selezionare **Self-service** e configurare l'accesso alle app self-service. Per informazioni, vedere [Come usare l'accesso alle app self-service](../manage-apps/access-panel-manage-self-service-access.md). 

    > [!NOTE]
    > Per l'impostazione **Gruppo a cui devono essere aggiunti gli utenti assegnati** selezionare il gruppo creato nella sezione precedente.
7. In **Gestisci** selezionare **Utenti e gruppi** e verificare che il gruppo self-service creato sia visualizzato nell'elenco.
8. Per aggiungere l'app al pannello di accesso del proprietario del gruppo, selezionare **Aggiungi**  >  **utenti e gruppi**utente. Cercare il proprietario del gruppo e selezionare l'utente, fare clic su **Seleziona** e quindi fare clic su **Assegna** per aggiungere l'utente all'app.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Licenze per la Collaborazione B2B di Azure AD](licensing-guidance.md)