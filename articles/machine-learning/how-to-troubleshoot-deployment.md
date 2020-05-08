---
title: Guida alla risoluzione dei problemi di distribuzione
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere, risolvere e risolvere i problemi relativi agli errori di distribuzione comuni di Docker con il servizio Azure Kubernetes e le istanze di contenitore di Azure con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 01fa9c111371c3ede5d3be33f4066f325bad4680
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929248"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Risoluzione dei problemi relativi a Azure Machine Learning servizio Azure Kubernetes e alla distribuzione di istanze di contenitore di Azure

Informazioni su come risolvere gli errori comuni di distribuzione di Docker con istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS) usando Azure Machine Learning.

Quando si distribuisce un modello in Azure Machine Learning, il sistema esegue una serie di attività.

L'approccio consigliato e quello più aggiornato per la distribuzione del modello è tramite l'API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) che usa un oggetto [Environment](how-to-use-environments.md) come parametro di input. In questo caso il servizio creerà un'immagine Docker di base per l'utente durante la fase di distribuzione e monterà tutti i modelli richiesti in un'unica chiamata. Le attività di distribuzione di base sono:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Definire la configurazione dell'inferenza:
    1. Creare un oggetto [ambiente](how-to-use-environments.md) in base alle dipendenze specificate nel file YAML dell'ambiente o usare uno degli ambienti di approvvigionamento.
    2. Creare una configurazione di inferenza (oggetto InferenceConfig) in base all'ambiente e allo script di assegnazione dei punteggi.

