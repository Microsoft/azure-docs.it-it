---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276686"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md). 

Per i progetti di esempio di funzione autonoma in Python, vedere gli [esempi di funzioni di Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modello di programmazione

Funzioni di Azure prevede che una funzione sia un metodo senza stato nello script Python che elabora l'input e genera l'output. Per impostazione predefinita, il runtime prevede che il metodo venga implementato come metodo globale denominato `main()` nel `__init__.py` file. È anche possibile [specificare un punto di ingresso alternativo](#alternate-entry-point).

I dati dei trigger e delle associazioni vengono associati alla funzione tramite gli attributi del metodo `name` usando la proprietà definita nel file *Function. JSON* . Il file _Function. JSON_ seguente, ad esempio, descrive una semplice funzione attivata da una richiesta HTTP `req`denominata:

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

In base a questa definizione, `__init__.py` il file che contiene il codice della funzione potrebbe essere simile all'esempio seguente:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

È anche possibile dichiarare in modo esplicito i tipi di attributo e il tipo restituito nella funzione usando le annotazioni di tipo Python. Questo consente di usare le funzionalità di IntelliSense e di completamento automatico fornite da molti editor di codice Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente.

## <a name="alternate-entry-point"></a>Punto di ingresso alternativo

È possibile modificare il comportamento predefinito di una funzione specificando facoltativamente le `scriptFile` proprietà `entryPoint` e nel file *Function. JSON* . Ad esempio, il file _Function. JSON_ seguente indica al runtime di usare `customentry()` il metodo nel file _Main.py_ , come punto di ingresso per la funzione di Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle consigliata per un progetto di funzioni Python è simile all'esempio seguente:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
La cartella principale del progetto\_\_(\_\_app) può contenere i file seguenti:

* *local. Settings. JSON*: usato per archiviare le impostazioni dell'app e le stringhe di connessione durante l'esecuzione in locale. Questo file non viene pubblicato in Azure. Per altre informazioni, vedere [local. Settings. file](functions-run-local.md#local-settings-file).
* *requirements. txt*: contiene l'elenco dei pacchetti installati dal sistema durante la pubblicazione in Azure.
* *host. JSON*: contiene le opzioni di configurazione globali che interessano tutte le funzioni in un'app per le funzioni. Questo file viene pubblicato in Azure. Non tutte le opzioni sono supportate durante l'esecuzione in locale. Per altre informazioni, vedere [host. JSON](functions-host-json.md).
* *. funcignore*: (facoltativo) dichiara i file che non devono essere pubblicati in Azure.
* *. gitignore*: (facoltativo) dichiara i file che sono esclusi da un repository git, ad esempio local. Settings. JSON.

Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json). 

Quando si distribuisce il progetto in un'app per le funzioni in Azure, l'intero contenuto della cartella principale del progetto (*\_\_app\_*) deve essere incluso nel pacchetto, ma non nella cartella. In questo esempio `tests`si consiglia di mantenere i test in una cartella separata dalla cartella del progetto. In questo modo si impedisce la distribuzione del codice di test con l'app. Per ulteriori informazioni, vedere [unit testing](#unit-testing).

## <a name="import-behavior"></a>Importare il comportamento

È possibile importare moduli nel codice della funzione utilizzando riferimenti espliciti relativi e assoluti. In base alla struttura di cartelle illustrata sopra, le importazioni seguenti funzionano dall'interno della funzione file * \_ \_\_\_app\_\My\_\_\_prima\_\\funzione _ init. py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Le importazioni seguenti *non funzionano* nello stesso file:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Il codice condiviso deve essere mantenuto in una cartella separata * \_ \_nell'\_app*. Per fare riferimento ai moduli nella cartella del *codice condiviso\_* , è possibile usare la sintassi seguente:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Trigger e input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Sebbene siano diversi nel file, `function.json` l'utilizzo è identico nel codice Python.  Le stringhe di connessione o i segreti per i trigger e le origini di `local.settings.json` input eseguono il mapping ai valori nel file durante l'esecuzione in locale e le impostazioni dell'applicazione durante l'esecuzione in Azure. 

Ad esempio, nel codice seguente viene illustrata la differenza tra i due:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Una voce verrà recuperata dall'archiviazione BLOB di Azure in base all' _ID_ nell'URL della route e resa disponibile come `obj` nel corpo della funzione.  In questo caso, l'account di archiviazione specificato è la stringa di connessione trovata nell'impostazione dell'app AzureWebJobsStorage, che è lo stesso account di archiviazione usato dall'app per le funzioni.


## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per produrre più output, utilizzare il `set()` metodo fornito dall' [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interfaccia per assegnare un valore all'associazione. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registrazione

L'accesso al logger del runtime di funzioni di Azure è disponibile [`logging`](https://docs.python.org/3/library/logging.html#module-logging) tramite un gestore radice nell'app per le funzioni. Il logger è associato ad Application Insights e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione della funzione.

L'esempio seguente registra un messaggio informativo quando la funzione viene richiamata tramite un trigger HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Sono disponibili altri metodi di registrazione che consentono di scrivere nella console a livelli di traccia diversi:

| Metodo                 | Descrizione                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Scrive un messaggio con livello critico nel logger radice.  |
| **`error(_message_)`**   | Scrive un messaggio con livello errore nel logger radice.    |
| **`warning(_message_)`**    | Scrive un messaggio con livello avviso nel logger radice.  |
| **`info(_message_)`**    | Scrive un messaggio con livello informativo nel logger radice.  |
| **`debug(_message_)`** | Scrive un messaggio con livello debug nel logger radice.  |

Per altre informazioni sulla registrazione, vedere [monitorare funzioni di Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Trigger e associazioni HTTP

Il trigger HTTP è definito nel file function. Jon. L' `name` oggetto dell'associazione deve corrispondere al parametro denominato nella funzione. Negli esempi precedenti viene usato un nome `req` di binding. Questo parametro è un oggetto [HttpRequest] e viene restituito un oggetto [HttpResponse] .

Dall'oggetto [HttpRequest] è possibile recuperare le intestazioni della richiesta, i parametri di query, i parametri di route e il corpo del messaggio. 

L'esempio seguente è basato sul [modello di trigger http per Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In questa funzione, il valore del parametro `name` di query viene ottenuto dal `params` parametro dell'oggetto [HttpRequest] . Il corpo del messaggio con codifica JSON viene letto usando il `get_json` metodo. 

Analogamente, è possibile impostare `status_code` e `headers` per il messaggio di risposta nell'oggetto [HttpResponse] restituito.

## <a name="scaling-and-concurrency"></a>Ridimensionamento e concorrenza

Per impostazione predefinita, funzioni di Azure monitora automaticamente il carico sull'applicazione e crea istanze host aggiuntive per Python in base alle esigenze. Funzioni utilizza soglie predefinite (non configurabili dall'utente) per diversi tipi di trigger per decidere quando aggiungere istanze, ad esempio l'età dei messaggi e le dimensioni della coda per QueueTrigger. Per altre informazioni, vedere [come funzionano i piani di consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Questo comportamento di ridimensionamento è sufficiente per molte applicazioni. Le applicazioni con le caratteristiche seguenti, tuttavia, non possono essere ridimensionate in modo efficace:

- L'applicazione deve gestire molte chiamate simultanee.
- L'applicazione elabora un numero elevato di eventi di I/O.
- L'applicazione è associata a I/O.

In questi casi, è possibile migliorare ulteriormente le prestazioni utilizzando i modelli asincroni e utilizzando più processi di lavoro in linguaggio.

### <a name="async"></a>Async

Poiché Python è un runtime a thread singolo, un'istanza host per Python può elaborare solo una chiamata di funzione alla volta. Per le applicazioni che elaborano un numero elevato di eventi di I/o e/o è associato a I/o, è possibile migliorare le prestazioni eseguendo funzioni in modo asincrono.

Per eseguire una funzione in modo asincrono, usare `async def` l'istruzione, che esegue direttamente la funzione con [asyncio](https://docs.python.org/3/library/asyncio.html) :

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Una funzione senza la `async` parola chiave viene eseguita automaticamente in un pool di thread asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usare più processi di lavoro in linguaggio

Per impostazione predefinita, ogni istanza host di funzioni ha un singolo processo di lavoro in linguaggio. È possibile aumentare il numero di processi di lavoro per host (fino a 10) usando l'impostazione dell'applicazione [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Funzioni di Azure tenta quindi di distribuire uniformemente le chiamate di funzioni simultanee tra questi thread di lavoro. 

Il FUNCTIONS_WORKER_PROCESS_COUNT si applica a ogni host creato dalle funzioni durante la scalabilità orizzontale dell'applicazione per soddisfare la domanda. 

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) l'argomento nella firma. 

Ad esempio:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**context**](/python/api/azure-functions/azure.functions.context?view=azure-python) presenta gli attributi stringa seguenti:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="global-variables"></a>Variabili globali

Non è garantito che lo stato dell'app verrà mantenuto per le esecuzioni future. Tuttavia, il runtime di funzioni di Azure spesso riutilizza lo stesso processo per più esecuzioni della stessa app. Per memorizzare nella cache i risultati di un calcolo oneroso, dichiararli come una variabile globale. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variabili di ambiente

Nelle funzioni, [le impostazioni dell'applicazione](functions-app-settings.md), ad esempio le stringhe di connessione del servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni dichiarando `import os` e quindi utilizzando `setting = os.environ["setting-name"]`,.

Nell'esempio seguente viene ottenuta l' [impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings)con la `myAppSetting`chiave denominata:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Per lo sviluppo locale, le impostazioni dell'applicazione vengono [mantenute nel file local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versione Python 

Funzioni di Azure supporta le seguenti versioni di Python:

| Versione di Funzioni | Versioni<sup>*</sup> di Python |
| ----- | ----- |
| 3.x | 3.8<br/>3,7<br/>3.6 |
| 2.x | 3,7<br/>3.6 |

<sup>*</sup>Distribuzioni CPython ufficiali

Per richiedere una versione specifica di Python quando si crea l'app per le funzioni in Azure `--runtime-version` , usare l' [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) opzione del comando. La versione del runtime di funzioni è impostata `--functions-version` dall'opzione. La versione di Python viene impostata quando l'app per le funzioni viene creata e non può essere modificata.  

Quando viene eseguito localmente, il runtime usa la versione di Python disponibile. 

## <a name="package-management"></a>Gestione dei pacchetti

Quando si sviluppa in locale usando Azure Functions Core Tools oppure Visual Studio Code, aggiungere i nomi e le versioni dei pacchetti necessari al file `requirements.txt` e installarli con `pip`. 

Ad esempio, per installare il pacchetto `requests` PyPI è possibile usare il file di requisiti e il comando pip seguenti.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Pubblicazione in Azure

Quando si è pronti per la pubblicazione, assicurarsi che tutte le dipendenze disponibili pubblicamente siano elencate nel file requirements. txt che si trova nella radice della directory del progetto. 

I file di progetto e le cartelle escluse dalla pubblicazione, inclusa la cartella dell'ambiente virtuale, sono elencati nel file con estensione funcignore.

Sono supportate tre azioni di compilazione per la pubblicazione del progetto Python in Azure:

+ Compilazione remota: le dipendenze vengono ottenute in modalità remota in base al contenuto del file requirements. txt. [Compilazione remota](functions-deployment-technologies.md#remote-build) è il metodo di compilazione consigliato. Remote è anche l'opzione di compilazione predefinita degli strumenti di Azure. 
+ Compilazione locale: le dipendenze vengono ottenute localmente in base al contenuto del file requirements. txt. 
+ Dipendenze personalizzate: il progetto usa pacchetti non disponibili pubblicamente per i nostri strumenti. (Richiede Docker).

Per compilare le dipendenze e pubblicare usando un sistema di recapito continuo (CD), [usare Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Compilazione remota

Per impostazione predefinita, il Azure Functions Core Tools richiede una compilazione remota quando si usa il comando [Func Azure functionapp Publish](functions-run-local.md#publish) seguente per pubblicare il progetto Python in Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

Per impostazione predefinita, l' [estensione funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) richiede anche una compilazione remota. 

### <a name="local-build"></a>Compilazione locale

È possibile impedire l'esecuzione di una compilazione remota usando il comando [Func Azure functionapp Publish](functions-run-local.md#publish) seguente per pubblicare con una compilazione locale. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure. 

Utilizzando l' `--build local` opzione, le dipendenze del progetto vengono lette dal file requirements. txt e i pacchetti dipendenti vengono scaricati e installati localmente. I file di progetto e le dipendenze vengono distribuiti dal computer locale in Azure. Questo comporta il caricamento di un pacchetto di distribuzione di dimensioni maggiori in Azure. Se per qualche motivo, le dipendenze nel file requirements. txt non possono essere acquisite dagli strumenti di base, è necessario usare l'opzione dipendenze personalizzate per la pubblicazione. 

### <a name="custom-dependencies"></a>Dipendenze personalizzate

Se il progetto usa pacchetti non disponibili pubblicamente per i nostri strumenti, è possibile renderli disponibili per l'app inserendoli nella \_ \_directory\_\_app/. python_packages. Prima della pubblicazione, eseguire il comando seguente per installare le dipendenze in locale:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Quando si utilizzano dipendenze personalizzate, è necessario `--no-build` utilizzare l'opzione di pubblicazione, dal momento che sono già state installate le dipendenze.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Ricordare di sostituire `<APP_NAME>` con il nome dell'app per le funzioni in Azure.

## <a name="unit-testing"></a>Testing unità

Le funzioni scritte in Python possono essere testate come altro codice Python usando Framework di test standard. Per la maggior parte delle associazioni, è possibile creare un oggetto di input fittizio creando un'istanza di una classe appropriata dal `azure.functions` pacchetto. Poiché il [`azure.functions`](https://pypi.org/project/azure-functions/) pacchetto non è immediatamente disponibile, assicurarsi di installarlo tramite il `requirements.txt` file come descritto nella sezione precedente relativa alla gestione dei [pacchetti](#package-management) . 

Ad esempio, di seguito è riportato un test fittizio di una funzione attivata tramite HTTP:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Di seguito è riportato un altro esempio con una funzione attivata dalla coda:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>File temporanei

Il `tempfile.gettempdir()` metodo restituisce una cartella temporanea, che in Linux è `/tmp`. L'applicazione può usare questa directory per archiviare i file temporanei generati e usati dalle funzioni durante l'esecuzione. 

> [!IMPORTANT]
> Non è garantito che i file scritti nella directory temporanea vengano mantenuti tra le chiamate. Durante la scalabilità orizzontale, i file temporanei non vengono condivisi tra le istanze. 

Nell'esempio seguente viene creato un file temporaneo denominato nella directory temporanea (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Si consiglia di mantenere i test in una cartella separata dalla cartella del progetto. In questo modo si impedisce la distribuzione del codice di test con l'app. 

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

### <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

Funzioni di Azure supporta la condivisione di risorse tra le origini (CORS). CORS viene configurato [nel portale](functions-how-to-use-azure-function-app-settings.md#cors) e tramite l'interfaccia della riga di comando di [Azure](/cli/azure/functionapp/cors). L'elenco delle origini consentite di CORS si applica a livello di app per le funzioni. Con CORS abilitato, le risposte includono `Access-Control-Allow-Origin` l'intestazione. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](functions-how-to-use-azure-function-app-settings.md#cors).

L'elenco delle origini consentite [non è attualmente supportato](https://github.com/Azure/azure-functions-python-worker/issues/444) per le app per le funzioni Python. A causa di questa limitazione, è necessario impostare esplicitamente l' `Access-Control-Allow-Origin` intestazione nelle funzioni http, come illustrato nell'esempio seguente:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Assicurarsi di aggiornare anche function. JSON per supportare il metodo HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Questo metodo HTTP viene usato dai Web browser per negoziare l'elenco di origini consentite. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Documentazione dell'API del pacchetto di funzioni di Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
