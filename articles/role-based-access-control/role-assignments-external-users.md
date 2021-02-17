---
title: Assegnare i ruoli di Azure agli utenti Guest esterni usando la portale di Azure-RBAC di Azure
description: Informazioni su come concedere l'accesso alle risorse di Azure per gli utenti esterni a un'organizzazione usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: it-pro
ms.openlocfilehash: d834f4ccd8dba26c895e0578f161813fc49332ea
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556294"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Assegnare i ruoli di Azure agli utenti Guest esterni usando il portale di Azure

Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](overview.md) consente una migliore gestione della sicurezza per le organizzazioni di grandi dimensioni e per le aziende di piccole e medie dimensioni che lavorano con collaboratori esterni, fornitori o freelance che necessitano dell'accesso a risorse specifiche nell'ambiente, ma non necessariamente all'intera infrastruttura o agli ambiti correlati alla fatturazione. È possibile usare le funzionalità di [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) per collaborare con utenti Guest esterni ed è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere solo le autorizzazioni necessarie per gli utenti guest nell'ambiente in uso.

## <a name="prerequisites"></a>Prerequisiti

Per assegnare i ruoli di Azure o rimuovere le assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)


## <a name="when-would-you-invite-guest-users"></a>Quando invitare gli utenti Guest?

Di seguito sono riportati alcuni scenari di esempio in cui è possibile invitare utenti guest all'organizzazione e concedere le autorizzazioni:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di gestire determinate risorse o un'intera sottoscrizione.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Differenze di autorizzazione tra utenti membro e utenti Guest

I membri nativi di una directory (utenti membro) hanno autorizzazioni diverse rispetto a quelle degli utenti invitati da un'altra directory come guest di collaborazione B2B (utenti guest). Ad esempio, l'utente dei membri può leggere quasi tutte le informazioni di directory mentre gli utenti Guest hanno autorizzazioni di directory limitate. Per ulteriori informazioni sugli utenti del membro e sugli utenti guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Aggiungere un utente guest alla directory

Attenersi alla seguente procedura per aggiungere un utente guest alla directory usando la pagina Azure Active Directory.

1. Assicurarsi che le impostazioni di collaborazione esterna dell'organizzazione siano configurate per consentire gli inviti agli utenti guest. Per altre informazioni, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](../active-directory/external-identities/delegate-invitations.md).

1. Nella portale di Azure fare clic su **Azure Active Directory**  >  **utenti**  >  **nuovo utente Guest**.

    ![Nuova funzionalità utente guest in portale di Azure](./media/role-assignments-external-users/invite-guest-user.png)

1. Seguire i passaggi per aggiungere un nuovo utente Guest. Per ulteriori informazioni, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

In seguito all'aggiunta di un utente guest alla directory, è possibile inviare all'utente guest un collegamento diretto a un'app condivisa oppure l'utente guest può selezionare l'URL di riscatto nel messaggio di posta elettronica di invito.

![Messaggio di posta elettronica di invito utente Guest](./media/role-assignments-external-users/invite-email.png)

Per consentire all'utente guest di accedere alla directory, è necessario completare il processo di invito.

