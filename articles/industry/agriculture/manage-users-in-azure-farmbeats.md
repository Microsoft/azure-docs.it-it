---
title: Gestire gli utenti in Azure FarmBeats
description: Questo articolo descrive come gestire gli utenti in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179902"
---
# <a name="manage-users"></a>Gestire gli utenti

Azure FarmBeats include la gestione degli utenti per utenti che fanno parte dell'istanza di Azure Active Directory (Azure AD). È possibile aggiungere utenti all'istanza di Azure FarmBeats per accedere alle API, visualizzare le mappe generate e accedere ai dati di telemetria dei sensori dalla farm.

## <a name="prerequisites"></a>Prerequisiti

- L'installazione di Azure FarmBeats è obbligatoria. Per altre informazioni, vedere [Install Azure FarmBeats](install-azure-farmbeats.md).
- ID di posta elettronica degli utenti che si vuole aggiungere o rimuovere dall'istanza di Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gestire gli utenti di Azure FarmBeats

Azure FarmBeats USA Azure AD per l'autenticazione, il controllo di accesso e i ruoli. È possibile aggiungere utenti nel tenant di Azure AD come utenti in Azure FarmBeats.

> [!NOTE]
> Se un utente non è un utente Azure AD tenant, seguire le istruzioni nella sezione **aggiungi Azure ad utenti** per completare la configurazione.

Azure FarmBeats supporta due tipi di ruoli utente:

 - **Amministratore**: completare l'accesso alle API datahub di Azure FarmBeats. Gli utenti con questo ruolo possono eseguire una query su tutti gli oggetti datahub di Azure FarmBeats ed eseguire tutte le operazioni dall'acceleratore FarmBeats.
 - Sola **lettura**: accesso di sola lettura alle API datahub di FarmBeats. Gli utenti possono visualizzare le API datahub, i dashboard acceleratori e le mappe. Gli utenti con accesso in sola lettura non possono eseguire operazioni quali la generazione di mappe, l'associazione di dispositivi o la creazione di farm.

## <a name="add-users-to-azure-farmbeats"></a>Aggiungere utenti ad Azure FarmBeats

Per aggiungere utenti ad Azure FarmBeats:

1. Accedere a acceleratore e quindi selezionare l'icona **Impostazioni** .
2. Selezionare **controllo di accesso**.

    ![Riquadro Impostazioni farm](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Immettere l'ID di posta elettronica dell'utente a cui si vuole concedere l'accesso.
4. Selezionare il ruolo desiderato, **amministratore** o di **sola lettura**.
5. Selezionare **Aggiungi ruolo**.

L'utente aggiunto ora può accedere ad Azure FarmBeats (sia datahub che Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Eliminare gli utenti da Azure FarmBeats

Per rimuovere gli utenti dal sistema FarmBeats di Azure:

1. Accedere a acceleratore e quindi selezionare l'icona **Impostazioni** .
2. Selezionare **controllo di accesso**.
3. Selezionare **Elimina**.

   L'utente viene eliminato dal sistema. Si riceverà il messaggio di conferma seguente:

   ![Messaggio di conferma di Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Aggiungere utenti di Azure AD

> [!NOTE]
> Gli utenti di Azure FarmBeats devono esistere nel tenant di Azure AD prima di assegnarli a applicazioni e ruoli. Se un utente non esiste nel tenant Azure AD, seguire le istruzioni riportate in questa sezione. Ignorare le istruzioni, se un utente esiste già nel tenant Azure AD.

Attenersi alla procedura per aggiungere utenti a Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In alto a destra selezionare l'account e quindi passare al tenant Azure AD associato a FarmBeats.
3. Selezionare gli **utenti** di  > **Azure Active Directory**.

    Viene visualizzato un elenco di Azure AD utenti.

4. Per aggiungere un utente alla directory, selezionare **nuovo utente**. Per aggiungere un utente esterno, selezionare **nuovo utente Guest**.

    ![Riquadro "tutti gli utenti"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selezionare il nome del nuovo utente e quindi completare i campi obbligatori per tale utente.
6. Selezionare **Crea**.

Per informazioni sulla gestione di utenti Azure AD, vedere [aggiungere o eliminare utenti in Azure ad](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="next-steps"></a>Passaggi successivi

Gli utenti sono stati aggiunti all'istanza di Azure FarmBeats. A questo punto, informazioni su come [creare e gestire Farm](manage-farms-in-azure-farmbeats.md#create-farms).