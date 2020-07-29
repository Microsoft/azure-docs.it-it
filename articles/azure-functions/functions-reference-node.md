---
title: Informazioni di riferimento per sviluppatori JavaScript per funzioni di Azure
description: Informazioni su come sviluppare funzioni con JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 636df121ea5b09a421ae7bedaf9e30cbbd1966f5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87280869"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guida per gli sviluppatori JavaScript di Funzioni di Azure

Questa guida contiene informazioni dettagliate che consentono di sviluppare funzioni di Azure con JavaScript.

Per gli sviluppatori di Express.js, Node.js o JavaScript, se non si ha familiarità con funzioni di Azure, prendere in considerazione prima di tutto uno degli articoli seguenti:

| Introduzione | Concetti| Apprendimento guidato |
| -- | -- | -- | 
| <ul><li>[Node.js funzione utilizzando Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)</li><li>[FunzioneNode.js con terminale/prompt dei comandi](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-javascript)</li></ul> | <ul><li>[Guida per sviluppatori](functions-reference.md)</li><li>[Opzioni di hosting](functions-scale.md)</li><li>[Funzioni TypeScript](#typescript)</li><li>[Considerazioni sulle prestazioni &nbsp;](functions-best-practices.md)</li></ul> | <ul><li>[Creare applicazioni serverless](/learn/paths/create-serverless-applications/)</li><li>[Refactoring Node.js ed Express API per le API senza server](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Nozioni fondamentali sulle funzioni JavaScript

Una funzione JavaScript (Node.js) è un oggetto esportato `function` che viene eseguito quando viene attivato ([i trigger sono configurati in function.jssu](functions-triggers-bindings.md)). Il primo argomento passato a ogni funzione è un `context` oggetto, che viene usato per la ricezione e l'invio di dati di binding, la registrazione e la comunicazione con il Runtime.

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle di output per un progetto JavaScript ha un aspetto simile alla seguente. Questa impostazione predefinita non può essere modificata. Per altre informazioni, vedere la sezione [scriptFile](#using-scriptfile) di seguito.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Nella radice del progetto è presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Per ogni funzione è presente una cartella con il file di codice (JS) e il file di configurazione delle associazioni (function.json) correlati. Il nome della directory padre di `function.json` è sempre il nome della funzione.

Le estensioni di associazione richieste nella [versione 2.x](functions-versions.md) del runtime di Funzioni sono definite nel file `extensions.csproj`, con gli effettivi file di libreria inclusi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](./functions-bindings-register.md#extension-bundles). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

## <a name="exporting-a-function"></a>Esportazione di una funzione

Le funzioni JavaScript devono essere esportate tramite [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (o [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). La funzione esportata deve essere una funzione JavaScript che viene eseguita quando attivata.

Per impostazione predefinita, il runtime di Funzioni cerca la funzione in `index.js`, dove `index.js` condivide la stessa directory padre del file `function.json` corrispondente. Nel caso predefinito la funzione esportata deve essere l'unica esportazione dal relativo file, ovvero l'esportazione denominata `run` o `index`. Per configurare il percorso del file e il nome di esportazione della funzione, vedere [Configurare il punto di ingresso della funzione](functions-reference-node.md#configure-function-entry-point) più avanti.

Alla funzione esportata vengono passati vari di argomenti per l'esecuzione. Il primo argomento accettato è sempre un oggetto `context`. Se la funzione è sincrona (non restituisce un suggerimento), è necessario passare l' `context` oggetto, perché la chiamata `context.done` è necessaria per l'uso corretto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Esportazione di una funzione asincrona
Quando si usa la [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) dichiarazione o le [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) JavaScript semplici nella versione 2. x del runtime di funzioni, non è necessario chiamare in modo esplicito il [`context.done`](#contextdone-method) callback per segnalare che la funzione è stata completata. La funzione viene completata al termine della funzione asincrona esportata o di Promise. Per le funzioni destinate al runtime della versione 1. x, è comunque necessario chiamare al [`context.done`](#contextdone-method) termine dell'esecuzione del codice.

L'esempio seguente è una semplice funzione che registra che è stata attivata e termina immediatamente l'esecuzione.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Quando si esporta una funzione asincrona, è possibile anche configurare il valore `return` per un'associazione di output. Questa procedura è consigliata in presenza di una sola associazione di output.

Per assegnare un output usando `return`, modificare la proprietà `name` in `$return` nel file `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In questo caso, la funzione dovrebbe essere simile all'esempio seguente:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Associazioni 
In JavaScript le [associazioni](functions-triggers-bindings.md) vengono configurate e definite nel file function.json di una funzione. Le funzioni interagiscono con le associazioni in molti modi.

### <a name="inputs"></a>Input
In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Le associazioni di trigger e di altri input (associazioni di `direction === "in"`) possono essere lette da una funzione in tre modi:
 - **_[Consigliato] _ Come parametri passati alla funzione.** Vengono passati alla funzione nell'ordine in cui sono definiti in *function.json*. La `name` proprietà definita in *function.json* non deve necessariamente corrispondere al nome del parametro, anche se deve essere.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Come membri dell'oggetto [`context.bindings`](#contextbindings-property).** Ogni membro è denominato in base alla proprietà `name` definita in *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Come input tramite l'oggetto JavaScript [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments).** Ciò equivale essenzialmente a passare gli input come parametri, ma consente di gestire in modo dinamico gli input.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Output
Gli output (associazioni di `direction === "out"`) possono essere scritti da una funzione in diversi modi. In tutti i casi, la proprietà `name` dell'associazione come definita in *function.json* corrisponde al nome del membro dell'oggetto scritto nella funzione. 

È possibile assegnare i dati alle associazioni di output in uno dei modi seguenti (non combinare questi metodi):

- **_[Consigliato per più output] _ Restituzione di un oggetto.** Se si usa una funzione di restituzione asincrona/Promise, è possibile restituire un oggetto con i dati di output assegnati. Nell'esempio seguente, le associazioni di output sono denominate "httpResponse" e "queueOutput" in *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Se si usa una funzione sincrona, è possibile restituire questo oggetto usando [`context.done`](#contextdone-method) (vedere l'esempio).
- **_[Consigliato per singolo output] _ Restituzione diretta di un valore e uso del nome di associazione $return.** Questo approccio vale solo per le funzioni asincrone o che restituiscono oggetti Promise. Vedere l'esempio in [Esportazione di una funzione asincrona](#exporting-an-async-function). 
- **Assegnazione di valori a `context.bindings`** È possibile assegnare valori direttamente a context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo di dati delle associazioni

Per definire il tipo di dati per un'associazione di input, usare la proprietà `dataType` nella definizione dell'associazione. Ad esempio, per leggere il contenuto di una richiesta HTTP in formato binario, usare il tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Le opzioni per `dataType` sono: `binary`, `stream` e `string`.

## <a name="context-object"></a>Oggetto context
Il runtime usa un oggetto `context` per passare dati dalla e alla funzione e consentire la comunicazione con il runtime. L'oggetto context può essere usato per la lettura e l'impostazione dei dati dalle associazioni, la scrittura di log e l'uso del callback `context.done` quando la funzione esportata è sincrona.

L'oggetto `context` è sempre il primo parametro per una funzione. Deve essere incluso perché contiene metodi importanti, come `context.done` e `context.log`. È possibile assegnare all'oggetto un nome qualsiasi, ad esempio `ctx` o `c`.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Proprietà context.bindings

```js
context.bindings
```

Restituisce un oggetto denominato utilizzato per leggere o assegnare dati di associazione. È possibile accedere ai dati di binding di input e trigger leggendo le proprietà su `context.bindings` . I dati dell'associazione di output possono essere assegnati aggiungendo dati a`context.bindings`

Ad esempio, le definizioni di associazione seguenti in function.json consentono di accedere al contenuto di una coda da `context.bindings.myInput` e di assegnare output a una coda tramite `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

È possibile scegliere di definire i dati di associazione di output usando il metodo `context.done` invece dell'oggetto `context.binding` (vedere di seguito).

### <a name="contextbindingdata-property"></a>Proprietà context.bindingData

```js
context.bindingData
```

Restituisce un oggetto denominato che contiene i metadati del trigger e i dati di chiamata della funzione (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Per un esempio dei metadati del trigger, vedere questo [esempio di hub eventi](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Metodo context.done

```js
context.done([err],[propertyBag])
```

Segnala al runtime che il codice è stato completato. Quando la funzione usa la [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) dichiarazione, non è necessario usare `context.done()` . Il callback `context.done` viene chiamato in modo implicito. Le funzioni asincrone sono disponibili in Node 8 o versione successiva, che richiede la versione 2.x del runtime di Funzioni.

Se la funzione non è una funzione asincrona, **è necessario chiamare** `context.done` per informare il runtime che la funzione è stata completata. Si verifica il timeout dell'esecuzione se manca.

Il metodo `context.done` permette di restituire un errore definito dall'utente al runtime e un oggetto JSON contenente i dati di associazione di output. Le proprietà passate a `context.done` sovrascrivono qualsiasi impostazione nell'oggetto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Metodo context.log  

```js
context.log(message)
```

Permette di scrivere nei log della console di streaming nel livello di traccia predefinito. In `context.log` sono disponibili altri metodi di registrazione che permettono di scrivere log di funzioni in altri livelli di traccia:


| Metodo                 | Descrizione                                |
| ---------------------- | ------------------------------------------ |
| **errore (_messaggio_)**   | Scrive nella registrazione a livello di errore o inferiore.   |
| **warn(_messaggio_)**    | Scrive nella registrazione a livello di avviso o inferiore. |
| **info(_messaggio_)**    | Scrive nella registrazione a livello di informazioni o inferiore.    |
| **verbose(_messaggio_)** | Scrive nella registrazione a livello dettagliato.           |

L'esempio seguente scrive un log nel livello di traccia di avviso:

```javascript
context.log.warn("Something has happened."); 
```

È possibile [configurare la soglia del livello di traccia per la registrazione](#configure-the-trace-level-for-console-logging) nel file host.json. Per altre informazioni sulla scrittura di log, vedere [Scrittura dell'output di traccia nella console](#writing-trace-output-to-the-console) più avanti.

Vedere [Monitoraggio di Funzioni di Azure](functions-monitoring.md) per altre informazioni sulla visualizzazione e sull'esecuzione di query su log di funzioni.

## <a name="writing-trace-output-to-the-console"></a>Scrittura dell'output di traccia nella console 

In Funzioni usare i metodi `context.log` per scrivere l'output di traccia nella console. In Funzioni di Azure v2.x gli output di traccia tramite `console.log` vengono acquisiti a livello di app per le funzioni. Ciò significa che gli output di `console.log` non sono associati a una chiamata di funzione specifica e non vengono visualizzati nei log di una funzione specifica. Vengono tuttavia propagati in Application Insights. In Funzioni di Azure v1.x non è possibile usare `console.log` per scrivere nella console.

Quando si chiama `context.log()`, il messaggio viene scritto nella console a livello di traccia predefinito, ovvero il livello di traccia _info_. Il codice seguente scrive nella console a livello di traccia informazioni:

```javascript
context.log({hello: 'world'});  
```

Questo codice equivale al codice mostrato sopra:

```javascript
context.log.info({hello: 'world'});  
```

Questo codice scrive nella console a livello di errore:

```javascript
context.log.error("An error has occurred.");  
```

Poiché _error_ è il livello di traccia più alto, questa traccia viene scritta nell'output a tutti i livelli di traccia, fintantoché la registrazione è abilitata.

Tutti i metodi `context.log` supportano lo stesso formato di parametri supportato dal metodo Node.js [ util.format](https://nodejs.org/api/util.html#util_util_format_format). Si consideri il codice seguente, che scrive log di funzioni usando il livello di traccia predefinito:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

È possibile scrivere lo stesso codice anche nel formato seguente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurare il livello di traccia per la registrazione della console

Con Funzioni 1.x è possibile definire la soglia del livello di traccia per la scrittura nella console. Ciò consente di controllare più facilmente il modo in cui le tracce vengono scritte nella console dalla funzione. Per impostare la soglia per tutte le tracce scritte nella console, usare la proprietà `tracing.consoleLevel` nel file host.json. Questa impostazione si applica a tutte le funzioni dell'app per le funzioni. L'esempio seguente imposta la soglia di traccia per abilitare la registrazione dettagliata:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

I valori di **consoleLevel** corrispondono ai nomi dei metodi `context.log`. Per disabilitare tutta la registrazione traccia nella console, impostare **consoleLevel** su _off_. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Trigger e associazioni HTTP

I trigger e i webhook HTTP e le associazioni di output HTTP usano oggetti di richiesta e risposta per rappresentare la messaggistica HTTP.  

### <a name="request-object"></a>Oggetto della richiesta

L'oggetto (richiesta) `context.req` ha le proprietà seguenti:

| Proprietà      | Descrizione                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Oggetto che contiene il corpo della richiesta.               |
| _intestazioni_     | Oggetto che contiene le intestazioni della richiesta.                   |
| _Metodo_      | Metodo HTTP della richiesta.                                |
| _originalUrl_ | URL della richiesta.                                        |
| _params_      | Oggetto che contiene i parametri di routing della richiesta. |
| _query_       | Oggetto che contiene i parametri di query della richiesta.                  |
| _rawBody_     | Il corpo del messaggio sotto forma di stringa.                           |


### <a name="response-object"></a>Oggetto della risposta

L'oggetto (risposta) `context.res` ha le proprietà seguenti:

| Proprietà  | Descrizione                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Oggetto che contiene il corpo della risposta.         |
| _intestazioni_ | Oggetto che contiene le intestazioni della risposta.             |
| _isRaw_   | Indica che la formattazione viene ignorata per la risposta.    |
| _Stato_  | Codice di stato HTTP della risposta.                     |
| _cookie_ | Matrice di oggetti cookie HTTP impostati nella risposta. Un oggetto cookie HTTP presenta `name` , `value` e altre proprietà del cookie, ad esempio `maxAge` o `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Accesso a richiesta e risposta 

Quando si usano trigger HTTP, è possibile accedere agli oggetti richiesta e risposta HTTP in diversi modi:

+ **Dalle proprietà `req` e `res` per l'oggetto `context`.** In questo modo per accedere ai dati HTTP dall'oggetto di contesto è possibile usare il modello convenzionale anziché il modello `context.bindings.name` completo. L'esempio seguente illustra come accedere agli oggetti `req` e `res` nell'oggetto `context`:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Dalle associazioni di input e di output denominate.** In questo modo, il trigger e le associazioni HTTP funzionano come qualsiasi altra associazione. L'esempio seguente imposta l'oggetto risposta usando un'associazione `response` denominata. 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Solo risposta]_ Chiamando `context.res.send(body?: any)`.** Viene creata una risposta HTTP con input `body` come corpo della risposta. Viene chiamato `context.done()` in modo implicito.

+ **_[Solo risposta]_ Chiamando `context.done()`.** Un tipo speciale di binding HTTP restituisce la risposta che viene passata al `context.done()` metodo. L'associazione di output HTTP seguente definisce un parametro di output `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Scalabilità e concorrenza

Per impostazione predefinita, funzioni di Azure monitora automaticamente il carico sull'applicazione e crea istanze host aggiuntive per Node.js in base alle esigenze. Funzioni usa soglie predefinite (non configurabili dall'utente) per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio l'età dei messaggi e le dimensioni della coda per QueueTrigger. Per altre informazioni, vedere [Come funzionano i piani a consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Questo comportamento di ridimensionamento è sufficiente per molte applicazioni Node.js. Per le applicazioni con associazione alla CPU, è possibile migliorare ulteriormente le prestazioni usando più processi di lavoro in lingua.

Per impostazione predefinita, ogni istanza host di Funzioni include un singolo processo di lavoro del linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) usando l'impostazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) dell'applicazione. Funzioni di Azure prova quindi a distribuire uniformemente le chiamate di funzioni simultanee tra questi processi di lavoro. 

FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato da Funzioni quando le istanze dell'applicazione vengono aumentate per soddisfare la domanda. 

## <a name="node-version"></a>Versione del nodo

La tabella seguente mostra le versioni correnti di Node.js supportate per ogni versione principale del runtime di funzioni, per sistema operativo:

| Versione di Funzioni | Versione del nodo (Windows) | Versione del nodo (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (bloccata dal runtime) | n/d |
| 2.x  | ~ 8<br/>~ 10 (scelta consigliata)<br/>~ 12<sup>*</sup> | ~ 8 (scelta consigliata)<br/>~ 10  |
| 3.x | ~ 10<br/>~ 12 (scelta consigliata)  | ~ 10<br/>~ 12 (scelta consigliata) |

<sup>*</sup>Il nodo ~ 12 è attualmente consentito nella versione 2. x del runtime di funzioni. Tuttavia, per ottenere prestazioni ottimali, è consigliabile usare funzioni runtime versione 3. x con node ~ 12. 

È possibile visualizzare la versione corrente usata dal runtime controllando l'impostazione dell'app seguente o stampando `process.version` da qualsiasi funzione. Specificare come destinazione la versione in Azure impostando l' [impostazione dell'app](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION su una versione LTS supportata, ad esempio `~10` .

## <a name="dependency-management"></a>Gestione delle dipendenze
Per poter usare librerie della community nel codice JavaScript, come mostrato nell'esempio seguente, è necessario assicurarsi che tutte le dipendenze siano installate nell'app per le funzioni in Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> È necessario definire un file `package.json` nella directory principale dell'app per le funzioni. La definizione del file consente a tutte le funzioni dell'app di condividere gli stessi pacchetti memorizzati nella cache garantendo prestazioni migliori. In caso di conflitto di versione, è possibile risolverlo aggiungendo un file `package.json` nella cartella di una funzione specifica.  

Quando si distribuiscono le app per le funzioni dal controllo del codice sorgente, durante la distribuzione qualsiasi file `package.json` presente nel repository attiverà un comando `npm install` nella cartella in cui si trova. Ma quando si esegue una distribuzione tramite il portale o l'interfaccia della riga di comando, è necessario installare il pacchetto manualmente.

Esistono due modi per installare pacchetti nell'app per le funzioni: 

### <a name="deploying-with-dependencies"></a>Distribuzione con le dipendenze
1. Installare tutti i pacchetti necessari in locale eseguendo `npm install`.

2. Distribuire il codice e assicurarsi che la cartella `node_modules` sia inclusa nella distribuzione. 


### <a name="using-kudu"></a>Tramite Kudu
1. Passare a `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **console di debug**  >  **cmd**.

3. Passare a `D:\home\site\wwwroot`, quindi trascinare il file package.json nella cartella **wwwroot** nella metà superiore della pagina.  
    È possibile caricare i file nell'app per le funzioni anche in altri modi. Per altre informazioni, vedere [Come aggiornare i file delle app per le funzioni](functions-reference.md#fileupdate). 

4. Dopo aver caricato il file package.json, eseguire il comando `npm install` nella ** console di esecuzione remota Kudu**.  
    Questa azione scarica i pacchetti indicati nel file package.json e riavvia l'app per le funzioni.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni usando `process.env` , come illustrato di seguito nella seconda e terza chiamata a `context.log()` dove si registrano le `AzureWebJobsStorage` variabili di ambiente e `WEBSITE_SITE_NAME` :

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Per l'esecuzione in locale, le impostazioni dell'app vengono lette dal file di progetto [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="configure-function-entry-point"></a>Configurare il punto di ingresso della funzione

È possibile usare le proprietà `scriptFile` e `entryPoint` di `function.json` per configurare il percorso e il nome della funzione esportata. Queste proprietà possono essere importanti quando JavaScript viene sottoposto a transpile.

### <a name="using-scriptfile"></a>Uso di `scriptFile`

Per impostazione predefinita, viene eseguita una funzione JavaScript da `index.js`, un file che condivide la stessa directory padre del file `function.json` corrispondente.

È possibile usare `scriptFile` per ottenere una struttura di cartelle simile a quella nell'esempio seguente:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Il file `function.json` per `myNodeFunction` deve includere una proprietà `scriptFile` che punta al file con la funzione esportata da eseguire.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Uso di `entryPoint`

In `scriptFile` (o `index.js`) una funzione deve essere esportata tramite `module.exports` per poter essere trovata ed eseguita. Per impostazione predefinita, la funzione eseguita quando viene attivata è l'unica esportazione dal file, ovvero l'esportazione denominata `run` o quella denominata `index`.

Questo aspetto può essere configurato tramite `entryPoint` in `function.json`, come illustrato nell'esempio seguente:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In Funzioni v2.x, che supporta il parametro `this` nelle funzioni utente, il codice della funzione può quindi essere simile all'esempio seguente:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In questo esempio, è importante notare che, anche se è in corso l'esportazione di un oggetto, non vi sono garanzie per mantenere lo stato tra le esecuzioni.

## <a name="local-debugging"></a>Debug locale

Quando viene avviato con il `--inspect` parametro, un Node.js processo è in attesa di un client di debug sulla porta specificata. In funzioni di Azure 2. x è possibile specificare gli argomenti da passare al processo di Node.js che esegue il codice aggiungendo la variabile di ambiente o l'impostazione dell'app `languageWorkers:node:arguments = <args>` . 

Per eseguire il debug in locale, aggiungere all' `"languageWorkers:node:arguments": "--inspect=5858"` `Values` interno del [local.settings.js](./functions-run-local.md#local-settings-file) nel file e alleghiare un debugger alla porta 5858.

Quando si esegue il debug con VS Code, il `--inspect` parametro viene aggiunto automaticamente usando il `port` valore nel file launch.jsdel progetto.

Nella versione 1. x, l'impostazione `languageWorkers:node:arguments` non funzionerà. È possibile selezionare la porta di debug con il [`--nodeDebugPort`](./functions-run-local.md#start) parametro in Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Quando la versione 2. x del runtime di funzioni è la destinazione, entrambe le [funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md) e il [Azure Functions Core Tools](functions-run-local.md) consentono di creare app per le funzioni usando un modello che supporta i progetti di app per le funzioni typescript. Il modello genera `package.json` `tsconfig.json` file di progetto e che semplificano il transpile, l'esecuzione e la pubblicazione di funzioni JavaScript dal codice typescript con questi strumenti.

Un `.funcignore` file generato viene usato per indicare quali file vengono esclusi quando un progetto viene pubblicato in Azure.  

I file TypeScript (. TS) vengono traspilati in file JavaScript (con estensione js) nella `dist` directory di output. I modelli TypeScript usano il [ `scriptFile` parametro](#using-scriptfile) in `function.json` per indicare la posizione del file con estensione js corrispondente nella `dist` cartella. Il percorso di output viene impostato dal modello utilizzando il `outDir` parametro nel `tsconfig.json` file. Se si modifica questa impostazione o il nome della cartella, il runtime non è in grado di trovare il codice da eseguire.

Il modo in cui si sviluppa e distribuisce localmente da un progetto TypeScript dipende dallo strumento di sviluppo.

### <a name="visual-studio-code"></a>Visual Studio Code

[Funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) estensione consente di sviluppare le funzioni usando typescript. Gli strumenti di base sono un requisito dell'estensione funzioni di Azure.

Per creare un'app per le funzioni TypeScript in Visual Studio Code, scegliere `TypeScript` come lingua quando si crea un'app per le funzioni.

Quando si preme **F5** per eseguire l'app in locale, transpilazione viene eseguita prima dell'inizializzazione dell'host (func.exe). 

Quando si distribuisce l'app per le funzioni in Azure usando il pulsante **Distribuisci in app per** le funzioni, l'estensione di funzioni di Azure genera prima di tutto una compilazione di file JavaScript pronta per la produzione dai file di origine typescript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Esistono diversi modi in cui un progetto TypeScript differisce da un progetto JavaScript quando si usano gli strumenti di base.

#### <a name="create-project"></a>Crea progetto

Per creare un progetto di app per le funzioni TypeScript usando gli strumenti di base, è necessario specificare l'opzione di lingua TypeScript quando si crea l'app per le funzioni. Questa operazione può essere eseguita in uno dei modi seguenti:

- Eseguire il `func init` comando, selezionare `node` come lo stack della lingua, quindi selezionare `typescript` .

- Eseguire il comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Esegui locale

Per eseguire localmente il codice dell'app per le funzioni usando gli strumenti di base, usare i comandi seguenti anziché `func host start` : 

```command
npm install
npm start
```

Il `npm start` comando è equivalente ai comandi seguenti:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Pubblicazione in Azure

Prima di usare il [`func azure functionapp publish`] comando per eseguire la distribuzione in Azure, creare una compilazione di file JavaScript pronta per la produzione dai file di origine typescript. 

I comandi seguenti preparano e pubblicano il progetto TypeScript usando gli strumenti principali: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

In questo comando sostituire `<APP_NAME>` con il nome dell'app per le funzioni.

## <a name="considerations-for-javascript-functions"></a>Considerazioni per le funzioni JavaScript

Quando si usano funzioni JavaScript, tenere presente le considerazioni indicate nelle due sezioni seguenti.

### <a name="choose-single-vcpu-app-service-plans"></a>Scegliere i piani di servizio app con una singola vCPU

Quando si crea un'app per le funzioni che usa il piano di servizio app, è consigliabile selezionare un piano con una singola vCPU anziché uno con più vCPU. Oggi Funzioni esegue funzioni JavaScript in modo più efficiente in macchine virtuali con una singola vCPU e l'uso di macchine virtuali di dimensioni maggiori non produce i miglioramenti delle prestazioni previsti. Quando necessario, è possibile aumentare manualmente il numero di istanze aggiungendo altre istanze di macchine virtuali vCPU. in alternativa, è possibile abilitare la scalabilità automatica. Per altre informazioni, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Avvio a freddo

Quando si sviluppano funzioni di Azure in un modello di hosting serverless, gli avvii a freddo sono una realtà. Il termine *avvio a freddo* si riferisce al fatto che quando viene avviata per la prima volta dopo un periodo di inattività, l'app per le funzioni richiede più tempo per l'avvio. Per le funzioni di JavaScript con alberi delle dipendenze estesi, in particolare, l'avvio a freddo può essere significativo. Per velocizzare il processo di avvio a freddo [eseguire le funzioni come un file di pacchetto](run-functions-from-deployment-package.md) quando possibile. Molti metodi di distribuzione usano il modello di esecuzione dal pacchetto per impostazione predefinita, ma in presenza di numerosi avvii a freddo e se questo non è il modello di esecuzione in uso, questa modifica può offrire miglioramenti significativi.

### <a name="connection-limits"></a>Limiti connessione

Quando si usa un client specifico del servizio in un'applicazione di funzioni di Azure, non creare un nuovo client con ogni chiamata di funzione. In alternativa, creare un singolo client statico nell'ambito globale. Per altre informazioni, vedere [gestione delle connessioni in funzioni di Azure](manage-connections.md).

### <a name="use-async-and-await"></a>Usare `async` e`await`

Quando si scrivono funzioni di Azure in JavaScript, è necessario scrivere codice usando le `async` `await` parole chiave e. La scrittura di codice tramite `async` e `await` anziché callback o `.then` e `.catch` con le promesse consente di evitare due problemi comuni:
 - Generazione di eccezioni non rilevate che [arrestano l'arresto anomalo del Node.js processo](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), che potenzialmente influisce sull'esecuzione di altre funzioni.
 - Comportamento imprevisto, ad esempio log mancanti da context. log, causato da chiamate asincrone non correttamente attese.

Nell'esempio seguente il metodo asincrono `fs.readFile` viene richiamato con una funzione di callback di errore-prima come secondo parametro. Questo codice causa entrambi i problemi menzionati in precedenza. Un'eccezione non rilevata in modo esplicito nell'ambito corretto è stata arrestata in modo anomalo nell'intero processo (problema #1). La chiamata al `context.done()` di fuori dell'ambito della funzione di callback significa che la chiamata della funzione può terminare prima che il file venga letto (problema #2). In questo esempio, chiamando `context.done()` troppo presto i risultati mancanti nelle voci di log che iniziano con `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

L'utilizzo `async` delle `await` parole chiave e consente di evitare questi errori. È consigliabile usare la funzione di utilità Node.js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) per trasformare le funzioni di tipo callback con errori prima in funzioni attese.

Nell'esempio seguente, le eccezioni non gestite generate durante l'esecuzione della funzione hanno esito negativo solo sulla chiamata che ha generato un'eccezione. La `await` parola chiave indica che i passaggi successivi `readFileAsync` vengono eseguiti solo dopo il `readFile` completamento di. Con `async` e `await` non è inoltre necessario chiamare il `context.done()` callback.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

+ [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
+ [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
+ [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

[' Func Azure functionapp publish ']: functions-run-local.md#project-file-deployment
