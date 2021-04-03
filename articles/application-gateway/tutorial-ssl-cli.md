---
title: Terminazione TLS con l'interfaccia della riga di comando-applicazione Azure gateway
description: Informazioni su come creare un gateway applicazione e aggiungere un certificato per la terminazione TLS usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 24dafd63de1a37140c6a56547c4701729df1c8fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566555"
---
# <a name="create-an-application-gateway-with-tls-termination-using-the-azure-cli"></a>Creare un gateway applicazione con la terminazione TLS tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per creare un [gateway applicazione](overview.md) con un certificato per la [terminazione TLS](ssl-overview.md). Per i server back-end, è possibile usare un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md) . In questo esempio il set di scalabilità contiene due istanze di macchine virtuali che vengono aggiunte al pool back-end predefinito del gateway applicazione.

In questo articolo vengono illustrate le operazioni seguenti:

* Creare un certificato autofirmato
* Configurare una rete
* Creare un gateway applicazione con il certificato
* Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se si preferisce, è possibile completare questa procedura usando [Azure PowerShell](tutorial-ssl-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Per questa esercitazione è necessaria la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per la produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questo articolo, creare un certificato autofirmato e un file con estensione pfx tramite il comando openssl.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/network/application-gateway) per creare il gateway applicazione. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. 

Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myAGPublicIPAddress* creati in precedenza. In questo esempio, associare il certificato creato e la relativa password quando si crea il gateway applicazione. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
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

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali che fornisce server per il pool back-end predefinito nel gateway applicazione. Le macchine virtuali nel set di scalabilità sono associate a *myBackendSubnet* e *appGatewayBackendPool*. Per creare il set di scalabilità, è possibile usare [az vmss create](/cli/azure/vmss#az-vmss-create).

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

Per ottenere l'indirizzo IP pubblico del gateway applicazione, è possibile usare [az network public-ip show](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Per questo esempio, l'URL è: **https://52.170.203.149** .

![Avviso di sicurezza](./media/tutorial-ssl-cli/application-gateway-secure.png)

Per accettare l'avviso di sicurezza se è stato usato un certificato autofirmato, selezionare **Dettagli** e quindi **passare alla pagina Web**. Il sito NGINX protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Testare l'URL di base nel gateway applicazione](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un gateway applicazione che ospita più siti Web](./tutorial-multiple-sites-cli.md)