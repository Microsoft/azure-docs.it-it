---
title: Procedure consigliate per l'operatore - Funzionalità di base dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per l'operatore del cluster per l'uso delle funzionalità di base dell'utilità di pianificazione, come quote di risorse e budget di interruzione dei pod, nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84704744"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Procedure consigliate per le funzionalità di base dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. L'utilità di pianificazione di Kubernetes offre funzionalità che consentono di controllare la distribuzione delle risorse di calcolo o limitare l'impatto degli eventi di manutenzione.

Questo articolo sulle procedure consigliate è incentrato sulle funzionalità di pianificazione di base di Kubernetes per gli operatori del cluster. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Usare le quote di risorse per fornire una quantità fissa di risorse ai team o ai carichi di lavoro
> * Limitare l'impatto della manutenzione pianificata mediante i budget di interruzione dei pod
> * Verificare le richieste di risorse e i limiti dei pod mancanti usando lo strumento `kube-advisor`

## <a name="enforce-resource-quotas"></a>Applicare le quote di risorse

**Indicazioni sulle procedure consigliate**. Pianificare e applicare le quote di risorse a livello di spazio dei nomi. Se i pod non definiscono le richieste di risorse e i limiti, rifiutare la distribuzione. Monitorare l'utilizzo delle risorse e modificare le quote in base alle esigenze.

Le richieste di risorse e i limiti vengono inseriti nella specifica del pod. Questi limiti vengono usati dall'utilità di pianificazione di Kubernetes in fase di distribuzione per trovare un nodo disponibile nel cluster. I limiti e le richieste funzionano a livello di singolo pod. Per altre informazioni su come definire questi valori, vedere [Definire le richieste di risorse e i limiti del pod][resource-limits]

Per riservare e limitare le risorse in un progetto o in un team di sviluppo, è consigliabile usare le *quote di risorse*. Queste quote sono definite in uno spazio dei nomi e possono essere usate per impostare le quote in base agli elementi seguenti:

* **Risorse di calcolo**, come CPU e memoria o le GPU.
* **Risorse di archiviazione**, che includono il numero totale di volumi o la quantità di spazio su disco per una classe di archiviazione specificata.
* **Conteggio oggetti**, come il numero massimo di segreti, servizi o processi che è possibile creare.

Kubernetes non esegue l'overcommit delle risorse. Una volta che il totale cumulativo delle richieste di risorse o dei limiti supera la quota assegnata, nessun'altra distribuzione successiva ha esito positivo.

Quando si definiscono le quote di risorse, tutti i pod creati nello spazio dei nomi devono fornire limiti o richieste nelle relative specifiche dei pod. Se questi valori non vengono forniti, è possibile rifiutare la distribuzione. In alternativa, è possibile [configurare richieste e limiti predefiniti per uno spazio dei nomi][configure-default-quotas].

L'esempio di manifesto YAML seguente denominato *dev-app-team-quotas.yaml* imposta un limite rigido di un totale di *10* CPU, *20Gi* di memoria e *10* pod:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Questa quota di risorse può essere applicata specificando lo spazio dei nomi, ad esempio *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Contattare gli sviluppatori e i proprietari delle applicazioni per comprenderne le esigenze e applicare le quote di risorse appropriate.

Per altre informazioni sugli oggetti risorsa disponibili, gli ambiti e le priorità, vedere [Resource quotas in Kubernetes][k8s-resource-quotas] (Quote di risorse in Kubernetes).

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Pianificare la disponibilità tramite i budget di interruzione dei pod

**Indicazioni sulle procedure consigliate**. Per mantenere la disponibilità delle applicazioni, definire i budget di interruzione dei pod in modo da garantire la disponibilità di un numero minimo di pod nel cluster.

Esistono due eventi che comportano interruzioni che causano la rimozione dei pod:

* Le *interruzioni involontarie* sono eventi che esulano dal controllo dell'operatore del cluster o del proprietario dell'applicazione.
  * Queste interruzioni involontarie includono un errore hardware nel computer fisico, un errore grave del kernel o l'eliminazione di una macchina virtuale del nodo