3. Distribuire il modello nel servizio istanza di contenitore di Azure (ACI) o in Azure Kubernetes Service (AKS).

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non si dispone di un, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* INTERFACCIA della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Estensione dell'interfaccia della riga [di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug in locale, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

    Per verificare l'installazione di Docker, usare il `docker run hello-world` comando da un terminale o da un prompt dei comandi. Per informazioni sull'installazione di Docker o sulla risoluzione dei problemi relativi agli errori di Docker, vedere la [documentazione](https://docs.docker.com/)di Docker.

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

Supponendo che si stia usando il metodo di distribuzione nuovo/consigliato tramite l'API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) con un oggetto [Environment](how-to-use-environments.md) come parametro di input, il codice può essere suddiviso in tre passaggi principali:

1. Registrare il modello. Ecco un esempio di codice:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definire la configurazione dell'inferenza per la distribuzione:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Distribuire il modello usando la configurazione di inferenza creata nel passaggio precedente:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Una volta suddiviso il processo di distribuzione in singole attività, è possibile esaminare alcuni degli errori più comuni.

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, provare a distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi. L'immagine Docker contenente il modello viene scaricata e avviata nel sistema locale.

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione in locale, modificare il `LocalWebservice.deploy_configuration()` codice in modo da usare per creare una configurazione di distribuzione. Usare `Model.deploy()` quindi per distribuire il servizio. Nell'esempio seguente viene distribuito un modello (contenuto nella variabile del modello) come servizio Web locale:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Si noti che se si definisce YAML specifica conda, è necessario elencare azureml-defaults con Version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web.

A questo punto, è possibile usare il servizio come di consueto. Ad esempio, nel codice seguente viene illustrato l'invio di dati al servizio:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe essere necessario aggiornare il `score.py` file per aggiungere la registrazione o tentare di risolvere eventuali problemi individuati. Per ricaricare le modifiche `score.py` apportate `reload()`al file, usare. Il codice seguente, ad esempio, consente di ricaricare lo script per il servizio e quindi di inviarvi dati. Il Punteggio dei dati viene eseguito usando il `score.py` file aggiornato:

> [!IMPORTANT]
> Il `reload` metodo è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione di a un'altra destinazione di calcolo, vedere la sezione relativa all'aggiornamento di [deploy Models](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dal percorso specificato dall' `InferenceConfig` oggetto utilizzato dal servizio.

Per modificare il modello, le dipendenze conda o la configurazione della distribuzione, utilizzare [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Nell'esempio seguente viene aggiornato il modello utilizzato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, utilizzare [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Esaminare il log di Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. Nell'esempio seguente viene illustrato come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo che l'immagine è stata compilata correttamente, il sistema tenta di avviare un contenitore usando la configurazione di distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nella sezione [esaminare il registro Docker](#dockerlog) per controllare i log.

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Spesso, nella `init()` funzione nello script di assegnazione dei punteggi, viene chiamata la funzione [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella del modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Questo esempio Mostra come stampare il percorso locale (relativo `/var/azureml-app`a) nel contenitore in cui lo script di assegnazione dei punteggi è in attesa di trovare il file o la cartella del modello. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.

L'impostazione del livello di registrazione su DEBUG può causare la registrazione di informazioni aggiuntive, che possono essere utili per identificare l'errore.

## <a name="function-fails-runinput_data"></a>Errore della funzione: run(input_data)

Se il servizio viene distribuito correttamente, ma si arresta in modo anomalo quando si pubblicano dati nell'endpoint di assegnazione dei punteggi, è possibile aggiungere un'istruzione di rilevamento degli errori nella funzione `run(input_data)` in modo che restituisca il messaggio di errore dettagliato. Ad esempio:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, non è necessario restituire messaggi di errore in questo modo in un ambiente di produzione.

## <a name="http-status-code-502"></a>Codice di stato HTTP 502

Un codice di stato 502 indica che il servizio ha generato un'eccezione o si è arrestato `run()` in modo anomalo nel metodo del file score.py. Usare le informazioni in questo articolo per eseguire il debug del file.

## <a name="http-status-code-503"></a>Codice di stato HTTP 503

Le distribuzioni del servizio Azure Kubernetes supportano la scalabilità automatica, che consente di aggiungere le repliche per supportare un carico aggiuntivo. Tuttavia, la scalabilità automatica è progettata per gestire modifiche **graduali** del carico. Se si ricevono picchi elevati di richieste al secondo, i client possono ricevere un codice di stato HTTP 503.

Esistono due elementi che consentono di prevenire i codici di stato 503:

* Modificare il livello di utilizzo a cui la scalabilità automatica crea nuove repliche.
    
    Per impostazione predefinita, l'utilizzo della destinazione per la scalabilità automatica è impostato su 70%, il che significa che il servizio è in grado di gestire picchi di richieste al secondo (RPS) fino al 30%. È possibile modificare la destinazione di utilizzo impostando `autoscale_target_utilization` su un valore inferiore.

    > [!IMPORTANT]
    > Questa modifica non comporta la creazione di repliche *più veloci*. Vengono invece creati con una soglia di utilizzo inferiore. Anziché attendere fino al 70% del servizio, la modifica del valore sul 30% comporta la creazione di repliche quando si verifica l'utilizzo del 30%.
    
    Se il servizio Web sta già usando le repliche massime correnti e si stanno ancora visualizzando 503 codici di stato, `autoscale_max_replicas` aumentare il valore per aumentare il numero massimo di repliche.

* Modificare il numero minimo di repliche. L'aumento delle repliche minime fornisce un pool più grande per gestire i picchi in ingresso.

    Per aumentare il numero minimo di repliche, impostare `autoscale_min_replicas` su un valore più alto. È possibile calcolare le repliche richieste usando il codice seguente, sostituendo i valori con i valori specifici del progetto:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Se si ricevono picchi di richiesta di dimensioni maggiori di quelle che possono essere gestite da una nuova replica minima, è possibile che si riceva nuovamente 503S. Ad esempio, quando aumenta il traffico verso il servizio, potrebbe essere necessario aumentare le repliche minime.

Per ulteriori informazioni sull'impostazione `autoscale_target_utilization`di `autoscale_max_replicas`, e `autoscale_min_replicas` per, vedere la Guida di riferimento al modulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .

## <a name="http-status-code-504"></a>Codice di stato HTTP 504

Un codice di stato 504 indica che si è verificato il timeout della richiesta. Il timeout predefinito è di 1 minuto.

È possibile aumentare il timeout o provare ad accelerare il servizio modificando il score.py per rimuovere le chiamate non necessarie. Se queste azioni non consentono di risolvere il problema, usare le informazioni in questo articolo per eseguire il debug del file score.py. Il codice può trovarsi in uno stato bloccato o in un ciclo infinito.

## <a name="advanced-debugging"></a>Debug avanzato

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di ingresso ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Utilizzando Visual Studio Code e il Python Tools for Visual Studio (PTVSD), è possibile connettersi al codice in esecuzione all'interno del contenitore docker.

> [!IMPORTANT]
> Questo metodo di debug non funziona quando si utilizzano `Model.deploy()` e `LocalWebservice.deploy_configuration` per distribuire un modello localmente. Al contrario, è necessario creare un'immagine utilizzando il metodo [Model. Package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Per altre informazioni sull'uso di Docker, vedere la [documentazione di Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare il Python Tools for Visual Studio (PTVSD) nell'ambiente di sviluppo locale VS Code, utilizzare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per ulteriori informazioni sull'utilizzo di PTVSD con VS Code, vedere [Remote Debugging](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare VS Code per la comunicazione con l'immagine Docker, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __debug__ e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __Launch. JSON__ .

    1. Nel file __Launch. JSON__ trovare la riga che contiene `"configurations": [`e inserire il testo seguente dopo di esso:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se nella sezione configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        Questa sezione si connette al contenitore Docker usando la porta 5678.

    1. Salvare il file __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Creare un'immagine che includa PTVSD

1. Modificare l'ambiente conda per la distribuzione in modo che includa PTVSD. Nell'esempio seguente viene illustrato come aggiungerlo `pip_packages` utilizzando il parametro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Per avviare PTVSD e attendere la connessione all'avvio del servizio, aggiungere quanto segue all'inizio del `score.py` file:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Creare un'immagine in base alla definizione dell'ambiente ed eseguire il pull dell'immagine nel registro di sistema locale. Durante il debug, potrebbe essere necessario apportare modifiche ai file dell'immagine senza ricrearla. Per installare un editor di testo (VIM) nell'immagine Docker, usare le `Environment.docker.base_image` proprietà `Environment.docker.base_dockerfile` e:

    > [!NOTE]
    > In questo esempio si `ws` presuppone che punti all'area di lavoro Azure Machine Learning `model` e che sia il modello distribuito. Il `myenv.yml` file contiene le dipendenze conda create nel passaggio 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Una volta che l'immagine è stata creata e scaricata, il percorso dell'immagine (inclusi repository, nome e tag, che in questo caso è anche il digest) viene visualizzato in un messaggio simile al seguente:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Per semplificare l'utilizzo dell'immagine, utilizzare il comando seguente per aggiungere un tag. Sostituire `myimagepath` con il valore location del passaggio precedente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per il resto dei passaggi, è possibile fare riferimento all'immagine locale `debug:1` anziché al valore del percorso completo dell'immagine.

### <a name="debug-the-service"></a>Eseguire il debug del servizio

> [!TIP]
> Se si imposta un timeout per la connessione PTVSD nel `score.py` file, è necessario connettere vs code alla sessione di debug prima della scadenza del timeout. Avviare VS Code, aprire la copia locale di `score.py`, impostare un punto di interruzione e fare in modo che sia possibile procedere prima di usare la procedura descritta in questa sezione.
>
> Per ulteriori informazioni sul debug e sull'impostazione di punti di interruzione, vedere [debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker usando l'immagine, usare il comando seguente:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Per aggiungere VS Code a PTVSD all'interno del contenitore, aprire VS Code e usare il tasto F5 oppure selezionare __debug__. Quando richiesto, selezionare la configurazione __Azure Machine Learning: Docker debug__ . È anche possibile selezionare l'icona debug dalla barra laterale, la voce di __debug Azure Machine Learning: Docker__ dal menu a discesa debug e quindi usare la freccia verde per collegare il debugger.

    ![Icona debug, pulsante Avvia debug e selettore configurazione](./media/how-to-troubleshoot-deployment/start-debugging.png)

A questo punto, VS Code si connette a PTVSD all'interno del contenitore Docker e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile esaminare il codice durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso di VS Code per eseguire il debug di Python, vedere [eseguire il debug del codice Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificare i file contenitore

Per apportare modifiche ai file nell'immagine, è possibile connettersi al contenitore in esecuzione ed eseguire una shell bash. Da qui è possibile usare VIM per modificare i file:

1. Per connettersi al contenitore in esecuzione e avviare una shell bash nel contenitore, usare il comando seguente:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Per trovare i file usati dal servizio, usare il comando seguente dalla shell bash nel contenitore se la directory predefinita è diversa da `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Da qui è possibile usare VIM per modificare il `score.py` file. Per altre informazioni sull'uso di vim, vedere [uso dell'editor vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Le modifiche apportate a un contenitore non sono in genere rese permanente. Per salvare le modifiche apportate, usare il comando seguente prima di uscire dalla shell avviata nel passaggio precedente, ovvero in un'altra shell:

    ```bash
    docker commit debug debug:2
    ```

    Questo comando crea una nuova immagine denominata `debug:2` che contiene le modifiche.

    > [!TIP]
    > Per rendere effettive le modifiche, sarà necessario arrestare il contenitore corrente e iniziare a usare la nuova versione.

1. Assicurarsi di rispettare le modifiche apportate ai file nel contenitore sincronizzati con i file locali utilizzati VS Code. In caso contrario, l'esperienza del debugger non funzionerà come previsto.

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, usare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: eseguire il training & distribuire modelli](tutorial-train-models-with-aml.md)
