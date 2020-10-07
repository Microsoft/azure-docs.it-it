---
title: 'Guida introduttiva: Aggiungere utenti guest nel portale di Azure - Azure AD'
description: Usare questa guida introduttiva per ottenere informazioni su come gli amministratori di Azure AD possono aggiungere utenti guest B2B nel portale di Azure e seguire il flusso di lavoro di invito B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f134a11b6d0a561a8b53156c927906afe2874a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87910610"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Guida introduttiva: Aggiungere utenti guest alla directory nel portale di Azure

È possibile invitare chiunque a collaborare con l'organizzazione aggiungendolo alla directory come utente guest. È quindi possibile inviare un messaggio di posta elettronica di invito contenente un collegamento di riscatto oppure inviare un collegamento diretto a un'app che si vuole condividere. Gli utenti guest possono accedere con la propria identità aziendale, dell'istituto di istruzione o di social networking. Oltre a questo argomento di avvio rapido, è possibile vedere altre informazioni sull'aggiunta di utenti guest [nel portale di Azure](add-users-administrator.md), tramite [PowerShell](b2b-quickstart-invite-powershell.md) o [in blocco](tutorial-bulk-invite.md).

In questo argomento di avvio rapido si aggiungerà un nuovo utente guest alla directory di Azure AD tramite il portale di Azure, si invierà un invito e si vedrà il processo di riscatto dell'invito da parte dell'utente guest.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

 - Un ruolo che consenta di creare utenti nella directory del tenant, ad esempio il ruolo Amministratore globale o uno dei ruoli della directory di amministratore con autorizzazioni limitate.
 - Un account di posta elettronica valido che sia possibile aggiungere alla directory del tenant e usare per ricevere il messaggio di invito di test.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Aggiungere un nuovo utente guest in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3.  In **Gestisci** selezionare **Utenti**.

    ![Screenshot che mostra dove selezionare l'opzione Utenti](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Selezionare **Nuovo utente guest**.

    ![Screenshot che mostra dove selezionare l'opzione Nuovo utente guest](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Nella pagina **Nuovo utente** selezionare **Invita l'utente** e quindi aggiungere le informazioni dell'utente Guest. 

   - **Nome.** Nome e cognome dell'utente guest.
   - **Indirizzo e-mail (obbligatorio)** . Indirizzo e-mail dell'utente guest.
   - **Messaggio personale (facoltativo)** .Includere un messaggio di benvenuto personale per l'utente guest.
   - **Gruppi**. È possibile aggiungere l'utente guest a uno o più gruppi esistenti, anche in un secondo momento.
   - **Ruolo della directory**. Se sono necessari autorizzazioni amministrative Azure AD per l'utente, è possibile aggiungerle a un ruolo di Azure AD. 

6. Selezionare **Invita** per inviare automaticamente l'invito all'utente guest. Viene visualizzata una notifica in alto a destra con il messaggio **L'utente è stato invitato**. 
7.  Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.

## <a name="assign-an-app-to-the-guest-user"></a>Assegnare un'app all'utente guest
Aggiungere l'app Salesforce nel tenant di test e assegnare all'app l'utente guest di test.
1.  Accedere al portale di Azure come amministratore di Azure AD.
2.  Nel riquadro sinistro selezionare **Applicazioni aziendali**.
3.  Selezionare **Nuova applicazione**.
4. In **Aggiungi dalla raccolta** cercare **Salesforce** e selezionarla.

    ![Screenshot che mostrala casella di ricerca Aggiungi dalla raccolta](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Selezionare **Aggiungi**.
6. In **Gestisci** selezionare **Single Sign-On** e in **Modalità Single Sign-On** selezionare **Accesso basato su password** e fare clic su **Salva**.
7. In **Gestisci** selezionare **Utenti e gruppi** > **Aggiungi utente** > **Utenti e gruppi**.
8. Usare la casella di ricerca per cercare l'utente di test (se necessario) e selezionare l'utente di test nell'elenco. Quindi fare clic su **Seleziona**.
9. Selezionare **Assegna**. 

## <a name="accept-the-invitation"></a>Accettare l'invito
Accedere ora come utente guest per visualizzare l'invito.
1.  Accedere all'account di posta elettronica dell'utente guest di test.
2.  Nella cartella Posta in arrivo cercare il messaggio "You're invited" ("Sei invitato").

    ![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Nel corpo del messaggio di posta elettronica selezionare **Get Started** (Inizia). Nel browser viene aperta la pagina **Verifica le autorizzazioni**. 

    ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Selezionare **Accetto**. Viene visualizzato il pannello di accesso, in cui sono elencate le applicazioni a cui può accedere l'utente guest.

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non sono più necessari, eliminare l'utente guest di test e l'app di test.
1.  Accedere al portale di Azure come amministratore di Azure AD.
2.  Nel riquadro sinistro selezionare **Azure Active Directory**.
3.  In **Gestisci** selezionare **Applicazioni aziendali**.
4.  Aprire l'applicazione **Salesforce** e quindi selezionare **Elimina**.
5.  Nel riquadro sinistro selezionare **Azure Active Directory**.
6.  In **Gestisci** selezionare **Utenti**.
7.  Selezionare l'utente di test e quindi selezionare **Elimina utente**.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un utente guest nel portale di Azure ed è stato inviato un invito a condividere un'app. È stato quindi illustrato il processo di riscatto dal punto di vista dell'utente guest ed è stato verificato che l'app fosse presente nel pannello di accesso dell'utente guest. Per altre informazioni sull'aggiunta di utenti guest per la collaborazione, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md).
