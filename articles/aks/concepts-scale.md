---
title: Concetti - Ridimensionare le applicazioni nel servizio Azure Kubernetes
description: Informazioni sul ridimensionamento nel servizio Azure Kubernetes, tra cui il ridimensionamento automatico orizzontale dei pod, il ridimensionamento automatico del cluster e il connettore di Istanze di Azure Container.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: 1a14615e96d5be4fbc8994073d66677997281131
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499886"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opzioni di ridimensionamento per le applicazioni nel servizio Azure Kubernetes

Quando si eseguono applicazioni nel servizio Azure Kubernetes può risultare necessario aumentare o ridurre la quantità di risorse di calcolo. Se cambia il numero di istanze dell'applicazione necessarie, potrebbe risultare necessario cambiare anche il numero di nodi Kubernetes sottostanti. Potrebbe anche essere necessario effettuare rapidamente il provisioning di un numero elevato di istanze di applicazioni aggiuntive.

Questo articolo introduce i principali concetti utili per gestire il ridimensionamento delle applicazioni nel servizio Azure Kubernetes:

- [Ridimensionamento manuale](#manually-scale-pods-or-nodes)
- [Ridimensionamento automatico orizzontale dei pod](#horizontal-pod-autoscaler)
- [Utilità di scalabilità automatica dei cluster](#cluster-autoscaler)
- [Integrazione di Istanza di Azure Container con il servizio Azure Kubernetes](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ridimensionare manualmente i pod o i nodi

È possibile ridimensionare manualmente le repliche (pod) e i nodi per verificare come risponde l'applicazione a una modifica delle risorse disponibili e dello stato. Il ridimensionamento manuale delle risorse consente anche di definire una quantità specifica di risorse da usare per mantenere un costo fisso, ad esempio il numero di nodi. Per eseguire manualmente la scalabilità, è necessario definire la replica o il numero di nodi. L'API Kubernetes pianifica quindi la creazione di Pod aggiuntivi o lo svuotamento dei nodi in base alla replica o al numero di nodi.

Quando si ridimensionano i nodi, l'API Kubernetes chiama l'API di calcolo di Azure pertinente collegata al tipo di calcolo usato dal cluster. Ad esempio, per i cluster basati su un set di scalabilità di macchine virtuali, la logica per la selezione dei nodi da rimuovere è determinata dall'API dei set di scalabilità di macchine virtuali. Per altre informazioni sulla modalità di selezione dei nodi per la rimozione in una scala ridotta, vedere le [domande frequenti su vmss](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Per informazioni introduttive sul ridimensionamento manuale di pod e nodi, vedere [Ridimensionare le applicazioni nel servizio Azure Kubernetes][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Utilità di scalabilità automatica orizzontale dei pod

Kubernetes usa il ridimensionamento automatico orizzontale dei pod per monitorare la richiesta di risorse e adattare automaticamente il numero di repliche. Per impostazione predefinita, il ridimensionamento automatico orizzontale dei pod controlla l'API Metriche ogni 30 secondi per rilevare eventuali modifiche richieste nel numero di repliche. Quando sono necessarie modifiche, il numero di repliche viene aumentato o ridotto di conseguenza. Il ridimensionamento automatico orizzontale dei pod può essere applicato ai cluster servizio Azure Kubernetes in cui è stato distribuito Metrics Server per Kubernetes 1.8+.

![Ridimensionamento automatico orizzontale dei pod Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Quando si configura il ridimensionamento automatico orizzontale dei pod per una distribuzione specifica, si definisce il numero minimo e massimo di repliche che può essere eseguito. È anche possibile definire le metriche da monitorare e su cui basare qualsiasi decisione per il ridimensionamento, ad esempio l'utilizzo della CPU.

Per informazioni introduttive sul ridimensionamento automatico orizzontale dei pod in servizio Azure Kubernetes, vedere [Scalare automaticamente i pod][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Raffreddamento degli eventi di ridimensionamento

Dato che il ridimensionamento automatico orizzontale dei pod controlla l'API Metriche ogni 30 secondi, è possibile che eventi di ridimensionamento precedenti non siano stati completati prima dell'esecuzione del controllo successivo. Questo comportamento potrebbe causare la modifica del numero di repliche prima che l'evento di scalabilità precedente possa ricevere il carico di lavoro dell'applicazione e la necessità di modificare di conseguenza la risorsa.

Per ridurre al minimo gli eventi Race, viene impostato un valore di ritardo. Questo valore definisce per quanto tempo il ridimensionamento automatico del Pod orizzontale deve attendere un evento di ridimensionamento prima che sia possibile attivare un altro evento di ridimensionamento. Questo comportamento consente di rendere effettivo il nuovo conteggio delle repliche e l'API metrica per riflettere il carico di lavoro distribuito. Non si verificano [ritardi per gli eventi di scalabilità verticale a partire da Kubernetes 1,12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), ma il ritardo sugli eventi di scalabilità verticale viene impostato su 5 minuti.

Attualmente, non è possibile ottimizzare i valori di cooldown dal valore predefinito.

## <a name="cluster-autoscaler"></a>Utilità di scalabilità automatica dei cluster

Per rispondere alle mutevoli richieste di Pod, Kubernetes dispone di un cluster AutoScaler, che regola il numero di nodi in base alle risorse di calcolo richieste nel pool di nodi. Per impostazione predefinita, il servizio di scalabilità automatica del cluster controlla il server API metrica ogni 10 secondi per eventuali modifiche necessarie nel numero di nodi. Se il ridimensionamento automatico del cluster determina che è necessaria una modifica, il numero di nodi nel cluster servizio Azure Kubernetes viene aumentato o ridotto di conseguenza. Il ridimensionamento automatico del cluster funziona con i cluster servizio Azure Kubernetes abilitati per RBAC che eseguono Kubernetes 1.10.x o versione successiva.

![Ridimensionamento automatico del cluster Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Il ridimensionamento automatico del cluster viene generalmente usato insieme al ridimensionamento automatico orizzontale dei pod. In combinazione, il l ridimensionamento automatico orizzontale dei pod aumenta o riduce il numero di pod in base alle richieste dell'applicazione e il ridimensionamento automatico del cluster regola di conseguenza il numero di nodi in base alle necessità per l'esecuzione di tali pod aggiuntivi.

Per informazioni introduttive sul ridimensionamento automatico del cluster nel servizio Azure Kubernetes, vedere [Ridimensionamento automatico del cluster sul servizio Azure Kubernetes][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Eventi di scalabilità orizzontale

Se un nodo non dispone di risorse di calcolo sufficienti per eseguire un pod richiesto, il Pod non può avanzare durante il processo di pianificazione. Il Pod non può essere avviato se non sono disponibili risorse di calcolo aggiuntive nel pool di nodi.

Quando il servizio di scalabilità automatica del cluster rileva i pod che non possono essere pianificati a causa dei vincoli delle risorse del pool di nodi, il numero di nodi all'interno del pool di nodi viene aumentato per fornire le risorse di calcolo aggiuntive. Dopo la distribuzione corretta di tali nodi aggiuntivi e quando sono disponibili per l'uso all'interno del pool di nodi, i pod vengono quindi pianificati per l'esecuzione su tali nodi.

Se l'applicazione deve essere ridimensionata rapidamente, alcuni pod potrebbero rimanere in attesa di pianificazione fino a quando i nodi aggiuntivi distribuiti dal ridimensionamento automatico del cluster non possono accettare i pod pianificati. Per le applicazioni con richieste burst elevate, è possibile gestire il ridimensionamento con nodi virtuali e Istanze di Azure Container.

### <a name="scale-in-events"></a>Ridimensionare gli eventi

Il servizio di scalabilità automatica del cluster monitora anche lo stato di pianificazione dei pod per i nodi che non hanno ricevuto di recente nuove richieste di pianificazione. Questo scenario indica che il pool di nodi ha più risorse di calcolo di quelle richieste e il numero di nodi può essere ridotto.

Un nodo che supera una soglia perché non è più richiesto per 10 minuti viene pianificato per l'eliminazione per impostazione predefinita. Quando si verifica questa situazione, i pod vengono pianificati per l'esecuzione su altri nodi all'interno del pool di nodi e il ridimensionamento automatico del cluster riduce il numero di nodi.

Le applicazioni potrebbero riscontrare alcuni problemi mentre i pod vengono pianificati per nodi diversi quando il ridimensionamento automatico del cluster riduce il numero di nodi. Per ridurre al minimo i disagi, evitare le applicazioni che usano una singola istanza di pod.

## <a name="burst-to-azure-container-instances"></a>Ridimensionamento burst in Istanze di Azure Container

Per ridimensionare rapidamente il cluster del servizio Azure Kubernetes, è possibile ricorrere all'integrazione con Istanze di Azure Container. Kubernetes include componenti predefiniti per modificare il numero di repliche e di nodi. Tuttavia, se l'applicazione deve essere ridimensionata rapidamente, il ridimensionamento automatico orizzontale dei pod può pianificare più pod di quelli che possono essere forniti dalle risorse di calcolo esistenti nel pool di nodi. Se configurato, questo scenario attiverebbe il ridimensionamento automatico del cluster per distribuire nodi aggiuntivi nel pool di nodi, ma potrebbero essere necessari alcuni minuti prima che venga completato il provisioning di tali nodi e prima che tali nodi consentano all'utilità di pianificazione di Kubernetes di eseguire pod su di essi.

![Ridimensionamento burst di Kubernetes in Istanze di contenitore di Azure](media/concepts-scale/burst-scaling.png)

Istanze di Azure Container consente di distribuire rapidamente istanze di contenitore senza sovraccarico aggiuntivo per l'infrastruttura. Quando ci si connette con il servizio Azure Kubernetes, Istanze di Azure Container diventa un'estensione logica protetta del cluster servizio Azure Kubernetes. Il componente [nodi virtuali][virtual-nodes-cli] , basato su [Kubelet virtuale][virtual-kubelet], viene installato nel cluster AKS che presenta ACI come nodo Kubernetes virtuale. Kubernetes può quindi pianificare i pod che vengono eseguiti come istanze di Istanze di Azure Container tramite i nodi virtuali e non come pod sui nodi macchina virtuale direttamente nel cluster del servizio Azure Kubernetes.

L'applicazione non richiede alcuna modifica per usare i nodi virtuali. Le distribuzioni possono essere ridimensionate su Istanze di Azure Container e il servizio Azure Kubernetes senza ritardo mentre il ridimensionamento automatico del cluster distribuisce nuovi nodi nel cluster del servizio Azure Kubernetes.

I nodi virtuali vengono distribuiti in una subnet aggiuntiva nella stessa rete virtuale del cluster del servizio Azure Kubernetes. Questa configurazione di rete virtuale consente di proteggere il traffico tra Istanze di Azure Container e il servizio Azure Kubernetes. Come un cluster del servizio Azure Kubernetes, un'istanza di Istanze di Azure Container è una risorsa di calcolo logica e sicura isolata dagli altri utenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le funzionalità di ridimensionamento delle applicazioni, seguire innanzitutto la [guida introduttiva per creare un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure][aks-quickstart]. È quindi possibile iniziare a ridimensionare manualmente o automaticamente le applicazioni nel cluster servizio Azure Kubernetes:

- Ridimensionare manualmente i [pod][aks-manually-scale-pods] o i [nodi][aks-manually-scale-nodes]
- Usare il [ridimensionamento automatico orizzontale dei pod][aks-hpa]
- Usare il [ridimensionamento][aks-cluster-autoscaler] automatico del cluster

Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Accesso e identità per Kubernetes/servizio Azure Kubernetes][aks-concepts-identity]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
