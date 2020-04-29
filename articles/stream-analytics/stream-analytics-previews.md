---
title: Funzionalità in anteprima di Analisi di flusso di Azure
description: Questo articolo elenca le funzionalità di Analisi di flusso di Azure attualmente in anteprima.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878307"
---
# <a name="azure-stream-analytics-preview-features"></a>Funzionalità in anteprima di Analisi di flusso di Azure

Questo articolo riepiloga tutte le funzionalità attualmente in anteprima per Analisi di flusso di Azure. Non è consigliabile usare le funzionalità in anteprima in un ambiente di produzione.

## <a name="public-previews"></a>Anteprime pubbliche

Nell'anteprima pubblica sono disponibili le funzionalità seguenti. È possibile sfruttare queste funzionalità adesso, ma non usarle nell'ambiente di produzione.

### <a name="online-scaling"></a>Scalabilità online

Con la scalabilità online, non è necessario arrestare il processo se è necessario modificare l'allocazione di SU. È possibile aumentare o diminuire la capacità SU di un processo in esecuzione senza doverla arrestare. Questa operazione si basa sulla promessa dei clienti di pipeline mission-critical a esecuzione prolungata attualmente offerte da analisi di flusso. Per altre informazioni, vedere [configurare unità di streaming di analisi di flusso di Azure](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Deserializzatori personalizzati C#
Gli sviluppatori possono sfruttare la potenza di analisi di flusso di Azure per elaborare i dati in protobuf, XML o in qualsiasi formato personalizzato. È possibile implementare i [deserializzatori personalizzati](custom-deserializer-examples.md) in C#, che possono essere usati per deserializzare gli eventi ricevuti da analisi di flusso di Azure.

### <a name="extensibility-with-c-custom-code"></a>Estendibilità con codice personalizzato C#

Gli sviluppatori che creano moduli di analisi di flusso nel cloud o in IoT Edge possono scrivere o riutilizzare funzioni C# personalizzate e richiamarle direttamente nella query tramite [funzioni definite dall'utente](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Eseguire il debug di passaggi della query in Visual Studio

Quando si esegue il test locale in strumenti di analisi di flusso di Azure per Visual Studio, è possibile visualizzare facilmente l'anteprima del set di righe intermedio in un diagramma di dati. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Test locali con dati dinamici in Visual Studio Code

È possibile testare le query su dati dinamici nel computer locale prima di inviare il processo ad Azure. Ogni iterazione di test richiede una media di meno di due o tre secondi, ottenendo un processo di sviluppo molto efficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code per Analisi di flusso di Azure

I processi di Analisi di flusso di Azure possono essere creati in Visual Studio Code. Vedere l' [esercitazione](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)introduttiva vs code.


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Assegnazione di punteggi in tempo reale ad alte prestazioni con modelli di ML personalizzati gestiti da Azure Machine Learning

Analisi di flusso di Azure supporta l'assegnazione di punteggi a prestazioni elevate e in tempo reale sfruttando i modelli di Machine Learning personalizzati pre-sottoposti a training gestiti da Azure Machine Learning e ospitati in Azure Kubernetes Service (AKS) o istanze di contenitore di Azure, usando un flusso di lavoro che non richiede la scrittura di codice. [Iscriversi](https://aka.ms/asapreview1) per l'anteprima


### <a name="live-data-testing-in-visual-studio"></a>Test dei dati live in Visual Studio

Gli strumenti di Visual Studio per Analisi di flusso di Azure migliorano la funzionalità di test locale che consente di testare le query nei flussi di eventi live da origini cloud come l'hub eventi o l'hub IoT. Per informazioni, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funzioni .NET definite dall'utente in IoT Edge

Con le funzioni .NET Standard definite dall'utente, è possibile eseguire il codice .NET Standard come parte della pipeline di streaming. È possibile creare semplici classi C# o importare librerie e progetti completi. In Visual Studio è supportata l'esperienza completa di creazione e debug. Per altre informazioni, vedere [Sviluppare funzioni .NET Standard definite dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Altre anteprime

Le funzionalità seguenti sono disponibili anche in anteprima su richiesta.

### <a name="support-for-azure-stack"></a>Supporto per Azure Stack
Questa funzionalità è abilitata nel runtime di Azure IoT Edge, sfrutta le funzionalità di Azure Stack personalizzate, ad esempio il supporto nativo per gli input e gli output locali eseguiti in Azure Stack (ad esempio hub eventi, hub degli oggetti, archiviazione BLOB). Questa nuova integrazione consente di creare architetture ibride che consentono di analizzare i dati vicino alla posizione in cui vengono generati, riducendo la latenza e ottimizzando le informazioni dettagliate.
Questa funzionalità consente di creare architetture ibride che consentono di analizzare i dati vicino alla posizione in cui vengono generati, riducendo la latenza e ottimizzando le informazioni dettagliate. È necessario [iscriversi](https://aka.ms/asapreview1) a questa versione di anteprima.