![Autorizzazioni di verifica dell'invito utente Guest](./media/role-assignments-external-users/invite-review-permissions.png)

Per ulteriori informazioni sul processo di invito, vedere [Azure Active Directory riscatto dell'invito di collaborazione B2B](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Assegnazione di un ruolo a un utente Guest

Nel controllo degli accessi in base al ruolo di Azure per concedere l'accesso, assegnare un ruolo. Per assegnare un ruolo a un utente Guest, seguire la [stessa procedura descritta](role-assignments-portal.md) per un utente membro, un gruppo, un'entità servizio o un'identità gestita. Seguire questi passaggi per assegnare un ruolo a un utente guest in ambiti diversi.

1. Nel portale di Azure fare clic su **Tutti i servizi**.

1.  Consente di selezionare il set di risorse a cui viene applicato l'accesso, noto anche come ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

    Lo screenshot seguente mostra un esempio del pannello Controllo di accesso (IAM) per un gruppo di risorse. Se si apportano modifiche al controllo di accesso, si applicano solo al gruppo di risorse.

    ![Pannello Controllo di accesso (IAM) per un gruppo di risorse](./media/role-assignments-external-users/access-control-resource-group.png)

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

    Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

    ![Menu Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment-menu.png)

    Viene visualizzato il riquadro Aggiungi assegnazione di ruolo.

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare l'utente Guest. Se l'utente non viene visualizzato nell'elenco, è possibile digitare nella casella **Seleziona** per cercare i nomi visualizzati, gli indirizzi di posta elettronica e gli identificatori di oggetto.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/role-assignments-external-users/add-role-assignment.png)

1. Fare clic su **Salva** per assegnare il ruolo all'ambito selezionato.

    ![Assegnazione di ruolo per collaboratore macchina virtuale](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Assegnare un ruolo a un utente Guest non ancora presente nella directory

Per assegnare un ruolo a un utente Guest, seguire la [stessa procedura descritta](role-assignments-portal.md) per un utente membro, un gruppo, un'entità servizio o un'identità gestita.

Se l'utente Guest non è ancora presente nella directory, è possibile invitare l'utente direttamente dal riquadro Aggiungi assegnazione ruolo.

1. Nel portale di Azure fare clic su **Tutti i servizi**.

1.  Consente di selezionare il set di risorse a cui viene applicato l'accesso, noto anche come ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi** > **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

    ![Menu Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment-menu.png)

    Viene visualizzato il riquadro Aggiungi assegnazione di ruolo.

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** Digitare l'indirizzo di posta elettronica della persona che si vuole invitare e selezionare tale persona.

   ![Invitare l'utente Guest nel riquadro Aggiungi assegnazione ruolo](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Fare clic su **Salva** per aggiungere l'utente guest alla directory, assegnare il ruolo e inviare un invito.

    Dopo alcuni istanti, viene visualizzata una notifica dell'assegnazione di ruolo e le informazioni sull'invito.

    ![Assegnazione di ruolo e notifica utente invitato](./media/role-assignments-external-users/invited-user-notification.png)

1. Per invitare manualmente l'utente Guest, fare clic con il pulsante destro del mouse e copiare il collegamento all'invito nella notifica. Non fare clic sul collegamento all'invito perché avvia il processo di invito.

    Il collegamento all'invito avrà il formato seguente:

    `https://invitations.microsoft.com/redeem/...`

1. Inviare il collegamento di invito all'utente Guest per completare il processo di invito.

    Per ulteriori informazioni sul processo di invito, vedere [Azure Active Directory riscatto dell'invito di collaborazione B2B](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Rimuovere un utente guest dalla directory

Prima di rimuovere un utente Guest da una directory, è necessario rimuovere prima le assegnazioni di ruolo per tale utente Guest. Attenersi alla seguente procedura per rimuovere un utente Guest da una directory.

1. Aprire il **controllo di accesso (IAM)** in un ambito, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui l'utente Guest ha un'assegnazione di ruolo.

1. Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'utente guest con l'assegnazione di ruolo che si desidera rimuovere.

   ![Rimuovere l'assegnazione di ruolo](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-external-users/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

1. Nella barra di spostamento a sinistra fare clic su **Azure Active Directory**  >  **utenti**.

1. Fare clic sull'utente guest che si desidera rimuovere.

1. Fare clic su **Elimina**.

   ![Elimina utente Guest](./media/role-assignments-external-users/delete-guest-user.png)

1. Nel messaggio di eliminazione visualizzato, fare clic su **Sì**.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="guest-user-cannot-browse-the-directory"></a>L'utente Guest non può esplorare la directory

Gli utenti guest hanno autorizzazioni di directory limitate. Ad esempio, gli utenti guest non possono esplorare la directory e non possono cercare gruppi o applicazioni. Per ulteriori informazioni, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![L'utente Guest non può esplorare gli utenti in una directory](./media/role-assignments-external-users/directory-no-users.png)

Se un utente Guest necessita di privilegi aggiuntivi nella directory, è possibile assegnare un ruolo della directory all'utente Guest. Se si desidera che un utente guest disponga di accesso in lettura completo alla directory, è possibile aggiungere l'utente Guest al ruolo [Readers di directory](../active-directory/roles/permissions-reference.md) in Azure ad. Per altre informazioni, vedere [concedere le autorizzazioni agli utenti dalle organizzazioni partner nel tenant del Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![Assegnare il ruolo lettori directory](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>L'utente Guest non può esplorare utenti, gruppi o entità servizio per assegnare i ruoli

Gli utenti guest hanno autorizzazioni di directory limitate. Anche se un utente Guest è un [proprietario](built-in-roles.md#owner) di un ambito, se tenta di assegnare un ruolo per concedere a un altro utente l'accesso, non può sfogliare l'elenco di utenti, gruppi o entità servizio.

![L'utente Guest non può esplorare le entità di sicurezza per assegnare i ruoli](./media/role-assignments-external-users/directory-no-browse.png)

Se l'utente Guest conosce il nome di accesso esatto di un utente nella directory, può concedere l'accesso. Se si desidera che un utente guest disponga di accesso in lettura completo alla directory, è possibile aggiungere l'utente Guest al ruolo [Readers di directory](../active-directory/roles/permissions-reference.md) in Azure ad. Per altre informazioni, vedere [concedere le autorizzazioni agli utenti dalle organizzazioni partner nel tenant del Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>L'utente Guest non può registrare le applicazioni o creare entità servizio

Gli utenti guest hanno autorizzazioni di directory limitate. Se un utente guest deve essere in grado di registrare le applicazioni o creare entità servizio, è possibile aggiungere l'utente Guest al ruolo di [sviluppatore dell'applicazione](../active-directory/roles/permissions-reference.md) in Azure ad. Per altre informazioni, vedere [concedere le autorizzazioni agli utenti dalle organizzazioni partner nel tenant del Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![L'utente Guest non può registrare le applicazioni](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>L'utente Guest non Visualizza la nuova directory

Se a un utente Guest è stato concesso l'accesso a una directory, ma non viene visualizzata la nuova directory elencata nel portale di Azure quando si tenta di passare al riquadro **directory + sottoscrizione** , assicurarsi che l'utente Guest abbia completato il processo di invito. Per ulteriori informazioni sul processo di invito, vedere [Azure Active Directory riscatto dell'invito di collaborazione B2B](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Le risorse non vengono visualizzate dall'utente Guest

Se a un utente Guest è stato concesso l'accesso a una directory, ma non vengono visualizzate le risorse a cui è stato concesso l'accesso nel portale di Azure, assicurarsi che l'utente Guest abbia selezionato la directory corretta. Un utente guest potrebbe avere accesso a più directory. Per cambiare directory, in alto a sinistra fare clic su **directory + sottoscrizione** e quindi fare clic sulla directory appropriata.

![Riquadro Directory + sottoscrizioni in portale di Azure](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../active-directory/external-identities/add-users-administrator.md)
- [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](../active-directory/external-identities/user-properties.md)
- [Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Aggiungere un utente guest come coamministratore](classic-administrators.md#add-a-guest-user-as-a-co-administrator)