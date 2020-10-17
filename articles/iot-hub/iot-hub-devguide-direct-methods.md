---
title: Informazioni sui metodi diretti dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: usare metodi diretti per richiamare il codice nei dispositivi da un'app di servizio."
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147804"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Comprendere e richiamare metodi diretti dall'hub IoT

L'hub IoT offre la possibilità di richiamare metodi diretti nei dispositivi dal cloud. I metodi diretti rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, dato che dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente. Questo approccio è utile per gli scenari in cui la linea di condotta immediata è diversa a seconda che il dispositivo sia in grado di rispondere o meno,

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ogni metodo del dispositivo è destinato a un unico dispositivo. In [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md) viene descritto come fornire un modo per richiamare i metodi diretti in più dispositivi e di pianificare la chiamata al metodo per i dispositivi disconnessi.

Chiunque abbia autorizzazioni di **connessione servizio** per l'hub IoT può richiamare un metodo in un dispositivo.

I metodi diretti si basano su un modello di tipo richiesta-risposta e sono destinati a comunicazioni che necessitano di una conferma immediata del risultato, ad esempio il controllo interattivo del dispositivo, come l'accensione di un ventilatore.

Vedere [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo) in caso di dubbi tra l'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="method-lifecycle"></a>Ciclo di vita dei metodi

I metodi diretti vengono implementati nel dispositivo. Per creare correttamente un'istanza possono essere necessari zero o più input nel payload del metodo. Per richiamare un metodo diretto è possibile usare un URI per il servizio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo riceve metodi diretti tramite un argomento MQTT specifico del dispositivo (`$iothub/methods/POST/{method name}/`) o tramite collegamenti AMQP (proprietà `IoThub-methodname` e `IoThub-status` dell'applicazione).

> [!NOTE]
> Quando si richiama un metodo diretto in un dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici stampabili US-ASCII, ad eccezione dei seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

I metodi diretti sono sincroni e possono avere esito positivo o negativo dopo il periodo di timeout (valore predefinito: 30 secondi, impostabile tra 5 e 300 secondi). Risultano utili negli scenari interattivi in cui si vuole che il dispositivo agisca esclusivamente se è online e riceve comandi, ad esempio nel caso dell'accensione di una luce da un telefono. In questi scenari l'esito positivo o negativo deve essere immediato, in modo che il servizio cloud possa agire in base al risultato il prima possibile. Il dispositivo può restituire un corpo del messaggio come risultato del metodo, ma non è necessario che il metodo esegua questa operazione. Nelle chiamate ai metodi non esiste alcuna garanzia di ordinamento o semantica di concorrenza.

I metodi diretti sono solo HTTPS dal lato cloud e MQTT, AMQP, MQTT su WebSocket o AMQP su WebSocket dal lato dispositivo.

Il payload per le richieste e le risposte del metodo è un documento JSON con dimensioni massime di 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Richiamare un metodo diretto da un'app back-end

Richiamare ora un metodo diretto da un'app back-end.

### <a name="method-invocation"></a>Chiamata al metodo

Le chiamate a metodi diretti in un dispositivo sono chiamate HTTPS composte dagli elementi seguenti:

