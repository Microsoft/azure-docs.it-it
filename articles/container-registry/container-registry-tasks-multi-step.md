---
title: Attività in più passaggi per compilare, testare & immagine patch
description: Introduzione alle attività in più passaggi, una funzionalità delle attività ACR in Azure Container Registry che fornisce flussi di lavoro basati su attività per la compilazione, il test e l'applicazione di patch alle immagini del contenitore nel cloud.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78399690"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Eseguire attività di creazione, test e applicazione di patch in più passaggi in Attività di Registro Azure Container

Le attività in più passaggi estendono la singola funzionalità di creazione ed esecuzione del push di Attività di Registro Azure Container con flussi di lavoro basati su più contenitori e più passaggi. Usare le attività in più passaggi per compilare ed eseguire il push di più immagini, in serie o in parallelo. Eseguire quindi queste immagini come comandi all'interno di una singola esecuzione di attività. Ogni passaggio definisce l'operazione di compilazione o push di un'immagine contenitore e può anche definire l'esecuzione di un contenitore. Ogni passaggio in un'attività in più passaggi usa un contenitore come ambiente di esecuzione.

> [!IMPORTANT]
> Se in precedenza sono state create attività durante l'anteprima con il comando `az acr build-task`, tali attività devono essere ricreate con il comando [az acr task][az-acr-task].

È ad esempio possibile eseguire un'attività con passaggi che automatizzano la logica seguente:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web che esegue test sul contenitore dell'applicazione in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Tutti i passaggi vengono eseguiti all'interno di Azure, riducendo così il lavoro per le risorse di calcolo di Azure e liberando l'utente dalla gestione dell'infrastruttura. Oltre al Registro Azure Container, si paga solo per le risorse che si usano. Per informazioni sui prezzi, vedere la sezione relativa alla **Creazione di contenitori** sul sito [Prezzi di Registro Azure Container][pricing].


## <a name="common-task-scenarios"></a>Scenari comuni di attività

Le attività in più passaggi rendono possibili scenari come la logica seguente:

* Creazione, applicazione di tag ed esecuzione del push di una o più immagini di contenitore, in serie o in parallelo.
* Esecuzione e acquisizione di risultati di unit test e code coverage.
* Esecuzione e acquisizione di test funzionali. Le attività di Registro Azure Container supportano l'esecuzione di più contenitori, eseguendo una serie di richieste reciproche.
* Esecuzione basata su attività, inclusi passaggi precedenti e successivi della creazione di un'immagine del contenitore.
* Distribuzione di uno o più contenitori con il motore di distribuzione preferito nell'ambiente di destinazione.

## <a name="multi-step-task-definition"></a>Definizione di attività in più passaggi

Un'attività in più passaggi in Attività di Registro Azure Container viene definita come una serie di passaggi all'interno di un file YAML. Ogni passaggio può specificare le dipendenze dal completamento di uno o più passaggi precedenti. Sono disponibili i tipi di passaggi di attività seguenti:

* [`build`](container-registry-tasks-reference-yaml.md#build): Compilare una o più immagini del contenitore usando una `docker build` sintassi familiare, in serie o in parallelo.
* [`push`](container-registry-tasks-reference-yaml.md#push): Eseguire il push delle immagini compilate in un registro contenitori. Sono supportati i registri privati, ad esempio Registro Azure Container, nonché l'hub Docker pubblico.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Esegue un contenitore, in modo che possa funzionare come una funzione all'interno del contesto dell'attività in esecuzione. È possibile passare parametri a `[ENTRYPOINT]` del contenitore e specificare proprietà come env, detach e altri parametri `docker run` comuni. Il tipo di passaggio `cmd` consente l'esecuzione di test funzionali e di unit test, con l'esecuzione simultanea del contenitore.

I frammenti di codice seguenti illustrano come combinare questi tipi di passaggi delle attività. Le attività in più passaggi possono essere semplici, come la creazione di un'unica immagine da un documento Dockerfile e il push nel registro, con un file YAML simile a:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Oppure possono essere più complesse, ad esempio la definizione fittizia di più passaggi, che includono i passaggi per compilazione, test, pacchetto Helm e distribuzione Helm (la configurazione del registro contenitori e del repository Helm non è illustrata):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Vedere gli [esempi di attività](container-registry-tasks-samples.md) per i file YAML dell'attività in più passaggi e dockerfile per diversi scenari.

## <a name="run-a-sample-task"></a>Eseguire un'attività di esempio

Le attività supportano sia l'esecuzione manuale, denominata "esecuzione veloce" che l'esecuzione automatica su commit GIT o in caso di aggiornamento dell'immagine di base.

Per eseguire un'attività, è prima di tutto necessario definire i passaggi dell'attività in un file YAML, quindi eseguire il comando [az acr run][az-acr-run] dell'interfaccia della riga di comando di Azure.

Ecco un comando dell'interfaccia della riga di comando di Azure di esempio che esegue un'attività usando un file YAML di attività di esempio. I passaggi creano e quindi eseguono il push di un'immagine. Prima di eseguire il comando, aggiornare `\<acrName\>` con il nome del proprio Registro Azure Container.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Quando si esegue l'attività, l'output dovrebbe mostrare lo stato di avanzamento di ogni passaggio definito nel file YAML. Nell'output seguente i passaggi appaiono come `acb_step_0` e `acb_step_1`.

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Per altre informazioni sulle creazioni automatiche su commit GIT o in caso di aggiornamento dell'immagine di base, vedere gli articoli [Automatizzare la compilazione di immagini](container-registry-tutorial-build-task.md) e [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base](container-registry-tutorial-base-image-update.md) nell'esercitazione.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare riferimenti ed esempi sulle attività in più passaggi qui:

* [Task reference](container-registry-tasks-reference-yaml.md) (Riferimenti alle attività): tipi, proprietà e utilizzo dei passaggi di attività.
* [Esempi di attività](container-registry-tasks-samples.md) : `task.yaml` file di esempio e Docker per diversi scenari, da semplici a complessi.
* [Repository di comandi](https://github.com/AzureCR/cmd): raccolta di contenitori come comandi per Attività di Registro Azure Container.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task