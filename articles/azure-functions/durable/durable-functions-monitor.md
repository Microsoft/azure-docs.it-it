---
title: Monitoraggi in Funzioni durevoli - Azure
description: Informazioni su come implementare un monitoraggio con stato usando l'estensione Funzioni durevoli per Funzioni di Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77562123"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scenario di monitoraggio in Funzioni durevoli - Esempio di watcher per il meteo

Il modello di monitoraggio fa riferimento a un processo *ricorrente* flessibile in un flusso di lavoro, ad esempio il polling finché vengono soddisfatte determinate condizioni. Questo articolo illustra un esempio che usa [Funzioni durevoli](durable-functions-overview.md) per implementare il monitoraggio.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

Questo esempio monitora le condizioni meteo correnti di una località e avvisa un utente tramite SMS quando il tempo è sereno. Per controllare il meteo e inviare gli avvisi, è possibile usare una normale funzione attivata tramite timer. Un problema di questo approccio è però la **gestione della durata**. Se deve essere inviato un solo avviso, il monitoraggio deve disabilitarsi dopo che sono state rilevate condizioni di tempo sereno. Il modello di monitoraggio può terminare la propria esecuzione, tra gli altri vantaggi:

* I monitoraggi vengono eseguiti a intervalli, non in base a pianificazioni: un trigger timer *viene eseguito* ogni ora. Un monitoraggio *attende* un'ora tra un'azione e l'altra. Se non specificato, le azioni di un monitoraggio non si sovrapporranno e questo può essere importante per le attività con esecuzione prolungata.
* I monitoraggi possono avere intervalli dinamici: il tempo di attesa può variare in base ad alcune condizioni.
* I monitoraggi possono terminare quando viene soddisfatta una condizione o essere terminati da un altro processo.
* Monitoraggi possono accettare parametri. L'esempio illustra come lo stesso processo di monitoraggio del meteo possa essere applicato a qualsiasi località richiesta e numero di telefono.
* Monitoraggi sono scalabili. Poiché ogni monitoraggio è un'istanza di orchestrazione, si possono creare più monitoraggi senza dover creare nuove funzioni o definire altro codice.
* I monitoraggi si integrano facilmente in flussi di lavoro più grandi. Un monitoraggio può essere una sezione di una funzione di orchestrazione più complessa o un'[orchestrazione secondaria](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Configurazione

### <a name="configuring-twilio-integration"></a>Configurazione dell'integrazione di Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Configurazione dell'integrazione di Weather Underground

Questo esempio prevede l'uso dell'API Weather Underground per controllare le condizioni meteo correnti di una località.

Prima di tutto, è necessario un account Weather Underground. È possibile crearne uno gratuitamente all'indirizzo [https://www.wunderground.com/signup](https://www.wunderground.com/signup) . Dopo avere creato l'account, sarà necessario acquisire una chiave API. È possibile eseguire questa operazione visitando [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) e quindi selezionando Impostazioni chiave. Il piano gratuito Stratus Developer è sufficiente per eseguire questo esempio.

Dopo avere acquisito la chiave API, aggiungere l'**impostazione app** seguente all'app per le funzioni.

