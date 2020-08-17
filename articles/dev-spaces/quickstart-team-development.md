---
title: Sviluppo in team in Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Questa guida di avvio rapido illustra come eseguire lo sviluppo in team in Kubernetes con contenitori e microservizi con Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 3b4aae5c249e4dcbc4365c98791415e9fdc55c0c
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080437"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Guida introduttiva: Sviluppo in team in Kubernetes - Azure Dev Spaces

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
- Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
- Testare un singolo microservizio in uno spazio di sviluppo isolato all'interno del contesto dell'applicazione completa.

![Sviluppo in team con Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
- [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 3 installato][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata][supported-regions]. I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio di sviluppo denominato *dev*.

> [!NOTE]
> Il comando `use-dev-spaces` installerà anche l'interfaccia della riga di comando di Azure Dev Spaces se non è già installata. Non è possibile installare l'interfaccia della riga di comando Azure Dev Spaces in Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo verrà usata l'[applicazione di esempio Bike Sharing di Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) per illustrare l'uso di Azure Dev Spaces.

Clonare l'applicazione da GitHub e passare alla relativa directory:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recuperare il valore HostSuffix per *dev*

Usare il comando `azds show-context` per visualizzare il valore HostSuffix per *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aggiornare il grafico Helm con il valore HostSuffix

Aprire [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e sostituire tutte le istanze di `<REPLACE_ME_WITH_HOST_SUFFIX>` con il valore HostSuffix recuperato in precedenza. Salvare le modifiche e chiudere il file.

## <a name="run-the-sample-application-in-kubernetes"></a>Eseguire l'applicazione di esempio in Kubernetes

I comandi per l'esecuzione dell'applicazione di esempio in Kubernetes fanno parte di un processo esistente e non presentano alcuna dipendenza dagli strumenti di Azure Dev Spaces. In questo caso, Helm è lo strumento usato per eseguire questa applicazione di esempio, ma potrebbero essere usati altri strumenti per eseguire l'intera applicazione in uno spazio dei nomi all'interno di un cluster. La destinazione dei comandi di Helm è lo spazio di sviluppo denominato *dev* creato in precedenza, ma questo spazio è anche uno spazio dei nomi Kubernetes. Di conseguenza, gli spazi di sviluppo possono essere la destinazione di altri strumenti come altri spazi dei nomi.

È possibile usare Azure Dev Spaces per lo sviluppo in team quando un'applicazione è in esecuzione in un cluster indipendentemente dagli strumenti usati per la distribuzione.

Usare il comando `helm install` per configurare e installare l'applicazione di esempio in un cluster.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Il completamento del comando `helm install` può richiedere alcuni minuti. Dopo l'installazione dell'applicazione di esempio nel cluster e l'abilitazione di Dev Spaces nel cluster, usare il comando `azds list-uris` per visualizzare gli URL per l'applicazione di esempio nello spazio di sviluppo *dev* attualmente selezionato.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Passare al servizio *bikesharingweb* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selezionare *Aurelia Briggs (customer)* come utente. Verificare che venga visualizzato il testo *Hi Aurelia Briggs | Sign out* nella parte superiore.

![Applicazione di esempio Bike Sharing di Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Creare spazi di sviluppo figlio

Usare il comando `azds space select` per creare due spazi figlio al di sotto dello spazio di sviluppo *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

I comandi precedenti creano due spazi figlio al di sotto di *dev* denominati *azureuser1* e *azureuser2*. Questi due spazi figlio rappresentano spazi di sviluppo distinti per *azureuser1* e *azureuser2* che gli sviluppatori possono usare per apportare modifiche all'applicazione di esempio.

Usare il comando `azds space list` per elencare tutti gli spazi di sviluppo e verificare che *dev/azureuser2* sia selezionato.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Usare `azds list-uris` per visualizzare gli URL per l'applicazione di esempio nello spazio attualmente selezionato, ovvero *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Verificare che gli URL visualizzati con il comando `azds list-uris` abbiano il prefisso *azureuser2.s.dev*. Questo prefisso conferma che lo spazio corrente selezionato è *azureuser2*, che è uno spazio figlio di *dev*.

Passare al servizio *bikesharingweb* per lo spazio di sviluppo *dev/azureuser2* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selezionare *Aurelia Briggs (customer)* come utente. Verificare che venga visualizzato il testo *Hi Aurelia Briggs | Sign out* nella parte superiore.

## <a name="update-code"></a>Aggiornare il codice

Aprire *BikeSharingWeb/components/Header.js* con un editor di testo e modificare il testo nell'[elemento span usando il valore di className `userSignOut`](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Salvare le modifiche e chiudere il file.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Compilare ed eseguire il servizio aggiornato bikesharingweb nello spazio di sviluppo *dev/azureuser2*

Passare alla directory *BikeSharingWeb/* ed eseguire il comando `azds up`.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Il comando crea ed esegue il servizio *bikesharingweb* nello spazio di sviluppo *dev/azureuser2*. Questo servizio viene eseguito oltre al servizio *bikesharingweb* in esecuzione nello spazio di sviluppo *dev* e viene usato solo per le richieste con il prefisso di URL *azureuser2.s*. Per altre informazioni sul funzionamento del routing tra spazi di sviluppo padre e figlio, vedere [Funzionamento e configurazione di Azure Dev Spaces](how-dev-spaces-works.md).

Passare al servizio *bikesharingweb* per lo spazio di sviluppo *dev/azureuser2* aprendo l'URL pubblico visualizzato nell'output del comando `azds up`. Selezionare *Aurelia Briggs (customer)* come utente. Verificare che in alto a destra sia visualizzato il testo aggiornato. Se questa modifica non è immediatamente visibile, potrebbe essere necessario aggiornare la pagina o cancellare la cache del browser.

![Applicazione di esempio Bike Sharing di Azure Dev Spaces aggiornata](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Quando si passa al servizio durante l'esecuzione di `azds up`, nell'output del comando `azds up` vengono visualizzate anche le tracce delle richieste HTTP. Queste tracce possono essere utili per la risoluzione dei problemi e per il debug del servizio. È possibile disabilitarle usando `--disable-http-traces` durante l'esecuzione di `azds up`.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Verificare che gli altri spazi di sviluppo siano rimasti invariati

Se il comando `azds up` è ancora in esecuzione, premere *CTRL+C*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Passare alla versione *dev* di *bikesharingweb* nel browser, scegliere *Aurelia Briggs (customer)* come utente e verificare che in alto a destra venga visualizzato il testo originale. Ripetere questi passaggi con l'URL *dev/azureuser1*. Si noti che le modifiche vengono applicate solo alla versione *dev/azureuser2* di *bikesharingweb*. Questo isolamento delle modifiche apportate a *dev/azureuser2* consente ad *azureuser2* di apportare modifiche senza effetti su *azureuser1*.

Affinché queste modifiche vengano applicate a *dev* e *dev/azureuser1*, è necessario seguire la pipeline CI/CD o il flusso di lavoro esistente del team. Ad esempio, questo flusso di lavoro può richiedere il commit della modifica nel sistema di controllo delle versioni e la distribuzione dell'aggiornamento tramite una pipeline CI/CD o strumenti come Helm.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](how-dev-spaces-works.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
