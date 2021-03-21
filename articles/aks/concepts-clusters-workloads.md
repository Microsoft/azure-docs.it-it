---
title: Concetti - Nozioni di base di Kubernetes per il servizio Azure Kubernetes
description: Informazioni sui componenti di base del cluster e del carico di lavoro di Kubernetes e sulle loro relazioni con le funzionalità del servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 2a1718d906ab5f51ea71be9b304028576c9fffa0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122443"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Concetti di base di Kubernetes per il servizio Azure Kubernetes

Quando lo sviluppo di applicazioni si sposta verso un approccio basato su contenitori, è importante la necessità di orchestrare e gestire le risorse. Kubernetes è la piattaforma leader che offre programmazione affidabile di carichi di lavoro applicativi dotati di tolleranza agli errori. Il servizio Azure Kubernetes è un'offerta Kubernetes gestita che semplifica ulteriormente lo sviluppo e la gestione di applicazioni basate su contenitori.

Questo articolo presenta i componenti principali dell'infrastruttura Kubernetes, ad esempio il *piano di controllo*, i *nodi* e i *pool di nodi*. Sono presentate anche risorse del carico di lavoro come *pod*, *distribuzioni* e *set*, nonché la procedura per raggruppare risorse in *spazi dei nomi*.

## <a name="what-is-kubernetes"></a>Cos'è Kubernetes

Kubernetes è una piattaforma in rapida evoluzione che gestisce applicazioni basate su contenitori e i componenti di rete e archiviazione associati. La piattaforma è incentrata sui carichi di lavoro applicativi e non sui componenti dell'infrastruttura sottostante. Kubernetes offre un approccio dichiarativo alle distribuzioni, supportato da una gamma completa di API per operazioni di gestione.

È possibile compilare ed eseguire applicazioni basate su microservizi moderne e portabili che sfruttano la capacità di Kubernetes di orchestrare e gestire la disponibilità di tali componenti applicativi. Kubernetes supporta applicazioni sia con stato che senza stato mentre l'adozione delle applicazioni basate su microservizi da parte dei team aumenta.

