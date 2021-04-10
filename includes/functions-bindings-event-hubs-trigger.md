---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 32f98eb9b98168bdab270ecff07446c31f8d706d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729878"
---
Usare il trigger di funzioni per rispondere a un evento inviato a un flusso di eventi di Hub eventi. Per configurare il trigger è necessario avere accesso in lettura all'hub eventi sottostante. Quando la funzione viene attivata, il messaggio passato alla funzione viene tipizzato come stringa.

## <a name="scaling"></a>Scalabilità

Ogni istanza di una funzione attivata da un evento è supportata da una singola istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Il trigger (basato su Hub eventi) garantisce che solo un'istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) possa ottenere un lease in una determinata partizione.

Si consideri ad esempio un Hub eventi come quello indicato di seguito:

* 10 partizioni
* 1\.000 eventi distribuiti in modo uniforme fra tutte le partizioni, con 100 messaggi in ogni partizione

Quando la funzione viene abilitata per la prima volta, è presente solo un'istanza della funzione. Assegniamo alla prima istanza della funzione il nome `Function_0`. La funzione `Function_0` ha un'unica istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) con un lease in tutte le dieci partizioni. Questa istanza legge gli eventi dalle partizioni da 0 a 9. A partire da questo punto potranno verificarsi una delle condizioni seguenti:

* **Non sono necessarie nuove istanze della funzione**: `Function_0` è in grado di elaborare tutti i 1.000 eventi prima che la logica di ridimensionamento delle funzioni abbia effetto. In questo caso, tutti i 1.000 messaggi vengono elaborati da `Function_0`.

* **Viene aggiunta un'altra istanza della funzione**: se la logica di ridimensionamento delle funzioni determina che `Function_0` ha più messaggi di quanti ne possa elaborare, viene creata una nuova istanza dell'app per le funzioni (`Function_1`). A questa nuova funzione è anche associata un'istanza di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). Quando l'hub eventi sottostante rileva che una nuova istanza dell'host sta tentando di leggere i messaggi, bilancia il carico delle partizioni fra le istanze dell'host. Ad esempio, le partizioni da 0 a 4 possono essere assegnate a `Function_0` e le partizioni a 5 a 9 a `Function_1`.

* **Vengono aggiunte altre N istanze della funzione**: Se la logica di ridimensionamento delle funzioni di Azure determina che sia `Function_0` che `Function_1` hanno più messaggi di quanti ne possano elaborare, vengono create nuove istanze dell'app per le funzioni `Functions_N`.  Vengono create app finché `N` non è maggiore del numero delle partizioni dell'hub eventi. In questo esempio l'Hub eventi bilancia nuovamente il carico delle partizioni, in questo caso tra le istanze `Function_0`...`Functions_9`.

Quando avviene il ridimensionamento, `N` istanze è un numero maggiore del numero di partizioni dell'hub eventi. Questo modello viene usato per assicurare che vi siano delle istanze di [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) disponibili per ottenere blocchi sulle partizioni appena diventano disponibili da altre istanze. Vengono addebitati solo i costi delle risorse usate quando viene eseguita l'istanza della funzione. In altre parole, non vengono addebitati costi per questo provisioning in eccesso.

