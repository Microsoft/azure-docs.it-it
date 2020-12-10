---
title: "Esercitazione: Attivare la creazione di immagini all'aggiornamento delle immagini di base"
description: Questa esercitazione illustra come configurare un'attività del Registro Azure Container per attivare automaticamente le compilazioni delle immagini dei contenitori nel cloud quando viene aggiornata un'immagine di base nello stesso registro.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 342f7fc37edb3a1623b72c57e8766b1a484d0e8d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559151"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Esercitazione: Automatizzare la compilazione di immagini dei contenitori quando viene aggiornata un'immagine in Registro Azure Container 

[Attività del Registro Azure Container](container-registry-tasks-overview.md) supporta le compilazioni di immagini dei contenitori automatizzate quando viene [aggiornata l'immagine di base](container-registry-tasks-base-images.md) di un contenitore, ad esempio per applicare patch al sistema operativo o al framework applicazioni in una delle immagini di base. 

Questa esercitazione illustra come creare un'attività del Registro Azure Container che attiva una compilazione nel cloud in seguito al push dell'immagine di base del contenitore nello stesso registro. È anche possibile provare un'esercitazione per creare un'attività del Registro Azure Container che attiva una compilazione dell'immagine quando viene eseguito il push di un'immagine di base in [un altro Registro Azure Container](container-registry-tutorial-private-base-image-update.md). 

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Compilare l'immagine di base
> * Creare un'immagine dell'applicazione nello stesso registro per tenere traccia dell'immagine di base 
> * Aggiornare l'immagine di base per attivare un'attività dell'immagine dell'applicazione
> * Visualizzare l'attività attivata
> * Verificare l'immagine dell'applicazione aggiornata

## <a name="prerequisites"></a>Prerequisiti

### <a name="complete-the-previous-tutorials"></a>Completare le esercitazioni precedenti

Questa esercitazione presuppone che l'ambiente sia già stato configurato e che siano già state completate le procedure delle prime due esercitazioni della serie, in cui viene illustrato come:

- Creare un'istanza di Registro Azure Container
- Creare una copia tramite fork del repository di esempio
- Clonare il repository di esempio
- Creare un token di accesso personale GitHub

Se non è già stato fatto, prima di procedere completare le esercitazioni seguenti:

[Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md)

[Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Configurare l'ambiente

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

Popolare queste variabili di ambiente della shell con i valori appropriati per l'ambiente in uso. Questo passaggio non è obbligatorio, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popolano queste variabili di ambiente, sarà necessario sostituire manualmente ogni valore in ogni occorrenza nei comandi di esempio.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Scenario di aggiornamento dell'immagine di base

Questa esercitazione illustra uno scenario di aggiornamento dell'immagine di base in cui un'immagine di base e un'immagine dell'applicazione vengono gestite in un unico registro. 

L'[esempio di codice][code-sample] include due documenti Dockerfile: un'immagine dell'applicazione e un'immagine specificata come immagine di base. Nelle sezioni seguenti viene creata un'attività di Registro Azure Container che attiva automaticamente una compilazione dell'immagine dell'applicazione quando viene eseguito il push di una nuova versione dell'immagine di base nello stesso registro contenitori.

* [Dockerfile-app][dockerfile-app]: applicazione Web Node.js di piccole dimensioni che esegue il rendering di una pagina Web statica in cui viene visualizzata la versione di Node.js su cui è basata. La stringa della versione è simulata, perché mostra il contenuto della variabile di ambiente `NODE_VERSION` definita nell'immagine di base.

* [Dockerfile-base][dockerfile-base]: immagine specificata da `Dockerfile-app` come base. È a sua volta basata su un'immagine [Node][base-node] e include la variabile di ambiente `NODE_VERSION`.

Nelle sezioni seguenti si crea un'attività, si aggiorna il valore di `NODE_VERSION` nel documento Dockerfile dell'immagine di base e quindi si usa ACR Tasks per compilare l'immagine di base. Quando l'attività di ACR Tasks esegue il push della nuova immagine di base nel registro, attiva automaticamente una compilazione dell'immagine dell'applicazione. Facoltativamente, si esegue l'immagine del contenitore dell'applicazione in locale per verificare le diverse stringhe della versione nelle immagini compilate.

In questa esercitazione l'attività di Registro Azure Container crea un'immagine del contenitore dell'applicazione specificata in un documento Dockerfile e ne esegue il push. Le funzionalità di Attività del Registro Azure Container consentono anche di eseguire [attività in più passi](container-registry-tasks-multi-step.md), usando un file YAML per definire i passaggi per creare, eseguire il push e facoltativamente testare più contenitori.

## <a name="build-the-base-image"></a>Compilare l'immagine di base

Per iniziare, compilare l'immagine di base con un'*attività rapida* di Attività del Registro Azure Container usando il comando [az acr build][az-acr-build]. Come illustrato nella [prima esercitazione](container-registry-tutorial-quick-task.md) della serie, questo processo non solo compila l'immagine, ma ne esegue anche il push nel registro contenitori, se la compilazione viene completata correttamente.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Crea un'attività

Creare quindi un'attività con [az acr task create][az-acr-task-create]:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Questa attività, simile a quella creata nell'[esercitazione precedente](container-registry-tutorial-build-task.md), indica ad ACR Tasks di attivare una compilazione dell'immagine quando viene eseguito il push di commit nel repository specificato da `--context`. Mentre il documento Dockerfile usato per creare l'immagine nell'esercitazione precedente specifica un'immagine di base pubblica (`FROM node:15-alpine`), il documento Dockerfile in questa attività, [Dockerfile-app][dockerfile-app], specifica un'immagine di base nello stesso registro:

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Questa configurazione semplifica la simulazione di una patch del framework nell'immagine di base più avanti in questa esercitazione.

## <a name="build-the-application-container"></a>Compilare il contenitore dell'applicazione

Usare [az acr task run][az-acr-task-run] per attivare manualmente l'attività e creare l'immagine dell'applicazione. Questo passaggio è necessario per garantire che l'attività tenga traccia delle dipendenze dell'immagine dell'applicazione nell'immagine di base.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

Al termine dell'attività, se si intende completare il passaggio facoltativo seguente prendere nota del valore di **ID esecuzione** (ad esempio, "da6").

### <a name="optional-run-application-container-locally"></a>Facoltativo: eseguire il contenitore dell'applicazione in locale

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, eseguire il contenitore per verificare il rendering dell'applicazione in un Web browser prima di ricompilare l'immagine di base. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `az acr login` né `docker run`.

Prima di tutto, eseguire l'autenticazione nel registro contenitori con [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Eseguire quindi il contenitore in locale con `docker run`. Sostituire **\<run-id\>** con l'ID esecuzione riportato nell'output del passaggio precedente (ad esempio, "da6"). Questo esempio assegna al contenitore il nome `myapp` e include il parametro `--rm` per rimuovere il contenitore quando viene arrestato.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Passare a `http://localhost:8080` nel browser. Nella pagina Web verrà visualizzato il rendering del numero di versione di Node.js, simile allo screenshot seguente. In un passaggio successivo si modificherà la versione aggiungendo una "a" alla stringa della versione.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Screenshot dell'applicazione di esempio nel browser":::

Per arrestare e rimuovere il contenitore, eseguire questo comando:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Elencare le compilazioni

Elencare quindi le esecuzioni delle attività completate dal Registro Azure Container per il registro usando il comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Se è stata completata l'esercitazione precedente e non è stato eliminato il registro, l'output visualizzato dovrebbe essere simile al seguente. Prendere nota del numero di esecuzioni delle attività e del valore di ID esecuzione più recente per poter confrontare l'output dopo l'aggiornamento dell'immagine di base nella sezione successiva.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>Aggiornare l'immagine di base

In questa sezione si simula l'applicazione di una patch del framework nell'immagine di base. Modificare **Dockerfile-base** e aggiungere una "a" dopo il numero di versione definito in `NODE_VERSION`:

```dockerfile
ENV NODE_VERSION 15.2.1a
```

Eseguire un'attività rapida per compilare l'immagine di base modificata. Prendere nota del valore di **ID esecuzione** nell'output.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

Al termine della compilazione e del push della nuova immagine di base nel registro, ACR Tasks attiva una compilazione dell'immagine dell'applicazione. Prima che l'attività creata in precedenza attivi la compilazione dell'immagine dell'applicazione potrebbero trascorrere alcuni istanti, perché deve essere rilevata l'immagine di base appena compilata e sottoposta a push.

## <a name="list-updated-build"></a>Elencare la compilazione aggiornata

Dopo aver aggiornato l'immagine di base, visualizzare di nuovo l'elenco delle esecuzioni delle attività per confrontarlo con l'elenco precedente. Se inizialmente l'output non presenta differenze, eseguire periodicamente il comando finché la nuova esecuzione dell'attività non risulta inclusa nell'elenco.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

L'output è simile al seguente. Il valore di TRIGGER per l'ultima compilazione eseguita sarà "Image Update", che indica che l'attività è stata avviata dall'attività rapida dell'immagine di base.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

Se si vuole eseguire il passaggio facoltativo seguente che prevede l'esecuzione del contenitore appena compilato per visualizzare il numero di versione aggiornato, prendere nota del valore di **ID esecuzione** della compilazione attivata da "Image Update" (nell'output precedente, "ca11").

### <a name="optional-run-newly-built-image"></a>Facoltativo: eseguire l'immagine appena compilata

Se si lavora in locale (non in Cloud Shell) ed è installato Docker, al termine della compilazione eseguire la nuova immagine dell'applicazione. Sostituire `<run-id>` con il valore di ID esecuzione ottenuto nel passaggio precedente. Se si usa Cloud Shell, ignorare questa sezione. Cloud Shell non supporta `docker run`.

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Passare a http://localhost:8081 nel browser. Nella pagina Web verrà visualizzato il numero di versione di Node.js aggiornato con "a":

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Screenshot dell'applicazione di esempio aggiornata nel browser":::


È importante notare che si è aggiornata l'immagine di **base** con un nuovo numero di versione, ma questo viene visualizzato nell'ultima immagine compilata dell'**applicazione**. ACR Tasks ha rilevato la modifica apportata all'immagine di base e ha ricompilato automaticamente l'immagine dell'applicazione.

Per arrestare e rimuovere il contenitore, eseguire questo comando:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare un'attività per attivare automaticamente compilazioni delle immagini dei contenitori in seguito all'aggiornamento dell'immagine di base. Passare ora all'esercitazione successiva per informazioni su come attivare le attività in base a una pianificazione definita.

> [!div class="nextstepaction"]
> [Eseguire un'attività in base a una pianificazione](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
