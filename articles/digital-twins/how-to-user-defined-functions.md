---
title: Come creare funzioni definite dall'utente-in dispositivi gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come creare funzioni definite dall'utente, matcher e assegnazioni di ruolo con Gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276930"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Come creare funzioni definite dall'utente in Gemelli digitali di Azure

Le [funzioni definite dall'utente](./concepts-user-defined-functions.md) consentono agli utenti di configurare la logica da eseguire da messaggi di telemetria in ingresso e metadati del grafo spaziale. Gli utenti possono quindi inviare gli eventi a [endpoint](./how-to-egress-endpoints.md) predefiniti.

Questa guida illustra un esempio che dimostra come rilevare e segnalare eventuali letture che superano una determinata temperatura dagli eventi di temperatura ricevuti.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Informazioni di riferimento sulla libreria client

Le funzioni disponibili come metodi helper nel runtime delle funzioni definite dall'utente sono elencate nel documento di [riferimento sulla libreria client](./reference-user-defined-functions-client-library.md).

## <a name="create-a-matcher"></a>Creare un matcher

I matcher sono oggetti del grafico che determinano quali funzioni definite dall'utente eseguire per un messaggio di telemetria specifico.

- Confronti di condizione validi per i matcher:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinazioni delle condizioni valide per i matcher:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Il matcher di esempio seguente restituisce true per ogni evento di telemetria dei sensori con tipo di dati `"Temperature"`. È possibile creare più matcher in una funzione definita dall'utente eseguendo una richiesta HTTP POST autenticata per:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Con corpo JSON:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| valore | Sostituire con |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Area del server in cui è ospitata l'istanza |

## <a name="create-a-user-defined-function"></a>Creare una funzione definita dall'utente

La creazione di una funzione definita dall'utente comporta l'implementazione di una richiesta HTTP multipart per l'API di gestione di dispositivi Gemelli digitali di Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Dopo aver creato i matcher, caricare il frammento della funzione con la seguente richiesta HTTP POST autenticata:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Usare il corpo seguente:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| valore | Sostituire con |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nome di un limite di contenuto multipart |
| YOUR_SPACE_IDENTIFIER | Identificatore dello spazio  |
| YOUR_MATCHER_IDENTIFIER | ID del matcher da usare |

1. Verificare che le intestazioni includano: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Verificare che il corpo sia costituito da più parti:

   - La prima parte contiene i necessari metadati di funzione definita dall'utente.
   - La seconda parte contiene la logica di calcolo JavaScript.

1. Nella sezione **USER_DEFINED_BOUNDARY** sostituire i valori **spaceId** (`YOUR_SPACE_IDENTIFIER`) e **matchers** (`YOUR_MATCHER_IDENTIFIER`).
1. Verificare che la funzione JavaScript definita dall'utente sia fornita come `Content-Type: text/javascript`.

### <a name="example-functions"></a>Funzioni di esempio

Impostare la lettura dei dati di telemetria del sensore direttamente per un sensore con tipo di dati **Temperatura**, ovvero `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Il parametro di **telemetria** espone gli attributi **SensorId** e **Message** , corrispondenti a un messaggio inviato da un sensore. Il parametro **executionContext** espone i seguenti attributi:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

Nel prossimo esempio, registriamo un messaggio se la lettura dei dati di telemetria del sensore supera una soglia predefinita. Se le impostazioni di diagnostica sono abilitate nell'istanza di Gemelli digitali di Azure, vengono inoltrati anche i log delle funzioni definite dall'utente:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Il codice seguente attiva una notifica se il livello di temperatura sale sopra il valore costante predefinito:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Per un esempio di codice di funzione definito dall'utente più complesso, leggere la [Guida introduttiva di uso](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

Creare un'assegnazione di ruolo per l'esecuzione della funzione definita dall'utente. Se non esiste alcuna assegnazione di ruolo per la funzione definita dall'utente, non si avranno le autorizzazioni appropriate per interagire con l'API Gestione o per eseguire azioni sugli oggetti grafo. Le azioni eseguite dalla funzione definita dall'utente non sono esenti dal controllo degli accessi in base al ruolo delle API di gestione di Gemelli digitali di Azure. Ad esempio, è possibile limitare l'ambito delle funzioni definite dall'utente specificando determinati ruoli o percorsi di controllo di accesso. Per ulteriori informazioni, vedere la documentazione relativa al [controllo degli accessi in base al ruolo](./security-role-based-access-control.md) .

1. [Effettuare una query nell'API di sistema](./security-create-manage-role-assignments.md#retrieve-all-roles) per fare in modo che tutti i ruoli ricevano l'ID da assegnare alle funzioni definite dall'utente. Eseguire questa operazione effettuando una richiesta HTTP GET autenticata a:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Mantenere l'ID del ruolo desiderato. Viene passato come attributo del corpo JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) di seguito.

1. **ObjectID** (`YOUR_USER_DEFINED_FUNCTION_ID`) sarà l'ID funzione definito dall'utente creato in precedenza.
1. Trovare il valore di **path** (`YOUR_ACCESS_CONTROL_PATH`) eseguendo una query sugli spazi con `fullpath`.
1. Copiare il valore `spacePaths` restituito. Usare quello indicato di seguito. Effettuare una richiesta HTTP GET autenticata a:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | valore | Sostituire con |
    | --- | --- |
    | YOUR_SPACE_NAME | Nome dello spazio da usare |

1. Incollare il valore `spacePaths` restituito in **path** per creare un'assegnazione di ruolo della funzione definita dall'utente, effettuando una richiesta HTTP POST autenticata a:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Con corpo JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | valore | Sostituire con |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identificatore del ruolo desiderato |
    | YOUR_USER_DEFINED_FUNCTION_ID | L'ID per la funzione definita dall'utente da usare |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID che specifica il tipo di funzione definita dall'utente`UserDefinedFunctionId`() |
    | YOUR_ACCESS_CONTROL_PATH | Percorso di controllo di accesso |

>[!TIP]
> Per altre informazioni su operazioni ed endpoint dell'API Gestione collegati alla funzione definita dall'utente, leggere l'articolo [Come creare e gestire le assegnazioni di ruolo](./security-create-manage-role-assignments.md).

## <a name="send-telemetry-to-be-processed"></a>Inviare i dati di telemetria da elaborare

Il sensore definito nel grafo intelligence spaziale invia dati di telemetria. A loro volta, i dati di telemetria attivano l'esecuzione della funzione definita dall'utente che è stata caricata. L'elaboratore di dati seleziona i dati di telemetria. Viene quindi creato un piano di esecuzione per la chiamata della funzione definita dall'utente.

1. Recuperare i matcher per il sensore da cui è stata generata la lettura.
1. A seconda dei matcher valutati correttamente, recuperare le funzioni definite dall'utente associate.
1. Eseguire ogni funzione definita dall'utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare endpoint di Gemelli digitali di Azure](./how-to-egress-endpoints.md) a cui inviare eventi.

- Per altre informazioni sul routing in Gemelli digitali di Azure, leggere [Routing di eventi e messaggi](./concepts-events-routing.md).

- Esaminare la [documentazione di riferimento della libreria client](./reference-user-defined-functions-client-library.md).