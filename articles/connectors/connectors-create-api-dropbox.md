---
title: Connettersi a Dropbox
description: Automatizzare le attività e i flussi di lavoro che caricano e gestiscono i file in Dropbox usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665752"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Caricare e gestire i file in Dropbox usando app per la logica di Azure

Con il connettore Dropbox e le app per la logica di Azure, è possibile creare flussi di lavoro automatizzati che consentono di caricare e gestire i file nell'account Dropbox. 

Questo articolo illustra come connettersi a Dropbox dall'app per la logica e quindi aggiungere il trigger Dropbox **quando viene creato un file** e il contenuto di Dropbox **get file usando il percorso** .

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account Dropbox](https://www.dropbox.com/), che è possibile iscriversi gratuitamente. Le credenziali dell'account sono necessarie per creare una connessione tra l'app per la logica e l'account Dropbox.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per questo esempio, è necessaria un'app per la logica vuota.

## <a name="add-trigger"></a>Aggiunta di trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Nell'elenco di trigger selezionare questo trigger: **When a file is created** (Quando viene creato un file)

   ![Selezionare un trigger Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Accedere con le credenziali dell'account Dropbox e autorizzare l'accesso ai dati di Dropbox delle App per la logica di Azure.

1. Specificare le informazioni richieste per il trigger. 

   In questo esempio, selezionare la cartella in cui si vuole tenere traccia della creazione di file. Per esplorare le cartelle, scegliere l'icona della cartella accanto alla casella **cartella** .

## <a name="add-action"></a>Aggiungere un'azione

Aggiungere ora un'azione che ottiene il contenuto da un nuovo file.

1. Nel trigger scegliere **Passaggio successivo**. 

1. Nella casella di ricerca scegliere **Tutti**. Nella casella di ricerca immettere "dropbox" come filtro.
Dall'elenco delle azioni selezionare questa azione: **ottenere il contenuto del file tramite il percorso**

1. Se non sono già state autorizzate app per la logica di Azure per accedere a Dropbox, autorizzare l'accesso ora.

1. Per passare al percorso del file che si vuole usare, accanto alla casella **percorso file** , scegliere il pulsante con i puntini di sospensione (**...**). 

   È anche possibile fare clic all'interno della casella **percorso file** e nell'elenco contenuto dinamico selezionare **percorso file**, il cui valore è disponibile come output del trigger aggiunto nella sezione precedente.

1. Al termine, salvare l'app per la logica.

1. Per attivare l'app per la logica, creare un nuovo file in Dropbox.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/dropbox/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
