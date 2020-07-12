---
title: Criteri di riavvio per le attività Esegui una sola volta
description: Informazioni su come usare Istanze di Azure Container per eseguire attività eseguite fino al completamento, ad esempio nella compilazione, nei test o nei processi per il rendering di immagini.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: a582036ae54a0b100b768e37bcf0d952521559d9
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261341"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Eseguire attività in contenitori con criteri di riavvio

La semplicità e la velocità della distribuzione di contenitori in Istanze di Azure Container offre una piattaforma interessante per l'esecuzione di attività eseguite una sola volta come la compilazione, il test e il rendering di immagini in un'istanza del contenitore.

Con un criterio di riavvio configurabile, è possibile specificare l'arresto dei contenitori al completamento dei processi. Poiché le istanze del contenitore vengono fatturate al secondo, vengono addebitate solo le risorse di calcolo usate mentre il contenitore che esegue l'attività è in esecuzione.

Gli esempi presentati in questo articolo usano l'interfaccia della riga di comando di Azure. È necessario avere [installato in locale][azure-cli-install] l'interfaccia della riga di comando di Azure versione 2.0.21 o successiva o in alternativa usare l'interfaccia della riga di comando di Azure in [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Criteri di riavvio del contenitore

Quando si crea un [gruppo di contenitori](container-instances-container-groups.md) in Istanze di Azure Container, è possibile specificare una delle tre impostazioni dei criteri di riavvio.

| Criterio di riavvio   | Descrizione |
| ---------------- | :---------- |
| `Always` | I contenitori nel gruppo contenitore vengono sempre riavviati. Questa è l'impostazione **predefinita** applicata quando non si specifica alcun criterio di riavvio al momento della creazione del contenitore. |
| `Never` | I contenitori nel gruppo contenitore non vengono riavviati mai. I contenitori vengono eseguiti al massimo una volta. |
| `OnFailure` | I contenitori nel gruppo contenitore vengono riavviati solo quando il processo eseguito nel contenitore ha esito negativo, quando termina con un codice di uscita diverso da zero. I contenitori vengono eseguiti almeno una volta. |

## <a name="specify-a-restart-policy"></a>Specificare un criterio di riavvio

Le modalità con cui si specificano i criteri di riavvio dipendano da come si creano le istanze del contenitore, ad esempio con l'interfaccia della riga di comando di Azure, i cmdlet di Azure PowerShell o nel portale di Azure. Nell'interfaccia della riga di comando di Azure specificare il parametro `--restart-policy` quando si chiama [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Eseguire l'esempio di completamento

Per visualizzare i criteri di riavvio in azione, creare un'istanza di contenitore dall'immagine Microsoft [ACI-WordCount][aci-wordcount-image] e specificare il `OnFailure` criterio di riavvio. Questo contenitore di esempio esegue uno script di Python che, per impostazione predefinita, analizza il testo [Amleto](http://shakespeare.mit.edu/hamlet/full.html) di Shakespeare, scrive le 10 parole più comuni in STDOUT ed esce.

Eseguire il contenitore di esempio con il comando seguente [az container create][az-container-create]:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Istanze di Azure Container avvia il contenitore e lo interrompe quindi quando la sua applicazione, o lo script in questo caso, esce. Quando Istanze di Azure Container arresta un contenitore i cui criteri di riavvio sono `Never` o `OnFailure`, lo stato del contenitore viene impostato su **Terminato**. È possibile controllare lo stato del contenitore usando il comando [az container show][az-container-show]:

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Output di esempio:

```bash
"Terminated"
```

Quando lo stato del contenitore di esempio mostra *Terminato*, è possibile visualizzare l'output dell'attività visualizzando i log dei contenitori. Eseguire il comando [az container logs][az-container-logs] per visualizzare l'output dello script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Output:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Questo esempio mostra l'output che lo script ha inviato a STDOUT. Le attività nei contenitori, tuttavia, potrebbero invece scrivere l'output in un archivio permanente per il recupero successivo. Ad esempio in una [condivisione file di Azure](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Passaggi successivi

Gli scenari basati su attività, ad esempio l'elaborazione batch di un set di dati di grandi dimensioni con diversi contenitori, possono sfruttare le [variabili di ambiente](container-instances-environment-variables.md) o le [righe di comando](container-instances-start-command.md) personalizzate in fase di esecuzione.

Per informazioni dettagliate su come mantenere l'output dei contenitori che vengono eseguiti fino al completamento, vedere [Montaggio di una condivisione file di Azure con Istanze di Azure Container](./container-instances-volume-azure-files.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
