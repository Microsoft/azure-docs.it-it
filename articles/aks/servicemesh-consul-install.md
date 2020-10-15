---
title: Installare Consul nel servizio Azure Kubernetes
description: Informazioni su come installare e usare Consul per creare una mesh di servizi in un cluster Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2f47ded22209a9d53510c7d0a2f8270096161354
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86244132"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Installare e usare Consul in Azure Kubernetes Service (AKS)

[Console][consul-github] è una mesh di servizi open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono l'individuazione dei servizi, il controllo dell'integrità, la segmentazione dei servizi e l'osservabilità. Per ulteriori informazioni su Consul, vedere la documentazione ufficiale di [che cos'è Consul?][consul-docs-concepts] .

Questo articolo illustra come installare Console. I componenti Consul sono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno riferimento alla versione di console `1.6.0` e usano almeno la versione Helm `2.14.2` .
>
> Le versioni di Consul `1.6.x` possono essere eseguite con le versioni Kubernetes `1.13+` . Altre versioni di Consul sono disponibili nelle versioni di [GitHub-console][consul-github-releases] e informazioni su ognuna delle versioni in [console-note sulla versione][consul-release-notes].

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Installare i componenti di console su AKS
> * Convalidare l'installazione di Consul
> * Disinstalla console da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.13` e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart]. Verificare che il cluster disponga di almeno 3 nodi nel pool di nodi Linux.

È necessario [Helm][helm] per seguire queste istruzioni e installare Console. È consigliabile che nel cluster sia installata e configurata correttamente la versione stabile più recente. Per assistenza sull'installazione di Helm, vedere la Guida all' [installazione di AKS Helm][helm-install]. Anche tutti i pod console devono essere pianificati per l'esecuzione nei nodi Linux.

Questo articolo separa le linee guida per l'installazione di Consul in diversi passaggi discreti. Il risultato finale è lo stesso nella struttura delle [linee guida][consul-install-k8]per l'installazione ufficiale di console.

### <a name="install-the-consul-components-on-aks"></a>Installare i componenti di console su AKS

Si inizierà scaricando `v0.10.0` la versione del grafico Helm del console. Questa versione del grafico include la versione di console `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Usare Helm e il `consul-helm` grafico scaricato per installare i componenti Consul nello `consul` spazio dei nomi nel cluster AKS. 

> [!NOTE]
> **Opzione di installazione**
> 
> Come parte dell'installazione di vengono usate le opzioni seguenti:
> - `connectInject.enabled=true` -abilitare i proxy da inserire nei Pod
> - `client.enabled=true` -abilitare i client console per l'esecuzione in ogni nodo
> - `client.grpc=true` -Abilita listener gRPC per connectInject
> - `syncCatalog.enabled=true` -Sincronizza i servizi Kubernetes e Consul
>
> **Selettori di nodo**
>
> Console attualmente deve essere pianificata per l'esecuzione nei nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod console siano pianificati solo per l'esecuzione nei nodi Linux. Verranno usati i [selettori di nodo][kubernetes-node-selectors] per assicurarsi che i pod siano pianificati per i nodi corretti.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Il `Consul` grafico Helm distribuisce un certo numero di oggetti. È possibile visualizzare l'elenco dall'output del `helm install` comando precedente. La distribuzione dei componenti Consul può richiedere circa 3 minuti, a seconda dell'ambiente cluster.

A questo punto, è stato distribuito console per il cluster AKS. Per assicurarsi che la distribuzione di Consul venga completata, passare alla sezione successiva per convalidare l'installazione di Consul.

## <a name="validate-the-consul-installation"></a>Convalidare l'installazione di Consul

Verificare che le risorse siano state create correttamente. Usare i comandi [kubectl Get SVC][kubectl-get] e [kubectl Get Pod][kubectl-get] per eseguire una query sullo `consul` spazio dei nomi, in cui sono stati installati i componenti di console tramite il `helm install` comando:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

L'output di esempio seguente mostra i servizi e i pod (pianificati sui nodi Linux) che ora devono essere in esecuzione:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Tutti i pod dovrebbero mostrare lo stato `Running` . Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-consul-ui"></a>Accesso all'interfaccia utente di console

L'interfaccia utente di console è stata installata nella configurazione precedente e fornisce la configurazione basata sull'interfaccia utente per console. L'interfaccia utente per Consul non è esposta pubblicamente tramite un indirizzo IP esterno. Per accedere all'interfaccia utente di console di, usare il comando [kubectl Port-inoltr][kubectl-port-forward] . Questo comando crea una connessione sicura tra il computer client e il Pod pertinente nel cluster AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

È ora possibile aprire un browser e fare in modo che punti a `http://localhost:8080/ui` per aprire l'interfaccia utente di console. Quando si apre l'interfaccia utente, verrà visualizzato quanto segue:

![Interfaccia utente console](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Disinstalla console da AKS

> [!WARNING]
> L'eliminazione di Consul da un sistema in esecuzione può causare problemi correlati al traffico tra i servizi. Assicurarsi di aver fatto in modo che il sistema continui a funzionare correttamente senza Consul prima di procedere.

### <a name="remove-consul-components-and-namespace"></a>Rimuovere i componenti e lo spazio dei nomi del console

Per rimuovere Consul dal cluster AKS, usare i comandi seguenti. Il `helm delete` grafico viene rimosso `consul` e il `kubectl delete namespace` comando rimuoverà lo `consul` spazio dei nomi.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Passaggi successivi

Per esplorare altre opzioni di installazione e configurazione per Consul, vedere gli articoli seguenti del console ufficiale:

- [Guida all'installazione di Consul-Helm][consul-install-k8]
- [Opzioni di installazione di Consul-Helm][consul-install-helm-options]

È anche possibile seguire altri scenari usando:

- [Applicazione di esempio console][consul-app-example]
- [Architettura di riferimento di console Kubernetes][consul-reference]
- [Gateway mesh console][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
