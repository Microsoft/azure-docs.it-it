---
title: Usare i proxy in funzioni di Azure
description: Informazioni generali sull'uso dei proxy in Funzioni di Azure
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74849191"
---
# <a name="work-with-azure-functions-proxies"></a>Usare i proxy di Funzioni di Azure

Questo articolo illustra come configurare e usare i proxy in Funzioni di Azure. Questa funzionalità consente di specificare gli endpoint nell'app per le funzioni implementati da un'altra risorsa. È possibile usare questi proxy per suddividere un'API di grandi dimensioni in più app per le funzioni (come in un'architettura di microservizio), pur continuando a presentare una singola superficie API per i client.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Si applicano le tariffe standard per Funzioni quando vengono eseguiti i proxy. Per altre informazioni, vedere [Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Creazione di un proxy

In questa sezione viene descritto come creare un proxy nel portale Funzioni.

1. Aprire il [Portale di Azure] e passare all'app per le funzioni.
2. Nel riquadro sinistro selezionare **Nuovo proxy**.
3. Dare un nome al proxy.
4. Configurare l'endpoint esposto in questa app per le funzioni, specificando il **Modello di route** e i **Metodi HTTP**. Questi parametri si comportano in base alle regole dei [trigger HTTP].
5. Impostare l'**URL di back-end** su un altro endpoint. Questo endpoint potrebbe essere una funzione in un'altra app per le funzioni oppure di qualsiasi altra API. Il valore non deve essere statico e può fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].
6. Fare clic su **Crea**.

Il proxy è ora presente come un nuovo endpoint sull'app per le funzioni. Dalla prospettiva del client, è equivalente a un HttpTrigger nelle Funzioni di Azure. È possibile provare il nuovo proxy copiando l'URL del proxy ed eseguendo un test con il proprio client HTTP preferito.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Modificare richieste e risposte

Proxy di Funzioni di Azure consente di modificare le richieste al back-end e le risposte dal back-end. Queste trasformazioni possono usare le variabili come definito in [Usare le variabili].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Modificare la richiesta al back-end

Per impostazione predefinita, la richiesta al back-end viene inizializzata come una copia della richiesta originale. Oltre a impostare l'URL di back-end, è possibile apportare modifiche ai parametri del metodo HTTP, delle intestazioni e della stringa di query. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione] e ai [parametri della richiesta del client originale].

Le richieste di back-end possono essere modificate nel portale espandendo la sezione *Override della richiesta* nella pagina dei dettagli del proxy. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Modificare la risposta

Per impostazione predefinita, la risposta del client viene inizializzata come una copia della risposta back-end. È possibile apportare modifiche al codice di stato, al motivo, alle intestazioni e al corpo della risposta. I valori modificati possono fare riferimento alle [impostazioni dell'applicazione], ai [parametri della richiesta del client originale] e ai [paramenti della risposta back-end].

Le richieste di back-end possono essere modificate nel portale espandendo la sezione *Override della richiesta* nella pagina dei dettagli del proxy. 

## <a name="use-variables"></a><a name="using-variables"></a>Usare le variabili

La configurazione di un proxy non deve essere statica. È possibile condizionarla per fare in modo che usi le variabili della richiesta client originale, la risposta back-end o le impostazioni applicazione.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Fare riferimento alle funzioni locali
È possibile usare `localhost` per fare direttamente riferimento a una funzione nella stessa app per le funzioni, senza una richiesta del proxy di round trip.

`"backendurl": "https://localhost/api/httptriggerC#1"` farà riferimento a una funzione attivata tramite HTTP locale nella route `/api/httptriggerC#1`

 
>[!Note]  
>Se la funzione usa il livello di autorizzazione *function, admin o sys*, sarà necessario specificare il codice e il clientId in base all'URL della funzione originale. In questo caso il riferimento avrà un aspetto simile `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` al seguente: è consigliabile archiviare queste chiavi nelle [impostazioni dell'applicazione] e fare riferimento a quelle presenti nei proxy. In questo modo si evita di archiviare i segreti nel codice sorgente. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametri di riferimento della richiesta

I parametri della richiesta possono essere usati come input per la proprietà URL di back-end o come parte della modifica di richieste e risposte. Alcuni parametri possono essere associati dal modello di route specificato nella configurazione del proxy di base, mentre altri derivano dalle proprietà della richiesta in ingresso.

#### <a name="route-template-parameters"></a>Parametri del modello di route
È possibile fare riferimento ai parametri usati nel modello di route in base al nome. I nomi dei parametri sono racchiusi tra parentesi graffe ({}).

