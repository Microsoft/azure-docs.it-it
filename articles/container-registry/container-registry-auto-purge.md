---
title: Elimina i tag e i manifesti
description: Usare un comando Purge per eliminare più tag e manifesti da un registro contenitori di Azure in base all'età e a un filtro tag e, facoltativamente, pianificare le operazioni di ripulitura.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087324"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Ripulire automaticamente le immagini da un registro contenitori di Azure

Quando si usa un registro contenitori di Azure come parte di un flusso di lavoro di sviluppo, il registro di sistema può riempire rapidamente immagini o altri artefatti che non sono necessari dopo un breve periodo di tempo. Potrebbe essere necessario eliminare tutti i tag precedenti a una determinata durata o corrispondere a un filtro di nome specificato. Per eliminare rapidamente più elementi, questo articolo introduce il `acr purge` comando che è possibile eseguire come attività ACR su richiesta o [pianificata](container-registry-tasks-scheduled.md) . 

Il `acr purge` comando è attualmente distribuito in un'immagine del contenitore pubblico`mcr.microsoft.com/acr/acr-cli:0.1`(), compilato dal codice sorgente nel repository [Acr-CLI](https://github.com/Azure/acr-cli) in GitHub.

È possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di attività ACR in questo articolo. Se si vuole usarlo localmente, è richiesta la versione 2.0.69 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install]. 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

> [!WARNING]
> Usare il `acr purge` comando con cautela. i dati dell'immagine eliminati sono irreversibili. Se si dispone di sistemi che effettuano il pull di immagini dal digest del manifesto (in contrapposizione al nome dell'immagine), non eliminare le immagini senza tag. L'eliminazione delle immagini senza tag impedirà a tali sistemi di eseguire il pull delle immagini dal registro. Invece di eseguire il pull tramite manifesto, adottare uno schema di *assegnazione di tag univoci*, una [procedura consigliata](container-registry-image-tag-version.md).

Se si vuole eliminare singoli tag di immagine o manifesti usando i comandi dell'interfaccia della riga di comando di Azure, vedere [eliminare immagini del contenitore in azure container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Usare il comando Purge

Il `acr purge` comando contenitore elimina le immagini in base al tag in un repository che corrisponde a un filtro di nome e che hanno una durata superiore a quella specificata. Per impostazione predefinita, vengono eliminati solo i riferimenti ai tag, non i [manifesti](container-registry-concepts.md#manifest) sottostanti e i dati del livello. Il comando ha un'opzione per eliminare anche i manifesti. 

> [!NOTE]
> `acr purge`non elimina un tag immagine o un repository in cui `write-enabled` l'attributo è impostato `false`su. Per informazioni, vedere [bloccare un'immagine del contenitore in un registro contenitori di Azure](container-registry-image-lock.md).

