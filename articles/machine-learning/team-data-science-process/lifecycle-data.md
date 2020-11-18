---
title: Acquisizione e comprensione dei dati nel processo di data science per i team
description: Obiettivi, attività e risultati finali per la fase di acquisizione e comprensione dei dati dei progetti di data science
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fffb52e333bea1b2be11b127a9eab6656dc1d1f5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94740329"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Fase di acquisizione e comprensione dei dati nel processo di data science per i team

Questo articolo descrive gli obiettivi, le attività e i risultati finali associati alla fase di acquisizione e comprensione dei dati nel processo di data science per i team (TDSP). Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Informazioni aziendali**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Produrre un set di dati pulito e di alta qualità, di cui sia compresa la relazione con le variabili di destinazione. Inserire il set di dati nell'ambiente di analisi appropriato in modo da creare un modello.
* Sviluppare un'architettura della soluzione della pipeline di dati per aggiornare e valutare regolarmente i dati.

## <a name="how-to-do-it"></a>Come eseguirla
Questa fase comprende tre attività principali:

   * **Inserire i dati** nell'ambiente di analisi target.
   * **Esplorare i dati** per determinare se la qualità dei dati è sufficiente per rispondere alla domanda. 
   * **Configurare una pipeline di dati** per calcolare dati nuovi o regolarmente aggiornati.

### <a name="ingest-the-data"></a>Inserire i dati
Impostare il processo per spostare i dati dalle posizioni di origine alle posizioni destinazione dove si eseguono operazioni di analisi, ad esempio il training e le stime. Per i dettagli tecnici e per le opzioni su come spostare i dati con vari servizi di dati di Azure, vedere [Caricare i dati in ambienti di archiviazione per l'analisi](ingest-data.md). 

### <a name="explore-the-data"></a>Esplorare i dati
Prima di eseguire il training dei modelli, è necessario sviluppare una buona comprensione dei dati. I set di dati reali sono spesso fastidiosi, mancano di valori o presentano altre discrepanze. La visualizzazione e il riepilogo dei dati consentono di controllare la qualità dei dati e offrono le informazioni necessarie per elaborare i dati prima che siano pronti per la modellazione. Spesso, questo processo è iterativo. Per istruzioni sulla pilizia dei dati, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](prepare-data.md).  

Dopo che si è soddisfatti della qualità dei dati ripuliti, il passaggio successivo consiste nel capire meglio i modelli inerenti nei dati. Questa analisi dei dati consente di scegliere e sviluppare un modello predittivo appropriato per la destinazione. Cercare la prova della precisa connessione dei dati all'obiettivo. Determinare quindi se i dati sono sufficienti per passare alle successive fasi di modellazione. Anche questo processo è iterativo. Potrebbe essere necessario trovare nuove origini dati con dati più accurati o più pertinenti per aumentare il set di dati identificato nella fase precedente. 

### <a name="set-up-a-data-pipeline"></a>Impostare una pipeline di dati
Oltre all'inserimento iniziale e alla pulizia dei dati, è necessario in genere impostare un processo per valutare nuovi dati o aggiornare regolarmente i dati, come parte di un processo costante di apprendimento. Il Punteggio può essere completato con una pipeline di dati o un flusso di lavoro. L'articolo [spostare i dati da un'istanza di SQL Server al database SQL di Azure con Azure Data Factory](move-sql-azure-adf.md) fornisce un esempio di come configurare una pipeline con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In questa fase si sviluppa un'architettura della soluzione della pipeline di dati. Si sviluppa la pipeline in parallelo con la fase successiva del progetto di data science. A seconda delle esigenze aziendali e dei vincoli dei sistemi esistenti in cui la soluzione è integrata, la pipeline può essere una delle seguenti opzioni: 

   * Basata su batch
   * In streaming o in tempo reale 
   * Un ibrido 

## <a name="artifacts"></a>Artifacts
Di seguito sono descritti i risultati finali di questa fase:

   * [Report sulla qualità dei dati](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): il report include riepiloghi dei dati, relazioni tra ogni attributo e l'obiettivo, valutazione delle variabili e così via. 
   * **Architettura della soluzione**: può trattarsi di un diagramma o della descrizione della pipeline di dati usata per eseguire valutazioni o stime sui nuovi dati dopo aver creato un modello. Include anche la pipeline per ripetere il training del modello basato su nuovi dati. Archiviare il documento nella directory di [progetto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando si usa il modello della struttura di directory del progetto TDSP.
   * **Decisione di checkpoint**: prima di iniziare la progettazione completa delle funzionalità e la compilazione del modello, è possibile rivalutare il progetto per determinare se il valore previsto è sufficiente per continuare a usarlo. È possibile ad esempio che tutto sia predisposto per procedere, che si debbano raccogliere altri dati o abbandonare il progetto, se non esistono dati per rispondere alla domanda.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Informazioni aziendali](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Vengono fornite procedure dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning]().
