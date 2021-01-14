---
title: Configurazione del cluster nei servizi Azure Kubernetes (AKS)
description: Informazioni su come configurare un cluster nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/13/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eacca50e00dfe8625d86362c444544e2fd5d5511
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201111"
---
# <a name="configure-an-aks-cluster"></a>Configurare un cluster del servizio Azure Kubernetes

Per creare un cluster AKS, potrebbe essere necessario personalizzare la configurazione del cluster in base alle esigenze. Questo articolo introduce alcune opzioni per la personalizzazione del cluster AKS.

## <a name="os-configuration"></a>Configurazione del sistema operativo

AKS supporta ora Ubuntu 18,04 come sistema operativo node (OS) in disponibilità generale per i cluster nelle versioni kubernetes più alta di 1.18.8. Per le versioni inferiori a 1.18. x, AKS Ubuntu 16,04 è ancora l'immagine di base predefinita. Da kubernetes v 1.18. x e versioni successive, la base predefinita è AKS Ubuntu 18,04.

### <a name="use-aks-ubuntu-1804-generally-available-on-new-clusters"></a>Usare AKS Ubuntu 18,04 disponibile a livello generale nei nuovi cluster

I cluster creati in Kubernetes v 1.18 o versione successiva vengono predefiniti per l' `AKS Ubuntu 18.04` immagine del nodo. I pool di nodi in una versione Kubernetes supportata inferiore a 1,18 continueranno `AKS Ubuntu 16.04` a essere visualizzati come immagine del nodo, ma verranno aggiornati in `AKS Ubuntu 18.04` una volta che la versione Kubernetes del cluster o del pool di nodi verrà aggiornata a v 1.18 o versione successiva.

