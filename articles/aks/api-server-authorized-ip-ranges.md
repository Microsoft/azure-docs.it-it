---
title: Intervalli IP autorizzati del server API in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ca6e1c06b3ad90ef12c9bf375bae50d46c5f7c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890638"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteggere l'accesso al server API usando gli intervalli di indirizzi IP autorizzati in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve le richieste per eseguire le azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP autorizzati del server API per limitare gli indirizzi IP e CIDRs che possono accedere al piano di controllo.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo illustra come creare un cluster AKS usando l'interfaccia della riga di comando di Azure.

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.76 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="limitations"></a>Limitazioni

La funzionalità degli intervalli IP autorizzati del server API presenta le limitazioni seguenti:
- Nei cluster creati dopo che gli intervalli di indirizzi IP autorizzati del server API sono stati spostati all'esterno dell'anteprima nel 2019 ottobre, gli intervalli di indirizzi IP autorizzati del server API sono supportati solo nel servizio di bilanciamento del carico dello SKU *standard* . I cluster esistenti con lo SKU di *base* Load Balancer e gli intervalli di indirizzi IP autorizzati del server API configurati continueranno a funzionare così come sono, ma non è possibile eseguirne la migrazione a un servizio di bilanciamento del carico *standard* . I cluster esistenti continueranno a funzionare anche se la versione o il piano di controllo di Kubernetes viene aggiornato. Gli intervalli di indirizzi IP autorizzati del server API non sono supportati per i cluster privati.
- Questa funzionalità non è compatibile con i cluster che usano la [funzionalità di anteprima dei pool di nodi per nodo IP pubblico per nodo](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. AKS fornisce un piano di controllo cluster a tenant singolo con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è necessario controllare l'accesso tramite il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) o il controllo degli accessi in base al ruolo di Azure

Per proteggere l'accesso al server API o al piano di controllo AKS accessibile pubblicamente, è possibile abilitare e usare gli intervalli di indirizzi IP autorizzati. Questi intervalli IP autorizzati consentono solo gli intervalli di indirizzi IP definiti per la comunicazione con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati è bloccata. Continuare a usare Kubernetes RBAC o controllo degli accessi in base al ruolo di Azure per autorizzare gli utenti e le azioni richieste.

Per altre informazioni sul server API e altri componenti cluster, vedere [concetti di base di Kubernetes per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Creare un cluster AKS con gli intervalli di indirizzi IP autorizzati del server API abilitati

Creare un cluster usando [AZ AKS create][az-aks-create] e specificare il *`--api-server-authorized-ip-ranges`* parametro per fornire un elenco di intervalli di indirizzi IP autorizzati. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi usati dalle reti locali o dagli indirizzi IP pubblici. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP compreso nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

> [!IMPORTANT]
> Per impostazione predefinita, il cluster usa il servizio di [bilanciamento del carico SKU standard][standard-sku-lb] che è possibile usare per configurare il gateway in uscita. Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, l'indirizzo IP pubblico per il cluster è consentito anche per impostazione predefinita, oltre agli intervalli specificati. Se si specifica *""* o nessun valore per *`--api-server-authorized-ip-ranges`* , gli intervalli di indirizzi IP autorizzati del server API verranno disabilitati. Si noti che se si usa PowerShell, usare *`--api-server-authorized-ip-ranges=""`* (con segno di uguale) per evitare eventuali problemi di analisi.

L'esempio seguente crea un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup* con gli intervalli di indirizzi IP autorizzati del server API abilitati. Gli intervalli di indirizzi IP consentiti sono *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> È necessario aggiungere questi intervalli a un elenco Consenti:
> - Indirizzo IP pubblico del firewall
> - Qualsiasi intervallo che rappresenta le reti da cui verrà amministrato il cluster
> - Se si usa Azure Dev Spaces nel cluster AKS, è necessario consentire [intervalli aggiuntivi in base all'area geografica][dev-spaces-ranges].
>
> Il limite massimo per il numero di intervalli IP che è possibile specificare è 200.
>
> Le regole possono richiedere fino a 2min per la propagazione. Per eseguire il test della connessione, attendere fino a quel momento.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Specificare gli IP in uscita per il servizio di bilanciamento del carico SKU standard

Quando si crea un cluster AKS, se si specificano gli indirizzi IP in uscita o i prefissi per il cluster, sono consentiti anche questi indirizzi o prefissi. Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Nell'esempio precedente, tutti gli indirizzi IP specificati nel parametro *`--load-balancer-outbound-ip-prefixes`* sono consentiti insieme agli indirizzi IP nel *`--api-server-authorized-ip-ranges`* parametro.

È invece possibile specificare il *`--load-balancer-outbound-ip-prefixes`* parametro per consentire i prefissi IP del servizio di bilanciamento del carico in uscita.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Consenti solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard

Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, l'indirizzo IP pubblico in uscita per il servizio di bilanciamento del carico dello SKU standard per il cluster è consentito anche per impostazione predefinita, oltre agli intervalli specificati. Per consentire solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard, usare *0.0.0.0/32* quando si specifica il *`--api-server-authorized-ip-ranges`* parametro.

Nell'esempio seguente è consentito solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard ed è possibile accedere al server API solo dai nodi all'interno del cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aggiornare gli intervalli IP autorizzati del server API del cluster

Per aggiornare gli intervalli di indirizzi IP autorizzati del server API in un cluster esistente, usare il comando [AZ AKS Update][az-aks-update] e usare i *`--api-server-authorized-ip-ranges`* parametri,--Load-Balancer-Outbound-IP-prefissis *, *`--load-balancer-outbound-ips`* o--Load-Balancer-Outbound-IP-prefissis* .

L'esempio seguente aggiorna gli intervalli IP autorizzati del server API nel cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. L'intervallo di indirizzi IP da autorizzare è *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

È anche possibile usare *0.0.0.0/32* quando si specifica il *`--api-server-authorized-ip-ranges`* parametro per consentire solo l'indirizzo IP pubblico del servizio di bilanciamento del carico dello SKU standard.

## <a name="disable-authorized-ip-ranges"></a>Disabilitare gli intervalli IP autorizzati

Per disabilitare gli intervalli IP autorizzati, usare [AZ AKS Update][az-aks-update] e specificare un intervallo vuoto per disabilitare gli intervalli IP autorizzati del server API. Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Trova intervalli IP autorizzati esistenti

Per trovare gli intervalli IP autorizzati, usare [AZ AKS Show][az-aks-show] e specificare il nome del cluster e il gruppo di risorse. Ad esempio:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Aggiornare, disabilitare e trovare gli intervalli IP autorizzati utilizzando portale di Azure

Le operazioni sopra descritte per l'aggiunta, l'aggiornamento, la ricerca e la disabilitazione degli intervalli di indirizzi IP autorizzati possono essere eseguite anche nell'portale di Azure. Per accedere a, passare a **rete** in **Impostazioni** nel pannello menu della risorsa cluster.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="In un browser Mostra le impostazioni di rete della risorsa cluster portale di Azure pagina. Le opzioni ' Imposta intervallo IP specificato ' è intervalli IP specificati ' sono evidenziate.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Come trovare l'indirizzo IP da includere in `--api-server-authorized-ip-ranges` ?

È necessario aggiungere i computer di sviluppo, gli strumenti o gli indirizzi IP di automazione all'elenco di cluster AKS degli intervalli IP approvati per accedere al server API da questa posizione. 

Un'altra opzione consiste nel configurare un JumpBox con gli strumenti necessari all'interno di una subnet separata nella rete virtuale di Firewall. Si presuppone che l'ambiente disponga di un firewall con la rispettiva rete e che siano stati aggiunti gli IP del firewall agli intervalli autorizzati. Analogamente, se è stato forzato il tunneling dalla subnet AKS alla subnet del firewall, è possibile che anche il JumpBox nella subnet del cluster sia troppo preciso.

Aggiungere un altro indirizzo IP agli intervalli approvati con il comando seguente.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> L'esempio precedente aggiunge gli intervalli di indirizzi IP autorizzati del server API nel cluster. Per disabilitare gli intervalli IP autorizzati, usare AZ AKS Update e specificare un intervallo vuoto "". 

Un'altra opzione consiste nell'usare il comando seguente nei sistemi Windows per ottenere l'indirizzo IPv4 pubblico oppure è possibile seguire la procedura descritta in [trovare l'indirizzo IP](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address).

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

È anche possibile trovare questo indirizzo cercando "Qual è l'indirizzo IP" in un browser Internet.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati abilitati gli intervalli di indirizzi IP autorizzati del server API. Questo approccio è una parte del modo in cui è possibile eseguire un cluster AKS sicuro.

Per altre informazioni, vedere [concetti relativi alla sicurezza per le applicazioni e i cluster in AKS][concepts-security] e [procedure consigliate per la sicurezza e gli aggiornamenti del cluster in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
