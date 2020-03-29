---
title: Errore durante l'aggiunta di un'applicazione della raccolta di Azure AD| Microsoft Docs
description: Informazioni sui problemi frequenti che si riscontrano quando si aggiungono applicazioni della raccolta di Azure AD e sulle azioni da eseguire per risolverli
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784436"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Errore durante l'aggiunta di un'applicazione della raccolta di Azure AD

Questo articolo illustra i problemi frequenti che si riscontrano durante l'aggiunta di applicazioni della raccolta di Azure AD e le operazioni da eseguire per risolverli.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Quando si fa clic sul pulsante "Aggiungi" trascorre molto tempo prima che l'applicazione venga visualizzata

In alcuni casi, potrebbero trascorrere uno o due minuti e a volte di più prima che l'applicazione venga visualizzata dopo essere stata aggiunta alla directory. Sebbene non si tratti delle normali prestazioni previste, è possibile visualizzare l'aggiunta dell'applicazione in corso facendo clic sull'icona **Notifiche** (la campanella) nella parte superiore destra del [portale di Azure](https://portal.azure.com/) e cercando una notifica con stato **In corso** o **Completato** con l'etichetta **Aggiunta dell'applicazione**.

Se l'applicazione non viene mai aggiunta o si verifica un errore quando si fa clic sul pulsante **Aggiungi**, viene visualizzata una **Notifica** con stato **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Quando si fa clic sul pulsante "Aggiungi" l'applicazione non viene visualizzata

In alcuni casi, a causa di problemi temporanei, problemi di rete o un bug, l'aggiunta di un'applicazione ha esito negativo. È possibile verificare il problema facendo clic sull'icona **Notifiche** (la campanella) in alto a destra nel portale di Azure. Accanto alla notifica **Aggiunta dell'applicazione** viene visualizzato un punto esclamativo (!) rosso. Questa icona indica che si è verificato un errore durante la creazione dell'applicazione.

Se si verifica un errore quando si fa clic sul pulsante **Aggiungi**, viene visualizzata una **Notifica** con stato **Errore**. Se si desidera visualizzare altri dettagli sull'errore per saperne di più o per effettuarne la condivisione con un tecnico del supporto, è possibile visualizzare altre informazioni sull'errore seguendo i passaggi illustrati nella sezione [Come visualizzare i dettagli di una notifica del portale](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Non si sa come configurare l'applicazione dopo averla aggiunta

Se hai bisogno di aiuto per conoscere le applicazioni, [l'elenco di esercitazioni su come integrare le app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) è un buon punto di partenza.

Vedere anche la [raccolta di documenti sulle applicazioni di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) che include informazioni sull'accesso Single Sign-On con Azure AD e ne illustra il funzionamento.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Come visualizzare i dettagli di una notifica del portale

È possibile visualizzare i dettagli di qualsiasi notifica del portale seguendo questa procedura:

1.  Selezionare l'icona Notifiche (la campana) in alto a destra nel portale di AzureSelect the **Notifications** icon (the bell) in the upper right of the Azure portal

2.  Selezionare una notifica con stato **Errore** contrassegnata con un punto esclamativo (!) rosso.

    >[!NOTE]
    >Non è possibile fare clic sulle notifiche con stato **Operazione completata** o **In corso**.
    >
    >

4.  Usare le informazioni in **Dettagli notifica**per ottenere maggiori dettagli sul problema.

5.  In caso sia ancora necessaria assistenza sul problema, è possibile condividere queste informazioni con un tecnico di supporto o con il gruppo del prodotto.

6.  Fare clic **sull'icona** di **copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli della notifica da condividere con un supporto o un tecnico del gruppo di prodotti.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Come ottenere assistenza inviando i dettagli di notifica a un tecnico del supporto

È molto importante condividere **tutti i dettagli elencati di seguito** con un tecnico del supporto per poter ricevere assistenza immediata. A tale scopo, è possibile **acquisire uno screenshot** o fare clic sull'**icona Copia errore** che si trova a destra della casella di testo **Copia errore**.

## <a name="notification-details-explained"></a>Spiegazione dei dettagli della notifica

Vedere le descrizioni seguenti per altri dettagli sulle notifiche.

### <a name="essential-notification-items"></a>Elementi essenziali della notifica

- **Titolo**: il titolo descrittivo della notifica

  * Esempio: **impostazioni proxy dell'applicazione**

- **Descrizione**: la descrizione di ciò che si è verificato a seguito dell'operazione

  -   Esempio: **L'URL interno immesso è già usato da un'altra applicazione**

- **ID notifica**: ID univoco della notifica

  -   Esempio: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID richiesta client**: ID specifico della richiesta creato dal browser

  -   Esempio: **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Timestamp (UTC)**: il timestamp in cui si è verificata la notifica, basato sul sistema UTC

  -   Esempio: **2017-03-23T19:50:43.7583681Z**

- **ID transazione interno** – l'ID interno che possiamo utilizzare per cercare l'errore nei nostri sistemi

  -   Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN **: l'utente che ha eseguito l'operazione

  -   Esempio – **tperkins\@f128.info**

- **ID tenant**: ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione

  -   Esempio: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ID oggetto utente**: ID univoco dell'utente che ha eseguito l'operazione

  -   Esempio: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementi della notifica dettagliati

-   **Nome visualizzato**: **(può essere vuoto)** un nome visualizzato più dettagliato per l'errore

    -   Esempio: **impostazioni proxy dell'applicazione**

-   **Stato**: lo stato specifico della notifica

    -   Esempio – **Non riuscitoExample** – Failed

-   **ID oggetto**: **(può essere vuoto)** ID dell'oggetto su cui è stata eseguita l'operazione

    -   Esempio: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Dettagli**: la descrizione dettagliata di ciò che si è verificato come conseguenza dell'operazione

    -   Esempio: **l'URL `https://bing.com/` interno non è valido perché è già in uso**

-   **Copia errore**: fare clic sull'**icona Copia** a destra della casella di testo **Copia errore** per copiare tutti i dettagli della notifica da condividere con un tecnico del supporto o del gruppo di 
-   engineer

    -   Esempio```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