Al termine dell'esecuzione di tutte le funzioni (con o senza errori), i checkpoint vengono aggiunti all'account di archiviazione associato. Quando il checkpoint ha esito positivo, non vengono più recuperati nuovamente tutti i 1.000 messaggi.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](../articles/azure-functions/functions-dotnet-class-library.md) che registra il corpo del messaggio del trigger per Hub eventi.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai [metadati dell'evento](#event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.Azure.EventHubs`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice.  

> [!NOTE]
> Quando vengono ricevuti in un batch non è possibile eseguire l'associazione ai parametri del metodo come nell'esempio precedente con `DateTime enqueuedTimeUtc` ed è necessario riceverli da ciascun oggetto `EventData`  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione script C#](../articles/azure-functions/functions-reference-csharp.md) che usa l'associazione. La funzione registra il corpo del messaggio del trigger per Hub eventi.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

### <a name="version-2x-and-higher"></a>Versione 2.x e successive

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice script C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Per ottenere l'accesso ai [metadati dell'evento](#event-metadata) nel codice funzione, associare un oggetto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (richiede un'istruzione using per `Microsoft.Azure.EventHubs`). È possibile anche accedere alle stesse proprietà usando le espressioni di associazione nella firma del metodo.  Nell'esempio seguente vengono illustrati entrambi i modi per ottenere gli stessi dati:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Per ricevere gli eventi in un batch, impostare `string` o `EventData` come matrice:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger per Hub eventi in un file *function.json* e una [funzione JavaScript](../articles/azure-functions/functions-reference-node.md) che usa l'associazione. La funzione legge i [metadati dell'evento](#event-metadata) e registra il messaggio.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

### <a name="version-2x-and-higher"></a>Versione 2.x e successive

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Per ricevere gli eventi in un batch, impostare `cardinality` su `many` nel file *function.json*, come illustrato negli esempi seguenti.

### <a name="version-2x-and-higher"></a>Versione 2.x e successive

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger per hub eventi in un file *function.json* e una [funzione Python](../articles/azure-functions/functions-reference-python.md) che usa l'associazione. La funzione legge i [metadati dell'evento](#event-metadata) e registra il messaggio.

Gli esempi seguenti illustrano il data binding di Hub eventi nel file *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ecco il codice Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info(f'Function triggered to process a message: {event.get_body().decode()}')
    logging.info(f'  EnqueuedTimeUtc = {event.enqueued_time}')
    logging.info(f'  SequenceNumber = {event.sequence_number}')
    logging.info(f'  Offset = {event.offset}')

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = {event.metadata[key]}')
```

# <a name="java"></a>[Java](#tab/java)

L'esempio seguente illustra un'associazione di trigger di Hub eventi che registra il corpo del messaggio del trigger di Hub eventi.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `EventHubTrigger` per i parametri il cui valore deriva da Hub eventi. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

 ---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](../articles/azure-functions/functions-dotnet-class-library.md) usare l'attributo [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Il costruttore dell'attributo accetta il nome di Hub eventi, il nome del gruppo di consumer e il nome di un'impostazione di app che contiene la stringa di connessione. Per altre informazioni su queste impostazioni, vedere la [sezione relativa alla configurazione dei trigger](#configuration). Di seguito è riportato un esempio di attributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Trigger - esempio in C#](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Nella [libreria di runtime delle funzioni](/java/api/overview/azure/functions/runtime) Java usare l'annotazione [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) per i parametri il cui valore deriva da Hub eventi. I parametri con queste annotazioni attivano l'esecuzione della funzione quando viene ricevuto un evento. Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `EventHubTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `eventHubTrigger`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `in`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta l'elemento evento nel codice della funzione. |
|**path** |**EventHubName** | Solo Funzioni 1.x. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. |
|**eventHubName** |**EventHubName** | Funzioni 2.x e versioni successive. Nome di Hub eventi. Quando il nome dell'hub eventi è presente anche nella stringa di connessione, tale valore sostituisce questa proprietà in fase di runtime. È possibile farvi riferimento tramite le [impostazioni dell'app](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) `%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | Proprietà facoltativa usata per impostare il [gruppo di consumer](../articles/event-hubs/event-hubs-features.md#event-consumers) usato per effettuare la sottoscrizione agli eventi nell'hub. Se omessa, al suo posto viene usato il gruppo di consumer `$Default`. |
|**cardinality** | n/d | Usata per tutti i linguaggi diversi da C#. Impostare su `many` per abilitare l'invio in batch.  Se omessa o impostata su `one`, alla funzione viene passato un singolo messaggio.<br><br>In C# questa proprietà viene assegnata automaticamente ogni volta che il trigger ha una matrice per il tipo.|
|**connection** |**Connection** | Nome di un'impostazione dell'app che contiene la stringa di connessione per lo spazio dei nomi di Hub eventi. Copiare questa stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo [spazio dei nomi](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), non per lo stesso Hub eventi. Per attivare il trigger, questa stringa di connessione deve disporre almeno delle autorizzazioni Read.<br><br>Se si usa [la versione 5. x o una versione successiva dell'estensione](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), anziché una stringa di connessione, è possibile fornire un riferimento a una sezione di configurazione che definisce la connessione. Vedere [connessioni](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Predefinito

È possibile usare i tipi di parametro seguenti per l'hub eventi di attivazione:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Le proprietà predefinite di EventData vengono fornite nell'oggetto per lo [spazio dei nomi Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

### <a name="additional-types"></a>Tipi aggiuntivi 
Le app che usano la versione 5.0.0 o successiva dell'estensione dell'hub eventi usano il `EventData` tipo in [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) anziché quello nello [spazio dei nomi Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Questa versione Elimina il supporto per il `Body` tipo legacy a favore dei tipi seguenti:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

### <a name="default"></a>Predefinito

È possibile usare i tipi di parametro seguenti per l'hub eventi di attivazione:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Le proprietà predefinite di EventData vengono fornite nell'oggetto per lo [spazio dei nomi Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata).

### <a name="additional-types"></a>Tipi aggiuntivi 
Le app che usano la versione 5.0.0 o successiva dell'estensione dell'hub eventi usano il `EventData` tipo in [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata) anziché quello nello [spazio dei nomi Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata). Questa versione Elimina il supporto per il `Body` tipo legacy a favore dei tipi seguenti:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="java"></a>[Java](#tab/java)

Per informazioni dettagliate, vedere l' [esempio di trigger](#example) Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Per informazioni dettagliate, vedere l' [esempio di trigger](#example) JavaScript.

# <a name="python"></a>[Python](#tab/python)

Per informazioni dettagliate, vedere l' [esempio di trigger](#example) Python.


---


## <a name="event-metadata"></a>Metadati di evento

Il trigger di Hub eventi fornisce diverse [proprietà di metadati](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Le proprietà di metadati possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Le proprietà derivano dalla classe [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Proprietà|Type|Descrizione|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Istanza di `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Il tempo di accodamento in formato UTC.|
|`Offset`|`string`|L'offset dei dati rispetto al flusso di partizione di Hub eventi. L'offset è un indicatore o un identificatore per un evento all'interno del flusso di Hub eventi. L'identificatore è univoco all'interno di una partizione del flusso di Hub eventi.|
|`PartitionKey`|`string`|La partizione a cui devono essere inviati i dati dell'evento.|
|`Properties`|`IDictionary<String,Object>`|Le proprietà dell'utente dei dati dell'evento.|
|`SequenceNumber`|`Int64`|Il numero di sequenza logica dell'evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Le proprietà di sistema, inclusi di dati dell'evento.|

Vedere gli [esempi di codice](#example) che usano queste proprietà in precedenza in questo articolo.