`acr purge`è progettato per essere eseguito come comando contenitore in un' [attività ACR](container-registry-tasks-overview.md), in modo che esegua l'autenticazione automatica con il registro di sistema in cui viene eseguita l'attività ed esegua le azioni. Gli esempi di attività in questo articolo usano `acr purge` l' [alias](container-registry-tasks-reference-yaml.md#aliases) del comando al posto di un comando completo dell'immagine del contenitore.

Specificare come minimo quanto segue quando si esegue `acr purge`:

* `--filter`: Un repository e un' *espressione regolare* per filtrare i tag nel repository. Esempi: `--filter "hello-world:.*"` corrisponde a tutti i tag `hello-world` nel repository e `--filter "hello-world:^1.*"` corrisponde ai tag che `1`iniziano con. Passare più `--filter` parametri per ripulire più repository.
* `--ago`: [Stringa di durata](https://golang.org/pkg/time/) di tipo Go per indicare una durata oltre la quale le immagini vengono eliminate. La durata è costituita da una sequenza di uno o più numeri decimali, ognuno con un suffisso di unità. Le unità di tempo valide includono "d" per i giorni, "h" per le ore e "m" per i minuti. Ad esempio, `--ago 2d3h6m` seleziona tutte le immagini filtrate modificate più di due giorni, 3 ore e 6 minuti fa e seleziona le `--ago 1.5h` immagini modificate per l'ultima volta più di 1,5 ore fa.

`acr purge`supporta diversi parametri facoltativi. Gli esempi riportati di seguito vengono usati in questo articolo:

* `--untagged`: Specifica che i manifesti che non dispongono di tag associati (*manifesti senza tag*) vengono eliminati.
* `--dry-run`-Specifica che non vengono eliminati dati, ma l'output è uguale a quello di se il comando viene eseguito senza questo flag. Questo parametro è utile per il test di un comando Purge per assicurarsi che non elimini inavvertitamente i dati che si desidera mantenere.

Per ulteriori parametri, eseguire `acr purge --help`. 

`acr purge`supporta altre funzionalità dei comandi delle attività ACR, incluse le [variabili Run](container-registry-tasks-reference-yaml.md#run-variables) e i [log di esecuzione delle attività](container-registry-tasks-logs.md) , che vengono trasmessi e salvati per il recupero successivo.

### <a name="run-in-an-on-demand-task"></a>Eseguire in un'attività su richiesta

Nell'esempio seguente viene usato il comando [AZ ACR Run][az-acr-run] per eseguire `acr purge` il comando su richiesta. Questo esempio Elimina tutti i tag di immagine e i `hello-world` manifesti nel repository in registro di *sistema* modificati più di un giorno fa. Il comando contenitore viene passato usando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Esegui in un'attività pianificata

Nell'esempio seguente viene usato il comando [AZ ACR task create][az-acr-task-create] per creare un' [attività ACR pianificata](container-registry-tasks-scheduled.md)giornaliera. L'attività Elimina i tag modificati più di 7 giorni fa nel `hello-world` repository. Il comando contenitore viene passato usando una variabile di ambiente. L'attività viene eseguita senza un contesto di origine.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Eseguire il comando [AZ ACR task show][az-acr-task-show] per verificare che il trigger del timer sia configurato.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Elimina un numero elevato di tag e manifesti

L'eliminazione di un numero elevato di tag e manifesti potrebbe richiedere diversi minuti o più. Per eliminare migliaia di tag e manifesti, potrebbe essere necessario eseguire il comando più a lungo del tempo di timeout predefinito di 600 secondi per un'attività su richiesta o di 3600 secondi per un'attività pianificata. Se viene superato il tempo di timeout, viene eliminato solo un subset di tag e manifesti. Per assicurarsi che sia stata completata un'operazione di ripulitura su larga `--timeout` scala, passare il parametro per aumentare il valore. 

Ad esempio, l'attività su richiesta seguente imposta un tempo di timeout di 3600 secondi (1 ora):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Esempio: ripulitura pianificata di più repository in un registro

Questo esempio illustra l'uso `acr purge` di per pulire periodicamente più repository in un registro di sistema. Ad esempio, si potrebbe avere una pipeline di sviluppo che inserisce le immagini nei `samples/devimage1` repository `samples/devimage2` e. Si importano periodicamente immagini di sviluppo in un repository di produzione per le distribuzioni, quindi non sono più necessarie le immagini di sviluppo. Con cadenza settimanale, i `samples/devimage1` repository e `samples/devimage2` vengono eliminati in modo da essere preparati per il lavoro della settimana in arrivo.

### <a name="preview-the-purge"></a>Anteprima dell'eliminazione

Prima di eliminare i dati, è consigliabile eseguire un'attività di ripulitura su `--dry-run` richiesta utilizzando il parametro. Questa opzione consente di visualizzare i tag e i manifesti che il comando eliminerà, senza rimuovere i dati. 

Nell'esempio seguente, il filtro in ogni repository seleziona tutti i tag. Il `--ago 0d` parametro corrisponde alle immagini di tutte le età nei repository che corrispondono ai filtri. Modificare i criteri di selezione in base alle esigenze per lo scenario. Il `--untagged` parametro indica di eliminare i manifesti oltre ai tag. Il comando contenitore viene passato al comando [AZ ACR Run][az-acr-run] usando una variabile di ambiente.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Esaminare l'output del comando per visualizzare i tag e i manifesti corrispondenti ai parametri di selezione. Poiché il comando viene eseguito con `--dry-run`, nessun dato viene eliminato.

Output di esempio:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Pianificare l'eliminazione

Dopo aver verificato l'esecuzione a secco, creare un'attività pianificata per automatizzare l'eliminazione. Nell'esempio seguente viene pianificata un'attività settimanale la domenica alle 1:00 UTC per eseguire il comando di eliminazione precedente:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Eseguire il comando [AZ ACR task show][az-acr-task-show] per verificare che il trigger del timer sia configurato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre opzioni per [eliminare i dati immagine](container-registry-delete.md) in Azure container Registry.

Per altre informazioni sull'archiviazione di immagini, vedere [archiviazione di immagini contenitore in Azure container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

