---
title: Configurare notifiche e modelli di posta elettronica
titleSuffix: Azure API Management
description: Informazioni su come configurare notifiche e modelli di posta elettronica in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690303"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Come configurare notifiche e modelli di posta elettronica in Gestione API di Azure

Gestione API consente di configurare notifiche per eventi specifici nonché di configurare modelli di posta elettronica da usare per comunicare con gli amministratori e gli sviluppatori di un'istanza di Gestione API. In questo articolo viene illustrato come configurare notifiche per gli eventi disponibili e viene offerta una panoramica sulla configurazione di modelli di posta elettronica usati per tali eventi.

## <a name="prerequisites"></a>Prerequisiti

Se non si dispone di un'istanza del servizio Gestione API, completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Configurare le notifiche

1.  Selezionare l'istanza di **gestione API** .
2.  Fare clic su **Notifiche** per visualizzare le notifiche disponibili.

    ![Notifiche dell'editore][api-management-publisher-notifications]

    L'elenco di eventi seguente può essere configurato per le notifiche.

    -   **Richieste di sottoscrizione (che richiedono approvazione)** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica sulle richieste di sottoscrizione per prodotti API che richiedono approvazione.
    -   **Nuove sottoscrizioni** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica sulle nuove sottoscrizioni a prodotti API.
    -   **Richieste relative alla raccolta di applicazioni** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando nuove applicazioni vengono inviate alla raccolta di applicazioni.
    -   **BCC** - I destinatari e gli utenti di posta elettronica specificati riceveranno copie per conoscenza nascosta di tutti i messaggi di posta elettronica inviati agli sviluppatori.
    -   **Nuovo problema o commento** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando un nuovo problema o commento viene inviato al portale per sviluppatori.
    -   **Messaggio di chiusura account** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando un account viene chiuso.
    -   **Raggiungimento limite quota sottoscrizione** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando l'uso della sottoscrizione si avvicina al limite di quota di utilizzo.

        > [!NOTE]
        > Le notifiche vengono attivate solo dalla [quota per criterio di sottoscrizione](api-management-access-restriction-policies.md#SetUsageQuota) . La [quota per criterio chiave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) non genera notifiche.

    Per ogni evento, è possibile specificare i destinatari di posta elettronica usando la casella di testo per l'indirizzo di posta elettronica oppure è possibile selezionare gli utenti da un elenco.

3.  Per specificare gli indirizzi di posta elettronica a cui inviare le notifiche, immettere tali indirizzi nella casella di testo relativa all'indirizzo di posta elettronica. Se ci sono più indirizzi di posta elettronica, separarli usando delle virgole.

    ![Destinatari delle notifiche][api-management-email-addresses]

4.  Premere **Aggiungi**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Configurare i modelli di notifica

Gestione API fornisce modelli di notifica per i messaggi di posta elettronica che vengono inviati durante l'amministrazione e l'uso del servizio. Sono forniti i modelli di posta elettronica indicati di seguito.

-   Invio alla raccolta di applicazioni approvato
-   Lettera di commiato sviluppatore
-   Notifica raggiungimento limite quota sviluppatore
-   Invito utente
-   Nuovo commento aggiunto a problema
-   Nuovo problema ricevuto
-   Nuova sottoscrizione attivata
-   Conferma rinnovo sottoscrizione
-   Richiesta sottoscrizione rifiutata
-   Richiesta sottoscrizione ricevuta

Questi modelli possono essere modificati a piacimento.

Per visualizzare e configurare i modelli di messaggio di posta elettronica per l'istanza di Gestione API, fare clic su **Modelli di notifica**.

![Modelli di messaggi di posta][api-management-email-templates]

Ogni modello di posta elettronica ha un oggetto in testo normale e una definizione di corpo in formato HTML. Ogni elemento può essere personalizzato a piacimento.

![Editor dei modelli di posta elettronica][api-management-email-template]

L'elenco **Parametri** contiene un elenco di parametri che, quando inserito nell'oggetto o nel corpo, verrà sostituito dal valore designato quando viene inviato il messaggio di posta elettronica. Per inserire un parametro, posizionare il cursore dove si vuole inserire il parametro, quindi fare clic sulla freccia a sinistra del nome del parametro.

> [!NOTE]
> I parametri non vengono sostituiti con valori effettivi quando si visualizzano anteprime o si inviano messaggi di prova.

Per salvare le modifiche apportate al modello di posta elettronica, fare clic su **Salva** oppure, per annullare le modifiche, fare clic su **Ignora**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
