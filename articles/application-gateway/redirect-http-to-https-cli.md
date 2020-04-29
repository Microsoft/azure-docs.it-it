---
title: Reindirizzamento da HTTP a HTTPS tramite l'interfaccia della riga di comando
titleSuffix: Azure Application Gateway
description: Informazioni su come creare un gateway applicazione e aggiungere un certificato per la terminazione TLS usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: victorh
ms.openlocfilehash: 6bf8f3b7bfb446db78f0c97a246977fec6cd54cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312149"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Creare un gateway applicazione con reindirizzamento da HTTP a HTTPS tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare un [gateway applicazione](overview.md) con un certificato per la terminazione TLS/SSL. Viene usata una regola di routing per reindirizzare il traffico HTTP verso la porta HTTPS nel gateway applicazione. In questo esempio viene creato anche un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per il pool back-end del gateway applicazione che contiene due istanze di macchine virtuali.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Aggiungere un listener e una regola di reindirizzamento
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per l'uso in ambiente di produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questa esercitazione, creare un certificato autofirmato e un file con estensione pfx tramite il comando openssl.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Immettere i valori appropriati per il certificato. È possibile accettare i valori predefiniti.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Immettere la password per il certificato. In questo esempio, *Azure123456!* è la password usata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet). È quindi possibile aggiungere la subnet denominata *myBackendSubnet* necessaria per i server back-end tramite [az network vnet subnet create](/cli/azure/network/vnet/subnet). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) per creare il gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. 

Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. In questo esempio, associare il certificato creato e la relativa password quando si crea il gateway applicazione. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: regola di routing predefinita associata ad *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Aggiungere un listener e una regola di reindirizzamento

### <a name="add-the-http-port"></a>Aggiungere la porta HTTP

È possibile usare [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) per aggiungere la porta HTTP al gateway applicazione.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Aggiungere il listener HTTP

È possibile usare [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) per aggiungere il listener denominato *myListener* al gateway applicazione.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Aggiungere la configurazione di reindirizzamento

Aggiungere la configurazione di reindirizzamento da HTTP a HTTPS al gateway applicazione tramite [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Aggiungere la regola di routing

Aggiungere la regola di routing denominata *rule2* con la configurazione di reindirizzamento al gateway applicazione con [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali denominato *myvmss*che fornisce i server per il pool back-end nel gateway applicazione. Le macchine virtuali nel set di scalabilità sono associate a *myBackendSubnet* e *appGatewayBackendPool*. Per creare il set di scalabilità, è possibile usare [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installare NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, è possibile usare [az network public-ip show](/cli/azure/network/public-ip). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Avviso di sicurezza](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Per accettare l'avviso di sicurezza se è stato usato un certificato autofirmato, selezionare **Dettagli** e quindi **passare alla pagina Web**. Il sito NGINX protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Testare l'URL di base nel gateway applicazione](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Aggiungere un listener e una regola di reindirizzamento
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito


