---
title: Accedere e gestire i file in Microsoft OneDrive
description: Caricare e gestire i file in OneDrive creando flussi di lavoro automatizzati in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040234"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Accedere e gestire i file in OneDrive Connector usando app per la logica di Azure

Usando app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il [connettore OneDrive](/connectors/onedriveconnector/), è possibile creare attività e flussi di lavoro automatizzati per gestire i file, ad esempio caricare, ottenere, eliminare file e altro ancora. Con OneDrive è possibile eseguire queste attività:

* Creare un flusso di lavoro mediante l'archiviazione di file in OneDrive o aggiornare i file esistenti in OneDrive. 
* Usare trigger per avviare il flusso di lavoro quando un file viene creato o aggiornato in OneDrive.
* Usare le azioni per creare un file, eliminarlo e così via. Ad esempio, creare un nuovo file in OneDrive (azione) quando viene ricevuto un nuovo messaggio di posta elettronica di Office 365 con un allegato (trigger).

Questo articolo illustra come usare il connettore OneDrive in un'app per la logica ed elenca i trigger e le azioni.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-overview.md) e [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Connettersi a OneDrive

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a OneDrive, è necessaria prima di tutto una *connessione* a OneDrive. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Quindi, per creare la connessione a OneDrive, immettere le credenziali dell'account OneDrive.

### <a name="create-the-connection"></a>Creare la connessione

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Usare un trigger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Nella finestra di progettazione dell'app per la logica digitare `onedrive` per ottenere un elenco dei trigger:  

   ![Una finestra di dialogo intitolata "Mostra Microsoft Managed A P I" contiene una casella contenente "OneDrive". Di seguito è riportato un elenco di quattro trigger. Il primo di questi è "OneDrive-quando viene creato un file". Il secondo, "OneDrive-when a file is modified", è stato selezionato.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selezionare **Quando viene modificato un file**. Se esiste già una connessione, selezionare il pulsante Mostra selezione per selezionare una cartella.

   ![Una finestra di dialogo intitolata "quando un file viene modificato" contiene una casella denominata "cartella" con un pulsante Sfoglia associato.](./media/connectors-create-api-onedrive/sample-folder.png)

   Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo articolo elenca i passaggi necessari.

   In questo esempio l'app per la logica viene eseguita quando viene aggiornato un file nella cartella scelta. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un messaggio di posta elettronica al proprio indirizzo. Ad esempio, aggiungere l'azione di Office 365 Outlook *Invia un messaggio di posta elettronica* per ricevere un messaggio di posta elettronica quando un file viene aggiornato.

3. Selezionare il pulsante **Modifica** e impostare i valori **Frequenza** e **Intervallo**. Ad esempio, se si vuole che il trigger esegua il poll ogni 15 minuti, impostare **Frequenza** su **Minuto** e **Intervallo** su **15**. 

   ![Una finestra di dialogo intitolata "quando un file viene modificato" Mostra cinque caselle con etichetta: "cartella", "frequenza", "intervallo", "fuso orario" e "ora di inizio". Sono disponibili elenchi a discesa per i campi "FREQUENCY" e "TIME ZONE".](./media/connectors-create-api-onedrive/trigger-properties.png)

4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="use-an-action"></a>Usare un'azione

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.

   ![Una schermata mostra quattro pulsanti: "+ nuovo passaggio", "Aggiungi un'azione", "Aggiungi una condizione" e "... Altro ".](./media/connectors-create-api-onedrive/add-action.png)

2. Selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca digitare `onedrive` per ottenere un elenco di tutte le azioni disponibili.

   ![Una finestra di dialogo intitolata "Mostra Microsoft Managed A P I" contiene una casella contenente "OneDrive". Di seguito è riportato un elenco di otto azioni. Il primo è "OneDrive-create file" ed è selezionato.](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. Nell'esempio scegliere **OneDrive - Crea file**. Se esiste già una connessione, selezionare il **percorso della cartella** in cui inserire il file, immettere il **nome del file** e scegliere il **contenuto del file** desiderato:  

   ![Una finestra di dialogo intitolata "Crea file" Mostra tre caselle con etichetta "percorso cartella", "Nome FILE" e "contenuto cartella". Accanto alla casella "percorso cartella" è presente un pulsante Sfoglia directory.](./media/connectors-create-api-onedrive/sample-action.png)

   Se vengono richieste le informazioni di connessione, immettere i dettagli per [creare la connessione, come descritto](#create-the-connection) in questo argomento.

   In questo esempio viene creato un nuovo file in una cartella OneDrive. Per creare il file di OneDrive è possibile usare l'output di un altro trigger. Ad esempio aggiungere il trigger di Office 365 Outlook *All'arrivo di un nuovo messaggio di posta elettronica*. Quindi aggiungere l'azione di OneDrive *Crea file* che usa i campi Allegati e Content-Type in un ciclo ForEach per creare il nuovo file in OneDrive.

   ![Una finestra di dialogo intitolata "per ogni" ha una casella con etichetta "selezionare un OUTPUT dai passaggi precedenti" che contiene "allegati". È presente una finestra di dialogo "Crea file" che copre il resto della casella "per ogni", con caselle con etichetta "percorso cartella", "Nome FILE" e "contenuto FILE". ](./media/connectors-create-api-onedrive/foreach-action.png)

5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Passaggi successivi

* [Connettori per App per la logica di Azure](apis-list.md)