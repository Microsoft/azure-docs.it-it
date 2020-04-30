---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Trovare le risposte ad alcune domande comuni sul servizio Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: a58c3510d8937b209bf6c73d33237785ecab161d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206610"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>Quali aree di Azure forniscono attualmente AKS?

Per un elenco completo delle aree disponibili, vedere la pagina relativa alla [disponibilità e alle aree AKS][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>servizio Azure Kubernetes supporta la scalabilità automatica dei nodi?

Sì, la possibilità di ridimensionare automaticamente i nodi agente orizzontalmente in AKS è attualmente disponibile in anteprima. Per istruzioni, vedere [ridimensionare automaticamente un cluster per soddisfare le richieste dell'applicazione in AKS][aks-cluster-autoscaler] . La scalabilità automatica AKS si basa su [Kubernetes AutoScaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire servizio Azure Kubernetes nella rete virtuale esistente?

Sì, è possibile distribuire un cluster AKS in una rete virtuale esistente usando la [funzionalità di rete avanzata][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>È possibile limitare gli utenti che possono accedere al server API Kubernetes?

Sì, è possibile limitare l'accesso al server API Kubernetes usando gli [intervalli IP autorizzati del server API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>È possibile rendere accessibile il server API Kubernetes solo all'interno della rete virtuale?

Non in questo momento, ma questo è pianificato. È possibile tenere traccia dello stato di avanzamento nel [repository GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>È possibile avere dimensioni diverse per le macchine virtuali in un singolo cluster?

Sì, è possibile usare dimensioni diverse per le macchine virtuali nel cluster AKS creando [più pool di nodi][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Azure applica automaticamente le patch di sicurezza ai nodi Linux del cluster in base a una pianificazione notturna. Tuttavia, l'utente è responsabile di assicurarsi che questi nodi Linux vengano riavviati in modo obbligatorio. Sono disponibili diverse opzioni per il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Il cluster aggiorna automaticamente i [nodi Cordon e drain][cordon-drain] , quindi riporta un nuovo nodo online con l'immagine Ubuntu più recente e una nuova versione patch o una versione secondaria di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Usando [KURED](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. KURED viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per la presenza di un file che indica che è necessario riavviare il computer. Nel cluster, i riavvii del sistema operativo vengono gestiti dallo stesso [processo di Cordon e svuotamento][cordon-drain] di un aggiornamento del cluster.

Per altre informazioni sull'utilizzo di Kured, vedere [Apply security and kernel updates to nodes in AKS][node-updates-kured] (Applicare aggiornamenti di sicurezza e del kernel ai nodi del servizio Azure Kubernetes).

### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente e vengono applicati gli aggiornamenti più recenti. In base a una pianificazione regolare per il ciclo di rilascio Windows Update e per il processo di convalida, è necessario eseguire un aggiornamento nel cluster e nei pool di nodi di Windows Server nel cluster AKS. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server, quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

AKS si basa su una serie di risorse dell'infrastruttura di Azure, inclusi i set di scalabilità di macchine virtuali, le reti virtuali e i dischi gestiti. In questo modo è possibile sfruttare molte delle funzionalità principali della piattaforma Azure all'interno dell'ambiente Kubernetes gestito fornito da AKS. Ad esempio, la maggior parte dei tipi di macchine virtuali di Azure può essere usata direttamente con AKS e le prenotazioni di Azure possono essere usate per ricevere automaticamente sconti su tali risorse.

Per abilitare questa architettura, ogni distribuzione di AKS si estende su due gruppi di risorse:

1. Si crea il primo gruppo di risorse. Questo gruppo contiene solo la risorsa del servizio Kubernetes. Il provider di risorse AKS crea automaticamente il secondo gruppo di risorse durante la distribuzione. Un esempio del secondo gruppo di risorse è *MC_myResourceGroup_myAKSCluster_eastus*. Per informazioni su come specificare il nome del secondo gruppo di risorse, vedere la sezione successiva.
1. Il secondo gruppo di risorse, noto come *gruppo di risorse nodo*, contiene tutte le risorse dell'infrastruttura associate al cluster. come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Per impostazione predefinita, il nome del gruppo di risorse del nodo è come *MC_myResourceGroup_myAKSCluster_eastus*. AKS Elimina automaticamente la risorsa del nodo ogni volta che il cluster viene eliminato, quindi deve essere usato solo per le risorse che condividono il ciclo di vita del cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>È possibile specificare un nome personalizzato per il gruppo di risorse del nodo AKS?

Sì. Per impostazione predefinita, AKS assegna un nome al gruppo di risorse del nodo *MC_resourcegroupname_clustername_location*, ma è anche possibile specificare un nome personalizzato.

Per specificare il nome del gruppo di risorse, installare la versione dell'estensione dell'interfaccia della riga di comando di Azure [AKS-Preview][aks-preview-cli] *0.3.2* o versioni successive. Quando si crea un cluster AKS usando il comando [AZ AKS create][az-aks-create] , usare il parametro *--node-Resource-Group* e specificare un nome per il gruppo di risorse. Se si [Usa un modello di Azure Resource Manager][aks-rm-template] per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse usando la proprietà *nodeResourceGroup* .

* Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella propria sottoscrizione.
* È possibile specificare un nome di gruppo di risorse personalizzato solo quando si crea il cluster.

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

* Specificare un gruppo di risorse esistente per il gruppo di risorse del nodo.
* Specificare una sottoscrizione diversa per il gruppo di risorse del nodo.
* Modificare il nome del gruppo di risorse del nodo dopo la creazione del cluster.
* Specificare i nomi per le risorse gestite all'interno del gruppo di risorse del nodo.
* Modificare o eliminare tag di risorse gestite all'interno del gruppo di risorse del nodo. (Per altre informazioni, vedere la sezione successiva).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>È possibile modificare tag e altre proprietà delle risorse AKS nel gruppo di risorse del nodo?

Se si modificano o eliminano i tag creati da Azure e altre proprietà delle risorse nel gruppo di risorse del nodo, è possibile ottenere risultati imprevisti, ad esempio la scalabilità e l'aggiornamento degli errori. AKS consente di creare e modificare tag personalizzati. Potrebbe essere necessario creare o modificare tag personalizzati, ad esempio, per assegnare una business unit o un centro di costo. Modificando le risorse nel gruppo di risorse nodo del cluster AKS, si interrompe l'obiettivo del livello di servizio (SLO). Per ulteriori informazioni, vedere la pagina relativa all' [offerta di un contratto di servizio da AKS.](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta servizio Azure Kubernetes? È possibile aggiungere o rimuovere i controller di ammissione?

servizio Azure Kubernetes supporta i seguenti [controller di ammissione][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

Attualmente non è possibile modificare l'elenco dei controller di ammissione in AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>È possibile usare I webhook del controller di ammissione su AKS?

Sì, è possibile usare i webhook del controller di ammissione su AKS. È consigliabile escludere gli spazi dei nomi AKS interni contrassegnati con l' **etichetta del piano di controllo.** Ad esempio, aggiungendo il seguente alla configurazione del webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>I webhook del controller di ammissione influiscano sugli spazi dei nomi Kube-System e Internal AKS?

Per proteggere la stabilità del sistema e impedire che i controller di ammissione personalizzati influiscano sui servizi interni in Kube-System, lo spazio dei nomi AKS ha un **Enforcer di ammissione**, che esclude automaticamente gli spazi dei nomi Kube-System e AKS Internal. Questo servizio garantisce che i controller di ammissione personalizzati non influiscano sui servizi in esecuzione in Kube-System.

Se si ha un caso d'uso critico per la distribuzione di un elemento in Kube-System (scelta non consigliata), che è necessario coprire con il webhook di ammissione personalizzato, è possibile aggiungere l'etichetta o l'annotazione seguente in modo che l'applicazione di ammissione la ignori.

Etichetta: ```"admissions.enforcer/disabled": "true"``` o annotazione:```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in servizio Azure Kubernetes?

AKS attualmente non è integrato in modo nativo con Azure Key Vault. Tuttavia, il [progetto Azure Key Vault FlexVolume per Kubernetes][keyvault-flexvolume] consente l'integrazione diretta da Kubernetes pod a Key Vault segreti.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in servizio Azure Kubernetes?

Sì, i contenitori di Windows Server sono disponibili su AKS. Per eseguire i contenitori di Windows Server in AKS, è possibile creare un pool di nodi che esegue Windows Server come sistema operativo guest. I contenitori di Windows Server possono usare solo Windows Server 2019. Per iniziare, vedere [creare un cluster AKS con un pool di nodi di Windows Server][aks-windows-cli].

Il supporto di Windows Server per il pool di nodi include alcune limitazioni che fanno parte di Windows Server upstream nel progetto Kubernetes. Per ulteriori informazioni su queste limitazioni, vedere la pagina relativa ai [contenitori di Windows Server nelle limitazioni di AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre un contratto di servizio?

In un contratto di servizio (SLA), il provider accetta di rimborsare il costo del servizio del cliente se il livello di servizio pubblicato non è soddisfatto. Poiché AKS è gratuito, non è disponibile alcun costo per il rimborso, quindi AKS non ha un contratto di servizio formale. Tuttavia, AKS cerca di mantenere la disponibilità di almeno il 99,5% per il server API Kubernetes.

È importante riconoscere la distinzione tra la disponibilità del servizio AKS, che fa riferimento al tempo di esecuzione del piano di controllo Kubernetes e alla disponibilità del carico di lavoro specifico in esecuzione nelle macchine virtuali di Azure. Sebbene il piano di controllo non sia disponibile se il piano di controllo non è pronto, i carichi di lavoro del cluster in esecuzione in macchine virtuali di Azure possono comunque funzionare. Le macchine virtuali di Azure sono risorse a pagamento supportate da un contratto di contratto finanziario. Leggere [qui per altre informazioni](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sul contratto di contratto per le VM di Azure e su come aumentare la disponibilità con funzionalità come [zone di disponibilità][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Perché non è possibile impostare maxPods sotto 30?

In AKS è possibile impostare il `maxPods` valore durante la creazione del cluster usando l'interfaccia della riga di comando di Azure e i modelli Azure Resource Manager. Tuttavia, sia Kubenet che Azure CNI richiedono un *valore minimo* (convalidato al momento della creazione):

| Rete | Minima | Massimo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Poiché AKS è un servizio gestito, i componenti aggiuntivi e i pod vengono distribuiti e gestiti come parte del cluster. In passato, gli utenti potevano definire un `maxPods` valore inferiore a quello necessario per l'esecuzione dei Pod gestiti (ad esempio, 30). Il servizio AKS calcola ora il numero minimo di pod usando la formula seguente: ((maxPods o (maxPods * vm_count)) > i pod aggiuntivi gestiti sono minimi.

Gli utenti non possono eseguire `maxPods` l'override della convalida minima.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>È possibile applicare gli sconti per le prenotazioni di Azure ai nodi dell'agente AKS?

I nodi dell'agente AKS vengono fatturati come macchine virtuali standard di Azure, pertanto se sono state acquistate [prenotazioni di Azure][reservation-discounts] per le dimensioni della macchina virtuale in uso nel servizio contenitore di Azure, tali sconti vengono applicati automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>È possibile spostare/migrare il cluster tra i tenant di Azure?

Il `az aks update-credentials` comando può essere usato per spostare un cluster AKS tra i tenant di Azure. Seguire le istruzioni in [scegliere di aggiornare o creare un'entità servizio](https://docs.microsoft.com/azure/aks/update-credentials) e quindi [aggiornare il cluster AKS con le nuove credenziali](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>È possibile spostare/migrare il cluster tra le sottoscrizioni?

Lo spostamento di cluster tra sottoscrizioni non è al momento supportato.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>È possibile spostare i cluster AKS dalla sottoscrizione di Azure corrente a un'altra? 

Lo stato di trasferimento del cluster AKS e delle risorse associate tra le sottoscrizioni di Azure non è supportato.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Perché l'eliminazione del cluster richiede molto tempo? 

La maggior parte dei cluster viene eliminata in seguito alla richiesta dell'utente. in alcuni casi, in particolare quando i clienti portano il proprio gruppo di risorse o l'eliminazione di attività tra RG può richiedere ulteriore tempo o non riuscire. In caso di problemi con le eliminazioni, verificare che non siano presenti blocchi sull'RG, che tutte le risorse esterne all'RG siano dissociate dal RG e così via.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se si dispone di Pod/distribuzioni nello stato ' nodelost ' o ' Unknown ', è ancora possibile aggiornare il cluster?

È possibile, ma AKS sconsiglia questa operazione. Gli aggiornamenti dovrebbero essere eseguiti idealmente quando lo stato del cluster è noto e integro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se si dispone di un cluster con uno o più nodi in uno stato non integro o arrestato, è possibile eseguire un aggiornamento?

No, eliminare o rimuovere tutti i nodi con stato di errore o rimossi dal cluster prima di eseguire l'aggiornamento.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>È stata eseguita un'eliminazione del cluster, ma è stato visualizzato l'errore`[Errno 11001] getaddrinfo failed` 

In genere, ciò è dovuto al fatto che gli utenti che dispongono di uno o più gruppi di sicurezza di rete (gruppi) sono ancora in uso e associati al cluster.  Rimuoverli e ripetere l'operazione di eliminazione.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ho eseguito un aggiornamento, ma ora i pod sono in cicli di arresto anomalo e i probe di conformità hanno esito negativo?

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS Service Principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS Update Credentials](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Il mio cluster funzionava, ma improvvisamente non è in grado di eseguire il provisioning di LoadBalancers, montare PVC e così via? 

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS Service Principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS Update Credentials](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>È possibile usare le API del set di scalabilità di macchine virtuali per la scalabilità manuale?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. Usare le API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>È possibile usare I set di scalabilità di macchine virtuali per eseguire manualmente la scalabilità a 0 nodi?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>È possibile arrestare o deallocare tutte le VM?

Sebbene AKS includa meccanismi di resilienza per resistere a tale configurazione ed eseguire il ripristino da tale configurazione, questa non è una configurazione consigliata.

## <a name="can-i-use-custom-vm-extensions"></a>È possibile usare estensioni di VM personalizzate?

Nessun AKS è un servizio gestito e la manipolazione delle risorse IaaS non è supportata. Per installare i componenti personalizzati e così via. utilizzare le API e i meccanismi di Kubernetes. Ad esempio, utilizzare gli elementi daemonset per installare i componenti necessari.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
