---
title: Visualizzare i dati con Cartelle di lavoro di Monitoraggio di Azure in Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per informazioni su come visualizzare i dati con le cartelle di lavoro in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491481"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Esercitazione: Visualizzare e monitorare i dati

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è possibile visualizzare e monitorare i dati usando l'adozione di Azure Sentinel delle cartelle di lavoro di monitoraggio di Azure, che offre versatilità nella creazione di dashboard personalizzati. Anche se le cartelle di lavoro sono visualizzate in modo diverso in Azure Sentinel, può essere utile vedere come [creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel consente di creare cartelle di lavoro personalizzate tra i dati e include anche modelli di cartella di lavoro predefiniti per ottenere rapidamente informazioni dettagliate sui dati non appena si connette un'origine dati.

Questa esercitazione consente di visualizzare i dati in Azure Sentinel.
> [!div class="checklist"]
> * Usare cartelle di lavoro predefinite
> * Creare nuove cartelle di lavoro

## <a name="prerequisites"></a>Prerequisiti

Per il gruppo di risorse dell'area di lavoro di Azure Sentinel è necessario disporre almeno delle autorizzazioni di **collaboratore** **Reader** o cartella di lavoro.

> [!NOTE]
> Le cartelle di lavoro che è possibile visualizzare in Azure Sentinel vengono salvate nel gruppo di risorse dell'area di lavoro di Azure Sentinel e sono contrassegnate dall'area di lavoro in cui sono state create.

## <a name="use-built-in-workbooks"></a>Usare cartelle di lavoro predefinite

1. Passare a **Cartelle di lavoro** e quindi selezionare **Modelli** per visualizzare l'elenco completo delle cartelle di lavoro predefinite di Azure Sentinel. 

    Per sapere quali sono pertinenti per i tipi di dati connessi, il campo **Tipi di dati obbligatori** in ogni cartella di lavoro elenca il tipo di dati accanto a un segno di spunta verde se i dati rilevanti sono già stati trasmessi ad Azure Sentinel.

    [![Passare a cartelle di lavoro. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Selezionare **Visualizza modello** per visualizzare il modello popolato con i dati.

1. Per modificare la cartella di lavoro, selezionare **Save (Salva**) e quindi selezionare il percorso in cui si vuole salvare il file JSON per il modello.

   > [!NOTE]
   > In questo modo si crea una risorsa di Azure basata sul modello pertinente e si salva il file JSON della cartella di lavoro e non i dati.


1. Selezionare **Visualizza cartella di lavoro salvata**. 

    [![Visualizzare le cartelle di lavoro. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Selezionare il pulsante **modifica** nella barra degli strumenti della cartella di lavoro per personalizzare la cartella di lavoro in base alle esigenze. Al termine, selezionare **Salva** per salvare le modifiche.

    Per altre informazioni, vedere come [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Per clonare la cartella di lavoro, selezionare **modifica** e quindi **Salva** con nome, assicurandosi di salvarla con un altro nome, nella stessa sottoscrizione e nello stesso gruppo di risorse.
> Le cartelle di lavoro clonate vengono visualizzate nella scheda **cartelle di lavoro** .
>
## <a name="create-new-workbook"></a>Creare una nuova cartella di lavoro

1. Per creare una nuova cartella di lavoro, passare a **Cartelle di lavoro** e quindi selezionare **Aggiungi la cartella di lavoro**.

    [![Nuova cartella di lavoro. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Per modificare la cartella di lavoro, selezionare **Modifica** e quindi aggiungere testo, query e parametri, se necessari. Per altre informazioni su come personalizzare la cartella di lavoro, vedere [Creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md). 

1. Quando si compila una query, assicurarsi che l' **origine dati** sia impostata su **logs** e **tipo di risorsa** sia impostato su **log Analytics**, quindi scegliere le aree di lavoro pertinenti. 

1. Dopo aver creato la cartella di lavoro, salvare la cartella di lavoro, assicurandosi di salvarla nella sottoscrizione e nel gruppo di risorse dell'area di lavoro di Azure Sentinel.

1. Se si vuole consentire ad altri utenti dell'organizzazione di usare la cartella di lavoro, in **Salva in** selezionare **Report condivisi**. Se si vuole che la cartella di lavoro non sia disponibile per altri utenti, selezionare **Report personali**.

1. Per passare da una cartella di lavoro all'altra nell'area di lavoro, selezionare **Apri** ![ icona per aprire una cartella di lavoro.](./media/tutorial-monitor-data/switch.png) sulla barra degli strumenti di qualsiasi cartella di lavoro. La schermata passa a un elenco di altre cartelle di lavoro a cui è possibile passare.

    Selezionare la cartella di lavoro che si desidera aprire:

    [![Cambiare le cartelle di lavoro. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Aggiornare i dati della cartella di lavoro

Aggiornare la cartella di lavoro per visualizzare i dati aggiornati. Nella barra degli strumenti selezionare una delle opzioni seguenti:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::**Aggiornare** per aggiornare manualmente i dati della cartella di lavoro.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Aggiornamento automatico** per impostare la cartella di lavoro per l'aggiornamento automatico in un intervallo configurato.

    - Gli intervalli di aggiornamento automatico supportati sono compresi tra **5 minuti** e **1 giorno**.

    - L'aggiornamento automatico viene sospeso durante la modifica di una cartella di lavoro e gli intervalli vengono riavviati ogni volta che si passa di nuovo alla modalità di visualizzazione dalla modalità di modifica.

    - Gli intervalli di aggiornamento automatico vengono riavviati anche se si aggiornano manualmente i dati.

    > [!TIP]
    > Per impostazione predefinita, l'aggiornamento automatico è disattivato. Per ottimizzare le prestazioni, l'aggiornamento automatico viene disattivato ogni volta che si chiude una cartella di lavoro e non viene eseguita in background. Riattivare l'aggiornamento automatico in base alle esigenze alla successiva apertura della cartella di lavoro.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Stampare una cartella di lavoro o salvarla come PDF

Per stampare una cartella di lavoro o salvarla in formato PDF, usare il menu opzioni a destra del titolo della cartella di lavoro.

1. Selezionare opzioni > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **stampa contenuto**. 
2. Nella schermata di stampa modificare le impostazioni di stampa in base alle esigenze oppure selezionare **Salva con nome PDF** per salvarlo in locale.

Ad esempio:

[![Stampare la cartella di lavoro o salvarla in formato PDF. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Come eliminare le cartelle di lavoro

Per eliminare una cartella di lavoro salvata (un modello salvato o una cartella di lavoro personalizzata), nella pagina cartelle di lavoro selezionare la cartella di lavoro salvata che si desidera eliminare e selezionare **Elimina**. La cartella di lavoro salvata verrà rimossa.

> [!NOTE]
> In questo modo viene rimossa la risorsa cartella di lavoro e tutte le modifiche apportate al modello. mentre il modello originale rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come visualizzare i dati in Azure Sentinel, usando le cartelle di lavoro di Azure.

Per informazioni su come automatizzare le risposte alle minacce, vedere [Configurare le risposte automatiche alle minacce in Azure Sentinel](tutorial-respond-threats-playbook.md).