* Le *interruzioni volontarie* sono eventi richiesti dall'operatore del cluster o dal proprietario dell'applicazione.
  * Queste interruzioni volontarie includono gli aggiornamenti del cluster, un modello di distribuzione aggiornato o l'eliminazione accidentale di un pod.

L'impatto delle interruzioni involontarie può essere attenuato usando più repliche dei pod in una distribuzione. L'esecuzione di più nodi nel cluster servizio Azure Kubernetes risulta utile anche nel caso di queste interruzioni involontarie. Per le interruzioni volontarie, Kubernetes offre *budget di interruzione dei pod* che consentono all'operatore del cluster di definire un conteggio risorse minimo disponibile o massimo non disponibile. Questi budget di interruzione dei pod consentono di pianificare la modalità di risposta delle distribuzioni o dei set di repliche quando si verifica un evento di interruzione volontaria.

Se è necessario aggiornare un cluster o un modello di distribuzione, l'utilità di pianificazione di Kubernetes verifica che vengano pianificati ulteriori pod su altri nodi prima che gli eventi di interruzione volontaria possano continuare. L'utilità di pianificazione attende prima che un nodo sia riavviato finché il numero definito di pod non viene pianificato correttamente su altri nodi nel cluster.

Verrà ora preso in esame un set di repliche con cinque pod che eseguono NGINX. Ai pod del set di repliche viene assegnata l'etichetta `app: nginx-frontend` . Durante un evento di interruzione volontaria, ad esempio un aggiornamento del cluster, ci si vuole assicurare che almeno tre pod continuino a essere eseguiti. Il manifesto YAML seguente per un oggetto *PodDisruptionBudget* definisce questi requisiti:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

È anche possibile definire una percentuale, ad esempio *60%*, che consente di compensare automaticamente il ridimensionamento del numero di pod nel set di repliche.

È possibile definire un numero massimo di istanze non disponibili in un set di repliche. Anche in questo caso, può essere definita una percentuale per il numero massimo di pod non disponibili. Il manifesto YAML seguente per il budget di interruzione dei pod definisce che non più di due pod nel set di repliche possono essere non disponibili:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Dopo che il budget di interruzione dei pod è stato definito, viene creato nel cluster servizio Azure Kubernetes come qualsiasi altro oggetto di Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Contattare gli sviluppatori e i proprietari delle applicazioni per comprenderne le esigenze e applicare i budget di interruzione dei pod appropriati.

Per altre informazioni sull'uso dei budget di interruzione dei pod, vedere [Specify a disruption budget for your application][k8s-pdbs] (Specificare un budget di interruzione per l'applicazione).

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verificare regolarmente la presenza di problemi del cluster con kube-advisor

**Indicazioni sulle procedure consigliate** : eseguire regolarmente la versione più recente dello `kube-advisor` strumento open source per rilevare i problemi del cluster. Se si applicano le quote di risorse in un cluster servizio Azure Kubernetes esistente, eseguire per prima cosa `kube-advisor` per trovare i pod che non hanno richieste di risorse e limiti definiti.

Lo strumento [Kube-Advisor][kube-advisor] è un progetto open source AKS associato che analizza un cluster Kubernetes e segnala i problemi riscontrati. Un controllo utile consiste nell'identificare i pod che non hanno richieste di risorse e limiti applicati.

Con lo strumento kube-advisor è possibile creare report su limiti e richieste di risorse mancanti nei file PodSpec per le applicazioni Windows e le applicazioni Linux, ma è necessario pianificare l'esecuzione di tale strumento in un pod Linux. Per pianificare l'esecuzione di un pod in un pool di nodi con un sistema operativo specifico, è possibile usare un [selettore di nodo][k8s-node-selector] nella configurazione del pod.

In un cluster servizio Azure Kubernetes che ospita più team di sviluppo e applicazioni può essere difficile tenere traccia dei pod senza questi limiti e richieste di risorse impostati. Come procedura consigliata, eseguire regolarmente `kube-advisor` nei cluster servizio Azure Kubernetes, soprattutto se non si assegnano quote di risorse agli spazi dei nomi.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato le funzionalità di base dell'utilità di pianificazione di Kubernetes. Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-cluster-isolation]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticazione e autorizzazione][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
