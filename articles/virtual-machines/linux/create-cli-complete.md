---
title: Creare un ambiente Linux con l'interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure per creare da zero una risorsa di archiviazione, una VM di Linux, una rete virtuale con subnet, il bilanciamento del carico, una scheda di interfaccia di rete, un IP pubblico e un gruppo di sicurezza di rete.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 4348d3d71259b5bdf63b1c52af53bff59c650086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829017"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Creare una macchina virtuale Linux completa con l'interfaccia della riga di comando di Azure
Per creare rapidamente una macchina virtuale in Azure, è possibile usare un singolo comando dell'interfaccia della riga di comando di Azure che si serve dei valori predefiniti per creare tutte le risorse di supporto richieste. Le risorse, ad esempio una rete virtuale, l'indirizzo IP pubblico e regole del gruppo di sicurezza di rete, vengono create automaticamente. Per un maggiore controllo dell'ambiente di produzione è possibile creare queste risorse in anticipo e quindi aggiungervi le macchine virtuali. In questo articolo descrive come creare una macchina virtuale e tutte le risorse di supporto, una alla volta.

Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di avere eseguito la registrazione a un account di Azure con [az login](/cli/azure/reference-index).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.

## <a name="create-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale e delle risorse della rete virtuale di supporto. Creare il gruppo di risorse con [AZ Group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Per impostazione predefinita, l'output del comando dell'interfaccia della riga di comando di Azure è in formato JSON, ovvero JavaScript Object Notation. Per trasformare l'output predefinito in un elenco o in una tabella, ad esempio, usare [az configure --output](/cli/azure/reference-index). È possibile anche aggiungere `--output` a qualsiasi comando per apportare una modifica una sola volta nel formato di output. L'esempio seguente illustra l'output JSON ottenuto dal comando `az group create`:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet
Dopodiché è necessario creare una rete virtuale in Azure e una subnet in cui poter creare le macchine virtuali. Usare [az network vnet create](/cli/azure/network/vnet) per creare una rete virtuale denominata *myVnet* con il prefisso dell'indirizzo *192.168.0.0/16*. Aggiungere anche una subnet denominata *mySubnet* con un prefisso di indirizzo di *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

L'output mostra la subnet creata in modo logico all'interno della rete virtuale:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Creare ora un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). L'indirizzo IP pubblico consente di connettersi alle macchine virtuali da Internet. Poiché l'indirizzo predefinito è dinamico, è necessario creare una voce DNS denominata con il parametro `--domain-name-label`. L'esempio seguente crea un indirizzo IP pubblico chiamato *myPublicIP* con il nome DNS *mypublicdns*. Dal momento che il nome DNS deve essere univoco, è necessario inserire il proprio nome DNS univoco:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Output:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete
Per controllare il flusso del traffico in ingresso e in uscita dalle macchine virtuali, applicare un gruppo di sicurezza di rete a una subnet o una scheda di interfaccia di rete virtuale. L'esempio seguente usa [az network nsg create](/cli/azure/network/nsg) per creare un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

