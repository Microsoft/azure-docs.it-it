---
title: Usare il controller di ingresso con IP statico
titleSuffix: Azure Kubernetes Service
description: Informazioni su come installare e configurare un controller di ingresso NGINX con un indirizzo IP pubblico statico in un cluster del servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 89068210e0a2656c0a0642417532b28d8f10d93a
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87130852"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso con un indirizzo IP pubblico statico nel servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Azure Kubernetes. Il controller di ingresso è configurato con un indirizzo IP pubblico statico. Il progetto [cert-manager][cert-manager] viene usato per generare e configurare automaticamente certificati [Let's Encrypt][lets-encrypt]. Infine, due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS con un indirizzo IP pubblico dinamico][aks-ingress-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Questo articolo usa [Helm 3][helm] per installare il controller di ingresso nginx e Cert-Manager. Assicurarsi di usare l'ultima versione di Helm. Per istruzioni sull'aggiornamento, vedere la documentazione relativa all' [installazione di Helm][helm-install]. Per altre informazioni sulla configurazione e sull'uso di Helm, vedere [installare applicazioni con Helm in Azure Kubernetes Service (AKS)][use-helm].

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per impostazione predefinita, un controller di ingresso NGINX viene creato con l'assegnazione di un nuovo indirizzo IP pubblico. Questo indirizzo IP pubblico è solo statico per la durata del controller di ingresso e viene perso se il controller viene eliminato e ricreato. Un requisito di configurazione comune consiste nel fornire al controller di ingresso NGINX un indirizzo IP pubblico statico esistente. L'indirizzo IP pubblico statico rimane quando il controller di ingresso viene eliminato. Questo approccio consente di usare i record DNS e le configurazioni di rete esistenti in modo coerente per tutto il ciclo di vita delle applicazioni.