* *URI della richiesta* specifico del dispositivo con la [versione dell'API](/rest/api/iothub/service/devices/invokemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metodo* POST

* *Intestazioni* contenenti l'autorizzazione, l'ID richiesta, il tipo di contenuto e la codifica del contenuto.

* *Corpo* JSON trasparente nel formato seguente:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Il valore fornito come `responseTimeoutInSeconds` nella richiesta è la quantità di tempo per cui il servizio hub Internet deve attendere il completamento dell'esecuzione di un metodo diretto in un dispositivo. Impostare questo timeout almeno fino a quando il tempo di esecuzione previsto di un metodo diretto da parte di un dispositivo. Se il timeout non viene specificato, viene usato il valore predefinito di 30 secondi. I valori minimo e massimo per `responseTimeoutInSeconds` sono rispettivamente 5 e 300 secondi.

Il valore fornito come `connectTimeoutInSeconds` nella richiesta è la quantità di tempo dopo la chiamata di un metodo diretto che il servizio hub Internet deve attendere per la connessione di un dispositivo disconnesso. Il valore predefinito è 0, ovvero i dispositivi devono essere già online al momento della chiamata a un metodo diretto. Il valore massimo per `connectTimeoutInSeconds` è 300 secondi.

#### <a name="example"></a>Esempio

Questo esempio consentirà di avviare in modo sicuro una richiesta per richiamare un metodo diretto su un dispositivo Internet degli utenti registrato in un hub Azure.

Per iniziare, usare l' [estensione Microsoft Azure Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure per creare un SharedAccessSignature.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Sostituire quindi l'intestazione di autorizzazione con il SharedAccessSignature appena generato, quindi modificare i `iothubName` `deviceId` parametri, `methodName` e `payload` in modo che corrispondano all'implementazione nel `curl` comando di esempio seguente.  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

Eseguire il comando modificato per richiamare il metodo diretto specificato. Le richieste riuscite restituiranno un codice di stato HTTP 200.

> [!NOTE]
> Nell'esempio precedente viene illustrato come richiamare un metodo diretto in un dispositivo.  Se si vuole richiamare un metodo diretto in un modulo IoT Edge, è necessario modificare la richiesta URL come illustrato di seguito:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Risposta

L'app back-end riceve una risposta composta dagli elementi seguenti:

* *Codice di stato http*:
  * 200 indica l'esecuzione corretta del metodo diretto;
  * 404 indica che l'ID dispositivo non è valido o che il dispositivo non era online al momento della chiamata di un metodo diretto e per `connectTimeoutInSeconds` poi (usare un messaggio di errore accompagnato per comprendere la causa principale);
  * 504 indica il timeout del gateway causato dal dispositivo che non risponde a una chiamata al metodo diretto all'interno di `responseTimeoutInSeconds` .

* *Intestazioni* contenenti l'ETag, l'ID richiesta, il tipo di contenuto e la codifica del contenuto.

* *Corpo* JSON nel formato seguente:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Sia `status` che `body` vengono forniti dal dispositivo e usati per rispondere con la descrizione e/o il codice di stato del dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Chiamata al metodo per i moduli di IoT Edge

La chiamata di metodi diretti mediante un ID modulo è supportata nell'[SDK per C# del client del servizio IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

A tale scopo, usare il metodo `ServiceClient.InvokeDeviceMethodAsync()` e passare `deviceId` e `moduleId` come parametri.

## <a name="handle-a-direct-method-on-a-device"></a>Gestire un metodo diretto in un dispositivo

Si vedrà ora come gestire un metodo diretto in un dispositivo IoT.

### <a name="mqtt"></a>MQTT

La sezione seguente è per il protocollo MQTT.

#### <a name="method-invocation"></a>Chiamata al metodo

I dispositivi ricevono richieste di metodi diretti nell'argomento MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. Il numero di sottoscrizioni per dispositivo è limitato a 5. È pertanto consigliabile non sottoscrivere ogni metodo diretto singolarmente. Valutare invece la possibilità di sottoscrivere `$iothub/methods/POST/#` e quindi di filtrare i messaggi recapitati in base ai nomi dei metodi desiderati.

Il corpo ricevuto dal dispositivo è nel formato seguente:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Le richieste di metodo sono QoS 0.

#### <a name="response"></a>Risposta

Il dispositivo invia risposte a `$iothub/methods/res/{status}/?$rid={request id}`, in cui:

* La proprietà `status` è lo stato di esecuzione del metodo fornito dal dispositivo.

* La proprietà `$rid` è l'ID richiesta della chiamata al metodo ricevuta dall'hub IoT.

Il corpo è impostato dal dispositivo e accetta qualsiasi stato.

### <a name="amqp"></a>AMQP

La sezione seguente è per il protocollo AMQP.

#### <a name="method-invocation"></a>Chiamata al metodo

Il dispositivo riceve richieste di metodi diretti tramite la creazione di un collegamento di ricezione sull'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Il messaggio AMQP arriva sul collegamento di ricezione che rappresenta la richiesta del metodo. Contiene le sezioni seguenti:

* La proprietà ID di correlazione, contenente un ID richiesta che deve essere passato con la relativa risposta del metodo.

* Una proprietà dell'applicazione denominata `IoThub-methodname`, contenente il nome del metodo richiamato.

* Il corpo del messaggio AMQP, contenente il payload del metodo in formato JSON.

#### <a name="response"></a>Risposta

Il dispositivo crea un collegamento di invio per restituire la risposta del metodo all'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

La risposta del metodo viene restituita sul collegamento di invio ed è strutturata come segue:

* La proprietà ID di correlazione, che contiene l'ID richiesta passato nel messaggio di richiesta del metodo.

* Una proprietà dell'applicazione denominata `IoThub-status`, contenente lo stato del metodo fornito dall'utente.

* Il corpo del messaggio AMQP, contenente la risposta del metodo in formato JSON.

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.

* [Limitazione e quote](iot-hub-devguide-quotas-throttling.md) descrive le quote applicabili al comportamento di limitazione previsto quando si usa l'hub IoT.

* [Azure IoT SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.

* [Il linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing messaggi](iot-hub-devguide-query-language.md) descrive il linguaggio di query dell'hub IoT che è possibile usare per recuperare informazioni dall'hub IoT sui processi e i dispositivi gemelli.

* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md) offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i metodi diretti, è possibile vedere un altro articolo di interesse della Guida per sviluppatori dell'hub IoT:

* [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md)

Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Usare metodi diretti](quickstart-control-device-node.md)
* [Gestione dei dispositivi con Azure IoT Tools per VS Code](iot-hub-device-management-iot-toolkit.md)