Si consiglia vivamente di testare i carichi di lavoro nei pool di nodi AKS Ubuntu 18,04 prima di usare i cluster in 1,18 o versione successiva. Leggere le informazioni su come [testare i pool di nodi Ubuntu 18,04](#test-aks-ubuntu-1804-generally-available-on-existing-clusters).

Per creare un cluster usando un' `AKS Ubuntu 18.04` immagine del nodo, è sufficiente creare un cluster che esegue kubernetes v 1.18 o versione successiva, come illustrato di seguito.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-generally-available-on-existing-clusters"></a>Usare AKS Ubuntu 18,04 disponibile a livello generale nei cluster esistenti

I cluster creati in Kubernetes v 1.18 o versione successiva vengono predefiniti per l' `AKS Ubuntu 18.04` immagine del nodo. I pool di nodi in una versione Kubernetes supportata inferiore a 1,18 continueranno `AKS Ubuntu 16.04` a essere visualizzati come immagine del nodo, ma verranno aggiornati in `AKS Ubuntu 18.04` una volta che la versione Kubernetes del cluster o del pool di nodi verrà aggiornata a v 1.18 o versione successiva.

Si consiglia vivamente di testare i carichi di lavoro nei pool di nodi AKS Ubuntu 18,04 prima di usare i cluster in 1,18 o versione successiva. Leggere le informazioni su come [testare i pool di nodi Ubuntu 18,04](#test-aks-ubuntu-1804-generally-available-on-existing-clusters).

Se i cluster o i pool di nodi sono pronti per l' `AKS Ubuntu 18.04` immagine del nodo, è possibile aggiornarli semplicemente a un v 1.18 o versione successiva, come indicato di seguito.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Se si vuole solo aggiornare un solo pool di nodi:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-generally-available-on-existing-clusters"></a>Test AKS Ubuntu 18,04 disponibile a livello generale nei cluster esistenti

Pool di nodi creati in Kubernetes v 1.18 o superiore per impostazione predefinita nell' `AKS Ubuntu 18.04` immagine del nodo. I pool di nodi in una versione Kubernetes supportata inferiore a 1,18 continueranno `AKS Ubuntu 16.04` a essere visualizzati come immagine del nodo, ma verranno aggiornati a `AKS Ubuntu 18.04` una volta che la versione Kubernetes del pool di nodi verrà aggiornata a v 1.18 o successiva.

Si consiglia vivamente di testare i carichi di lavoro nei pool di nodi AKS Ubuntu 18,04 prima di aggiornare i pool di nodi di produzione.

Per creare un pool di nodi usando un' `AKS Ubuntu 18.04` immagine del nodo, è sufficiente creare un pool di nodi che esegue kubernetes v 1.18 o versione successiva. Il piano di controllo del cluster deve essere almeno in v 1.18 o superiore, ma gli altri pool di nodi possono rimanere in una versione precedente di kubernetes.
Di seguito viene prima di tutto aggiornato il piano di controllo e quindi viene creato un nuovo pool di nodi con v 1.18 che riceverà la nuova versione del sistema operativo dell'immagine del nodo.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Usare AKS Ubuntu 18,04 nei nuovi cluster (anteprima)

La sezione seguente illustra come usare e testare AKS Ubuntu 18,04 in cluster che non usano ancora una versione 1.18. x di kubernetes o versioni successive oppure che sono stati creati prima che questa funzionalità venisse resa disponibile a livello generale usando l'anteprima della configurazione del sistema operativo.

Devono essere installate le risorse seguenti:

- [Interfaccia della][azure-cli-install]riga di comando di Azure, versione 2.2.0 o successiva
- Estensione aks-preview 0.4.35

Per installare l'estensione aks-preview 0.4.35 o una versione successiva, usare i comandi seguenti dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrare la funzionalità `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configurare il cluster per l'uso di Ubuntu 18.04 quando viene creato il cluster. Usare il flag `--aks-custom-headers` per impostare Ubuntu 18.04 come sistema operativo predefinito.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Per creare cluster con l'immagine AKS Ubuntu 16,04, è possibile omettere il `--aks-custom-headers` tag personalizzato.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Usare i cluster esistenti AKS Ubuntu 18,04 (anteprima)

Configurare un nuovo pool di nodi per usare Ubuntu 18.04. Usare il flag `--aks-custom-headers` per impostare Ubuntu 18.04 come sistema operativo predefinito per il pool di nodi.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se si desidera creare pool di nodi con l'immagine AKS Ubuntu 16,04, è possibile omettere il `--aks-custom-headers` tag personalizzato.

## <a name="container-runtime-configuration"></a>Configurazione del runtime del contenitore

Un runtime contenitore è un software che esegue contenitori e gestisce le immagini del contenitore in un nodo. Il runtime consente di astrarre la funzionalità sys-calls o del sistema operativo (OS) specifica per l'esecuzione di contenitori in Linux o Windows. I cluster AKS che usano i pool di nodi Kubernetes versione 1,19 e sono più usati `containerd` come runtime del contenitore. I cluster AKS che usano Kubernetes prima della versione 1.19 per i pool di nodi usano [Moby](https://mobyproject.org/) (upstream Docker) come runtime del contenitore.

![IRC Docker 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) è un runtime del contenitore principale conforme a [OCI](https://opencontainers.org/) (Open Container Initiative) che fornisce il set minimo di funzionalità necessarie per l'esecuzione di contenitori e la gestione delle immagini in un nodo. È stata [donata](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) al cloud native Compute Foundation (CNCF) a marzo 2017. La versione corrente di Moby usata da AKS usa già e si basa su `containerd` , come illustrato in precedenza.

Con un `containerd` nodo basato su e i pool di nodi, anziché comunicare con `dockershim` , il kubelet parlerà direttamente con `containerd` il plug-in dell'interfaccia di runtime del contenitore, rimuovendo gli hop aggiuntivi nel flusso rispetto all'implementazione di IRC di Docker. Di conseguenza, si noterà una migliore latenza di avvio del Pod e un minor utilizzo di risorse (CPU e memoria).

Usando `containerd` per i nodi AKS, la latenza di avvio del Pod migliora e l'utilizzo delle risorse del nodo da parte del runtime del contenitore diminuisce. Questi miglioramenti sono abilitati da questa nuova architettura, in cui kubelet comunica direttamente con `containerd` il plug-in di cri, mentre nell'architettura di Moby/Docker kubelet comunicherà con il `dockershim` motore Docker e prima di raggiungere `containerd` , ottenendo hop aggiuntivi nel flusso.

![CRI di Docker 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funziona a ogni versione GA di Kubernetes in AKS e in ogni versione Kubernetes upstream precedente alla versione 1.19 e supporta tutte le funzionalità Kubernetes e AKS.

> [!IMPORTANT]
> I cluster con pool di nodi creati in Kubernetes v 1.19 o superiore vengono predefiniti per `containerd` il runtime del contenitore. I cluster con pool di nodi in una versione Kubernetes supportata inferiore a 1,19 ricevono `Moby` per il runtime del contenitore, ma verranno aggiornati a `ContainerD` una volta che la versione Kubernetes del pool di nodi verrà aggiornata a v 1.19 o successiva. È comunque possibile usare `Moby` i pool di nodi e i cluster in versioni precedenti supportate fino a quando non sono disponibili supporto.
> 
> Si consiglia vivamente di testare i carichi di lavoro nei pool di nodi AKS con `containerD` prima di usare i cluster 1,19 o versione successiva.

La sezione seguente illustra come usare e testare AKS con `containerD` in cluster che non usano ancora una versione di Kubernetes 1,19 o successiva oppure che sono stati creati prima che questa funzionalità sia diventata disponibile a livello generale, usando l'anteprima della configurazione del runtime del contenitore.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Usare `containerd` come runtime del contenitore (anteprima)

È necessario disporre dei prerequisiti seguenti:

- [Interfaccia della][azure-cli-install]riga di comando di Azure, versione 2.8.0 o successiva installata
- L'estensione AKS-Preview versione 0.4.53 o successiva
- `UseCustomizedContainerRuntime`Flag funzionalità registrato
- `UseCustomizedUbuntuPreview`Flag funzionalità registrato

Per installare l'estensione AKS-Preview 0.4.53 o versione successiva, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrare le `UseCustomizedContainerRuntime` `UseCustomizedUbuntuPreview` funzionalità e:

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Usare `containerd` nei nuovi cluster (anteprima)

Configurare il cluster da usare `containerd` durante la creazione del cluster. Usare il `--aks-custom-headers` flag per impostare `containerd` come runtime del contenitore.

> [!NOTE]
> Il `containerd` Runtime è supportato solo nei nodi e nei pool di nodi usando l'immagine AKS Ubuntu 18,04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Per creare cluster con il runtime Moby (Docker), è possibile omettere il `--aks-custom-headers` tag personalizzato.

### <a name="use-containerd-on-existing-clusters-preview"></a>Usare `containerd` nei cluster esistenti (anteprima)

Configurare un nuovo pool di nodi da usare `containerd` . Usare il `--aks-custom-headers` flag per impostare `containerd` come runtime per il pool di nodi.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Se si desidera creare pool di nodi con il runtime Moby (Docker), è possibile omettere il `--aks-custom-headers` tag personalizzato.


### <a name="containerd-limitationsdifferences"></a>`Containerd` limitazioni/differenze

* Per usare `containerd` come runtime del contenitore è necessario usare AKS Ubuntu 18,04 come immagine del sistema operativo di base.
* Mentre il set di strumenti Docker è ancora presente nei nodi, Kubernetes USA `containerd` come runtime del contenitore. Quindi, poiché Moby/Docker non gestisce i contenitori creati da Kubernetes nei nodi, non è possibile visualizzare o interagire con i contenitori usando i comandi di Docker (ad esempio `docker ps` ) o l'API docker.
* Per `containerd` , è consigliabile usare [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) come interfaccia della riga di comando sostitutiva anziché l'interfaccia della riga di comando di Docker per la **risoluzione dei problemi relativi** a Pod, contenitori e immagini contenitore nei nodi Kubernetes (ad esempio, `crictl ps` ). 
   * Non fornisce la funzionalità completa dell'interfaccia della riga di comando di Docker. È progettato solo per la risoluzione dei problemi.
   * `crictl` offre una visualizzazione più intuitiva dei contenitori, con concetti quali pod e così via.
* `Containerd` imposta la registrazione usando il formato di registrazione standardizzato, `cri` che è diverso da quello attualmente ottenuto dal driver JSON di Docker. La soluzione di registrazione deve supportare il `cri` formato di registrazione, ad esempio [monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-enable-new-cluster.md).
* Non è più possibile accedere al motore Docker, `/var/run/docker.sock` o usare Docker-in-Docker (DinD).
  * Se al momento si estraggono i registri applicazioni o i dati di monitoraggio dal motore Docker, usare invece un elemento come [monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-enable-new-cluster.md) . Inoltre, AKS non supporta l'esecuzione di comandi fuori banda sui nodi dell'agente che potrebbero causare instabilità.
  * Anche quando si usa Moby/Docker, la creazione di immagini e la possibilità di sfruttare direttamente il motore Docker con i metodi precedenti sono fortemente sconsigliate. Kubernetes non è in grado di riconoscere le risorse [usate e questi](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)approcci presentano molti problemi descritti [qui](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) , ad esempio.
* Compilazione di immagini: è possibile continuare a usare il flusso di lavoro di compilazione Docker corrente come di consueto, a meno che non si stiano compilando imagages all'interno del cluster AKS. In questo caso, si consiglia di passare all'approccio consigliato per la creazione di immagini con [attività ACR](../container-registry/container-registry-quickstart-task-cli.md)o un'opzione in cluster più sicura, ad esempio [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Macchine virtuali di seconda generazione (anteprima)

Azure supporta [macchine virtuali (Gen2) di seconda generazione](../virtual-machines/generation-2.md). Le macchine virtuali di seconda generazione supportano le funzionalità principali che non sono supportate nelle macchine virtuali di prima generazione (Gen1). Queste funzionalità includono una maggior quantità di memoria, Intel Software Guard Extensions (Intel SGX) e la memoria persistente virtuale (vPMEM).

Le macchine virtuali di seconda generazione usano la nuova architettura di avvio basata su UEFI anziché l'architettura basata su BIOS usata dalle macchine virtuali di prima generazione.
Solo SKU e dimensioni specifiche supportano le VM Gen2. Controllare l' [elenco delle dimensioni supportate](../virtual-machines/generation-2.md#generation-2-vm-sizes)per verificare se lo SKU supporta o richiede Gen2.

Inoltre, non tutte le immagini di macchina virtuale supportano Gen2, nelle macchine virtuali AKS Gen2 utilizzeranno la nuova [immagine AKS Ubuntu 18,04](#os-configuration). Questa immagine supporta tutti gli SKU e le dimensioni di Gen2.

Per usare le macchine virtuali Gen2 durante la fase di anteprima, è necessario:
- Estensione dell'interfaccia della riga di comando `aks-preview` installata.
- `Gen2VMPreview`Flag funzionalità registrato.

Registrare la funzionalità `Gen2VMPreview`:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Per installare l'estensione dell'interfaccia della riga di comando AKS-Preview, usare i seguenti comandi dell'interfaccia della riga di comando

```azurecli
az extension add --name aks-preview
```

Per aggiornare l'estensione aks-preview dell'interfaccia della riga di comando, usare i comandi seguenti dell'interfaccia della riga di comando di Azure:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Usare macchine virtuali Gen2 in nuovi cluster (anteprima)
Configurare il cluster per l'uso di macchine virtuali Gen2 per lo SKU selezionato quando viene creato il cluster. Usare il `--aks-custom-headers` flag per impostare Gen2 come generazione di VM in un nuovo cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se si vuole creare un cluster normale usando macchine virtuali di generazione 1 (Gen1), è possibile omettere il `--aks-custom-headers` tag personalizzato. È anche possibile scegliere di aggiungere altre macchine virtuali Gen1 o Gen2 come indicato di seguito.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Usare macchine virtuali Gen2 in cluster esistenti (anteprima)
Configurare un nuovo pool di nodi per l'uso di macchine virtuali Gen2. Usare il `--aks-custom-headers` flag per impostare Gen2 come generazione di VM per il pool di nodi.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se si desidera creare pool di nodi Gen1 normali, è possibile omettere il `--aks-custom-headers` tag personalizzato.


## <a name="ephemeral-os"></a>Sistema operativo temporaneo

Per impostazione predefinita, Azure replica automaticamente il disco del sistema operativo per una macchina virtuale in archiviazione di Azure, in modo da evitare la perdita di dati se la VM deve essere rilocata in un altro host. Tuttavia, poiché i contenitori non sono progettati per lo stato locale in modo permanente, questo comportamento offre un valore limitato, offrendo alcuni svantaggi, tra cui il provisioning di nodi più lenti e una latenza di lettura/scrittura superiore.

Al contrario, i dischi del sistema operativo temporanei vengono archiviati solo nel computer host, proprio come un disco temporaneo. Questo garantisce una latenza di lettura/scrittura più bassa, oltre a un aumento più rapido dei nodi e agli aggiornamenti del cluster.

Come il disco temporaneo, un disco del sistema operativo temporaneo è incluso nel prezzo della macchina virtuale, pertanto non si verificano costi di archiviazione aggiuntivi.

> [!IMPORTANT]
>Quando un utente non richiede in modo esplicito i dischi gestiti per il sistema operativo, AKS utilizzerà per impostazione predefinita il sistema operativo temporaneo, se possibile, per una determinata configurazione nodepool.

Quando si usa il sistema operativo temporaneo, il disco del sistema operativo deve adattarsi alla cache della VM. Le dimensioni della cache VM sono disponibili nella [documentazione di Azure](../virtual-machines/dv3-dsv3-series.md) tra parentesi accanto alla velocità effettiva di i/o ("dimensioni della cache in Gib").

Usando le dimensioni della macchina virtuale AKS predefinite Standard_DS2_v2 con le dimensioni del disco del sistema operativo predefinite di 100 GB come esempio, questa dimensione della VM supporta il sistema operativo temporaneo, ma ha solo 86GB di dimensioni della cache. Per impostazione predefinita, questa configurazione gestirà i dischi se l'utente non specifica in modo esplicito. Se un utente ha richiesto in modo esplicito il sistema operativo temporaneo, riceverebbe un errore di convalida.

Se un utente richiede lo stesso Standard_DS2_v2 con un disco del sistema operativo da 60 GB, questa configurazione per impostazione predefinita è il sistema operativo temporaneo: la dimensione richiesta di 60 GB è inferiore alla dimensione massima della cache di 86GB.

Usando Standard_D8s_v3 con il disco del sistema operativo 100 GB, questa dimensione della VM supporta il sistema operativo temporaneo e ha 200 GB di spazio nella cache. Se un utente non specifica il tipo di disco del sistema operativo, per impostazione predefinita il nodepool riceverà il sistema operativo temporaneo. 

Il sistema operativo temporaneo richiede almeno la versione 2.15.0 dell'interfaccia della riga di comando di Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Usare il sistema operativo temporaneo nei nuovi cluster

Configurare il cluster per l'uso di dischi del sistema operativo temporanei quando viene creato il cluster. Usare il `--node-osdisk-type` flag per impostare il sistema operativo temporaneo come tipo di disco del sistema operativo per il nuovo cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Se si vuole creare un cluster normale usando dischi del sistema operativo collegati alla rete, è possibile specificare `--node-osdisk-type=Managed` . È anche possibile scegliere di aggiungere più pool di nodi del sistema operativo temporanei come indicato di seguito.

### <a name="use-ephemeral-os-on-existing-clusters"></a>USA sistema operativo temporaneo nei cluster esistenti
Configurare un nuovo pool di nodi per l'uso di dischi del sistema operativo temporanei. Usare il `--node-osdisk-type` flag per impostare come tipo di disco del sistema operativo come tipo di disco del sistema operativo per il pool di nodi.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Con il sistema operativo temporaneo è possibile distribuire le immagini di macchine virtuali e istanze fino alla dimensione della cache VM. Nel caso di AKS, la configurazione del disco del sistema operativo del nodo predefinito usa 128GB, il che significa che è necessario disporre di una dimensione della macchina virtuale con una cache maggiore di 128GB. Il Standard_DS2_v2 predefinito ha una dimensione della cache di 86GB, che non è sufficientemente grande. Il Standard_DS3_v2 ha una dimensione della cache di 172GB, che è sufficientemente grande. È anche possibile ridurre le dimensioni predefinite del disco del sistema operativo usando `--node-osdisk-size` . La dimensione minima per le immagini AKS è 30 GB. 

Se si desidera creare pool di nodi con dischi del sistema operativo collegati alla rete, è possibile specificare `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Nome del gruppo di risorse personalizzato

Quando si distribuisce un cluster del servizio Kubernetes di Azure in Azure, viene creato un secondo gruppo di risorse per i nodi di lavoro. Per impostazione predefinita, AKS assegna un nome al gruppo di risorse del nodo `MC_resourcegroupname_clustername_location`, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome per il gruppo di risorse, installare la versione 0.3.2 o una versione successiva dell'estensione aks-preview dell'interfaccia della riga di comando di Azure. Nell'interfaccia della riga di comando di Azure, usare il parametro `--node-resource-group` del comando `az aks create` per specificare un nome personalizzato per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse usando la proprietà `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse Azure nella propria sottoscrizione. Quando viene creato il cluster, è possibile specificare solo il nome del gruppo di risorse personalizzato. 

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

- Specificare un gruppo di risorse esistente come gruppo di risorse del nodo.
- Specificare una sottoscrizione diversa per il gruppo di risorse nodo.
- Cambiare il nome del gruppo di risorse nodo dopo la creazione del cluster.
- Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
- Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [aggiornare le immagini dei nodi](node-image-upgrade.md) nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes](upgrade-cluster.md).
- Scopri di più su [ `containerd` e Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md).
- Scopri di più sui [dischi del sistema operativo temporaneo](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
