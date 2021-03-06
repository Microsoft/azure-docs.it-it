---
title: Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes
description: Informazioni su come configurare le funzionalità di rete avanzate di Azure CNI nel servizio Azure Kubernetes, inclusa la distribuzione di un cluster del servizio Azure Kubernetes in una rete e in una subnet virtuali esistenti.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 839aa012cedaaa6f5bd3d1edad60e3ea7278133b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775890"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes

Per impostazione predefinita, i cluster del servizio Azure Kubernetes usano [kubenet][kubenet] e la creazione di una rete virtuale e di una subnet avviene automaticamente. Con *kubenet* i nodi ottengono un indirizzo IP da una subnet della rete virtuale. Il protocollo NAT (Network Address Translation) viene quindi configurato nei nodi e i pod ricevono un indirizzo IP "nascosto" dietro l'indirizzo IP del nodo. Questo approccio riduce il numero di indirizzi IP che è necessario riservare ai pod nello spazio degli indirizzi della rete.

Con [Azure Container Networking Interface (CNI)][cni-networking] ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una maggiore pianificazione e spesso conduce all'esaurimento degli indirizzi IP o alla necessità di riconfigurare i cluster in una subnet di dimensioni maggiori man mano che aumentano le richieste dell'applicazione.

Questo articolo illustra come usare le funzionalità di rete di *Azure CNI* per creare e usare una subnet di rete virtuale per un cluster del servizio Azure Kubernetes. Per altre informazioni sulle opzioni di rete e le relative considerazioni, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][aks-network-concepts].

## <a name="prerequisites"></a>Prerequisiti

