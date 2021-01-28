---
title: Creare un IP pubblico-interfaccia della riga di comando di Azure
description: Informazioni su come creare un indirizzo IP pubblico usando l'interfaccia della riga di comando di Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d2f339f5a1339b5f249172170fed54c91f1dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927193"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Guida introduttiva: creare un indirizzo IP pubblico usando l'interfaccia della riga di comando

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando l'interfaccia della riga di comando di Azure. Per altre informazioni sulle risorse a cui questo può essere associato, sulla differenza tra lo SKU Basic e standard e altre informazioni correlate, vedere [indirizzi IP pubblici](./public-ip-addresses.md).  Per questo esempio, si concentrerà solo sugli indirizzi IPv4; per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per Azure VNet](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [AZ Group create](/cli/azure/group#az-group-create) denominato **myResourceGroup** nel percorso **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Crea IP pubblico

---
# <a name="standard-sku---using-zones"></a>[**SKU standard-uso di zone**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione API attualmente in uso, vedere [provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md).

Usare il comando [AZ Network Public-IP create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Per le versioni dell'API precedenti alla 2020-08-01, eseguire il comando precedente senza specificare un parametro di zona per creare un indirizzo IP con ridondanza della zona. 
>

Per creare un indirizzo IP pubblico di zona standard in Zona 2 denominato **myStandardZonalPublicIP** in **myResourceGroup**, usare il comando seguente:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Si noti che le opzioni precedenti per le zone sono selezioni valide solo nelle aree con [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU standard-nessuna zona**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione API attualmente in uso, vedere [provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md).

Usare il comando [AZ Network Public-IP create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico standard come risorsa non di zona denominata **myStandardPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Questa selezione è valida in tutte le aree ed è la selezione predefinita per gli indirizzi IP pubblici standard in aree senza [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

Usare il comando [AZ Network Public-IP create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico statico di base denominato **myBasicPublicIP** in **myResourceGroup**.  Gli indirizzi IP pubblici di base non hanno il concetto di zone di disponibilità.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Se è accettabile che l'indirizzo IP cambi nel tempo, è possibile selezionare l'assegnazione IP **dinamica** cambiando il metodo di allocazione in ' Dynamic '.

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altri dettagli sulle singole variabili elencate in precedenza, vedere [gestire gli indirizzi IP pubblici](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Passaggi successivi
- Associare un [indirizzo IP pubblico a una macchina virtuale](./associate-public-ip-address-vm.md#azure-portal).
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
