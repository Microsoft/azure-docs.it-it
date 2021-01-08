---
title: Visualizzare i processi di Analisi di flusso di Azure in Visual Studio
description: Informazioni su come visualizzare, avviare e arrestare, testare le connessioni, controllare i risultati ed esportare i processi di analisi di flusso di Azure con Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019126"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure

Gli strumenti di Analisi di flusso di Azure per Visual Studio consentono agli sviluppatori di gestire con facilità i processi di Analisi di flusso direttamente dall'ambiente di sviluppo integrato. Con gli strumenti Analisi di flusso di Azure è possibile:
- [Creare nuovi processi](stream-analytics-quick-create-vs.md)
- Avviare, arrestare e [monitorare processi](stream-analytics-monitor-jobs-use-vs.md)
- Verificare i risultati di un processo
- Esportare i processi esistenti in un progetto
- Testare le connessioni di input e output
- [Eseguire query in locale](stream-analytics-vs-tools-local-run.md)

Vedere le istruzioni per [installare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Esplorare la visualizzazione del processo

È possibile usare la visualizzazione del processo per interagire con i processi di Analisi di flusso di Azure da Visual Studio.

### <a name="open-the-job-view"></a>Aprire la visualizzazione del processo

1. In **Esplora server** selezionare **Processi di Analisi di flusso** e quindi **Aggiorna**. Il processo viene visualizzato in **Processi di Analisi di flusso**.

    ![Elenco di Esplora server per Analisi di flusso](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Espandere il nodo del processo e fare doppio clic sul nodo **Vista processi** per aprire la visualizzazione del processo.
    
   ![Nodo del processo espanso](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Avviare e arrestare processi

I processi di Analisi di flusso di Azure possono essere gestiti completamente dalla visualizzazione del processo in Visual Studio. Usare i controlli per avviare, arrestare o eliminare un processo.
    
   ![Controlli dei processi di Analisi di flusso](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Verificare i risultati di un processo

Gli strumenti di Analisi di flusso per Visual Studio supportano attualmente l'anteprima di output per l'archiviazione BLOB e Azure Data Lake Storage. Per visualizzare i risultati, è sufficiente fare doppio clic sul nodo di output del diagramma del processo nella **visualizzazione del processo** e immettere le credenziali appropriate.

   ![Output di archiviazione BLOB del processo di Analisi di flusso](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Esportare i processi in un progetto

Esistono due modi per esportare un processo esistente in un progetto.

1. In **Esplora server**, nel nodo Processi di Analisi di flusso, fare clic con il pulsante destro del mouse sul nodo del processo. Selezionare **Esporta in un nuovo progetto di analisi di flusso**.
    
   ![Esportare il processo in un progetto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Il progetto generato viene visualizzato in **Esplora soluzioni**.
    
   ![Esplora soluzioni](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Nella vista processi selezionare **Genera progetto**.
    
   ![Generare il progetto dalla visualizzazione del processo](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testare le connessioni

È possibile testare le connessioni di input e output dalla **visualizzazione del processo** selezionando un'opzione dall'elenco a discesa **Test connessione**.

   ![Elenco a discesa Test connessione](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

I risultati di **Test connessione** verranno visualizzati nella finestra **Output**.

   ![Risultati di Test connessione](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire i processi di Analisi di flusso di Azure con Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Eseguire integrazione e sviluppo in modo continuo con gli strumenti di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md)
