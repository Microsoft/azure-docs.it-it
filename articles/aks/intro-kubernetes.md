---
title: Introduzione al servizio Azure Kubernetes
description: Informazioni sulle funzioni e i vantaggi del servizio Azure Kubernetes nella distribuzione e gestione delle applicazioni basate su contenitore in Azure.
services: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 713e4e162ff16b674df88036adbaba6fa210c815
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106060"
---
# <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes

Il servizio Azure Kubernetes semplifica la distribuzione di un cluster Kubernetes gestito in Azure. servizio Azure Kubernetes riduce la complessità e i costi operativi di gestione di Kubernetes, affidando la maggior parte di queste attività ad Azure. Come servizio Kubernetes ospitato, Azure gestisce attività critiche quali il monitoraggio dell'integrità e la manutenzione per l'utente. I master di Kubernetes sono gestiti da Azure. È possibile gestire solo i nodi agente. servizio Azure Kubernetes è gratuito in quanto servizio Kubernetes gestito: si paga solo per i nodi dell'agente all'interno dei cluster e non per i master.

È possibile creare un cluster servizio Azure Kubernetes nel portale di Azure, con l'interfaccia della riga di comando di Azure o mediante opzioni di distribuzione basate su modello come i modelli di Gestione risorse e Terraform. Quando si distribuisce un cluster servizio Azure Kubernetes, il master Kubernetes e tutti i nodi vengono distribuiti e configurati automaticamente. Durante il processo di distribuzione è anche possibile configurare caratteristiche aggiuntive come funzionalità di rete avanzate, integrazione di Azure Active Directory e monitoraggio. I contenitori di Windows Server sono supportati nel servizio Azure Kubernetes.

Per altre informazioni sulle nozioni di base di Kubernetes, vedere [Concetti di base di Kubernetes per il servizio Azure Kubernetes][concepts-clusters-workloads].

