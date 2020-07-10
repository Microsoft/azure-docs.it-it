---
title: Esercitazione - Distribuire un gruppo multi-contenitore- YAML
description: In questa esercitazione si apprenderà come distribuire un gruppo di contenitori con più contenitori in istanze di Azure Container usando un file YAML con l'interfaccia della riga di comando di Azure.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f101d19814687082ab02955a3a860486d3988211
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169681"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Esercitazione: Distribuire un gruppo multi-contenitore usando un file YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Gestione risorse](container-instances-multi-container-group.md)
>

Istanze di Azure Container supporta la distribuzione di più contenitori in un singolo host usando un [gruppo di contenitori](container-instances-container-groups.md). Un gruppo di contenitori è utile quando si crea un contenitore collaterale dell'applicazione per la registrazione, il monitoraggio o qualsiasi altra configurazione in cui un servizio necessita di un secondo processo associato.

In questa esercitazione si esegue la procedura per l'esecuzione di una semplice configurazione collaterale a due contenitori distribuendo un [file YAML](container-instances-reference-yaml.md) tramite l'interfaccia della riga di comando di Azure. Un file YAML fornisce un formato conciso per specificare le impostazioni dell'istanza. Si apprenderà come:

> [!div class="checklist"]
> * Configurare un file YAML
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

> [!NOTE]
> I gruppi multicontenitore sono attualmente limitati ai contenitori Linux.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Configurare un file YAML

Per distribuire un gruppo multi-contenitore con il comando [az container create][az-container-create] nell'interfaccia della riga di comando di Azure, è necessario specificare la configurazione del gruppo di contenitori in un file YAML. Quindi passare il file YAML come parametro per il comando.

Per iniziare, copiare il file YAML seguente in un nuovo file denominato **deploy-aci.yaml**. In Azure Cloud Shell è possibile usare Visual Studio Code per creare il file nella directory di lavoro:

```
code deploy-aci.yaml
```

Questo file YAML definisce un gruppo di contenitori chiamato "myContainerGroup" con due contenitori, un indirizzo IP pubblico e due porte esposte. I contenitori vengono distribuiti dalle immagini Microsoft pubbliche. Il primo contenitore nel gruppo esegue un'applicazione Web con connessione Internet. Il secondo contenitore, quello collaterale, esegue periodicamente le richieste HTTP per l'applicazione Web in esecuzione nel primo contenitore tramite la rete locale del gruppo di contenitori.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Per usare un registro privato di immagini di contenitori, aggiungere la proprietà `imageRegistryCredentials` al gruppo di contenitori con i valori modificati per l'ambiente specifico:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Distribuire il gruppo di contenitori

Creare un gruppo di risorse con il comando [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il gruppo di contenitori con il comando [az container create][az-container-create], passando il file YAML come argomento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure.

## <a name="view-deployment-state"></a>Visualizzare lo stato della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [az container show][az-container-show] seguente:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se si desidera visualizzare l'applicazione in esecuzione, passare al relativo indirizzo IP nel browser. Ad esempio, l'indirizzo IP è `52.168.26.124` in questo output di esempio:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

Visualizzare l'output del log di un contenitore con il comando [az container logs][az-container-logs]. L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio viene specificato il contenitore `aci-tutorial-app`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Per visualizzare i log per il contenitore collaterale, eseguire un comando simile specificando il contenitore `aci-tutorial-sidecar`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Come si può notare, il contenitore collaterale invia periodicamente una richiesta HTTP all'applicazione Web principale tramite la rete locale del gruppo per verificare che l'applicazione sia in esecuzione. Questo esempio di contenitore collaterale può essere esteso per attivare un avviso se si riceve un codice di risposta HTTP diverso da `200 OK`.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un file YAML per distribuire un gruppo multi-contenitore in Istanze di Azure Container. Si è appreso come:

> [!div class="checklist"]
> * Configurare un file YAML per un gruppo multi-contenitore
> * Distribuire il gruppo di contenitori
> * Visualizzare i log dei contenitori

È anche possibile distribuire un gruppo multi-contenitore usando un [modello di Resource Manager](container-instances-multi-container-group.md). Un modello di Resource Manager può essere facilmente adattato agli scenari in cui è necessario distribuire risorse aggiuntive di servizi di Azure con il gruppo di contenitori.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