Ad esempio, se un proxy ha un modello di route come `/pets/{petId}`, l'URL di back-end può includere il valore `{petId}`, come in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se il modello di route termina con un carattere jolly, ad esempio `/api/{*restOfPath}`, il valore `{restOfPath}` è una rappresentazione di stringa dei segmenti del percorso rimanente della richiesta in ingresso.

#### <a name="additional-request-parameters"></a>Parametri aggiuntivi della richiesta
Oltre ai parametri del modello di route, i valori seguenti possono essere usati nei valori di configurazione:

* **{request.method}**: il metodo HTTP usato nella richiesta originale.
* **{request.headers.\<HeaderName\>}**: un'intestazione che può essere letta dalla richiesta originale. Sostituire * \<headerName\> * con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella richiesta, il valore sarà una stringa vuota.
* **{request.querystring.\<ParameterName\>}**: un parametro di stringa di query che può essere letto dalla richiesta originale. Sostituire * \<ParameterName\> * con il nome del parametro che si desidera leggere. Se il parametro non è incluso nella richiesta, il valore sarà una stringa vuota.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Parametri di riferimento della risposta dal back-end

I parametri di risposta possono essere usati come parte della modifica della risposta al client. I valori seguenti possono essere usati nei valori di configurazione:

* **{backend.response.statusCode}**: il codice di stato HTTP restituito nella risposta dal back-end.
* **{backend.response.statusReason}**: la frase per il motivo HTTP restituita nella risposta dal back-end.
* **{backend.response.headers.\<HeaderName\>}**: un'intestazione che può essere letta dalla risposta dal back-end. Sostituire * \<headerName\> * con il nome dell'intestazione che si desidera leggere. Se l'intestazione non è inclusa nella risposta, il valore sarà una stringa vuota.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Impostazioni di riferimento dell'applicazione

È anche possibile fare riferimento alle [impostazioni dell'applicazione definite per l'app per le funzioni](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) racchiudendo il nome dell'impostazione tra i segni di percentuale (%).

Un URL back-end di *https://%ORDER_PROCESSING_HOST%/api/orders* , ad esempio, avrebbe sostituito "% ORDER_PROCESSING_HOST%" con il valore dell'impostazione ORDER_PROCESSING_HOST.

> [!TIP] 
> Usare le impostazioni dell'applicazione per gli host di back-end quando si dispone di più distribuzioni o ambienti di test. In questo modo, è possibile assicurarsi di comunicare sempre con il back-end corretto per quell'ambiente.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Risolvere i problemi relativi al proxy

Aggiungendo il flag `"debug":true` a un proxy in `proxies.json` si abiliterà la registrazione del debug. I log vengono archiviati in `D:\home\LogFiles\Application\Proxies\DetailedTrace` e sono accessibili tramite gli strumenti avanzati (Kudu). Le risposte HTTP conterranno anche un'intestazione `Proxy-Trace-Location` con un URL per accedere al file di log.

È possibile eseguire il debug di un proxy dal lato client aggiungendo un'intestazione `Proxy-Trace-Enabled` impostata su `true`. In questo modo verrà anche registrata una traccia nel file system e l'URL della traccia verrà restituito come intestazione nella risposta.

### <a name="block-proxy-traces"></a>Bloccare le tracce del proxy

Per motivi di sicurezza, potrebbe essere necessario impedire a chiunque chiami il servizio di generare una traccia. Questi utenti non potranno accedere ai contenuti della traccia senza le credenziali di accesso, ma la generazione della traccia utilizza le risorse e rivela che si sta usando i proxy di funzioni.

Disabilitare completamente le tracce aggiungendo `"debug":false` a proxy specifici in `proxies.json`.

## <a name="advanced-configuration"></a>Configurazione avanzata

I proxy configurati vengono archiviati in un file *proxies.json*, situato nella radice di una directory dell'app per le funzioni. È possibile modificare manualmente questo file e distribuirlo come parte dell'app quando si usa uno dei [metodi di distribuzione](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) supportati da Funzioni. 

> [!TIP] 
> Se non è stato configurato uno dei metodi di distribuzione, è anche possibile usare il file *proxies.json* nel portale. Passare all'app per le funzioni e selezionare **Funzionalità della piattaforma** ed **Editor del servizio app**. Questo consentirà di visualizzare la struttura dell'intero file dell'app per le funzioni e di apportare modifiche.