Per iniziare, completare l'avvio rapido del servizio Azure Kubernetes [nel portale di Azure][aks-portal] o [usando l'interfaccia della riga di comando di Azure][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Accesso, sicurezza e monitoraggio

Per migliorare la sicurezza e la gestione, servizio Azure Kubernetes consente l'integrazione con Azure Active Directory e l'uso dei controlli di accesso basato sui ruoli di Kubernetes. È anche possibile monitorare l'integrità del cluster e delle risorse.

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

Per limitare l'accesso alle risorse del cluster, il servizio Azure Kubernetes supporta il [controllo degli accessi in base al ruolo di Kubernetes][kubernetes-rbac]. Il controllo degli accessi in base al ruolo consente di controllare l'accesso alle risorse e agli spazi dei nomi di Kubernetes, nonché le autorizzazioni per tali risorse. Si può anche configurare un cluster servizio Azure Kubernetes per l'integrazione con Azure Active Directory. Con l'integrazione di Azure Active Directory è possibile configurare l'accesso a Kubernetes in base all'identità e all'appartenenza ai gruppi esistenti. Gli utenti di Azure Active Directory e i gruppi esistenti possono ottenere l'accesso ad servizio Azure Kubernetes con un'esperienza di accessi integrata.

Per altre informazioni sull'identità, vedere [Opzioni di accesso e identità per il servizio Azure Kubernetes][concepts-identity].

Per proteggere i cluster del servizio Azure Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Registrazione e monitoraggio integrati

Per capire come sono le prestazioni del cluster servizio Azure Kubernetes e delle applicazioni distribuite, Monitoraggio di Azure per l'integrità dei contenitori raccoglie le metriche di memoria e processore da contenitori, nodi e controller. Sono disponibili i log dei contenitori ed è anche possibile [esaminare i log master di Kubernetes][aks-master-logs]. Questi dati di monitoraggio sono archiviati in un'area di lavoro Azure Log Analytics e sono disponibili tramite il portale di Azure, l'interfaccia della riga di comando di Azure o un endpoint REST.

Per altre informazioni, vedere [Monitorare l'integrità dei contenitori del servizio Azure Kubernetes][container-health].

## <a name="clusters-and-nodes"></a>Cluster e nodi

I nodi di servizio Azure Kubernetes sono eseguiti in macchine virtuali di Azure. È possibile connettere una risorsa di archiviazione a nodi e pod, aggiornare i componenti cluster e usare GPU. Il servizio Azure Kubernetes supporta i cluster Kubernetes che eseguono più pool di nodi per supportare sistemi operativi misti e contenitori Windows Server. I nodi Linux eseguono un'immagine del sistema operativo Ubuntu personalizzata e i nodi Windows Server eseguono un'immagine del sistema operativo Windows Server 2019 personalizzata.

### <a name="cluster-node-and-pod-scaling"></a>Ridimensionamento dei nodi del cluster e dei pod

Al variare della richiesta di risorse, il numero di nodi del cluster o di pod che eseguono i servizi aumenta o diminuisce automaticamente. È possibile usare sia la scalabilità automatica orizzontale dei pod che la scalabilità automatica del cluster. Questo approccio al ridimensionamento consente al cluster servizio Azure Kubernetes di adattarsi automaticamente alle richieste eseguendo solo le risorse necessarie.

Per altre informazioni, vedere [Ridimensionare un cluster del servizio Azure Kubernetes][aks-scale].

### <a name="cluster-node-upgrades"></a>Aggiornamenti dei nodi del cluster

Il servizio Azure Kubernetes offre più versioni di Kubernetes. Non appena sono disponibili nuove versioni in servizio Azure Kubernetes, il cluster può essere aggiornato tramite il portale di Azure o l'interfaccia della riga di comando di Azure. Durante il processo di aggiornamento, i nodi vengono accuratamente contrassegnati come non pianificabili e svuotati per ridurre al minimo le interruzioni nelle applicazioni in esecuzione.

Per altre informazioni sulle versioni del ciclo di vita, vedere [Versioni di Kubernetes supportate nel servizio Azure Kubernetes][aks-supported versions]. Per la procedura di aggiornamento, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nodi abilitati per la GPU

Il servizio Azure Container supporta la creazione di pool di nodi abilitati per la GPU. Azure offre attualmente macchine virtuali abilitate per una GPU singola o più GPU. Le macchine virtuali abilitate per la GPU sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica.

Per altre informazioni, vedere [Uso di GPU nel servizio Azure Kubernetes][aks-gpu].

### <a name="storage-volume-support"></a>Supporto di volumi di archiviazione

Per supportare carichi di lavoro applicativi, possono essere montati volumi di archiviazione per dati persistenti. È possibile usare volumi sia statici che dinamici. A seconda di quanti pod connessi devono condividere la risorsa di archiviazione, è possibile usare risorse di archiviazione basate su dischi di Azure per l'accesso da un singolo pod o File di Azure per l'accesso da parte di più pod simultaneamente.

Per altre informazioni, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

Capire come usare i volumi persistenti dinamici con [Dischi di Azure][azure-disk] o [File di Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Reti virtuali e ingresso

Un cluster servizio Azure Kubernetes può essere distribuito in una rete virtuale esistente. In questa configurazione ogni pod del cluster ottiene un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod nel cluster e con altri nodi della rete virtuale. I pod possono inoltre connettersi ad altri servizi in una rete virtuale con peering e a reti locali tramite ExpressRoute o connessioni VPN da sito a sito (S2S).

Per altre informazioni, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][aks-networking].

Per iniziare a usare il traffico in ingresso, vedere [Routing di applicazioni HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Ingresso con routing di applicazioni HTTP

Il componente aggiuntivo per il routing di applicazioni HTTP semplifica l'accesso alle applicazioni distribuite nel cluster servizio Azure Kubernetes. Quando abilitata, la soluzione Routing di applicazioni HTTP configura un controller di ingresso nel cluster servizio Azure Kubernetes. Al momento della distribuzione delle applicazioni, i nomi DNS accessibili pubblicamente vengono configurati automaticamente. Il routing di applicazioni HTTP consente di configurare una zona DNS e la integra con il cluster servizio Azure Kubernetes. È quindi possibile distribuire le risorse Kubernetes in ingresso come di consueto.

Per iniziare a usare il traffico in ingresso, vedere [Routing di applicazioni HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrazione di strumenti di sviluppo

Kubernetes include un ricco ecosistema di strumenti di sviluppo e di gestione, come Helm e l'estensione di Kubernetes per Visual Studio Code. Questi strumenti funzionano perfettamente con servizio Azure Kubernetes.

Inoltre, Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team. Con una configurazione minima è possibile eseguire i contenitori ed effettuarne il debug direttamente in servizio Azure Kubernetes. Per iniziare, vedere[Azure Dev Spaces][azure-dev-spaces].

Il progetto DevOps di Azure offre una soluzione semplice per trasferire il codice esistente e il repository Git in Azure. Il progetto DevOps crea automaticamente le risorse di Azure come servizio Azure Kubernetes, una pipeline di versione in Azure DevOps Services che include una pipeline di compilazione per l'integrazione continua, configura una pipeline di versione per la distribuzione continua e quindi crea una risorsa di Application Insights di Azure per il monitoraggio.

Per altre informazioni, vedere [Progetto DevOps di Azure][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Supporto per le immagini Docker e il registro contenitori privato

servizio Azure Kubernetes supporta il formato di immagine Docker. Per l'archiviazione privata delle immagini Docker è possibile integrare servizio Azure Kubernetes con Registro Azure Container.

Per creare l'archivio immagini privato, vedere [Registro Azure Container][acr-docs].

## <a name="kubernetes-certification"></a>Certificazione Kubernetes

Il servizio Azure Kubernetes ha ottenuto la certificazione CNCF per la conformità a Kubernetes.

## <a name="regulatory-compliance"></a>Conformità alle normative

Il servizio Azure Kubernetes è conforme agli standard SOC, ISO, PCI DSS e HIPAA. Per altre informazioni, vedere [Panoramica della conformità di Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione e la gestione di servizio Azure Kubernetes sono disponibili nella guida introduttiva sull'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Guida introduttiva al servizio Azure Kubernetes][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
