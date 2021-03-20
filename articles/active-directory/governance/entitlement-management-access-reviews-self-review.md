---
title: Revisione automatica di un pacchetto di accesso in Azure AD gestione dei diritti
description: Informazioni su come verificare l'accesso degli utenti ai pacchetti di accesso alla gestione dei diritti in Azure Active Directory le verifiche di accesso (anteprima).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798444"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Revisione automatica di un pacchetto di accesso in Azure AD gestione dei diritti

Azure AD gestione dei diritti semplifica il modo in cui le aziende gestiscono l'accesso a gruppi, applicazioni e siti di SharePoint. Questo articolo descrive come un utente esegue una verifica automatica dei pacchetti di accesso assegnati.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Per eseguire una verifica di accesso, è innanzitutto necessario aprire la verifica di accesso. Usare la procedura seguente per trovare e aprire la verifica di accesso:

1. Il licenziatario potrà ricevere un messaggio di posta elettronica da Microsoft che richiede di verificare l'accesso. Individuare il messaggio di posta elettronica per aprire la verifica di accesso. Di seguito è riportato un esempio di un messaggio di posta elettronica che richiede una verifica dell'accesso: 
    
    ![Indirizzo di posta elettronica del revisore della verifica di accesso](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Fare clic sul collegamento **Verifica accesso** .

1. https://myaccess.microsoft.comSe non si riceve un messaggio di posta elettronica, è anche possibile passare direttamente a per trovare le verifiche di accesso in sospeso.  Per il governo degli Stati Uniti, usare `https://myaccess.microsoft.us` invece.

1. Fare clic su verifiche di **accesso** sulla barra di spostamento a sinistra per visualizzare un elenco delle verifiche di accesso in sospeso assegnate all'utente.


1.  Fare clic sulla revisione che si desidera avviare.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, è possibile visualizzare l'accesso. Per eseguire la verifica di accesso, attenersi alla procedura seguente:

1.  Decidere se è ancora necessario accedere al pacchetto di accesso. Ad esempio, il progetto su cui si sta lavorando non è completo, quindi è ancora necessario l'accesso per continuare a lavorare sul progetto.

1.  Fare clic su **Sì** per Mantenete l'accesso oppure su **No** per rimuovere l'accesso.
    >[!NOTE]
    >Se è stato dichiarato che l'accesso non è più necessario, non viene rimosso immediatamente dal pacchetto di accesso. Si verrà rimossi dal pacchetto di accesso al termine della verifica o se un amministratore interrompe la revisione.

1.  Se si fa clic su **Sì**, potrebbe essere necessario includere un'istruzione di giustificazione nella casella **motivo** .

1.  Fare clic su **Submit** (Invia).

È possibile tornare alla verifica se si cambia idea e si decide di modificare la risposta prima della fine della verifica.

## <a name="next-steps"></a>Passaggi successivi

- [Controllare l'accesso ai pacchetti di Access](entitlement-management-access-reviews-review-access.md) 