Il file *proxies.json* è definito da un oggetto proxy, composto da proxy denominati e dalle relative definizioni. È facoltativamente possibile fare riferimento a uno [schema JSON](http://json.schemastore.org/proxies) per il completamento del codice se l'editor lo supporta. Un esempio di file apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Ogni proxy ha un nome descrittivo, come *proxy1* nell'esempio precedente. L'oggetto di definizione del proxy corrispondente viene definito dalle proprietà seguenti:

* **matchCondition**: obbligatorio, un oggetto che definisce le richieste che attivano l'esecuzione di questo proxy. Contiene due proprietà condivise con i [trigger HTTP]:
    * _methods_: una matrice di metodi HTTP a cui il proxy risponde. Se non viene specificata, il proxy risponderà a tutti i metodi HTTP nel route.
    * _route_: obbligatorio, definisce il modello di route, controllando a quali URL delle richieste il proxy risponde. A differenza dei trigger HTTP, non vi è alcun valore predefinito.
* **backendUri**: l'URL della risorsa di back-end a cui la richiesta deve essere trasmessa tramite proxy. Questo valore può fare riferimento alle impostazioni dell'applicazione e ai parametri della richiesta del client originale. Se questa proprietà non è inclusa, Funzioni di Azure risponde con un HTTP 200 OK.
* **requestOverrides**: un oggetto che definisce le trasformazioni alla richiesta al back-end. Vedere [Definire un oggetto requestOverrides].
* **responseOverrides**: un oggetto che definisce le trasformazioni alla risposta del client. Vedere [Definire un oggetto responseOverrides].

> [!NOTE] 
> La proprietà *route* in Proxy di Funzioni di Azure non rispetta la proprietà *routePrefix* della configurazione host dell'app per le funzioni. Per includere un prefisso, ad esempio `/api`, deve essere incluso nella proprietà *route*.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Disabilitare i singoli proxy

È possibile disabilitare un singolo proxy aggiungendo `"disabled": true` al proxy nel file `proxies.json`. In questo modo le richieste che soddisfano matchCondidtion restituiranno 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a>Impostazioni applicazione

Il comportamento del proxy può essere controllato da diverse impostazioni dell'app. Sono tutti indicati nel [Informazioni di riferimento sulle impostazioni dell'app per le funzioni](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Caratteri riservati (formattazione della stringa)

I proxy leggono tutte le stringhe all'esterno di un file JSON, utilizzando \ come carattere di escape. I proxy interpretano anche le parentesi graffe. Di seguito è riportata una serie completa di esempi.

|Carattere|Carattere di escape|Esempio|
|-|-|-|
|{ or }|{{ or }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definire un oggetto requestOverrides

L'oggetto requestOverrides definisce le modifiche apportate alla richiesta quando viene chiamata la risorsa back-end. L'oggetto viene definito dalle proprietà seguenti:

* **backend.request.method**: il metodo HTTP usato per chiamare il back-end.
* **backend.request.querystring.\<ParameterName\>**: un parametro di stringa di query che può essere impostato per la chiamata al back-end. Sostituire * \<ParameterName\> * con il nome del parametro che si desidera impostare. Si noti che se viene specificata una stringa vuota, il parametro è ancora incluso nella richiesta back-end.
* **backend.request.headers.\<HeaderName\>**: un'intestazione che può essere impostata per la chiamata al back-end. Sostituire * \<headerName\> * con il nome dell'intestazione che si desidera impostare. Se viene fornita una stringa vuota, il parametro non viene incluso nella richiesta al back-end.

I valori possono fare riferimento alle impostazioni dell'applicazione e ai parametri della richiesta del client originale.

Un esempio di configurazione apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definire un oggetto responseOverrides

L'oggetto responseOverrides definisce le modifiche apportate alla risposta passata al client. L'oggetto viene definito dalle proprietà seguenti:

* **response.statusCode**: il codice di stato HTTP da restituire al client.
* **response.statusReason**: la frase del motivo HTTP da restituire al client.
* **response.body**: la rappresentazione di stringa del corpo da restituire al client.
* **response.headers.\<HeaderName\>**: un'intestazione che può essere impostata per la risposta al client. Sostituire * \<headerName\> * con il nome dell'intestazione che si desidera impostare. Se viene fornita una stringa vuota, l'intestazione non viene inclusa nella richiesta al back-end.

I valori possono fare riferimento alle impostazioni dell'applicazione, ai parametri della richiesta del client originale e ai paramenti della risposta back-end.

Un esempio di configurazione apparirà come segue:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In questo esempio il corpo della risposta viene impostato direttamente, quindi non sono necessarie proprietà `backendUri`. L'esempio illustra come usare i proxy di Funzioni di Azure per le API di simulazione.

[Azure portal]: https://portal.azure.com
[Trigger HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definire un oggetto requestOverrides]: #requestOverrides
[Definire un oggetto responseOverrides]: #responseOverrides
[Impostazioni applicazione]: #use-appsettings
[Usare le variabili]: #using-variables
[parametri della richiesta del client originale]: #request-parameters
[paramenti della risposta back-end]: #response-parameters
