---
title: Guida di riferimento per gli sviluppatori Python per Funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura serverless, Python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 619db07204b88609314d0d3d06709eaa93cb7a43
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188035"
---
# <a name="azure-functions-python-developer-guide"></a>Guida per sviluppatori Python per Funzioni di Azure

Questo articolo è un'introduzione allo sviluppo di Funzioni di Azure con Python. Il contenuto presuppone che l'utente abbia già letto il [Manuale dello sviluppatore di Funzioni di Azure](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modello di programmazione

Una funzione di Azure deve essere un metodo senza stato nello script Python che elabora un input e restituisce un output. Per impostazione predefinita, il runtime si aspetta che sia implementata come metodo globale denominato `main()` nel file `__init__.py`.

È possibile modificare la configurazione predefinita specificando le proprietà `scriptFile` e `entryPoint` nel file `function.json`. Ad esempio, il file _function.json_ di seguito indica al runtime di usare il metodo _customentry()_ nel file _main.py_ come punto di ingresso per la funzione di Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

I dati da trigger e associazioni vengono associati alla funzione tramite gli attributi del metodo usando la proprietà `name` definita nel file di configurazione `function.json`. Ad esempio, il file _function.json_ descrive una semplice funzione attivata da una richiesta HTTP denominata `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Il file `__init__.py` contiene il codice funzione seguente:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Facoltativamente, è anche possibile dichiarare i tipi di parametro e il tipo restituito nella funzione tramite le annotazioni di tipo Python. Ad esempio, la stessa funzione può essere scritta usando annotazioni, come segue:

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Usare le annotazioni Python incluse nel pacchetto [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) per associare input e output ai metodi dell'utente. 

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle per un progetto di Funzioni Python ha un aspetto simile al seguente:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

È presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice e il file di configurazione delle associazioni (function.json). 

Il codice condiviso deve essere mantenuto in una cartella separata. Per fare riferimento a moduli nella cartella SharedCode, si può usare la sintassi seguente:

```
from ..SharedCode import myFirstHelperFunction
```

Le estensioni di associazione usate nel runtime di Funzioni sono definite nel file `extensions.csproj`, con gli effettivi file di libreria inclusi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](functions-triggers-bindings.md#local-development-azure-functions-core-tools) con Azure Functions Core Tools. 

Quando si distribuisce un progetto Funzioni a un'app per le funzioni in Azure, occorre includere nel pacchetto l'intero contenuto della cartella FunctionApp, ma non la cartella stessa.

## <a name="inputs"></a>Input

In Funzioni di Azure, gli input vengono suddivisi in due categorie, ovvero l'input del trigger e un input aggiuntivo. Anche se sono diversi in `function.json`, l'uso nel codice Python è diverso. Si consideri il frammento di codice di esempio seguente:

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando viene richiamata questa funzione, la richiesta HTTP viene passata alla funzione come `req`. Una voce verrà recuperata dall'archivio BLOB di Azure in base all' _id_ nell'URL di route e verrà resa disponibile come `obj` nel corpo della funzione.

## <a name="outputs"></a>Output

Gli output possono essere espressi sia nel valore restituito che nei parametri di output. Se è presente un solo output, è consigliabile usare il valore restituito. Per più output, è necessario usare invece i parametri di output.

Per usare il valore restituito di una funzione come valore di un'associazione di output, la proprietà `name` dell'associazione deve essere impostata su `$return` in `function.json`.

Per generare più output, usare il metodo `set()` fornito dall'interfaccia `azure.functions.Out` per assegnare un valore all'associazione. Ad esempio, la funzione seguente può eseguire il push di un messaggio in una coda e anche restituire una risposta HTTP.

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

L'accesso al logger di runtime di Funzioni di Azure è disponibile tramite un gestore radice [`logging`](https://docs.python.org/3/library/logging.html#module-logging) nell'app per le funzioni. Il logger è associato ad Application Insights e consente di contrassegnare avvisi ed errori rilevati durante l'esecuzione della funzione.

L'esempio seguente registra un messaggio informativo quando la funzione viene richiamata tramite un trigger HTTP.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Sono disponibili altri metodi di registrazione che consentono di scrivere nella console a livelli di traccia diversi:

| Metodo                 | DESCRIZIONE                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_messaggio_)**   | Scrive un messaggio con livello critico nel logger radice.  |
| logging.**error(_messaggio_)**   | Scrive un messaggio con livello errore nel logger radice.    |
| logging.**warning(_messaggio_)**    | Scrive un messaggio con livello avviso nel logger radice.  |
| logging.**info(_messaggio_)**    | Scrive un messaggio con livello informativo nel logger radice.  |
| logging.**debug(_messaggio_)** | Scrive un messaggio con livello debug nel logger radice.  |

## <a name="importing-shared-code-into-a-function-module"></a>Importazione di codice condiviso in un modulo di funzione

I moduli Python pubblicati insieme ai moduli di funzione vanno importati usando la sintassi di importazione relativa:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

In alternativa, inserire il codice condiviso in un pacchetto autonomo, pubblicarlo in un'istanza pubblica o privata di PyPI e specificarlo come una dipendenza normale.

## <a name="async"></a>Async

Poiché può esistere solo un singolo processo Python per ogni app per le funzioni, è consigliabile implementare la funzione di Azure come coroutine asincrona usando l'istruzione `async def`.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Se la funzione main() è sincrona (nessun qualificatore `async`), viene eseguita automaticamente in un pool di thread `asyncio`.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

Per ottenere il contesto di chiamata di una funzione durante l'esecuzione, includere l'argomento `context` nella sua firma. 

Ad esempio: 

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe **Context** ha i metodi seguenti:

`function_directory`  
Directory in cui la funzione è in esecuzione.

`function_name`  
Nome della funzione.

`invocation_id`  
ID della chiamata di funzione corrente.

## <a name="python-version-and-package-management"></a>Versione di Python e gestione dei pacchetti

Funzioni di Azure supporta attualmente solo Python 3.6.x (distribuzione ufficiale di CPython).

Quando si sviluppa in locale usando Azure Functions Core Tools oppure Visual Studio Code, aggiungere i nomi e le versioni dei pacchetti necessari al file `requirements.txt` e installarli con `pip`.

Ad esempio, per installare il pacchetto `requests` PyPI è possibile usare il file di requisiti e il comando pip seguenti.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Quando si è pronti per la pubblicazione, assicurarsi che tutte le dipendenze siano elencate nel file `requirements.txt`, che si trova nella radice della directory del progetto. Per la corretta esecuzione di Funzioni di Azure, il file di requisiti deve contenere almeno i pacchetti seguenti:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Pubblicazione in Azure

Se si usa un pacchetto che richiede un compilatore e non supporta l'installazione di pacchetti wheel compatibili con manylinux da PyPI, la pubblicazione in Azure avrà esito negativo con l'errore seguente: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Per creare e configurare automaticamente i file binari necessari, [installare Docker](https://docs.docker.com/install/) nel computer locale ed eseguire il comando seguente per pubblicare con [Azure Functions Core Tools](functions-run-local.md#v2) (func). Ricordare di sostituire `<app name>` con il nome dell'app per le funzioni in Azure. 

```bash
func azure functionapp <app name> --build-native-deps
```

Core Tools userà Docker per eseguire l'immagine [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) come contenitore nel computer locale. Usando questo ambiente, creerà e installerà quindi i moduli necessari dalla distribuzione di origine, prima di inserirli in un pacchetto per la distribuzione finale in Azure.

> [!NOTE]
> Core Tools (func) usa il programma PyInstaller per bloccare il codice e le dipendenze dell'utente in un unico file eseguibile autonomo per l'esecuzione in Azure. Questa funzionalità è attualmente in anteprima e può non essere applicabile a tutti i tipi di pacchetti Python. Se non è possibile importare i moduli, provare a pubblicare nuovamente usando l'opzione `--no-bundler`. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Se i problemi persistono,[aprire un problema](https://github.com/Azure/azure-functions-core-tools/issues/new) includendo la relativa descrizione. 


Per compilare le dipendenze e pubblicare mediante un sistema di recapito continuo e integrazione continua, è possibile usare una [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) oppure uno [script personalizzato Travis CI](https://docs.travis-ci.com/user/deployment/script/). 

Ecco un esempio di script `azure-pipelines.yml` per il processo di compilazione e pubblicazione.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Ecco un esempio di script `.travis.yaml` per il processo di compilazione e pubblicazione.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

Tutti i problemi noti e le richieste di funzionalità vengono registrati tramite l'elenco di [problemi di GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Associazioni dell'archiviazione BLOB](functions-bindings-storage-blob.md)
* [Associazioni di webhook e HTTP](functions-bindings-http-webhook.md)
* [Associazioni di Archiviazione code](functions-bindings-storage-queue.md)
* [Trigger timer](functions-bindings-timer.md)
