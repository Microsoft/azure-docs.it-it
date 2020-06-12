---
title: Connettersi a Project Online da App per la logica di Azure
description: Automatizzare i flussi di lavoro per il monitoraggio, la creazione e la gestione di progetti, attività e risorse di Project Online con App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834867"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gestire progetti, attività e risorse di Project Online con App per la logica di Azure

Con App per la logica di Azure e il connettore Project Online, è possibile creare attività e flussi di lavoro automatizzati per progetti, attività e risorse in Project Online con Office 365. I flussi di lavoro possono eseguire queste e altre azioni, ad esempio:

* Eseguire il monitoraggio quando vengono creati nuovi progetti, attività o risorse. In alternativa, è possibile eseguire il monitoraggio quando vengono pubblicati nuovi progetti.
* Creare nuovi progetti, attività o risorse.
* Elencare progetti o attività esistenti.
* Estrarre, archiviare o pubblicare progetti.

Project Online consente di pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio progetti praticamente ovunque e da qualsiasi dispositivo, offrendo potenti funzionalità per la gestione dei progetti. È possibile usare i trigger di Project Online per ottenere risposte da Project Online e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire diverse attività in Project Online. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Project Online, disponibile tramite un account [Office 365](https://www.office.com/). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere ai dati di Project Online. Per iniziare con un trigger di Project Online, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare le azioni di Project Online, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-project-online"></a>Connettersi a Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "Project Online" come filtro. 
   Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti, nel passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. Nella casella di ricerca immettere "Project Online" come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

1. Se viene chiesto di accedere a Project Online, effettuare l'accesso.

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione a Project Online e all'accesso ai dati.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/projectonline/) del connettore.

## <a name="get-support"></a>Supporto

* Per domande, visitare la [pagina Microsoft delle domande e risposte per le App per la logica di Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)