| Nome impostazione app | Descrizione del valore |
| - | - |
| **WeatherUndergroundApiKey**  | Chiave API Weather Underground. |

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E3_Monitor`: Funzione dell'agente di [orchestrazione](durable-functions-bindings.md#orchestration-trigger) che chiama `E3_GetIsClear` periodicamente. Chiama `E3_SendGoodWeatherAlert` se `E3_GetIsClear` restituisce true.
* `E3_GetIsClear`: [Funzione di attività](durable-functions-bindings.md#activity-trigger) che controlla le condizioni meteo correnti per una località.
* `E3_SendGoodWeatherAlert`: funzione dell'attività che invia un SMS tramite Twilio.

### <a name="e3_monitor-orchestrator-function"></a>Funzione dell'agente di orchestrazione E3_Monitor

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

L'agente di orchestrazione richiede un percorso da monitorare e un numero di telefono a cui inviare un messaggio quando l'oggetto diventa chiaro in corrispondenza della posizione. Questi dati vengono passati all'agente di orchestrazione come oggetto fortemente tipizzato `MonitorRequest` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

La funzione **E3_Monitor** usa il codice *function.json* standard per le funzioni dell'agente di orchestrazione.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Di seguito è riportato il codice che implementa la funzione:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Le azioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Ottiene **MonitorRequest** costituita dalla *località* da monitorare e dal *numero di telefono* al quale invierà un SMS di notifica.
2. Determina la scadenza del monitoraggio. L'esempio usa un valore hardcoded per ragioni di brevità.
3. Chiama **E3_GetIsClear** per determinare se nella località richiesta il tempo è sereno.
4. Se il tempo è sereno, chiama **E3_SendGoodWeatherAlert** per inviare un SMS di notifica al numero di telefono richiesto.
5. Crea un timer durevole per riprendere l'orchestrazione all'intervallo di polling successivo. L'esempio usa un valore hardcoded per ragioni di brevità.
6. Continua l'esecuzione fino all'ora UTC corrente che supera l'ora di scadenza del monitoraggio oppure viene inviato un avviso SMS.

È possibile eseguire contemporaneamente più istanze dell'agente di orchestrazione chiamando la funzione dell'agente di orchestrazione più volte. Si possono specificare la località da monitorare e il numero di telefono a cui inviare un SMS di avviso.

### <a name="e3_getisclear-activity-function"></a>Funzione E3_GetIsClear Activity

In modo analogo agli altri esempi, le funzioni di attività helper sono normali funzioni che usano l'associazione di trigger `activityTrigger`. La funzione **E3_GetIsClear** ottiene le condizioni meteo correnti usando l'API Weather Underground e determina se il tempo è sereno.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice *function.json* viene definito come segue:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

Di seguito ne viene riportata l'implementazione.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>Funzione E3_SendGoodWeatherAlert Activity

La funzione **E3_SendGoodWeatherAlert** usa l'associazione di Twilio per inviare un SMS che notifica all'utente finale che il tempo è adatto per una passeggiata.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> `Microsoft.Azure.WebJobs.Extensions.Twilio`Per eseguire il codice di esempio, sarà necessario installare il pacchetto NuGet.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice *function.json* è semplice:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

Di seguito è disponibile il codice che invia l'SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Eseguire l'esempio

Con le funzioni attivate da HTTP incluse nell'esempio, è possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

L'istanza **E3_Monitor** viene avviata ed esegue una query delle condizioni meteo correnti per la località richiesta. Se il tempo è sereno, chiama una funzione dell'attività per inviare un avviso. In caso contrario, imposta un timer. Quando il timer scade, l'orchestrazione viene ripresa.

È possibile visualizzare l'attività dell'orchestrazione esaminando i log della funzione nel portale Funzioni di Azure.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

L'orchestrazione [terminerà](durable-functions-instance-management.md) quando verrà raggiunto il timeout o verrà rilevato tempo sereno. È anche possibile usare `TerminateAsync` (.NET) o `terminate` (JavaScript) in un'altra funzione o richiamare il webhook HTTP POST **terminatePostUri** a cui si è fatto riferimento nella risposta 202 precedente, sostituendo `{text}` con il motivo della terminazione:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Passaggi successivi

Questo esempio ha illustrato come usare Funzioni durevoli per monitorare lo stato di un'origine esterna usando [timer durevoli](durable-functions-timers.md) e la logica condizionale. Nell'esempio successivo viene illustrato come usare eventi esterni e [timer durevoli](durable-functions-timers.md) per gestire l'interazione umana.

> [!div class="nextstepaction"]
> [Eseguire l'esempio di interazione umana](durable-functions-phone-verification.md)
