---
title: Configurare gli indirizzi IP privati per le macchine virtuali - Interfaccia della riga di comando di AzureConfigure private IP addresses for VMs - Azure CLI
description: Informazioni su come configurare indirizzi IP privati per le macchine virtuali usando l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199479"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configurare indirizzi IP privati per una macchina virtuale usando l'interfaccia della riga di comando di Azure


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono un ambiente semplice esistente. Se si desidera eseguire i comandi così come vengono visualizzati in questo documento, compilare innanzitutto l'ambiente di test descritto in [create a vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale

Per creare una VM denominata *DNS01* nella subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. Se questa operazione non è stata ancora eseguita, installare e configurare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e accedere a un account Azure usando il comando [az login](/cli/azure/reference-index).

2. Creare un IP pubblico per la VM con il comando [azure network public-ip create](/cli/azure/network/public-ip). Nell'elenco riportato dopo l'output sono indicati i parametri usati.

    > [!NOTE]
    > Potrebbe essere possibile o necessario usare valori diversi per gli argomenti in questo passaggio e nei passaggi successivi, a seconda dell'ambiente.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Output previsto:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: nome del gruppo di risorse in cui creare l'indirizzo IP pubblico.
   * `--name`: nome dell'indirizzo IP pubblico.
   * `--location`: area di Azure in cui creare l'indirizzo IP pubblico.

3. Eseguire il comando [az network nic create](/cli/azure/network/nic) per creare una scheda di interfaccia di rete con indirizzo IP privato statico. Nell'elenco riportato dopo l'output sono indicati i parametri usati. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Output previsto:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametri

    * `--private-ip-address`: indirizzo IP privato statico per la scheda di interfaccia di rete.
    * `--vnet-name`: nome della rete virtuale in cui creare la scheda di interfaccia di rete.
    * `--subnet`: nome della subnet in cui creare la scheda di interfaccia di rete.

4. Eseguire il comando [azure vm create](/cli/azure/vm/nic) per creare la VM usando l'indirizzo IP pubblico e il gruppo NIC creati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Output previsto:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parametri diversi dai parametri di base [az vm create](/cli/azure/vm).

   * `--nics`: nome della scheda di interfaccia di rete a cui è collegata la VM.
   
È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-cli.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private).

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperare le informazioni relative all'indirizzo IP privato statico per una macchina virtuale

Eseguire il comando seguente dell'interfaccia della riga di comando di Azure per osservare i valori di *Private IP alloc-method* e *Private IP address*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Output previsto:

```json
"192.168.1.101"
```

Per visualizzare informazioni sull'IP specifico della scheda di interfaccia di rete, eseguire una query sulla scheda di interfaccia di rete specifica:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Il risultato sarà simile al seguente:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Rimuovere un indirizzo IP statico privato da una macchina virtuale

Non è possibile rimuovere un indirizzo IP privato statico da una scheda di interfaccia di rete nell'interfaccia della riga di comando di Azure per le distribuzioni di Azure Resource Manager. È necessario:
- Creare una nuova scheda di interfaccia di rete che usa un indirizzo IP dinamico
- Impostare la scheda di rete della VM sulla scheda di rete appena creata. 

Per cambiare la scheda di interfaccia di rete per la VM usata nei comandi precedenti, seguire questa procedura:

1. Eseguire il comando **azure network nic create** per creare una nuova scheda di interfaccia di rete usando l'allocazione di IP dinamica con un nuovo indirizzo IP. Poiché non è specificato alcun indirizzo IP, il metodo di allocazione è **dinamico**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Output previsto:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Eseguire il comando **azure vm set** per cambiare il gruppo NIC usato dalla VM.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Output previsto:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Se le dimensioni della VM consentono di includere più schede di interfacce di rete, eseguire il comando **azure network nic delete** per eliminare la vecchia scheda.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire le [impostazioni dell'indirizzo IP](virtual-network-network-interface-addresses.md).
