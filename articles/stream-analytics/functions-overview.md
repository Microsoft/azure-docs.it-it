---
title: Funzioni definite dall'utente in analisi di flusso di Azure
description: Questo articolo è una panoramica delle funzioni definite dall'utente in analisi di flusso di Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020503"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funzioni definite dall'utente in analisi di flusso di Azure

Il linguaggio di query simile a SQL in analisi di flusso di Azure consente di implementare in modo semplice la logica di analisi in tempo reale sui dati di streaming. Analisi di flusso offre maggiore flessibilità tramite funzioni personalizzate richiamate nella query. L'esempio di codice seguente è una funzione definita dall'utente denominata `sampleFunction` che accetta un parametro, ogni record di input ricevuto dal processo e il risultato viene scritto nell'output come `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipi di funzioni

Analisi di flusso di Azure supporta i quattro tipi di funzioni seguenti: 

* Funzioni JavaScript definite dall'utente 
* aggregazioni JavaScript definite dall'utente 
* Funzioni definite dall'utente C# (con Visual Studio) 
* Azure Machine Learning 

È possibile usare queste funzioni per scenari come il punteggio in tempo reale usando modelli di apprendimento automatico, modifiche di stringhe, calcoli matematici complessi, codifica e decodifica dei dati. 

## <a name="limitations"></a>Limitazioni

Le funzioni definite dall'utente sono senza stato e il valore restituito può essere solo un valore scalare. Non è possibile chiamare gli endpoint REST esterni da queste funzioni definite dall'utente, perché probabilmente influirà sulle prestazioni del processo. 

Analisi di flusso di Azure non conserva un record di tutte le chiamate di funzioni e i risultati restituiti. Per garantire la ripetibilità. ad esempio, la riesecuzione del processo dal timestamp precedente produce gli stessi risultati. non usare funzioni come `Date.GetData()` o `Math.random()` , perché queste funzioni non restituiscono lo stesso risultato per ogni chiamata.  

## <a name="resource-logs"></a>Log risorse

Tutti gli errori di runtime sono considerati irreversibili e vengono esposti tramite log attività e risorse. È consigliabile che la funzione gestisca tutte le eccezioni e gli errori e restituisca un risultato valido alla query. In questo modo si impedisce al processo di passare a uno [stato di errore](job-states.md).  

## <a name="exception-handling"></a>Gestione delle eccezioni

Qualsiasi eccezione durante l'elaborazione dei dati viene considerata un errore irreversibile quando si utilizzano i dati in analisi di flusso di Azure. Le funzioni definite dall'utente hanno un potenziale maggiore per generare eccezioni e causare l'interruzione dell'elaborazione. Per evitare questo problema, usare un blocco *try-catch* in JavaScript o C# per rilevare le eccezioni durante l'esecuzione del codice. Le eccezioni rilevate possono essere registrate e gestite senza causare un errore di sistema. Si consiglia di eseguire sempre il wrapping del codice personalizzato in un blocco *try-catch* per evitare la generazione di eccezioni impreviste nel motore di elaborazione.

## <a name="next-steps"></a>Passaggi successivi

* [Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Aggregazioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-aggregates.md)
* [Sviluppare .NET Standard funzioni definite dall'utente per i processi di analisi di flusso di Azure](stream-analytics-edge-csharp-udf-methods.md)
* [Integrare Analisi di flusso di Azure con Azure Machine Learning](machine-learning-udf.md)
