---
title: Creare ed eliminare processi nel cluster di Analisi di flusso di Azure
description: Informazioni su come gestire i processi di Analisi di flusso in un cluster di Analisi di flusso di Azure
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018123"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Aggiungere e rimuovere processi in un cluster di Analisi di flusso di Azure

In un cluster di Analisi di flusso è possibile eseguire più processi di Analisi di flusso di Azure. L'esecuzione di processi in un cluster consiste in due semplici passaggi: aggiungere il processo al cluster e avviarlo. Questo articolo illustra come aggiungere e rimuovere processi da un cluster esistente. Seguire la guida di avvio rapido per [creare un cluster di Analisi di flusso](create-cluster.md) se non ne è già presente uno.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Aggiungere un processo di Analisi di flusso a un cluster

È possibile aggiungere ai cluster solo processi di Analisi di flusso esistenti. Seguire la guida di avvio rapido per [informazioni su come creare un processo](stream-analytics-quick-create-portal.md) tramite il portale di Azure. Dopo aver creato un processo da aggiungere a un cluster, seguire questa procedura.

1. Nel portale di Azure individuare e selezionare il cluster di Analisi di flusso.

1. In **Impostazioni** selezionare **Processo di Analisi di flusso**. Quindi selezionare **Aggiungi processo esistente**.

1. Selezionare la sottoscrizione e il processo di Analisi di flusso da aggiungere al cluster. È possibile aggiungere al cluster solo i processi di Analisi di flusso che si trovano nella stessa area del cluster.

   ![Aggiungere il processo al cluster](./media/manage-jobs-cluster/add-job.png)

1. Dopo aver aggiunto il processo al cluster, passare alla risorsa processo e [avviare il processo](start-job.md#azure-portal). Il processo verrà quindi avviato per l'esecuzione nel cluster.

È possibile eseguire tutte le altre operazioni, ad esempio monitoraggio, invio di avvisi e log di diagnostica, nella pagina della risorsa processo di Analisi di flusso.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Rimuovere un processo di Analisi di flusso da un cluster

Per rimuoverli dal cluster, è necessario che i processi di Analisi di flusso siano in uno stato interrotto. Se il processo è ancora in esecuzione, arrestarlo prima di procedere con i passaggi seguenti.

1. Individuare e selezionare il cluster di Analisi di flusso.

1. In **Impostazioni** selezionare **Processo di Analisi di flusso**.

1. Selezionare i processi da rimuovere dal cluster, quindi selezionare **Rimuovi**.

   ![Rimuovere un processo dal cluster](./media/manage-jobs-cluster/remove-job.png)

   I processi rimossi da un cluster di Analisi di flusso tornano nell'ambiente multi-tenant standard.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile aggiungere e rimuovere processi nel cluster di Analisi di flusso di Azure. Successivamente, acquisire informazioni su come gestire gli endpoint privati e dimensionare i cluster:

* [Dimensionare un cluster di Analisi di flusso di Azure](scale-cluster.md)
* [Gestire gli endpoint privati in un cluster di Analisi di flusso di Azure](private-endpoints.md)
