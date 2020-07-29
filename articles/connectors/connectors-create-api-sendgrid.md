---
title: Connettersi a SendGrid da App per la logica di Azure
description: Automatizzare le attività e i flussi di lavoro per l'invio di messaggi di posta elettronica e la gestione delle liste di distribuzione in SendGrid con App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290436"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Inviare messaggi di posta elettronica e gestire liste di distribuzione in SendGrid con App per la logica di Azure

Con App per la logica di Azure e il connettore SendGrid, è possibile creare attività e flussi di lavoro automatizzati per l'invio di messaggi di posta elettronica e la gestione degli elenchi di destinatari, ad esempio:

* Inviare posta elettronica.
* Aggiungere destinatari alle liste.
* Ottenere, aggiungere e gestire l'eliminazione globale.

È possibile usare azioni di SendGrid nelle app per la logica per eseguire queste attività. È anche possibile fare in modo che altre azioni usino l'output delle azioni di SendGrid. 

Questo connettore offre solo azioni, quindi per avviare l'app per la logica, usare un trigger distinto, ad esempio un trigger **Ricorrenza**. Se ad esempio si aggiungono regolarmente destinatari alle liste, è possibile inviare messaggi di posta elettronica su destinatari e liste usando il connettore Office 365 Outlook o Outlook.com.
Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Un [account SendGrid](https://www.sendgrid.com/) e una [chiave API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/).

   La chiave API autorizza l'app per la logica alla creazione di una connessione e all'accesso all'account SendGrid.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SendGrid. Per usare un'azione di SendGrid, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

## <a name="connect-to-sendgrid"></a>Connettersi a SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

     -oppure-

   * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
   Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "sendgrid" come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

1. Specificare un nome per la connessione. 

1. Immettere la chiave API SendGrid e quindi scegliere **Crea**.

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/sendgrid/) del connettore.

## <a name="get-support"></a>Supporto

* Per eventuali domande, visitare la [pagina Microsoft delle domande e risposte per App per la logica di Azure](/answers/topics/azure-logic-apps.html).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
