---
title: Visualizzare i log di kubelet nel servizio Azure Kubernetes
description: Informazioni su come visualizzare le informazioni sulla risoluzione dei problemi nei log di kubelet dai nodi del servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 355c665db2627fe04595a8b519b16bd475ebcadf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735149"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Ottenere i log di kubelet dai nodi del cluster del servizio Azure Kubernetes

Come parte del funzionamento di un cluster AKS, potrebbe essere necessario esaminare i log per risolvere un problema. Il portale di Azure è la possibilità di visualizzare i log per i [componenti Master AKS][aks-master-logs] o i [contenitori in un cluster AKS][azure-container-logs]. Occasionalmente, potrebbe essere necessario ottenere i registri *kubelet* da un nodo AKS per la risoluzione dei problemi.

Questo articolo illustra come è possibile usare `journalctl` per visualizzare i log di *kubelet* in un nodo AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Creare una connessione SSH

Per prima cosa, creare una connessione SSH con il nodo da cui occorre visualizzare i log di *kubelet*. Questa operazione è descritta in dettaglio nel documento [Accesso SSH in nodi del cluster del servizio Azure Kubernetes][aks-ssh].

## <a name="get-kubelet-logs"></a>Ottenere i log di kubelet

Dopo aver stabilito la connessione al nodo, eseguire il comando seguente per eseguire il pull dei log *kubelet* :

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> Per i nodi Windows, i dati di log sono in `C:\k` e possono essere visualizzati usando il comando *altro* :
> ```
> more C:\k\kubelet.log
> ```

Il seguente output di esempio indica che il log di *kubelet* è in corso:

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Passaggi successivi

Se sono necessarie informazioni aggiuntive sulla risoluzione dei problemi dallo schema di Kubernetes, vedere [Visualizzare i log di nodi nello schema di servizio Azure Kubernetes][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: ./view-control-plane-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md