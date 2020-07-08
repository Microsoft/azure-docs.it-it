---
title: Personalizzare CoreDNS per il servizio Azure Kubernetes (AKS)
description: Informazioni su come personalizzare CoreDNS per aggiungere sottodomini o estendere endpoint DNS personalizzati con il servizio Azure Kubernetes (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77595825"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizzare CoreDNS con il servizio Azure Kubernetes

Azure Kubernetes Service (AKS) usa il progetto [CoreDNS][coredns] per la gestione e la risoluzione del DNS del cluster con tutti i cluster *1.12. x* e versioni successive. In precedenza è stato usato il progetto Kube-DNS. Questo progetto Kube-DNS è ora deprecato. Per ulteriori informazioni sulla personalizzazione di CoreDNS e Kubernetes, vedere la [documentazione ufficiale upstream][corednsk8s].

Poiché AKS è un servizio gestito, non è possibile modificare la configurazione principale per CoreDNS ( *CoreFile*). Usare invece un *ConfigMap* Kubernetes per eseguire l'override delle impostazioni predefinite. Per visualizzare il valore predefinito di AKS CoreDNS ConfigMaps, usare il `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

Questo articolo illustra come usare ConfigMaps per le opzioni di personalizzazione di base di CoreDNS in AKS. Questo approccio è diverso dalla configurazione di CoreDNS in altri contesti, ad esempio l'uso di CoreFile. Verificare la versione di CoreDNS in esecuzione perché i valori di configurazione possono variare tra le versioni.

> [!NOTE]
> `kube-dns`offerte diverse [Opzioni di personalizzazione][kubednsblog] tramite una mappa di configurazione Kubernetes. CoreDNS **non** è compatibile con le versioni precedenti di Kube-DNS. Tutte le personalizzazioni usate in precedenza devono essere aggiornate per l'uso con CoreDNS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Elementi supportati/non supportati

Sono supportati tutti i plug-in CoreDNS predefiniti. Non sono supportati componenti aggiuntivi o plug-in di terze parti.

## <a name="rewrite-dns"></a>Riscrivi DNS

Uno scenario in cui si ha a disposizione è eseguire riscritture del nome DNS in tempo reale. Nell'esempio seguente sostituire `<domain to be written>` con il nome di dominio completo. Creare un file denominato `corednsms.yaml` e incollare la configurazione di esempio seguente:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Creare il ConfigMap usando il comando [kubectl Apply ConfigMap][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Per verificare che siano state applicate le personalizzazioni, usare [kubectl Get configmaps][kubectl-get] e specificare il ConfigMap *coredns-Custom* :

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Ora forzare il ricaricamento di ConfigMap in CoreDNS. Il comando [kubectl Delete Pod][kubectl delete] non è distruttivo e non comporta tempi di inattività. I `kube-dns` Pod vengono eliminati e l'utilità di pianificazione di Kubernetes li ricrea. Questi nuovi Pod contengono la modifica del valore TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Il comando precedente è corretto. Mentre si sta cambiando `coredns` , la distribuzione si trova sotto il nome del **DNS Kube** .

## <a name="custom-forward-server"></a>Server di invio personalizzato

Se è necessario specificare un server di avanzamento per il traffico di rete, è possibile creare un ConfigMap per personalizzare DNS. Nell'esempio seguente, aggiornare il `forward` nome e l'indirizzo con i valori per il proprio ambiente. Creare un file denominato `corednsms.yaml` e incollare la configurazione di esempio seguente:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Come negli esempi precedenti, creare il ConfigMap usando il comando [kubectl Apply ConfigMap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi forzare CoreDNS a ricaricare il ConfigMap usando [kubectl Delete Pod][kubectl delete] per l'utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Usare domini personalizzati

Potrebbe essere necessario configurare domini personalizzati che possono essere risolti solo internamente. Ad esempio, potrebbe essere necessario risolvere il dominio personalizzato *puglife. local*, che non è un dominio di primo livello valido. Senza un dominio personalizzato ConfigMap, il cluster AKS non riesce a risolvere l'indirizzo.

Nell'esempio seguente aggiornare il dominio personalizzato e l'indirizzo IP per indirizzare il traffico a con i valori per il proprio ambiente. Creare un file denominato `corednsms.yaml` e incollare la configurazione di esempio seguente:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Come negli esempi precedenti, creare il ConfigMap usando il comando [kubectl Apply ConfigMap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi forzare CoreDNS a ricaricare il ConfigMap usando [kubectl Delete Pod][kubectl delete] per l'utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domini Stub

CoreDNS può essere usato anche per configurare domini stub. Nell'esempio seguente aggiornare i domini personalizzati e gli indirizzi IP con i valori per il proprio ambiente. Creare un file denominato `corednsms.yaml` e incollare la configurazione di esempio seguente:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Come negli esempi precedenti, creare il ConfigMap usando il comando [kubectl Apply ConfigMap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi forzare CoreDNS a ricaricare il ConfigMap usando [kubectl Delete Pod][kubectl delete] per l'utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in host

Poiché tutti i plug-in integrati sono supportati, questo significa che il plug-in [host][coredns hosts] CoreDNS è disponibile anche per la personalizzazione:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Abilitare la registrazione per il debug di query DNS 

Per abilitare la registrazione delle query DNS, applicare la configurazione seguente in coredns-Custom ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato alcuni scenari di esempio per la personalizzazione di CoreDNS. Per informazioni sul progetto CoreDNS, vedere [la pagina relativa al progetto upstream di CoreDNS][coredns].

Per altre informazioni sui concetti di base relativi alla rete, vedere [concetti di rete per le applicazioni in AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
