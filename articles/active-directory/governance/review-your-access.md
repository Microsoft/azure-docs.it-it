---
title: Controllare l'accesso ai gruppi & app nelle verifiche di accesso-Azure AD
description: Informazioni su come verificare il proprio accesso a gruppi o applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422411"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Verificare l'accesso per se stessi a gruppi o applicazioni in Azure AD verifiche di accesso

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso a gruppi o applicazioni in Azure AD e altri Microsoft Online Services con una funzionalità denominata verifiche di accesso Azure AD.

Questo articolo descrive come verificare il proprio accesso a un gruppo o a un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso consiste nell'individuare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft che richiede di verificare l'accesso. Di seguito è riportato un esempio di messaggio di posta elettronica per esaminare l'accesso a un gruppo.

    ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/review-your-access/access-review-email.png)

1. Fare clic sul collegamento **Verifica accesso** per aprire la verifica di accesso.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le verifiche di accesso in sospeso seguendo questa procedura.

1. Accedere al portale app Web all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portale app Web che elenca le app a cui si hanno le autorizzazioni](./media/review-your-access/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Sul lato destro della pagina fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/review-your-access/access-reviews-list.png)

1. Fare clic sul collegamento **Begin Review** per la verifica di accesso che si desidera eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, è possibile visualizzare l'accesso.

1. Controllare l'accesso e decidere se è ancora necessario l'accesso.

    Se la richiesta prevede di verificare l'accesso per altri utenti, la pagina sarà diversa. Per altre informazioni, vedere [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

    ![Aprire la verifica di accesso chiedendo se è ancora necessario l'accesso a un gruppo](./media/review-your-access/perform-access-review.png)

1. Fare clic su **Sì** per Mantenete l'accesso oppure su **No** per rimuovere l'accesso.

1. Se si fa clic su **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **motivo** .

    ![Verifica di accesso completato che chiede se è ancora necessario l'accesso a un gruppo](./media/review-your-access/perform-access-review-submit.png)

1. Fare clic su **Submit** (Invia).

    La selezione viene inviata e si torna al portale app.

    Se si vuole modificare la risposta, aprire nuovamente la pagina delle verifiche di accesso e aggiornare la risposta. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    > [!NOTE]
    > Se è stato indicato che non è più necessario l'accesso, l'utente non viene rimosso immediatamente. L'utente viene rimosso al termine della verifica o quando un amministratore interrompe la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
