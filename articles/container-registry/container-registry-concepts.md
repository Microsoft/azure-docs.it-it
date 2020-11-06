---
title: Informazioni sui repository & immagini
description: Introduzione ai concetti chiave di registri contenitori di Azure, repository e immagini del contenitore.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: cd2f93c119817c722401f7290064894f3d39dac9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335895"
---
# <a name="about-registries-repositories-and-images"></a>Informazioni sui registri, i repository e le immagini

Questo articolo presenta i concetti chiave relativi a registri contenitori, repository e immagini del contenitore ed elementi correlati. 

## <a name="registry"></a>Registro

Un *registro* contenitori è un servizio che archivia e distribuisce immagini del contenitore. Docker Hub è un registro contenitori pubblico che supporta la community open source e funge da catalogo di immagini generale. Azure Container Registry offre agli utenti il controllo diretto delle proprie immagini, con l'autenticazione integrata, la [replica geografica](container-registry-geo-replication.md) che supporta la distribuzione e l'affidabilità globali per le distribuzioni con chiusura in rete, la [configurazione della rete virtuale e del firewall](container-registry-vnet.md), il [blocco dei tag](container-registry-image-lock.md)e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore Docker, Azure Container Registry supporta gli [artefatti di contenuto](container-registry-image-formats.md) correlati, inclusi i formati di immagine OCI (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementi indirizzabili del contenuto di un artefatto

L'indirizzo di un artefatto in un registro contenitori di Azure include gli elementi seguenti. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** : nome completo dell'host del registro di sistema. L'host del registro di sistema in un registro contenitori di Azure è nel formato *Registro* di sistema. azurecr.io (tutti minuscole). È necessario specificare loginUrl quando si usa Docker o altri strumenti client per eseguire il pull o il push di elementi in un registro contenitori di Azure. 
* **repository** -nome di un raggruppamento logico di una o più immagini o artefatti correlati, ad esempio le immagini per un'applicazione o un sistema operativo di base. Può includere il percorso *dello spazio dei nomi* . 
* identificatore di **tag** di una versione specifica di un'immagine o di un artefatto archiviato in un repository.

Ad esempio, il nome completo di un'immagine in un registro contenitori di Azure potrebbe essere simile al seguente:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Per informazioni dettagliate su questi elementi, vedere le sezioni seguenti.

## <a name="repository-name"></a>Nome del repository

Un *repository* è una raccolta di immagini del contenitore o di altri elementi con lo stesso nome, ma tag diversi. Ad esempio, le tre immagini seguenti si trovano nel repository "acr-helloworld":


- *ACR-HelloWorld: più recente*
- *ACR-HelloWorld: V1*
- *ACR-HelloWorld: V2*

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di identificare i repository correlati e la proprietà dell'artefatto nell'organizzazione usando nomi delimitati da barre. Tuttavia, il registro di sistema gestisce tutti i repository in modo indipendente, non come una gerarchia. Ad esempio:

- *Marketing/campaign10-18/Web: V2*
- *Marketing/campaign10-18/API: V3*
- *Marketing/campaign10-18/email-sender: V2*
- *prodotto-restituzione/Web-invio: 20180604*
- *Product-Returns/legacy-Integrator: 20180715*

I nomi dei repository possono includere solo caratteri alfanumerici minuscoli, punti, trattini, caratteri di sottolineatura e barre. 

Per le regole di denominazione complete del repository, vedere la [specifica Open Container Initiative Distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="image"></a>Immagine

Un'immagine del contenitore o un altro artefatto all'interno di un registro è associato a uno o più tag, ha uno o più livelli ed è identificato da un manifesto. Comprendere in che modo questi componenti sono correlati tra loro possono aiutare a gestire il registro di sistema in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro artefatto ne specifica la versione. Un singolo artefatto all'interno di un repository può essere assegnato a uno o più tag e può anche essere "senza tag". Ovvero, è possibile eliminare tutti i tag da un'immagine, mentre i dati dell'immagine (i relativi livelli) rimangono nel registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione. Il tag `latest` viene usato per impostazione predefinita se non ne viene fornito uno nei comandi di Docker.

Il modo in cui vengono contrassegnate le immagini del contenitore è guidato dagli scenari per lo sviluppo o la distribuzione. Ad esempio, sono consigliati Tag stabili per la manutenzione delle immagini di base e tag univoci per la distribuzione di immagini. Per altre informazioni, vedere [consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md).

Per le regole di denominazione dei tag, vedere la [documentazione di Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Livello

Le immagini del contenitore sono costituite da uno o più *livelli* , ognuno corrispondente a una riga nel Dockerfile che definisce l'immagine. Le immagini in un registro condividono i livelli comuni, aumentando così l'efficienza di archiviazione. Ad esempio, numerose immagini in repository diversi potrebbero condividere lo stesso livello di base Alpine Linux, ma ne verrà archiviata una sola copia nel registro.

La condivisione dei livelli ne ottimizza anche la distribuzione ai nodi, in quanto più immagini condivideranno i livelli comuni. Ad esempio, se un'immagine già presente in un nodo include il livello Alpine Linux come base, il pull successivo di un'immagine diversa che fa riferimento al medesimo livello non trasferirà nuovamente il livello al nodo, ma farà invece riferimento a quello già presente.

Per garantire l'isolamento e la protezione da potenziali manipolazioni dei livelli, i livelli non vengono condivisi tra i registri.

### <a name="manifest"></a>manifesto

Ogni immagine del contenitore o artefatto di cui è stato eseguito il push in un registro contenitori è associato a un *manifesto*. generato dal registro quando l'immagine viene inserita. Il manifesto identifica in modo univoco l'immagine e ne specifica i livelli. È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, elencare i manifesti per il repository "ACR-HelloWorld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Hash di manifesto

I manifesti sono identificati da un hash SHA-256 univoco, l' *hash di manifesto*. Ogni immagine o artefatto, indipendentemente dal fatto che sia contrassegnato o meno, viene identificato dal digest. Il valore dell'hash è univoco anche se i dati dei livelli dell'immagine sono identici a quelli di un'altra. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un'immagine da un registro specificandone l'hash nell'operazione. Alcuni sistemi sono configurati per eseguire il pull tramite hash perché in questo modo viene garantita la versione dell'immagine in questione, anche se in seguito viene eseguito il push nel registro di un'immagine con gli stessi tag.

Ad esempio, effettuare il pull di un'immagine dal repository "ACR-HelloWorld" dal digest del manifesto:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Se si esegue ripetutamente il push di immagini modificate con tag identici, è possibile che vengano create immagini orfane, ovvero prive di tag, che occupano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. L'eliminazione di un'immagine senza tag libera lo spazio del registro di sistema quando il manifesto è l'unico, o l'ultimo, che punta a un livello specifico. Per informazioni su come liberare spazio usato dalle immagini non contrassegnate, vedere [eliminare immagini del contenitore in Azure container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [archiviazione immagini](container-registry-storage.md) e i [formati di contenuto supportati](container-registry-image-formats.md) in Azure container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