È necessario definire le regole che consentono o bloccano un traffico specifico. Per consentire connessioni in ingresso sulla porta 22 (per abilitare l'accesso SSH), creare una regola di ingresso con il comando [az network nsg rule create](/cli/azure/network/nsg/rule). L'esempio seguente crea una regola denominata *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Per consentire connessioni in ingresso sulla porta 80, per il traffico Web, aggiungere un'altra regola del gruppo di sicurezza di rete. L'esempio seguente crea una regola denominata *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Esaminare il gruppo di sicurezza di rete e le regole con [az network nsg show](/cli/azure/network/nsg):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Output:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Creare una scheda di rete virtuale
Le schede di interfaccia di rete virtuale sono disponibili a livello di programmazione in quanto è possibile applicare delle regole di uso. In base alle [dimensioni della macchina virtuale](../sizes.md), è possibile associare alla macchina virtuale più schede di interfaccia di rete virtuale. Nel comando seguente [az network nic create](/cli/azure/network/nic) si crea una scheda di interfaccia di rete denominata *myNic* e la si associa al gruppo di sicurezza di rete. L'indirizzo IP pubblico *myPublicIP* è associato anche a una scheda di interfaccia di rete virtuale.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Output:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Creare un set di disponibilità
I set di disponibilità agevolano la distribuzione delle macchine virtuali nei domini di errore e nei domini di aggiornamento. Anche se si crea una sola macchina virtuale al momento, è consigliabile usare i set di disponibilità in modo da semplificarne l'espansione in futuro. 

I domini di errore definiscono un gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra un massimo di tre domini di errore. Un problema hardware in uno di questi domini di errore non influenza tutte le macchine virtuali che eseguono l'app.

I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. Durante la manutenzione pianificata, i domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale, ma ne viene riavviato uno solo alla volta.

Azure distribuisce automaticamente le macchine virtuali tra i domini di errore e di aggiornamento durante il posizionamento in un set di disponibilità. Per altre informazioni, vedere [Gestione della disponibilità delle macchine virtuali](manage-availability.md).

Creare un set di disponibilità per la macchina virtuale con [az vm availability-set create](/cli/azure/vm/availability-set). L'esempio seguente crea un set di disponibilità denominato *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

L'output elenca i domini di errore e i domini di aggiornamento:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Creare una macchina virtuale
Sono state create le risorse di rete per supportare le VM accessibili tramite Internet. A questo punto, creare una macchina virtuale e proteggerla con la chiave SSH. In questo esempio si creerà una macchina virtuale Ubuntu basata sull'LTS più recente. È possibile trovare immagini aggiuntive con [az vm image list](/cli/azure/vm/image), come descritto nell'articolo sulla [ricerca di immagini della macchina virtuale di Azure](cli-ps-findimage.md).

Specificare una chiave SSH da usare per l'autenticazione. Se non si dispone di una coppia di chiavi pubblica SSH, è possibile [crearla](mac-create-ssh-keys.md) o utilizzare il parametro `--generate-ssh-keys` per crearla automaticamente. Se si dispone già di una coppia di chiavi, questo parametro usa le chiavi esistenti in `~/.ssh`.

Creare la macchina virtuale unendo tutte le risorse e le informazioni con il comando [az vm create](/cli/azure/vm). L'esempio seguente crea una VM denominata *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Eseguire l'autenticazione SSH alla macchina virtuale con la voce DNS indicata al momento della creazione dell'indirizzo IP pubblico. Questo `fqdn` viene mostrato nell'output durante la creazione della macchina virtuale:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Output:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

È possibile installare NGINX e vedere il flusso del traffico alla macchina virtuale. Installare NGINX come indicato di seguito:

```bash
sudo apt-get install -y nginx
```

Per visualizzare il sito NGINX predefinito in azione, aprire il Web browser e immettere il nome FQDN:

![Sito NGINX predefinito nella macchina virtuale](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Esportare come modello
È possibile creare ora un ambiente di sviluppo aggiuntivo usando gli stessi parametri oppure creare un ambiente di produzione compatibile? Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile [compilare modelli JSON manualmente](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) o esportare un ambiente esistente per creare il modello JSON. Usare [az group export](/cli/azure/group) per esportare il gruppo di risorse come segue:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Questo comando crea il file `myResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti tutti i nomi delle risorse. È possibile inserire questi nomi nel file del modello aggiungendo il parametro `--include-parameter-default-value` al comando `az group export`. Modificare il modello JSON per specificare i nomi delle risorse o [creare un parameters.jsin un file](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) che specifichi i nomi delle risorse.

Per creare un ambiente in base al modello, usare [az group deployment create](/cli/azure/group/deployment) come indicato di seguito:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Se lo si desidera, è possibile consultare [ulteriori informazioni su come eseguire la distribuzione partendo dai modelli](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). Ulteriori informazioni su come aggiornare gli ambienti in modo incrementale, utilizzare il file di parametri e accedere ai modelli da un unico percorso di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a utilizzare più componenti di rete e VM. È possibile utilizzare questo ambiente di esempio per compilare l'applicazione utilizzando i componenti principali presentati qui.