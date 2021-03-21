---
title: Concatenamento di funzioni in Funzioni permanenti - Azure
description: Scopri come eseguire un esempio di Funzioni permanenti che effettua una sequenza di funzioni.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028251"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Concatenamento di funzioni in Funzioni permanenti - Esempio di sequenza di Hello

Il concatenamento di funzioni si riferisce al criterio di esecuzione di una sequenza di funzioni in un determinato ordine. Spesso l'output di una funzione deve essere applicato all'input di un'altra funzione. Questo articolo descrive la sequenza di concatenamento creata quando si completa la Guida introduttiva di Durable Functions ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)o [Python](quickstart-python-vscode.md)). Per altre informazioni su Durable Functions, vedere [Panoramica di Durable Functions](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E1_HelloSequence`: Funzione dell'agente di [orchestrazione](durable-functions-bindings.md#orchestration-trigger) che chiama `E1_SayHello` più volte in una sequenza. Archivia gli output delle chiamate `E1_SayHello` e registra i risultati.
* `E1_SayHello`: [Funzione di attività](durable-functions-bindings.md#activity-trigger) che antepone una stringa con "Hello".
* `HttpStart`: Funzione [client durevole](durable-functions-bindings.md#orchestration-client) attivata da http che avvia un'istanza dell'agente di orchestrazione.

### <a name="e1_hellosequence-orchestrator-function"></a>Funzione dell'agente di orchestrazione E1_HelloSequence

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Tutte le funzioni di orchestrazione C# devono avere un parametro di tipo `DurableOrchestrationContext` disponibile nell'assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Questo oggetto Context consente di chiamare altre funzioni di *attività* e passare parametri di input usando il relativo `CallActivityAsync` metodo.

Il codice chiama `E1_SayHello` tre volte in sequenza con valori dei parametri diversi. Il valore restituito di ogni chiamata viene aggiunto all'elenco `outputs`, che viene restituito al termine della funzione.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions JavaScript sono disponibili solo per il runtime di funzioni 3,0.

#### <a name="functionjson"></a>function.json

Se si usa Visual Studio Code o il portale di Azure per lo sviluppo, questo è il contenuto del file *function.json* per la funzione di orchestrazione. La maggior parte dei file *function.json* dell'agente di orchestrazione sono quasi identici a questo.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Fondamentale è il tipo di associazione `orchestrationTrigger`. Tutte le funzioni di orchestrazione devono usare questo tipo di trigger.

> [!WARNING]
> Per rispettare la regola "no I/O" delle funzioni di orchestrazione, non usare associazioni di input o output quando si usa l'associazione di trigger `orchestrationTrigger`.  Quando sono necessarie altre associazioni di input o output, occorre invece usarle nel contesto delle funzioni `activityTrigger` già chiamate dall'agente di orchestrazione. Per ulteriori informazioni, vedere l'articolo sui vincoli del codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js

Ecco la funzione dell'agente di orchestrazione:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Tutte le funzioni di orchestrazione JavaScript devono includere il [ `durable-functions` modulo](https://www.npmjs.com/package/durable-functions). Si tratta di una libreria che consente di scrivere Durable Functions in JavaScript. Esistono tre differenze significative tra una funzione dell'agente di orchestrazione e altre funzioni JavaScript:

1. La funzione dell'agente di orchestrazione è una [funzione del generatore](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Per la funzione viene eseguito il wrapping in una chiamata al metodo `durable-functions` del modulo `orchestrator` (qui `df`).
3. La funzione deve essere sincrona. Poiché il metodo 'orchestrator' gestisce la chiamata a 'context.done', la funzione deve semplicemente restituire.

L' `context` oggetto contiene un `df` oggetto di contesto dell'orchestrazione durevole che consente di chiamare altre funzioni di *attività* e passare parametri di input usando il relativo `callActivity` metodo. Il codice chiama `E1_SayHello` tre volte in sequenza con valori di parametro diversi, usando `yield` per indicare che l'esecuzione deve attendere la restituzione delle chiamate di funzioni di attività asincrone. Il valore restituito di ogni chiamata viene aggiunto alla `outputs` matrice, che viene restituita alla fine della funzione.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Durable Functions Python sono disponibili solo per il runtime di funzioni 3,0.


#### <a name="functionjson"></a>function.json

Se si usa Visual Studio Code o il portale di Azure per lo sviluppo, questo è il contenuto del file *function.json* per la funzione di orchestrazione. La maggior parte dei file *function.json* dell'agente di orchestrazione sono quasi identici a questo.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

Fondamentale è il tipo di associazione `orchestrationTrigger`. Tutte le funzioni di orchestrazione devono usare questo tipo di trigger.

> [!WARNING]
> Per rispettare la regola "no I/O" delle funzioni di orchestrazione, non usare associazioni di input o output quando si usa l'associazione di trigger `orchestrationTrigger`.  Quando sono necessarie altre associazioni di input o output, occorre invece usarle nel contesto delle funzioni `activityTrigger` già chiamate dall'agente di orchestrazione. Per ulteriori informazioni, vedere l'articolo sui vincoli del codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md) .

#### <a name="__init__py"></a>\_\_init\_\_.py

Ecco la funzione dell'agente di orchestrazione:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Tutte le funzioni di orchestrazione Python devono includere il [ `durable-functions` pacchetto](https://pypi.org/project/azure-functions-durable). Si tratta di una libreria che consente di scrivere Durable Functions in Python. Esistono due differenze significative tra una funzione dell'agente di orchestrazione e altre funzioni Python:

1. La funzione dell'agente di orchestrazione è una [funzione del generatore](https://wiki.python.org/moin/Generators).
2. Il _file_ deve registrare la funzione dell'agente di orchestrazione come agente di orchestrazione indicando `main = df.Orchestrator.create(<orchestrator function name>)` alla fine del file. Ciò consente di distinguerlo da altre funzioni helper dichiarate nel file.

L' `context` oggetto consente di chiamare altre funzioni di *attività* e passare parametri di input usando il relativo `call_activity` metodo. Il codice chiama `E1_SayHello` tre volte in sequenza con valori di parametro diversi, usando `yield` per indicare che l'esecuzione deve attendere la restituzione delle chiamate di funzioni di attività asincrone. Al termine della funzione viene restituito il valore restituito di ogni chiamata.

---

### <a name="e1_sayhello-activity-function"></a>Funzione E1_SayHello Activity

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Le attività usano l' `ActivityTrigger` attributo. Usare l'oggetto fornito `IDurableActivityContext` per eseguire azioni correlate all'attività, ad esempio l'accesso al valore di input usando `GetInput<T>` .

L'implementazione di `E1_SayHello` è un'operazione di formattazione delle stringhe relativamente semplice.

Anziché eseguire l'associazione a un oggetto `IDurableActivityContext` , è possibile eseguire l'associazione direttamente al tipo passato nella funzione Activity. Ad esempio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.js

Il file *function.json* per la funzione di attività `E1_SayHello` è simile a quello di `E1_HelloSequence` ad eccezione del fatto che usa un tipo di associazione `activityTrigger` anziché un tipo di associazione `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Tutte le funzioni di attività chiamate da una funzione di orchestrazione devono usare l' `activityTrigger` associazione.

L'implementazione di `E1_SayHello` è un'operazione di formattazione delle stringhe relativamente semplice.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A differenza della funzione di orchestrazione, una funzione di attività non necessita di alcuna configurazione speciale. L'input passato al metodo tramite la funzione dell'agente di orchestrazione è presente nell'oggetto `context.bindings` sotto il nome del binding `activityTrigger`, in questo caso `context.bindings.name`. Il nome del binding può essere impostato come parametro della funzione esportata e vi si può accedere direttamente, come nel codice di esempio.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.js

Il file *function.json* per la funzione di attività `E1_SayHello` è simile a quello di `E1_HelloSequence` ad eccezione del fatto che usa un tipo di associazione `activityTrigger` anziché un tipo di associazione `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Tutte le funzioni di attività chiamate da una funzione di orchestrazione devono usare l' `activityTrigger` associazione.

L'implementazione di `E1_SayHello` è un'operazione di formattazione delle stringhe relativamente semplice.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

A differenza della funzione dell'agente di orchestrazione, una funzione di attività non necessita di alcuna configurazione speciale. L'input passato tramite la funzione dell'agente di orchestrazione è direttamente accessibile come parametro della funzione.

---

### <a name="httpstart-client-function"></a>Funzione client HttpStart

È possibile avviare un'istanza della funzione dell'agente di orchestrazione utilizzando una funzione client. Si userà la `HttpStart` funzione attivata tramite HTTP per avviare le istanze di `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Per interagire con gli agenti di orchestrazione, la funzione deve includere un' `DurableClient` associazione di input. Il client viene utilizzato per avviare un'orchestrazione. Può inoltre essere utile per restituire una risposta HTTP contenente URL per verificare lo stato della nuova orchestrazione.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.js

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Per interagire con gli agenti di orchestrazione, la funzione deve includere un' `durableClient` associazione di input.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Utilizzare `df.getClient` per ottenere un `DurableOrchestrationClient` oggetto. Il client viene utilizzato per avviare un'orchestrazione. Può inoltre essere utile per restituire una risposta HTTP contenente URL per verificare lo stato della nuova orchestrazione.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.js

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Per interagire con gli agenti di orchestrazione, la funzione deve includere un' `durableClient` associazione di input.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Usare il `DurableOrchestrationClient` costruttore per ottenere un client Durable Functions. Il client viene utilizzato per avviare un'orchestrazione. Può inoltre essere utile per restituire una risposta HTTP contenente URL per verificare lo stato della nuova orchestrazione.

---

## <a name="run-the-sample"></a>Eseguire l'esempio

Per eseguire l' `E1_HelloSequence` orchestrazione, inviare la richiesta post http seguente alla `HttpStart` funzione.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Il frammento di codice HTTP precedente presuppone che sia presente una voce nel file `host.json` che consente di rimuovere il prefisso predefinito `api/` da tutti gli URL di funzioni di trigger HTTP. È possibile trovare il markup per la configurazione nel file `host.json` negli esempi.

Ad esempio, se si esegue l'esempio in un'app per le funzioni denominata "myfunctionapp", sostituire "{host}" con "myfunctionapp.azurewebsites.net".

Il risultato è una risposta HTTP 202, simile alla seguente (tagliata per brevità):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A questo punto, l'orchestrazione viene messa in coda e inizia ad avviarsi immediatamente. L'URL nell'intestazione `Location` può essere usato per controllare lo stato dell'esecuzione.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Il risultato è lo stato dell'orchestrazione. Si esegue e completa rapidamente e appare nello stato *Completato* con una risposta simile alla seguente (tagliata per brevità):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Come si può notare, il `runtimeStatus` dell'istanza è *Completato* e `output` contiene il risultato JSON serializzato dell'esecuzione della funzione di orchestrazione.

> [!NOTE]
> È possibile implementare una logica di avvio simile per altri tipi di trigger, ad esempio `queueTrigger`, `eventHubTrigger`, o `timerTrigger`.

Esaminare i log di esecuzione della funzione. La `E1_HelloSequence` funzione è stata avviata e completata più volte a causa del comportamento di riproduzione descritto nell'argomento relativo all' [affidabilità dell'orchestrazione](durable-functions-orchestrations.md#reliability) . In compenso, si sono verificate solo tre esecuzioni di `E1_SayHello` da quando tali esecuzioni delle funzioni non vengono riprodotte.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata illustrata una semplice orchestrazione di concatenamento delle funzioni. Nell'esempio successivo viene illustrato come implementare il criterio di fan-out/fan-in.

> [!div class="nextstepaction"]
> [Eseguire l'esempio di Fan-out/fan-in](durable-functions-cloud-backup.md)
