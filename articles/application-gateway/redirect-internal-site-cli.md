---
title: Reindirizzamento interno tramite l'interfaccia della riga di comando
titleSuffix: Azure Application Gateway
description: Informazioni su come creare un gateway applicazione che reindirizza il traffico Web interno al pool appropriato usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: c4aa8927422d1f95a642e1d7c383aebc03a4930e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775782"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Creare un gateway applicazione con reindirizzamento interno tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare il [reindirizzamento del traffico Web](multiple-site-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione viene definito un pool back-end usando un set di scalabilità di macchine virtuali, quindi si configurano i listener e le regole in base ai domini di cui si è proprietari per assicurarsi che il traffico Web arrivi al pool appropriato. Questa esercitazione presuppone che l'utente sia proprietario di più domini e che usi gli esempi di *www\.contoso.com* e *www\.contoso.org*.

In questo articolo vengono illustrate le operazioni seguenti:

* Configurare la rete
* Creare un gateway applicazione
* Aggiungere listener e una regola di reindirizzamento
* Creare un set di scalabilità di macchine virtuali con il pool back-end
* Creare un record CNAME nel dominio

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Per questa esercitazione è necessaria la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella località *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet). Successivamente, aggiungere la subnet denominata *myBackendSubnet* necessaria per i pool back-end dei server tramite [az network vnet subnet create](/cli/azure/network/vnet/subnet). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

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

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/network/application-gateway) per creare il gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. 

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
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: regola di routing predefinita associata ad *appGatewayHttpListener*.


## <a name="add-listeners-and-rules"></a>Aggiungere listener e regole 

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. In questa esercitazione vengono creati due listener per i due domini. In questo esempio vengono creati listener per i domini *www\.contoso.com* e *www\.contoso.org*.

Aggiungere i listener back-end necessari per instradare il traffico usando [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Aggiungere la configurazione di reindirizzamento

Aggiungere la configurazione di reindirizzamento che invia il traffico da *www \. consoto.org* al listener per *www \. contoso.com* nel gateway applicazione usando [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Aggiungere le regole di routing

Le regole vengono elaborate nell'ordine in cui sono state create e il traffico viene indirizzato usando la prima regola corrispondente all'URL inviato al gateway applicazione. Se ad esempio si dispone di due regole, una che usa un listener di base e una che usa un listener multisito, entrambe sulla stessa porta, la regola con il listener multisito deve essere elencata prima della regola con il listener di base per funzionare come previsto. 

In questo esempio si creano due nuove regole e si elimina la regola predefinita creata.  È possibile aggiungere la regola usando [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Creare set di scalabilità di macchine virtuali

In questo esempio si crea un set di scalabilità di macchine virtuali che supporta il pool back-end creato. Il set di scalabilità creato è denominato *myvmss* e contiene due istanze di macchina virtuale in cui è installato NGINX.

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

Eseguire questo comando nella finestra della shell:

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

## <a name="create-cname-record-in-your-domain"></a>Creare un record CNAME nel dominio

Dopo aver creato il gateway applicazione con l'indirizzo IP pubblico, è possibile ottenere l'indirizzo DNS e usarlo per creare un record CNAME nel dominio. Per ottenere l'indirizzo DNS del gateway applicazione è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare il valore *fqdn* di DNSSettings e usarlo come valore del record CNAME creato. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare quando il gateway applicazione viene riavviato.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Immettere il nome di dominio nella barra degli indirizzi del browser. Ad esempio, http:\//www.contoso.com.

![Testare il sito contoso nel gateway applicazione](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Modificare l'indirizzo nell'altro dominio, ad esempio http: /www.contoso.org e si dovrebbe vedere che il traffico è stato reindirizzato al listener per \/ www \. contoso.com.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> * Configurare la rete
> * Creare un gateway applicazione
> * Aggiungere listener e una regola di reindirizzamento
> * Creare un set di scalabilità di macchine virtuali con il pool back-end
> * Creare un record CNAME nel dominio
