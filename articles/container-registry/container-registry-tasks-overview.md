---
title: Panoramica delle Attività del Registro Azure Container
description: Introduzione alle attività di ACR, una suite di funzionalità in Azure Container Registry che fornisce la compilazione, la gestione e l'applicazione di patch di immagini del contenitore sicure e automatizzate nel cloud.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79087279"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizzare le compilazioni e la manutenzione delle immagini del contenitore con le attività ACR

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. Ciò che rimane, tuttavia, è la necessità di risolvere il modo in cui la virtualizzazione dell'applicazione viene gestita e applicata al ciclo di vita del contenitore.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. Offre funzionalità di creazione di immagini contenitore basate su cloud per le [piattaforme](#image-platforms) , tra cui Linux, Windows e ARM, e può automatizzare l'applicazione di [patch al sistema operativo e al Framework](#automate-os-and-framework-patching) per i contenitori docker. Le attività ACR non solo estendono il ciclo di sviluppo "Inner-loop" nel cloud con le compilazioni di immagini del contenitore su richiesta, ma consentono anche le compilazioni automatiche attivate dagli aggiornamenti del codice sorgente, gli aggiornamenti all'immagine di base di un contenitore o i timer. Con i trigger di aggiornamento dell'immagine di base, ad esempio, è possibile automatizzare il flusso di lavoro di patch del sistema operativo e dell'applicazione, mantenendo gli ambienti protetti rispettando al contempo i principi dei contenitori non modificabili.

## <a name="task-scenarios"></a>Scenari di attività

Le attività ACR supportano diversi scenari per la creazione e la gestione di immagini del contenitore e altri elementi. Per informazioni dettagliate, vedere le sezioni seguenti di questo articolo.

* **[Attività rapida](#quick-task)** : creare ed eseguire il push di una singola immagine del contenitore in un registro contenitori su richiesta, in Azure, senza che sia necessaria un'installazione locale del motore docker. Pensare `docker build`, `docker push` nel cloud.
* **Attività attivate automaticamente** : abilitare uno o più *trigger* per la compilazione di un'immagine:
  * **[Attiva l'aggiornamento del codice sorgente](#trigger-task-on-source-code-update)** 
  * **[Trigger sull'aggiornamento dell'immagine di base](#automate-os-and-framework-patching)** 
  * **[Attiva in base a una pianificazione](#schedule-a-task)** 
* **[Attività](#multi-step-tasks)** in più passaggi: estendere la funzionalità di compilazione e push di un'unica immagine delle attività ACR con flussi di lavoro multifase basati su più contenitori. 

A ogni attività ACR è associato un [contesto del codice sorgente](#context-locations) , ovvero la posizione di un set di file di origine usati per compilare un'immagine del contenitore o un altro artefatto. I contesti di esempio includono un repository git o un file system locale.

Le attività possono inoltre sfruttare le [variabili Run](container-registry-tasks-reference-yaml.md#run-variables), in modo da poter riutilizzare le definizioni delle attività e standardizzare i tag per le immagini e gli artefatti.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di Attività del Registro Azure Container può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Usando il noto formato `docker build`, il comando [az acr build][az-acr-build] nell'interfaccia della riga di comando di Azure accetta un [contesto](#context-locations) (l'insieme di file da compilare), lo invia al servizio ACR Tasks e al termine, per impostazione predefinita, esegue il push dell'immagine compilata nel registro.

Per un'introduzione, vedere la Guida introduttiva per [compilare ed eseguire un'immagine del contenitore](container-registry-quickstart-task-cli.md) in Azure container Registry.  

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. Eseguendo [az login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione di integrazione continua/distribuzione continua potrà quindi eseguire comandi [az acr build][az-acr-build] per avviare compilazioni delle immagini.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su Attività del Registro Azure Container, [Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se si vuole compilare ed eseguire il push di un'immagine direttamente dal codice sorgente, senza Dockerfile, Azure Container Registry fornisce il comando [AZ ACR Pack Build][az-acr-pack-build] (anteprima). Questo strumento crea ed esegue il push di un'immagine dal codice sorgente dell'applicazione usando il [cloud Buildpacks nativo](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Attività trigger sull'aggiornamento del codice sorgente

Attivare una compilazione dell'immagine del contenitore o un'attività in più passaggi quando viene eseguito il commit del codice o una richiesta pull eseguita o aggiornata a un repository git pubblico o privato in GitHub o Azure DevOps. Ad esempio, configurare un'attività di compilazione con il comando dell'interfaccia della riga di comando di Azure [AZ ACR task create][az-acr-task-create] specificando un repository git e, facoltativamente, un Branch e Dockerfile. Quando il team aggiorna il codice nel repository, un webhook creato da un'attività ACR attiva una compilazione dell'immagine del contenitore definita nel repository. 

Le attività ACR supportano i trigger seguenti quando si imposta un repository Git come contesto dell'attività:

| Trigger | Abilitata per impostazione predefinita |
| ------- | ------------------ |
| Commit | Sì |
| Richiesta pull | No |

Per configurare un trigger di aggiornamento del codice sorgente, è necessario fornire all'attività un token di accesso personale (PAT) per impostare il webhook nel repository GitHub pubblico o privato o Azure DevOps.

> [!NOTE]
> Attualmente, Attività del Registro Azure Container non supporta i trigger per il commit o le richieste pull nei repository di GitHub Enterprise.

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su Attività del Registro Azure Container, [Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

La potenza delle attività ACR per migliorare realmente il flusso di lavoro di compilazione del contenitore deriva dalla capacità di rilevare un aggiornamento a un' *immagine di base*. Una funzionalità della maggior parte delle immagini del contenitore, un'immagine di base è un'immagine padre su cui si basano una o più immagini dell'applicazione. Le immagini di base contengono in genere il sistema operativo e, talvolta, i Framework applicazione. 

È possibile configurare un'attività ACR per tenere traccia di una dipendenza da un'immagine di base durante la compilazione di un'immagine dell'applicazione. Quando viene effettuato il push dell'immagine di base aggiornata nel registro di sistema o un'immagine di base viene aggiornata in un repository pubblico, ad esempio in Docker Hub, le attività di ACR possono compilare automaticamente eventuali immagini dell'applicazione basate su di esso.
Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Altre informazioni sui [trigger di base](container-registry-tasks-base-images.md) per l'aggiornamento delle immagini per le attività ACR. Per informazioni su come attivare una compilazione di immagini quando viene eseguito il push di un'immagine di base in un registro contenitori, vedere l'esercitazione [automatizzare le compilazioni di immagini del contenitore quando un'immagine di base viene aggiornata in un registro contenitori di Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Pianificare un'attività

Facoltativamente, pianificare un'attività impostando uno o più *trigger timer* quando si crea o si aggiorna l'attività. La pianificazione di un'attività è utile per l'esecuzione di carichi di lavoro del contenitore in base a una pianificazione definita o per l'esecuzione di operazioni di manutenzione o di test su immagini con push regolari nel registro. Per informazioni dettagliate, vedere [eseguire un'attività ACR in base a una pianificazione definita](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Attività in più passaggi

Le attività in più passi consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi dell'attività definiti in un [file YAML](container-registry-tasks-reference-yaml.md) specificano le singole operazioni di compilazione e push per le immagini del contenitore o altri artefatti. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web, che esegue i test sul contenitore di applicazioni in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="context-locations"></a>Percorsi del contesto

La tabella seguente mostra alcuni esempi di percorsi di contesto supportati per ACR Tasks:

| Posizione contesto | Descrizione | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo master GitHub | File nel ramo master (o un altro valore predefinito) di un repository GitHub pubblico o privato.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub pubblico o privato.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub pubblico o privato. Esempio mostra la combinazione di una specifica di Branch e sottocartella. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Commit di GitHub | Commit specifico in un repository GitHub pubblico o privato. Esempio mostra la combinazione di una specifica di commit hash (SHA) e della sottocartella. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Sottocartella DevOps di Azure | File all'interno di una sottocartella in un repository di Azure pubblico o privato. Esempio mostra la combinazione di specifiche di Branch e sottocartelle. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Quando si usa un repository git privato come contesto per un'attività, è necessario fornire un token di accesso personale (PAT).

## <a name="image-platforms"></a>Piattaforme immagine

Per impostazione predefinita, le attività ACR compilano immagini per il sistema operativo Linux e l'architettura amd64. Specificare il `--platform` tag per compilare immagini Windows o immagini Linux per altre architetture. Specificare il sistema operativo e, facoltativamente, un'architettura supportata nel formato del sistema operativo/architettura (ad esempio, `--platform Linux/arm` ). Per le architetture ARM, è possibile specificare facoltativamente una variante nel formato sistema operativo/architettura/variante (ad esempio, `--platform Linux/arm64/v8` ):

| OS | Architecture|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Ogni esecuzione di attività genera l'output del log che è possibile esaminare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. Quando si attiva un'attività manualmente, l'output del log per l'esecuzione dell'attività viene trasmesso alla console e archiviato anche per il recupero successivo. Quando un'attività viene attivata automaticamente, ad esempio da un commit del codice sorgente o da un aggiornamento di un'immagine di base, i log delle attività vengono archiviati solo. Visualizzare i log di esecuzione nell'portale di Azure o usare il comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

Vedere altre informazioni sulla [visualizzazione e la gestione dei log attività](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti per automatizzare le compilazioni e la manutenzione delle immagini del contenitore nel cloud, vedere la [serie di esercitazioni sulle attività ACR](container-registry-tutorial-quick-task.md).

Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