In quanto piattaforma aperta, Kubernetes consente di compilare le applicazioni con il linguaggio di programmazione, il sistema operativo, le librerie e il bus di messaggistica preferiti. Gli strumenti esistenti di integrazione continua e recapito continuo (CI/CD) possono integrarsi con Kubernetes per la pianificazione e la distribuzione delle versioni.

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che riduce la complessità delle attività di distribuzione e delle attività principali di gestione, tra cui il coordinamento degli aggiornamenti. Il piano di controllo AKS è gestito dalla piattaforma Azure e si paga solo per i nodi AKS che eseguono le applicazioni. AKS è basato sul motore del servizio Kubernetes di Azure Open Source ([AKS-Engine][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Architettura del cluster Kubernetes

Un cluster Kubernetes è suddiviso in due componenti:

- Il *piano di controllo* fornisce i servizi Kubernetes principali e l'orchestrazione dei carichi di lavoro dell'applicazione.
- *Nodi* che eseguono i carichi di lavoro dell'applicazione.

![Componenti del piano e del nodo di controllo Kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Piano di controllo

Quando si crea un cluster AKS, viene creato e configurato automaticamente un piano di controllo. Questo piano di controllo viene fornito come una risorsa di Azure gestita astratta dall'utente. Il piano di controllo non prevede alcun costo, bensì solo i nodi che fanno parte del cluster AKS. Il piano di controllo e le relative risorse si trovano solo nell'area in cui è stato creato il cluster.

Il piano di controllo include i componenti Kubernetes principali seguenti:

- *kube-apiserver*: il server API indica il modo in cui le API Kubernetes sottostanti sono esposte. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes.
- *etcd*: per mantenere lo stato della configurazione e del cluster Kubernetes, l'*etcd* con disponibilità elevata è un archivio di valori chiave all'interno di Kubernetes.
- *kube-scheduler*: quando si creano o si ridimensionano applicazioni, l'Utilità di pianificazione determina quali nodi possono eseguire il carico di lavoro e li avvia.
- *kube-controller-manager*: lo strumento di gestione del controller supervisiona molti controller più piccoli che eseguono azioni, ad esempio la replica di pod e la gestione delle operazioni dei nodi.

AKS fornisce un piano di controllo a tenant singolo con un server API dedicato, un'utilità di pianificazione e così via. Si definiscono il numero e le dimensioni dei nodi e la piattaforma Azure configura la comunicazione protetta tra il piano di controllo e i nodi. L'interazione con il piano di controllo si verifica tramite le API Kubernetes, ad esempio `kubectl` o il dashboard di Kubernetes.

Questo piano di controllo gestito significa che non è necessario configurare componenti come un archivio *ETCD* a disponibilità elevata, ma significa anche che non è possibile accedere direttamente al piano di controllo. Gli aggiornamenti a Kubernetes vengono orchestrati tramite l'interfaccia della riga di comando di Azure o portale di Azure, che aggiorna il piano di controllo e quindi i nodi. Per risolvere i problemi possibili, è possibile esaminare i log del piano di controllo tramite i log di monitoraggio di Azure.

Se è necessario configurare il piano di controllo in un modo particolare o se è necessario accedervi direttamente, è possibile distribuire il proprio cluster Kubernetes usando [AKS-Engine][aks-engine].

Per le procedure consigliate associate, vedere procedure consigliate [per la sicurezza e gli aggiornamenti del cluster in AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nodi e pool di nodi

Per eseguire le applicazioni e i servizi di supporto, è necessario un *nodo* Kubernetes. Un cluster servizio Azure Kubernetes ha uno o più nodi, ovvero una macchina virtuale (VM) di Azure che esegue i componenti nodo e il runtime del contenitore di Kubernetes:

- `kubelet`È l'agente Kubernetes che elabora le richieste di orchestrazione dal piano di controllo e la pianificazione dell'esecuzione dei contenitori richiesti.
- La rete virtuale è gestita dal *kube-proxy* in ogni nodo. Il proxy instrada il traffico di rete e gestisce gli indirizzi IP per i servizi e i pod.
- Il *runtime del contenitore* è il componente che consente alle applicazioni in contenitori di eseguire e interagire con risorse aggiuntive, ad esempio la rete virtuale e la risorsa di archiviazione. I cluster AKS che usano i pool di nodi Kubernetes versione 1,19 e sono più usati `containerd` come runtime del contenitore. I cluster AKS che usano Kubernetes prima della versione 1.19 per i pool di nodi usano [Moby](https://mobyproject.org/) (upstream Docker) come runtime del contenitore.

![Macchina virtuale di Azure e risorse di supporto per un nodo di Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

La dimensione della macchina virtuale di Azure per i nodi definisce il numero di CPU, la quantità di memoria e la dimensione e tipo della risorsa di archiviazione disponibile, ad esempio unità SSD a prestazioni elevate o HDD normale. Se si prevede di aver bisogno di applicazioni che richiedono grandi quantità di CPU e memoria o archiviazione a prestazioni elevate, pianificare di conseguenza la dimensione dei nodi. È anche possibile scalare il numero di nodi nel cluster AKS per soddisfare la domanda.

In AKS l'immagine della macchina virtuale per i nodi del cluster è attualmente basata su Ubuntu Linux o Windows Server 2019. Quando si crea un cluster AKS o si aumenta il numero di nodi, la piattaforma Azure crea il numero richiesto di macchine virtuali e le Configura. Non è necessario eseguire alcuna configurazione manuale. I nodi agente vengono fatturati come macchine virtuali standard, quindi tutti gli sconti sulle dimensioni della macchina virtuale in uso (incluse le [prenotazioni di Azure][reservation-discounts]) vengono applicati automaticamente.

Se è necessario usare un sistema operativo host diverso, un altro runtime del contenitore o includere pacchetti personalizzati, è possibile distribuire il proprio cluster Kubernetes usando [servizio Azure Kubernetes-engine][aks-engine]. `aks-engine` upstream rilascia funzionalità e offre opzioni di configurazione prima che siano supportate ufficialmente nei cluster del servizio Azure Kubernetes. Se ad esempio si vuole usare un runtime del contenitore diverso da `containerd` o Moby, è possibile usare `aks-engine` per configurare e distribuire un cluster Kubernetes che soddisfi le esigenze correnti.

### <a name="resource-reservations"></a>Prenotazioni di risorse

Le risorse del nodo vengono usate da AKS per rendere la funzione del nodo come parte del cluster. Questo utilizzo può creare una discrepanza tra le risorse totali del nodo e le risorse allocabili quando viene usato in AKS. Queste informazioni sono importanti da tenere presente quando si impostano le richieste e i limiti per i pod distribuiti dall'utente.

Per trovare le risorse allocabili di un nodo, eseguire:
```kubectl
kubectl describe node [NODE_NAME]
```

Per mantenere le prestazioni e le funzionalità del nodo, le risorse vengono riservate in ogni nodo da AKS. Man mano che un nodo cresce più in risorse, la prenotazione delle risorse cresce a causa di una maggiore quantità di Pod distribuiti dall'utente che necessitano di gestione.

>[!NOTE]
> Usando i componenti aggiuntivi di AKS, ad esempio container Insights (OMS), utilizzerà risorse del nodo aggiuntive.

Sono riservati due tipi di risorse:

- **CPU: la** CPU riservata dipende dal tipo di nodo e dalla configurazione del cluster, che può causare una CPU meno allocabile a causa dell'esecuzione di funzionalità aggiuntive

   | Core CPU nell'host | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube-riservato (millicore)|60|100|140|180|260|420|740|

- **Memoria: la** memoria usata da AKS include la somma di due valori.

   1. Il daemon kubelet viene installato in tutti i nodi dell'agente Kubernetes per gestire la creazione e la terminazione del contenitore. Per impostazione predefinita, in AKS questo daemon presenta la seguente regola di rimozione: *Memory. available<750Mi*, il che significa che un nodo deve sempre avere almeno 750 mi allocabile in ogni momento.  Quando un host è al di sotto di tale soglia di memoria disponibile, il kubelet terminerà uno dei pod in esecuzione per liberare memoria nel computer host e proteggerlo. Questa azione viene attivata quando la memoria disponibile diminuisce oltre la soglia 750Mi.

   2. Il secondo valore è una frequenza regressiva per le prenotazioni di memoria per la corretta funzione del daemon kubelet (in base a Kube-reserved).
      - 25% dei primi 4 GB di memoria
      - 20% dei 4 GB di memoria successivi (fino a 8 GB)
      - 10% dei prossimi 8 GB di memoria (fino a 16 GB)
      - 6% dei 112 GB di memoria successivi (fino a 128 GB)
      - 2% di memoria superiore a 128 GB

Le regole precedenti per l'allocazione di memoria e CPU vengono usate per rendere integri i nodi dell'agente, inclusi alcuni pod di sistema di hosting cruciali per l'integrità del cluster. Queste regole di allocazione fanno anche in modo che il nodo segnali una quantità di memoria e CPU meno allocabile di quanto normalmente se non facesse parte di un cluster Kubernetes. Non è possibile modificare le prenotazioni di risorse sopra indicate.

Se, ad esempio, un nodo dispone di 7 GB, verrà segnalato il 34% della memoria non allocabile, inclusa la soglia di rimozione hardware 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Oltre alle prenotazioni per Kubernetes, il sistema operativo node sottostante riserva anche una quantità di risorse di CPU e memoria per mantenere le funzioni del sistema operativo.

Per le procedure consigliate associate, vedere procedure consigliate [per le funzionalità dell'utilità di pianificazione di base in AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pool di nodi

I nodi della stessa configurazione sono raggruppati in *pool di nodi*. Un cluster Kubernetes contiene uno o più pool di nodi. Il numero e la dimensione iniziale dei nodi sono definiti quando si crea un cluster servizio Azure Kubernetes, infatti viene creato un *pool di nodi predefinito*. Questo pool di nodi predefinito in servizio Azure Kubernetes contiene le macchine virtuali sottostanti che eseguono i nodi dell'agente.

> [!NOTE]
> Per garantire il funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito.

Quando si ridimensiona o si aggiorna un cluster servizio Azure Kubernetes, l'azione viene eseguita sul pool di nodi predefinito. È anche possibile scegliere di ridimensionare o aggiornare un pool di nodi specifico. Per le operazioni di aggiornamento, i contenitori in esecuzione vengono pianificati in altri nodi del pool fino a quando non vengono aggiornati tutti i nodi.

Per altre informazioni su come usare più pool di nodi in AKS, vedere [creare e gestire pool di nodi multipli per un cluster in AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selettori di nodo

In un cluster AKS che contiene più pool di nodi, potrebbe essere necessario indicare all'utilità di pianificazione Kubernetes il pool di nodi da usare per una determinata risorsa. I controller in ingresso, ad esempio, non devono essere eseguiti nei nodi di Windows Server. I selettori di nodo consentono di definire vari parametri, ad esempio il sistema operativo node, per controllare la posizione in cui deve essere pianificato un pod.

Nell'esempio di base seguente viene pianificata un'istanza NGINX in un nodo Linux usando il selettore di nodo *"beta.kubernetes.io/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Per altre informazioni su come controllare dove sono pianificati i pod, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pod

Kubernetes usa i *pod* per eseguire un'istanza dell'applicazione. Un pod rappresenta una singola istanza dell'applicazione. I pod hanno in genere un mapping 1:1 con i contenitori, anche se vi sono scenari avanzati in cui un pod può contenere più contenitori. I pod multi-contenitore sono pianificati insieme nello stesso nodo e consentono ai contenitori di condividere le risorse correlate.

Quando si crea un pod, è possibile definire *richieste di risorse* per richiedere una determinata quantità di risorse di CPU o di memoria. L'Utilità di pianificazione di Kubernetes tenta di pianificare i pod per l'esecuzione in un nodo con le risorse disponibili per soddisfare la richiesta. È anche possibile specificare limiti massimi per le risorse che impediscono a un pod dato di usare troppe risorse di calcolo del nodo sottostante. Una procedura consigliata consiste nell'includere limiti per le risorse per tutti i pod, per consentire all'l'Utilità di pianificazione di Kubernetes di capire quali risorse sono necessarie e consentite.

Per altre informazioni, vedere [Pod di Kubernetes][kubernetes-pods] e [Ciclo di vita dei pod di Kubernetes][kubernetes-pod-lifecycle].

Un pod è una risorsa logica, i carichi di lavoro applicativi sono eseguiti nei contenitori. I pod sono in genere risorse temporanee ed eliminabili e i pod con pianificazione individuale perdono alcune delle funzionalità di disponibilità elevate e ridondanza offerte da Kubernetes. I pod vengono invece distribuiti e gestiti dai *controller* Kubernetes, ad esempio il controller di distribuzione.

## <a name="deployments-and-yaml-manifests"></a>Distribuzioni e manifesti YAML

Una *distribuzione* rappresenta uno o più pod identici gestiti dal controller di distribuzione di Kubernetes. Una distribuzione specifica il numero di *repliche* (pod) da creare e l'Utilità di pianificazione di Kubernetes assicura che, in caso di problemi dei pod o dei nodi, vengano pianificati pod aggiuntivi su nodi integri.

È possibile aggiornare le distribuzioni per modificare la configurazione dei pod, l'immagine del contenitore utilizzata o la risorsa di archiviazione collegata. Il controller di distribuzione svuota e termina un determinato numero di repliche, crea repliche dalla nuova definizione della distribuzione e continua il processo fino a quando non vengono aggiornate tutte le repliche della distribuzione.

La maggior parte delle applicazioni senza stato in servizio Azure Kubernetes dovrebbe usare il modello di distribuzione anziché pianificare singoli pod. Kubernetes può monitorare l'integrità e lo stato delle distribuzioni per garantire che il numero di repliche richiesto si esegua all'interno del cluster. Quando si pianificano solo singoli Pod, i Pod non vengono riavviati se si verifica un problema e non vengono ripianificati in nodi integri se il nodo corrente rileva un problema.

Se un'applicazione richiede la disponibilità costante di un quorum specifico di istanze per consentire la presa di decisioni di gestione, è preferibile che il processo di aggiornamento non comprometta tale capacità. I *budget di interruzione dei pod* possono essere utilizzati per definire il numero di repliche di una distribuzione che possono essere arrestate durante un aggiornamento o un aggiornamento dei nodi. Se, ad esempio, si dispone di *cinque (5)* repliche nella distribuzione, è possibile definire un'alterazione del pod di *4* per consentire l'eliminazione o la ripianificazione di una sola replica alla volta. Come con i limiti delle risorse di pod, una procedura consigliata è definire i budget di interruzione dei pod nelle applicazioni che richiedono la presenza costante di un numero minimo di repliche.

Le distribuzioni vengono in genere create e gestite con `kubectl create` o `kubectl apply`. Per creare una distribuzione si definisce un file manifesto nel formato YAML (YAML Ain't Markup Language). L'esempio seguente crea una distribuzione di base del server Web NGINX. La distribuzione specifica *tre (3)* repliche da creare e richiede che la porta *80* sia aperta nel contenitore. Richieste e limiti relativi alle risorse sono definiti anche per la CPU e la memoria.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

È possibile creare applicazioni più complesse includendo anche servizi come quelli di bilanciamento del carico all'interno del manifesto YAML.

Per altre informazioni, vedere le [distribuzioni Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestione dei pacchetti con Helm

Un approccio comune alla gestione di applicazioni in Kubernetes è l'uso di [Helm][helm]. È possibile creare e usare *grafici* Helm pubblici esistente che contengono una versione pacchetto del codice applicativo e manifesti YAML di Kubernetes per distribuire le risorse. Questi grafici Helm possono essere archiviati in locale e spesso in un repository remoto, ad esempio un [repository per grafici Helm di Registro Azure Container][acr-helm].

Per usare Helm, installare il client Helm nel computer oppure usare il client Helm nella [Azure cloud Shell][azure-cloud-shell]. È possibile cercare o creare grafici Helm con il client e quindi installarli nel cluster Kubernetes. Per altre informazioni, vedere [Install existing Applications with Helm in AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Oggetti StatefulSet e DaemonSet

Il controller di distribuzione usa l'Utilità di pianificazione di Kubernetes per eseguire un determinato numero di repliche su qualsiasi nodo disponibile con le risorse disponibili. Questo approccio di usare le distribuzioni può essere sufficiente per le applicazioni senza stato, ma non per le applicazioni che richiedono una convenzione di denominazione o una risorsa di archiviazione permanente. Per le applicazioni che richiedono una replica per ogni nodo o per nodi selezionati di un cluster, il controller di distribuzione non esamina come le repliche sono distribuite tra i nodi.

Esistono due risorse di Kubernetes che consentono di gestire questi tipi di applicazioni:

- Gli oggetti *StatefulSet* mantengono lo stato delle applicazioni oltre il ciclo di vita di un singolo pod, ad esempio la risorsa di archiviazione.
- Gli oggetti *DaemonSet* assicurano che ci sia un'istanza in esecuzione in ogni nodo in una delle prime fasi del processo di bootstrap di Kubernetes.

### <a name="statefulsets"></a>Oggetti StatefulSet

Molte applicazioni sviluppate attualmente sono senza stato, mentre gli oggetti *StatefulSet* possono essere usati per le applicazioni con stato, ad esempio le applicazioni che includono componenti di database. Un oggetto StatefulSet è simile a una distribuzione per il fatto che vengono creati e gestiti uno o più pod identici. Le repliche in un oggetto StatefulSet seguono un approccio sequenziale normale alla distribuzione, il ridimensionamento, gli aggiornamenti e la chiusura. Con StatefulSet (quando le repliche vengono ripianificate) la convenzione di denominazione, i nomi di rete e l'archiviazione vengono mantenuti.

Si definisce l'applicazione nel formato YAML tramite `kind: StatefulSet` e poi il controller StatefulSet gestisce la distribuzione e la gestione delle repliche necessarie. I dati vengono scritti nella risorsa di archiviazione permanente fornita da Managed Disks di Azure o File di Azure. Con gli oggetti StatefulSet, la risorsa di archiviazione permanente sottostante rimane anche quando viene eliminato l'oggetto StatefulSet.

Per altre informazioni, vedere [Oggetti StatefulSet di Kubernetes][kubernetes-statefulsets].

Le repliche in un oggetto StatefulSet sono pianificate ed eseguire su tutti i nodi disponibili in un cluster servizio Azure Kubernetes. Se è necessario assicurarsi che almeno un pod del set venga eseguito in un nodo, è possibile usare un oggetto DaemonSet.

### <a name="daemonsets"></a>Oggetti DaemonSet

Per esigenze specifiche di raccolta di log o monitoraggio potrebbe essere necessario eseguire un pod specifico su tutti i nodi o su nodi selezionati. Un oggetto *DaemonSet* anche in questo caso viene usato per distribuire uno o più pod identici, ma il controller DaemonSet garantisce che ogni nodo specificato esegua un'istanza del pod.

Il controller DaemonSet può pianificare i pod nei nodi in una delle prime fasi del processo di avvio del cluster, prima che sia avviata l'utilità di pianificazione predefinita di Kubernetes. Questa capacità garantisce che i pod di un oggetto DaemonSet vengono avviati prima che siano pianificati i pod tradizionali in una distribuzione o in un oggetto StatefulSet.

Come gli oggetti StatefulSet, un oggetto DaemonSet viene definito come parte di una definizione YAML usando `kind: DaemonSet`.

Per altre informazioni, vedere [Oggetti DaemonSet di Kubernetes][kubernetes-daemonset].

> [!NOTE]
> Se si usa il [componente aggiuntivo dei nodi virtuali](virtual-nodes-cli.md#enable-virtual-nodes-addon), gli elementi daemonset non creerà i Pod nel nodo virtuale.

## <a name="namespaces"></a>Spazi dei nomi

Le risorse di Kubernetes, ad esempio i pod e le distribuzioni, vengono raggruppate logicamente in uno *spazio dei nomi*. Questi raggruppamenti consentono di dividere un cluster servizio Azure Kubernetes e limitare l'accesso per creare, visualizzare o gestire le risorse in modo logico. Ad esempio, è possibile creare spazi dei nomi per separare gruppi aziendali. Gli utenti possono interagire solo con le risorse all'interno degli spazi dei nomi assegnati.

![Spazi dei nomi di Kubernetes per dividere in modo logico risorse e applicazioni](media/concepts-clusters-workloads/namespaces.png)

Quando si crea un cluster servizio Azure Kubernetes, sono disponibili gli spazi dei nomi seguenti:

- *predefinito*: lo spazio dei nomi dove i pod e le distribuzioni vengono creati per impostazione predefinita se non ne viene specificato un altro. Negli ambienti più piccoli è possibile distribuire le applicazioni direttamente nello spazio dei nomi predefinito senza creare altre suddivisioni logiche. Quando si interagisce con l'API di Kubernetes, ad esempio `kubectl get pods`, viene usato lo spazio dei nomi predefinito se non ne viene specificato un altro.
- *kube system*: lo spazio dei nomi in cui sono presenti le risorse principali, ad esempio le funzionalità di rete come DNS e proxy o il dashboard di Kubernetes. In genere non si distribuiscono le proprie applicazioni in questo spazio dei nomi.
- *kube-public*: questo spazio dei nomi solitamente non viene usato ma può essere usato per le risorse che devono essere visibili nell'intero cluster ed essere visualizzate da tutti gli utenti.

Per altre informazioni, vedere [Spazi dei nomi di Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo tratta alcuni dei componenti principali di Kubernetes descrivendo come si applicano ai cluster servizio Azure Kubernetes. Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Accesso e identità per Kubernetes/servizio Azure Kubernetes][aks-concepts-identity]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