* La rete virtuale per il cluster servizio Azure Kubernetes deve consentire la connettività Internet in uscita.
* I cluster del servizio Kubernetes non possono usare , , o per l'intervallo di indirizzi del servizio `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` Kubernetes, l'intervallo di indirizzi pod o l'intervallo di indirizzi della rete `192.0.2.0/24` virtuale del cluster.
* L'identità del cluster usata dal cluster del servizio Web Disassoce deve avere almeno le autorizzazioni di [Collaboratore](../role-based-access-control/built-in-roles.md#network-contributor) Rete nella subnet all'interno della rete virtuale. Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* La subnet assegnata al pool di nodi del cluster del server di Gestione risorse di Microsoft Non può essere [una subnet delegata.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>Pianificare l'indirizzamento IP per il cluster

I cluster configurati con Azure CNI richiedono una pianificazione aggiuntiva. Le dimensioni della rete virtuale e della subnet devono essere sufficienti sia per il numero di pod che si prevede di eseguire che per il numero di nodi per il cluster.

Gli indirizzi IP per i pod e i nodi del cluster vengono assegnati dalla subnet specificata all'interno della rete virtuale. Ogni nodo è configurato con un indirizzo IP primario. Per impostazione predefinita, l'interfaccia di rete dei contenitori di Azure preconfigura 30 indirizzi IP aggiuntivi che vengono assegnati ai pod pianificati nel nodo. Quando si aumenta il numero di istanze del cluster, ogni nodo viene configurato in modo simile con gli indirizzi IP della subnet. È anche possibile visualizzare il [numero massimo di pod per nodo](#maximum-pods-per-node).

> [!IMPORTANT]
> Per calcolare il numero di indirizzi IP necessari, è opportuno considerare le eventuali operazioni di aggiornamento e ridimensionamento. Se si imposta l'intervallo di indirizzi IP solo per supportare un numero fisso di nodi, non è possibile aggiornare o ridimensionare il cluster.
>
> * Quando si esegue l'**aggiornamento** del cluster servizio Azure Kubernetes, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo e il nodo precedente viene rimosso dal cluster. Questo processo di aggiornamento in sequenza richiede la disponibilità di almeno un altro blocco di indirizzi IP. Il numero di nodi risulta quindi pari a `n + 1`.
>   * Questa considerazione è particolarmente importante quando si usano pool di nodi di Windows Server. I nodi di Windows Server nel servizio Web Diaks non applicano automaticamente gli aggiornamenti di Windows, ma si esegue un aggiornamento nel pool di nodi. Questo aggiornamento distribuisce nuovi nodi con l'immagine del nodo di base e le patch di sicurezza più recenti di Windows Server 2019. Per altre informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere [Aggiornare un pool di nodi nel servizio Web Diaks.][nodepool-upgrade]
>
> * Quando si esegue il **ridimensionamento** di un cluster servizio Azure Kubernetes, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo. Per calcolare l'intervallo di indirizzi IP, è necessario considerare il modo in cui si vuole aumentare il numero di nodi e pod supportati dal cluster. È necessario includere anche un nodo aggiuntivo per le operazioni di aggiornamento. Il numero di nodi risulta quindi pari a `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se si prevede che i nodi eseguano il numero massimo di pod e che distruggano e distribuiscano regolarmente pod, è necessario considerare alcuni indirizzi IP aggiuntivi per ogni nodo. L'uso di questi indirizzi implica che possano essere necessari alcuni secondi per eliminare un servizio e per consentire la distribuzione dell'indirizzo IP rilasciato per un nuovo servizio e l'acquisizione dei dati necessari.

Il piano di indirizzo IP per un cluster servizio Azure Kubernetes è costituito da una rete virtuale, almeno una subnet per i nodi e i pod e un intervallo di indirizzi per il servizio Kubernetes.

| Intervallo di indirizzi / Risorsa di Azure | Limiti e dimensioni |
| --------- | ------------- |
| Rete virtuale | La rete virtuale di Azure può avere dimensioni pari a /8, ma è limitata a 65.536 indirizzi IP configurati. Considerare tutte le esigenze di rete, inclusa la comunicazione con i servizi in altre reti virtuali, prima di configurare lo spazio indirizzi. Ad esempio, se si configurano spazi di indirizzi troppo grandi, si potrebbero verificare problemi con la sovrapposizione di altri spazi indirizzi all'interno della rete.|
| Subnet | Deve essere sufficientemente grande da contenere i nodi, i pod e tutte le risorse Kubernetes e Azure che potrebbero essere sottoposte a provisioning nel cluster. Ad esempio, se si distribuisce un Azure Load Balancer interno, i relativi indirizzi IP front-end vengono allocati dalla subnet del cluster, ma non gli indirizzi IP pubblici. Per calcolare le dimensioni della subnet, è opportuno considerare anche eventuali operazioni di aggiornamento o le esigenze di ridimensionamento future.<p />Per calcolare le dimensioni *minime* della subnet incluso un nodo aggiuntivo per eventuali operazioni di aggiornamento: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Esempio relativo a un cluster a 50 nodi: `(51) + (51  * 30 (default)) = 1,581` (/21 o più grande)<p/>Esempio relativo a un cluster a 50 nodi che include anche il provisioning per l'aggiunta di altri 10 nodi: `(61) + (61 * 30 (default)) = 1,891` (/21 o superiore)<p>Se non si specifica un numero massimo di pod per nodo al momento della creazione del cluster, l'impostazione predefinita è *30*. Il numero minimo di indirizzi IP richiesti si basa su questo valore. Se si calcolano i requisiti di indirizzi IP minimi in base a un valore massimo diverso, vedere la sezione relativa a [come configurare il numero massimo di pod per nodo](#configure-maximum---new-clusters) per impostare questo valore quando si distribuisce il cluster. |
| Intervallo di indirizzi del servizio Kubernetes | Questo intervallo non deve essere usato da nessun elemento della rete che si trova su questa rete virtuale o è connesso a essa. Il CIDR dell'indirizzo del servizio deve essere più piccolo di /12. È possibile riutilizzare questo intervallo in diversi cluster del servizio Web Diaks. |
| Indirizzo IP del servizio DNS Kubernetes | Indirizzo IP all'interno dell'intervallo di indirizzi del servizio Kubernetes che verrà usato dall'individuazione del servizio cluster. Non usare il primo indirizzo IP nell'intervallo di indirizzi, ad esempio .1. Il primo indirizzo nell'intervallo della subnet è usato per l'indirizzo *kubernetes.default.svc.cluster.local*. |
| Indirizzo del bridge Docker | L'indirizzo di rete del bridge Docker rappresenta l'indirizzo di rete del bridge *docker0* predefinito presente in tutte le installazioni di Docker. Anche se il bridge *Docker0* non viene usato dai cluster del servizio Contenitore di AKS o dai pod stessi, è necessario impostare questo indirizzo per continuare a supportare scenari come *docker build* all'interno del cluster del servizio Contenitore di Microsoft. È necessario selezionare un CIDR per l'indirizzo di rete del bridge Docker perché in caso contrario Docker selezionerà automaticamente una subnet, che potrebbe essere in conflitto con altri CIDR. È necessario selezionare uno spazio indirizzi che non sia in conflitto con il resto dei CIDR nelle reti, inclusi il CIDR del servizio del cluster e il CIDR del pod. Il valore predefinito è 172.17.0.1/16. È possibile riutilizzare questo intervallo in diversi cluster del servizio Web Diaks. |

## <a name="maximum-pods-per-node"></a>Numero massimo di pod per nodo

Il numero massimo di pod per nodo in un cluster del servizio Contenitore di Microsoft È 250. Il numero massimo *predefinito* di pod per nodo varia tra le funzionalità di rete *kubenet* e *Azure CNI* e il metodo di distribuzione del cluster.

| Metodo di distribuzione | Kubenet predefinito | Azure CNI predefinito | Configurabile in fase di distribuzione |
| -- | :--: | :--: | -- |
| Interfaccia della riga di comando di Azure | 110 | 30 | Sì (fino a 250) |
| Modello di Resource Manager | 110 | 30 | Sì (fino a 250) |
| Portale | 110 | 110 (configurato nella scheda Pool di nodi) | No |

### <a name="configure-maximum---new-clusters"></a>Configurare il valore massimo - nuovi cluster

È possibile configurare il numero massimo di pod per nodo in fase di distribuzione del cluster o quando si aggiungono nuovi pool di nodi. Se si esegue la distribuzione con l'interfaccia della riga di comando di Azure o con un modello Resource Manager, è possibile impostare il valore massimo di pod per nodo su 250.

Se non si specifica maxPods durante la creazione di nuovi pool di nodi, si riceve un valore predefinito di 30 per Azure CNI.

Viene applicato un valore minimo per il numero massimo di pod per nodo per garantire spazio per i pod di sistema critici per l'integrità del cluster. Il valore minimo che può essere impostato per il numero massimo di pod per nodo è 10 se e solo se la configurazione di ogni pool di nodi ha spazio per un minimo di 30 pod. Ad esempio, l'impostazione del numero massimo di pod per nodo su un minimo di 10 richiede che ogni singolo pool di nodi abbia un minimo di 3 nodi. Questo requisito si applica anche per ogni nuovo pool di nodi creato, quindi se 10 sono definiti come pod massimi per nodo, ogni pool di nodi aggiunto successivamente deve avere almeno 3 nodi.

| Rete | Minima | Massimo |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> Il valore minimo nella tabella precedente viene applicato rigorosamente dal servizio AKS. Non è possibile impostare un valore maxPods inferiore al valore minimo indicato in modo da impedire l'avvio del cluster.

* **Azure CLI**: specificare l'`--max-pods`argomento quando si distribuisce un cluster con il comando [az servizio Azure Kubernetes create][az-aks-create]. Il valore massimo è 250.
* **Modello di Gestione risorse**: specificare la `maxPods` proprietà nell'oggetto [ManagedClusterAgentPoolProfile] quando si distribuisce un cluster con un modello di Gestione risorse. Il valore massimo è 250.
* **Portale di Azure**: non è possibile modificare il numero massimo di pod per nodo quando si distribuisce un cluster con il portale di Azure. I cluster con funzionalità di rete Azure CNI sono limitati a 30 pod per ogni nodo quando si esegue la distribuzione con il portale di Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurare il valore massimo - cluster esistenti

L'impostazione maxPod per nodo può essere definita quando si crea un nuovo pool di nodi. Se è necessario aumentare l'impostazione maxPod per nodo in un cluster esistente, aggiungere un nuovo pool di nodi con il nuovo numero maxPod desiderato. Dopo aver migrato i pod al nuovo pool, eliminare il pool precedente. Per eliminare qualsiasi pool precedente in un cluster, assicurarsi di impostare le modalità del pool di nodi come definito nel documento [dei pool di nodi di sistema][system-node-pools].

## <a name="deployment-parameters"></a>Parametri di distribuzione

Quando si crea un cluster servizio Azure Kubernetes, per la rete Azure CNI i parametri seguenti sono configurabili:

**Rete virtuale**: rete virtuale in cui si vuole distribuire il cluster Kubernetes. Per creare una nuova rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una rete virtuale*. Per altre informazioni su limiti e quote per una rete virtuale di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Subnet**: subnet nella rete virtuale in cui si vuole distribuire il cluster. Per creare una nuova subnet nella rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una subnet*. Per la connettività ibrida, l'intervallo di indirizzi non deve sovrapporsi ad altre reti virtuali dell'ambiente in uso.

Plug-in di rete di Azure: quando si usa il plug-in di rete di **Azure,** non è possibile accedere al servizio LoadBalancer interno con "externalTrafficPolicy=Local" dalle macchine virtuali con un indirizzo IP in clusterCIDR che non appartiene al cluster del servizio AzureKs.

**Intervallo di indirizzi del servizio Kubernetes:** questo parametro è il set di indirizzi IP virtuali che Kubernetes assegna ai [servizi][services] interni del cluster. È possibile usare qualsiasi intervallo di indirizzi privati che soddisfi i requisiti seguenti:

* Non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster
* Non deve sovrapporsi ad altre reti virtuali con cui la rete virtuale del cluster effettua il peering
* Non deve sovrapporsi ad altri IP locali
* Non deve essere compreso negli `169.254.0.0/16` intervalli `172.30.0.0/16` , , `172.31.0.0/16` o `192.0.2.0/24`

Sebbene sia tecnicamente possibile specificare un intervallo di indirizzi del servizio all'interno della stessa rete virtuale del cluster, tale operazione non è consigliata. Se vengono usati intervalli IP che si sovrappongono, si può verificare un comportamento imprevedibile. Per altre informazioni, vedere la sezione [Domande frequenti](#frequently-asked-questions) di questo articolo. Per altre informazioni sui servizi Kubernetes, vedere [Services][services] (Servizi) nella documentazione di Kubernetes.

**Kubernetes DNS service IP address** (Indirizzo IP del servizio DNS Kubernetes): indirizzo IP per il servizio DNS del cluster. Questo indirizzo deve essere compreso nell'*intervallo degli indirizzi del servizio Kubernetes*. Non usare il primo indirizzo IP nell'intervallo di indirizzi, ad esempio .1. Il primo indirizzo nell'intervallo della subnet è usato per l'indirizzo *kubernetes.default.svc.cluster.local*.

**Indirizzo del bridge Docker:** l'indirizzo di rete del bridge Docker rappresenta l'indirizzo di rete del bridge *Docker0* predefinito presente in tutte le installazioni di Docker. Anche se il bridge *Docker0* non viene usato dai cluster del servizio Web del servizio Web di Microsoft Windows o dai pod stessi, è necessario impostare questo indirizzo per continuare a supportare scenari come la compilazione *docker* all'interno del cluster del servizio Web Di. È necessario selezionare un CIDR per l'indirizzo di rete del bridge Docker perché in caso contrario Docker selezionerà automaticamente una subnet che potrebbe essere in conflitto con altri CIDR. È necessario selezionare uno spazio indirizzi che non collide con il resto dei CIDR nelle reti, inclusi il CIDR del servizio del cluster e il pod CIDR.

## <a name="configure-networking---cli"></a>Configurare le impostazioni di rete - interfaccia della riga di comando

Quando si crea un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure, è anche possibile configurare le funzionalità di rete di Azure CNI. Usare i comandi seguenti per creare un nuovo cluster del servizio Azure Kubernetes con funzionalità di rete di Azure CNI abilitate.

Ottenere prima di tutto l'ID risorsa della subnet esistente in cui verrà aggiunto il cluster servizio Azure Kubernetes:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Usare il comando [az servizio Azure Kubernetes create][az-aks-create] con l'argomento `--network-plugin azure` per creare un cluster con funzionalità di rete avanzate. Aggiornare il valore `--vnet-subnet-id` con l'ID della subnet raccolto nel passaggio precedente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Configurare le impostazioni di rete - portale

Lo screenshot seguente dal portale di Azure illustra un esempio di configurazione di queste impostazioni durante la creazione del cluster servizio Azure Kubernetes:

! [Configurazione di rete avanzata nel portale di Azure] [portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Allocazione dinamica di indirizzi IP e supporto avanzato delle subnet (anteprima)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Questa funzionalità di anteprima è attualmente disponibile nelle aree seguenti:
>
> * Stati Uniti centro-occidentali

Uno svantaggio con il CNI tradizionale è l'esaurimento degli indirizzi IP dei pod man mano che il cluster del servizio Web del servizio Web aumenta, con conseguente necessità di ricompilare l'intero cluster in una subnet più grande. La nuova funzionalità di allocazione IP dinamica in Azure CNI risolve questo problema allocando gli INDIRIZZI IP del pod da una subnet separata dalla subnet che ospita il cluster del servizio Web Dick.  I vantaggi offerti sono i seguenti:

* **Migliore utilizzo IP:** gli indirizzi IP vengono allocati dinamicamente ai pod del cluster dalla subnet del pod. Ciò comporta un migliore utilizzo degli indirizzi IP nel cluster rispetto alla soluzione CNI tradizionale, che esegue l'allocazione statica degli indirizzi IP per ogni nodo.  

* **Scalabile e flessibile:** le subnet di nodi e pod possono essere ridimensionate in modo indipendente. Una singola subnet di pod può essere condivisa tra più pool di nodi di un cluster o tra più cluster del servizio Web Disattesi distribuiti nella stessa rete virtuale. È anche possibile configurare una subnet di pod separata per un pool di nodi.  

* **Prestazioni elevate:** poiché ai pod vengono assegnati IP di rete virtuale, hanno connettività diretta ad altri pod e risorse del cluster nella rete virtuale. La soluzione supporta cluster di dimensioni molto grandi senza alcuna riduzione delle prestazioni.

* **Criteri di rete virtuale** separati per i pod: poiché i pod hanno una subnet separata, è possibile configurare criteri di rete virtuale separati per essi diversi dai criteri dei nodi. Ciò consente molti scenari utili, ad esempio consentire la connettività Internet solo per i pod e non per i nodi, correggere l'INDIRIZZO IP di origine per il pod in un pool di nodi usando un NAT di rete virtuale e usare gruppi di sicurezza di rete per filtrare il traffico tra i pool di nodi.  

* **Criteri di rete di Kubernetes:** i criteri di rete di Azure e Calico funzionano con questa nuova soluzione.  

### <a name="install-the-aks-preview-azure-cli"></a>Installare l'interfaccia della `aks-preview` riga di comando di Azure

Sarà necessaria l'estensione dell'interfaccia della riga di comando di Azure *aks-preview.* Installare *l'estensione dell'interfaccia della* riga di comando di Azure aks-preview usando il [comando az extension add.][az-extension-add] In caso di installazione di eventuali aggiornamenti disponibili, usare [il comando az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Registrare la `PodSubnetPreview` funzionalità di anteprima

Per usare la funzionalità, è necessario abilitare anche il `PodSubnetPreview` flag di funzionalità nella sottoscrizione.

Registrare `PodSubnetPreview` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il [comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Al termine, aggiornare la registrazione del provider *di risorse Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Prerequisiti aggiuntivi

I prerequisiti già elencati per Azure CNI sono ancora applicabili, ma esistono alcune limitazioni aggiuntive:

* Sono supportati solo cluster di nodi Linux e pool di nodi.
* I cluster del motore del servizio Web Diy e del servizio Web del servizio Web non sono supportati.

### <a name="planning-ip-addressing"></a>Pianificazione dell'indirizzamento IP

Quando si usa questa funzionalità, la pianificazione è molto più semplice. Poiché i nodi e i pod vengono ridimensionati in modo indipendente, anche i relativi spazi indirizzi possono essere pianificati separatamente. Poiché le subnet di pod possono essere configurate in base alla granularità di un pool di nodi, i clienti possono sempre aggiungere una nuova subnet quando aggiungono un pool di nodi. Anche i pod di sistema in un cluster/pool di nodi ricevono gli indirizzi IP dalla subnet del pod, quindi questo comportamento deve essere in considerazione.

La pianificazione degli ip per i servizi K8S e il bridge Docker rimane invariata.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Numero massimo di pod per nodo in un cluster con allocazione dinamica di indirizzi IP e supporto di subnet avanzato

I valori dei pod per nodo quando si usano Azure CNI con l'allocazione dinamica degli IP sono cambiati leggermente rispetto al comportamento CNI tradizionale:

|Cni|Metodo di distribuzione|Predefinito|Configurabile in fase di distribuzione|
|--|--| :--: |--|
|Modello Azure CNI|Interfaccia della riga di comando di Azure|30|Sì (fino a 250)|
|Azure CNI con allocazione dinamica di IP|Interfaccia della riga di comando di Azure|250|Sì (fino a 250)|

Tutte le altre indicazioni relative alla configurazione del numero massimo di nodi per pod rimangono invariate.

### <a name="additional-deployment-parameters"></a>Parametri di distribuzione aggiuntivi

I parametri di distribuzione descritti in precedenza sono tutti ancora validi, con un'eccezione:

* Il **parametro subnet** fa ora riferimento alla subnet correlata ai nodi del cluster.
* Viene usata una **subnet pod** di parametri aggiuntiva per specificare la subnet i cui indirizzi IP verranno allocati dinamicamente ai pod.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Configurare la rete - Interfaccia della riga di comando con allocazione dinamica di indirizzi IP e supporto delle subnet avanzato

L'uso dell'allocazione dinamica degli indirizzi IP e del supporto avanzato delle subnet nel cluster è simile al metodo predefinito per la configurazione di un cluster Azure CNI. L'esempio seguente illustra la creazione di una nuova rete virtuale con una subnet per i nodi e una subnet per i pod e la creazione di un cluster che usa Azure CNI con allocazione dinamica di indirizzi IP e supporto di subnet avanzato. Assicurarsi di sostituire variabili come `$subscription` con valori personalizzati:

Creare prima di tutto la rete virtuale con due subnet:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Creare quindi il cluster, facendo riferimento alla subnet del nodo usando `--vnet-subnet-id` e alla subnet del pod usando `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Aggiunta del pool di nodi

Quando si aggiunge un pool di nodi, fare riferimento alla subnet del nodo `--vnet-subnet-id` usando e alla subnet del pod usando `--pod-subnet-id` . Nell'esempio seguente vengono create due nuove subnet a cui viene fatto riferimento nella creazione di un nuovo pool di nodi:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti si applicano alla configurazione delle funzionalità di rete di **Azure CNI**.

* *È possibile distribuire le VM nella subnet del cluster?*

  Sì.

* *Quale INDIRIZZO IP di origine vengono visualizzati nei sistemi esterni per il traffico che ha origine in un pod Azure CNI abilitato?*

  I sistemi nella stessa rete virtuale del cluster del servizio AKS vedono l'INDIRIZZO IP del pod come indirizzo di origine per qualsiasi traffico proveniente dal pod. I sistemi all'esterno della rete virtuale del cluster del servizio AKS vedono l'INDIRIZZO IP del nodo come indirizzo di origine per qualsiasi traffico proveniente dal pod.

* *È possibile configurare i criteri di rete per pod?*

  Sì, i criteri di rete di Kubernetes sono disponibili nel servizio Kubernetes. Per iniziare, vedere [Proteggere il traffico tra pod usando i criteri di rete nel servizio AppKs.][network-policy]

* *Il numero massimo di pod distribuibili in un nodo è configurabile?*

  Sì, quando si distribuisce un cluster con l'interfaccia della riga di comando di Azure o un modello di Gestione risorse. Consultare [Numero massimo di pod per nodo](#maximum-pods-per-node).

  Non è possibile modificare il numero massimo di pod per ogni nodo in un cluster esistente.

* *Ricerca per categorie configurare proprietà aggiuntive per la subnet creata durante la creazione del cluster del servizio AKS? Ad esempio, gli endpoint di servizio.*

  L'elenco completo delle proprietà per la rete virtuale e le subnet create durante la creazione del cluster servizio Azure Kubernetes può essere configurato nella pagina di configurazione della rete virtuale standard nel portale di Azure.

* *È possibile usare una subnet diversa all'interno della rete virtuale del cluster per l'* **intervallo di indirizzi del servizio Kubernetes**?

  Non è consigliabile, ma questa configurazione è possibile. L'intervallo di indirizzi del servizio è un set di indirizzi IP virtuali che Kubernetes assegna ai servizi interni nel cluster. La rete di Azure non ha visibilità sull'intervallo di indirizzi IP dei servizi del cluster Kubernetes. A causa di tale mancanza di visibilità, è possibile creare in un secondo momento nella rete virtuale del cluster una nuova subnet sovrapposta all'intervallo di indirizzi del servizio. Se si verifica una sovrapposizione di questo tipo, Kubernetes può assegnare a un servizio un indirizzo IP già usato da un'altra risorsa nella subnet, causando un comportamento imprevedibile o errori. Assicurandosi di usare un intervallo di indirizzi esterno alla rete virtuale del cluster, è possibile evitare il rischio di sovrapposizioni.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Domande frequenti sul supporto dell'allocazione dinamica di indirizzi IP e subnet avanzate

Le domande e le risposte seguenti si applicano alla configurazione Azure CNI di rete quando si usa l'allocazione dinamica degli indirizzi IP e il supporto **avanzato della subnet**.

* *È possibile assegnare più subnet di pod a un cluster/pool di nodi?*

  È possibile assegnare una sola subnet a un cluster o a un pool di nodi. Tuttavia, più cluster o pool di nodi possono condividere una singola subnet.

* *È possibile assegnare subnet pod da una rete virtuale diversa?*

  La subnet del pod deve essere della stessa rete virtuale del cluster.  

* *Alcuni pool di nodi in un cluster possono usare il CNI tradizionale mentre altri possono usare il nuovo CNI?*

  L'intero cluster deve usare un solo tipo di CNI.

## <a name="aks-engine"></a>Azure Kubernetes Engine

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] è un progetto open source che genera modelli di Azure Resource Manager che è possibile usare per la distribuzione di cluster Kubernetes in Azure.

I cluster Kubernetes creati con Azure Kubernetes Engine supportano entrambi i plug-in [kubenet][kubenet] e [Azure CNI][cni-networking]. Di conseguenza, entrambi gli scenari di rete sono supportati dal motore del servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete in servizio Azure Kubernetes, vedere gli articoli seguenti:

* [Usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes](static-ip.md)
* [Usare un servizio di bilanciamento del carico interno con il servizio Azure Container](internal-lb.md)

* [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
* [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
* [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
* [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
* [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
