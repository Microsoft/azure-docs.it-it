---
title: Sviluppo e debug iterativi in Azure Data Factory
description: Informazioni su come sviluppare ed eseguire il debug di pipeline di Data Factory in modo iterativo nell'UX di ADF
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712964"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Sviluppo e debug iterativi con Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory consente di sviluppare ed eseguire il debug in modo iterativo di pipeline Data Factory durante lo sviluppo di soluzioni di integrazione dei dati. Queste funzionalità consentono di testare le modifiche prima di creare una richiesta pull o di pubblicarle nel servizio data factory. 

Per un'introduzione di otto minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Debug di una pipeline

Quando si crea usando l'area di disegno della pipeline, è possibile testare le attività usando la funzionalità di **debug** . Durante un'esecuzione dei test, non è necessario pubblicare le modifiche in Data Factory prima di selezionare **Debug**. Questa funzione risulta utile negli scenari in cui ci si vuole assicurare che le modifiche funzionino come previsto prima di aggiornare il flusso di lavoro di Data Factory.

![Funzionalità di debug nel canvas della pipeline](media/iterative-development-debugging/iterative-development-1.png)

Quando la pipeline è in esecuzione, è possibile visualizzare i risultati di ogni attività nella scheda **output** dell'area di disegno della pipeline.

Visualizzare i risultati dell'esecuzione dei test nella finestra **Output** del canvas della pipeline.

![Finestra Output del canvas della pipeline](media/iterative-development-debugging/iterative-development-2.png)

Quando un'esecuzione dei test ha esito positivo, aggiungere altre attività alla pipeline e continuare il debug in modo iterativo. È anche possibile **annullare** un'esecuzione dei test in corso.

> [!IMPORTANT]
> Selezionando **Debug** la pipeline viene eseguita. Se, ad esempio, la pipeline contiene un'attività di copia, l'esecuzione dei test copia i dati dall'origine alla destinazione. Di conseguenza, durante il debug è consigliabile usare cartelle di test nelle attività di copia e in altre attività. Dopo aver eseguito il debug della pipeline, passare alle cartelle che si vogliono usare durante il funzionamento normale.

### <a name="setting-breakpoints"></a>Impostazione di punti di interruzione

Azure Data Factory consente di eseguire il debug di una pipeline fino a quando non si raggiunge una determinata attività nell'area di disegno della pipeline. Inserire un punto di interruzione nell'attività fino a quando si desidera eseguire il test e selezionare **debug**. Data Factory esegue i test solo fino all'attività con il punto di interruzione nel canvas della pipeline. Questa funzionalità *Debug Until* (Debug fino a) è utile quando non si vuole testare l'intera pipeline, ma solo un subset delle attività all'interno della pipeline.

![Punti di interruzione nel canvas della pipeline](media/iterative-development-debugging/iterative-development-3.png)

Per impostare un punto di interruzione, selezionare un elemento nel canvas della pipeline. L'opzione *Debug Until* (Debug fino a) viene visualizzata sotto forma di cerchio rosso vuoto nell'angolo in alto a destra dell'elemento.

![Prima dell'impostazione di un punto di interruzione sull'elemento selezionato](media/iterative-development-debugging/iterative-development-4.png)

Dopo avere selezionato l'opzione *Debug fino a*, diventa un cerchio rosso pieno per indicare che il punto di interruzione è abilitato.

![Dopo l'impostazione di un punto di interruzione sull'elemento selezionato](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Esegue il monitoraggio delle esecuzioni di debug

Quando si esegue un'esecuzione del debug della pipeline, i risultati vengono visualizzati nella finestra **output** dell'area di disegno della pipeline. La scheda output conterrà solo l'esecuzione più recente che si è verificata durante la sessione del browser corrente. 

![Finestra Output del canvas della pipeline](media/iterative-development-debugging/iterative-development-2.png)

Per visualizzare una visualizzazione cronologica delle esecuzioni di debug o visualizzare un elenco di tutte le esecuzioni di debug attive, è possibile passare all'esperienza di **monitoraggio** . 

![Selezionare l'icona View active debug runs (Visualizza esecuzioni di debug attive)](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Il servizio Azure Data Factory rende permanente la cronologia di esecuzione del debug per 15 giorni. 

## <a name="debugging-mapping-data-flows"></a>Debug di flussi di dati di mapping

I flussi di dati di mapping consentono di compilare la logica di trasformazione dei dati senza codice eseguita su larga scala. Quando si compila la logica, è possibile attivare una sessione di debug per lavorare in modo interattivo con i dati usando un cluster Spark attivo. Per altre informazioni, vedere informazioni sul [mapping della modalità di debug del flusso di dati](concepts-data-flow-debug-mode.md).

È possibile monitorare le sessioni di debug del flusso di dati attivo attraverso una factory nell'esperienza di **monitoraggio** .

![Visualizzare le sessioni di debug del flusso di dati](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

L'anteprima dei dati nella finestra di progettazione del flusso di dati e il debug di pipeline di flussi di dati sono progettati per funzionare al meglio con piccoli esempi di dati. Tuttavia, se è necessario testare la logica in una pipeline o in un flusso di dati con grandi quantità di dati, aumentare le dimensioni del Azure Integration Runtime usato nella sessione di debug con più core e almeno un calcolo per utilizzo generico.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Debug di una pipeline con un'attività flusso di dati

Quando si esegue una pipeline di debug eseguita con un flusso di dati, sono disponibili due opzioni per il calcolo da usare. È possibile usare un cluster di debug esistente o creare un nuovo cluster JIT per i flussi di dati.

L'uso di una sessione di debug esistente ridurrà notevolmente il tempo di avvio del flusso di dati mentre il cluster è già in esecuzione, ma non è consigliato per carichi di lavoro complessi o paralleli, in quanto potrebbero verificarsi errori quando più processi vengono eseguiti contemporaneamente.

L'uso del runtime di attività creerà un nuovo cluster usando le impostazioni specificate in ogni runtime di integrazione dell'attività flusso di dati. In questo modo è possibile isolare ogni processo e usarlo per carichi di lavoro complessi o test delle prestazioni. È inoltre possibile controllare la durata (TTL) nell'Azure IR in modo che le risorse del cluster utilizzate per il debug siano ancora disponibili per tale periodo di tempo per gestire richieste di processi aggiuntive.

> [!NOTE]
> Se si dispone di una pipeline con flussi di dati in esecuzione in parallelo o flussi di dati che devono essere testati con set di dati di grandi dimensioni, scegliere "utilizza Runtime attività" in modo che Data Factory possibile utilizzare il Integration Runtime selezionato nell'attività flusso di dati. In questo modo i flussi di dati possono essere eseguiti in più cluster e possono contenere le esecuzioni del flusso di dati parallele.

![Esecuzione di una pipeline con un flusso di data](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver testato le modifiche, promuoverle in ambienti più elevati usando l' [integrazione e la distribuzione continue in Azure Data Factory](continuous-integration-deployment.md).
