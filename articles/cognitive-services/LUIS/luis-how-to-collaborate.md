---
title: Collaborare con altri-LUIS
titleSuffix: Azure Cognitive Services
description: Un proprietario dell'app può aggiungere collaboratori alla risorsa di creazione. Questi collaboratori possono modificare il modello, eseguire il training e pubblicare l'app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f1aed6b1c03a2b83e3b4fd91f1106614adb5a701
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344391"
---
# <a name="add-contributors-to-your-app"></a>Aggiungere collaboratori all'app

Un proprietario dell'app può aggiungere collaboratori alle app. I collaboratori possono modificare il modello, eseguire il training e pubblicare l'app.

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Aggiungere un collaboratore alla risorsa di creazione di Azure

La procedura seguente è relativa a tutti gli utenti di cui è stata **eseguita la migrazione** per usare la risorsa di creazione di Azure.

È stata eseguita la migrazione se l'esperienza di creazione LUIS è associata a una risorsa di creazione nella pagina **> Gestisci le risorse di Azure** nel portale Luis.

1. Nella portale di Azure trovare la risorsa di creazione di Language Understanding (LUIS). Il tipo è `LUIS.Authoring` .
1. Nella pagina controllo di **accesso (IAM)** della risorsa selezionare **+ Aggiungi** e quindi selezionare **Aggiungi assegnazione ruolo**.

    ![In portale di Azure aggiungere assegnazione di ruolo alla risorsa di creazione.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il **ruolo** di collaboratore. Nell'opzione **assegna accesso a** selezionare **Azure ad utente, gruppo o entità servizio**. Nell'opzione **Seleziona** immettere l'indirizzo di posta elettronica dell'utente. Se l'utente è conosciuto da più di un indirizzo di posta elettronica per lo stesso dominio, assicurarsi di immettere l'account di posta elettronica _primario_ .

    ![Aggiungere la posta elettronica dell'utente al ruolo Collaboratore per Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando viene trovato l'indirizzo di posta elettronica dell'utente, selezionare l'account e selezionare **Salva**.

    In caso di problemi con questa assegnazione di ruolo, esaminare le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) e la [risoluzione dei problemi di controllo di accesso di Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Aggiungi collaboratore all'app LUIS

La procedura seguente è relativa a tutti gli utenti che non hanno eseguito la **migrazione** per usare la risorsa di creazione di Azure.

Non è stata eseguita la migrazione se l'esperienza di creazione di LUIS non è associata a una risorsa di creazione nella pagina **delle risorse di gestione > di Azure** nel portale Luis.

Un'app ha un unico autore, il proprietario, ma può avere molti collaboratori. Per consentire ai collaboratori di modificare l'app LUIS, alll'elenco di collaboratori è necessario aggiungere l'indirizzo di posta elettronica che useranno per accedere al portale di LUIS. Una volta aggiunta, l'app appare nel loro portale di LUIS.

1. Selezionare **Gestisci** dal menu in alto a destra, quindi selezionare **Collaboratori** nel menu a sinistra.

1. Selezionare **Aggiungi collaboratore** nella barra degli strumenti.

1. Immettere l'indirizzo di posta elettronica che il collaboratore userà per accedere al portale di LUIS.

    ![Aggiungere l'indirizzo di posta elettronica del collaboratore](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Utenti con più messaggi di posta elettronica

Se si aggiungono collaboratori/collaboratori a un'app LUIS, viene specificato l'indirizzo di posta elettronica esatto. Mentre Azure Active Directory (Azure AD) consente a un singolo utente di avere più di un account di posta elettronica usato in modo intercambiabile, LUIS richiede all'utente di accedere con l'indirizzo di posta elettronica specificato durante l'aggiunta del collaboratore/collaboratore.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Risorse Azure Active Directory

Se all'interno della propria organizzazione si usa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD), Language Understanding (LUIS) richiede l'autorizzazione per le informazioni relative all'accesso degli utenti quando desiderano usare LUIS. Le risorse richieste da LUIS sono minime.

La descrizione dettagliata viene visualizzata quando si tenta di effettuare l'iscrizione con un account che ha ottenuto il consenso da parte dell'amministratore o non richiede il consenso dello stesso, ad esempio il consenso amministratore:

* Consente di accedere all'app con l'account dell'organizzazione e consente all'app di leggere il profilo personale. Consente inoltre all'app di leggere le informazioni aziendali di base. Questo consente a LUIS di leggere i dati di profilo di base, come ad esempio l'ID utente, l'indirizzo di posta elettronica e il nome.
* Consente all'app di visualizzare e aggiornare i dati, anche quando non la si sta usando. L'autorizzazione è obbligatoria per aggiornare il token di accesso dell'utente.


### <a name="azure-active-directory-tenant-user"></a>Utente del tenant di Azure Active Directory

LUIS usa il flusso di consenso standard di Azure Active Directory (Azure AD).

L'amministratore del tenant collabora direttamente con l'utente che necessita dell'accesso per poter usare LUIS in Azure AD.

* Innanzitutto, l'utente accede a LUIS e visualizza la finestra popup che richiede l'approvazione dell'amministratore. L'utente contatta l'amministratore del tenant prima di continuare.
* L'amministratore del tenant accede quindi a LUIS e visualizza una finestra popup del flusso di consenso. La finestra consente all'amministratore di concedere l'autorizzazione all'utente. Dopo che l'amministratore ha accettato l'autorizzazione, l'utente sarà in grado di continuare a usare LUIS. Se non accede a LUIS, l'amministratore del tenant può accedere al [consenso](https://account.activedirectory.windowsazure.com/r#/applications) per LUIS, come illustrato nello screenshot seguente. Si noti che l'elenco è filtrato in modo da visualizzare gli elementi che includono il nome `LUIS`.

![Autorizzazione di Azure Active Directory dal sito Web dell'app](./media/luis-how-to-collaborate/tenant-permissions.png)

Se l'amministratore del tenant vuole che solo determinati utenti usino LUIS, sono due le soluzioni possibili:
* Dare il "consenso amministratore" (consenso a tutti gli utenti di Azure AD), ma successivamente impostare su "Sì" l'opzione "Assegnazione utenti obbligatoria" nelle proprietà dell'applicazione aziendale e infine assegnare/aggiungere all'applicazione solo gli utenti desiderati. In questo modo l'amministratore dà comunque il "consenso amministratore" per l'applicazione, ma è possibile controllare gli utenti che possono accedervi.
* Una seconda soluzione consiste nell'usare l' [API di gestione delle identità e degli accessi Azure ad in Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) per fornire il consenso a ogni utente specifico.

Altre informazioni sugli utenti di Azure Active Directory e sul consenso:
* [Limitare l'app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a un set di utenti

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come usare le versioni](luis-how-to-manage-versions.md) per controllare il ciclo di vita dell'app.
* Comprendere i concetti che includono la [risorsa di creazione](luis-concept-keys.md#authoring-key) e i [collaboratori](luis-concept-keys.md#contributions-from-other-authors) di tale risorsa.
* Informazioni [su come creare risorse di](luis-how-to-azure-subscription.md) creazione e di runtime
* Eseguire la migrazione alla nuova [risorsa di creazione](luis-migration-authoring.md)