Se è necessario creare un indirizzo IP pubblico statico, è necessario ottenere prima il nome del gruppo di risorse del cluster servizio Azure Kubernetes con il comando [az servizio Azure Kubernetes show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Successivamente, creare un indirizzo IP pubblico con il metodo di allocazione *statico* usando il comando [az network public-ip create][az-network-public-ip-create]. Nell'esempio seguente viene creato un indirizzo IP pubblico denominato *myservizio Azure KubernetesPublicIP* nel gruppo di risorse cluster servizio Azure Kubernetes ottenuto nel passaggio precedente:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> I comandi precedenti creano un indirizzo IP che verrà eliminato se si elimina il cluster AKS. In alternativa, è possibile creare un indirizzo IP in un gruppo di risorse diverso, che può essere gestito separatamente dal cluster AKS. Se si crea un indirizzo IP in un gruppo di risorse diverso, assicurarsi che l'entità servizio usata dal cluster AKS disponga di autorizzazioni delegate per l'altro gruppo di risorse, ad esempio *collaboratore rete*.

A questo punto distribuire il grafico *ingress nginx* con Helm. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

È necessario passare due parametri aggiuntivi alla versione Helm, in modo che il controller di ingresso sia in grado di riconoscere sia l'indirizzo IP statico del servizio di bilanciamento del carico da allocare al servizio del controller di ingresso che l'etichetta del nome DNS da applicare alla risorsa indirizzo IP pubblico. Per il corretto funzionamento dei certificati HTTPS, viene usata un'etichetta del nome DNS per configurare un nome di dominio completo per l'indirizzo IP del controller di ingresso.

1. Aggiungere il `--set controller.service.loadBalancerIP` parametro. Specificare l'indirizzo IP pubblico creato nel passaggio precedente.
1. Aggiungere il `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametro. Specificare un'etichetta del nome DNS da applicare all'indirizzo IP pubblico creato nel passaggio precedente.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-Basic*. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze. Se il cluster AKS non è abilitato per il controllo degli accessi in base al ruolo, aggiungere `--set rbac.create=false` ai comandi Helm.

> [!TIP]
> Per abilitare la [conservazione dell'indirizzo IP di origine client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando Helm install. L'IP di origine del client viene archiviato nell'intestazione della richiesta sotto *X-inoltred-for*. Quando si usa un controller di ingresso con la conservazione IP dell'origine client abilitata, il pass-through TLS non funzionerà.

Aggiornare lo script seguente con l' **indirizzo IP** del controller di ingresso e un **nome univoco** che si vuole usare per il prefisso FQDN:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato l'indirizzo IP statico, come illustrato nell'output dell'esempio seguente:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   STATIC_IP      80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Non sono ancora state create regole di ingresso, quindi se si passa all'indirizzo IP pubblico si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX. Le regole di ingresso sono configurate nei passaggi seguenti.

È possibile verificare che l'etichetta del nome DNS sia stata applicata eseguendo una query sul nome di dominio completo nell'indirizzo IP pubblico, come indicato di seguito:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Il controller di ingresso è ora accessibile tramite l'indirizzo IP o il nome di dominio completo.

## <a name="install-cert-manager"></a>Installare cert-manager

Il controller di ingresso NGINX supporta la terminazione TLS. Il recupero e la configurazione di certificati per HTTPS possono essere eseguiti in modi diversi. Questo articolo descrive l'uso di [cert-manager][cert-manager], che consente di generare automaticamente il certificato [Let's Encrypt][lets-encrypt] e che dispone di funzionalità di gestione.

> [!NOTE]
> Nell'articolo viene usato l'ambiente `staging` per Let's Encrypt. Nelle distribuzioni di produzione usare `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nella definizione delle risorse e durante l'installazione del grafico Helm.

Per installare il controller cert-manager in un cluster abilitato per il controllo degli accessi in base al ruolo, usare il comando `helm install` seguente:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Per altre informazioni sulla configurazione di cert-manager, vedere [progetto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Creare un'autorità di certificazione (CA) cluster

Per poter emettere i certificati, cert-manager richiede un [autorità di certificazione][cert-manager-issuer] o una risorsa [ClusterIssuer][cert-manager-cluster-issuer]. Queste risorse Kubernetes hanno funzionalità identiche, ma `Issuer` funziona in uno spazio dei nomi singolo, mentre `ClusterIssuer` funziona in tutti gli spazi dei nomi. Per altre informazioni, vedere la documentazione relativa all'[autorità di certificazione cert-manager][cert-manager-issuer].

Creare un'autorità di certificazione del cluster, ad esempio `cluster-issuer.yaml`, tramite il manifesto di esempio seguente. Aggiornare l'indirizzo di posta elettronica con un indirizzo valido della propria organizzazione:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Per creare l'autorità di certificazione, usare il comando `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Sono stati configurati un controller di ingresso e una soluzione di gestione dei certificati. A questo punto è possibile eseguire due applicazioni demo nel cluster servizio Azure Kubernetes. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione "Hello world".

Per visualizzare il controller di ingresso in azione, eseguire due applicazioni demo nel cluster AKS. In questo esempio si usa `kubectl apply` per distribuire due istanze di una semplice applicazione *Hello World* .

Creare un file *AKS-HelloWorld. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Creare un file in *ingresso-demo. YAML* e copiarlo nell'esempio YAML seguente:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Eseguire le due applicazioni demo usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono ora in esecuzione nel cluster Kubernetes, anche se sono configurate con un servizio di tipo `ClusterIP`. Di conseguenza, le applicazioni non sono accessibili da Internet. Per renderle disponibili pubblicamente, creare una risorsa di ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` viene instradato al servizio `ingress-demo`. Aggiornare gli elementi *hosts* e *host* in base al nome DNS creato in un passaggio precedente.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic`.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Creare un oggetto certificato

Successivamente, è necessario creare una risorsa certificato. La risorsa certificato definisce il certificato X.509 desiderato. Per ulteriori informazioni, vedere [certificati Cert-Manager][cert-manager-certificates].

Cert-manager ha probabilmente creato automaticamente un oggetto certificato per l'uso in ingress-shim, che viene distribuito automaticamente con cert-manager dalla versione 0.2.2. Per altre informazioni, consultare la [documentazione su ingress-shim][ingress-shim].

Per verificare che il certificato sia stato creato correttamente, usare il comando `kubectl describe certificate tls-secret --namespace ingress-basic`.

Se il certificato è stato rilasciato, l'output sarà simile al seguente:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se è necessario creare una risorsa certificato aggiuntiva, è possibile farlo con il manifesto di esempio seguente. Aggiornare gli elementi *dnsNames* e *domains* in base al nome DNS creato in un passaggio precedente. Se si usa solo un controller di ingresso interno specificare il nome DNS interno per il servizio.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Per creare la risorsa certificato, usare il comando `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testare la configurazione di ingresso

Aprire un Web browser per il nome di dominio completo del controller di ingresso Kubernetes, ad esempio *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Come si usa in questi esempi `letsencrypt-staging` , il certificato TLS/SSL emesso non è considerato attendibile dal browser. Accettare l'avviso per continuare l'applicazione. Le informazioni sul certificato indicano che il certificato *Fake LE Intermediate X1* è emesso da Let's Encrypt. Questo certificato fittizio indica che `cert-manager` ha elaborato la richiesta in modo corretto e ha ricevuto un certificato dal provider:

![Certificato temporaneo Let's Encrypt](media/ingress/staging-certificate.png)

Quando si modifica Let's Encrypt per usare `prod` anziché `staging`, viene usato un certificato attendibile emesso da Let's Encrypt, come illustrato nell'esempio seguente:

![Certificato Let's Encrypt](media/ingress/certificate.png)

L'applicazione demo viene visualizzata nel Web browser:

![Esempio di applicazione 1](media/ingress/app-one.png)

A questo punto aggiungere il percorso */hello-world-two* al nome FQDN, ad esempio *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*. Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Esempio di applicazione 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse

Questo articolo ha usato Helm per installare i componenti di ingresso, i certificati e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Elimina risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Rimuovere innanzitutto le risorse del certificato:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Elencare ora le versioni di Helm con il comando `helm list`. Cercare i grafici denominati *nginx-ingress* e *Cert-Manager* come illustrato nell'output di esempio seguente:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Disinstallare le versioni con il `helm uninstall` comando. Nell'esempio seguente viene disinstallata la distribuzione in ingresso NGINX e le distribuzioni di gestione certificati.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Rimuovere quindi le due applicazioni di esempio:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Eliminare lo spazio dei nomi stesso. Usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

Rimuovere infine l'indirizzo IP pubblico statico creato per il controller di ingresso. Specificare il nome del gruppo di risorse cluster *MC_* ottenuto nel primo passaggio di questo articolo, ad esempio *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]
- [cert-manager][cert-manager]

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
