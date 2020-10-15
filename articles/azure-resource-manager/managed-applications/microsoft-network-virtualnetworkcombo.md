---
title: Elemento VirtualNetworkCombo dell'interfaccia utente
description: Illustra l'elemento Microsoft.Network.VirtualNetworkCombo dell'interfaccia utente per il portale di Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033207"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento Microsoft.Network.VirtualNetworkCombo dell'interfaccia utente

Gruppo di controlli per la selezione di una rete virtuale nuova o esistente.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Quando l'utente seleziona una nuova rete virtuale, può personalizzare il nome di ogni subnet e il prefisso dell'indirizzo. La configurazione delle subnet è facoltativa.

![Nuovo Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Quando l'utente seleziona una rete virtuale esistente, deve eseguire il mapping di ogni subnet richiesta dal modello di distribuzione a una subnet esistente. In questo caso la configurazione delle subnet è obbligatoria.

![Microsoft.Network.VirtualNetworkCombo esistente](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Commenti

- Se specificato, il primo prefisso di indirizzo non sovrapposto di dimensioni pari a `defaultValue.addressPrefixSize` viene determinato automaticamente in base alle reti virtuali esistenti nella sottoscrizione dell'utente.
- Il valore predefinito per `defaultValue.name` e `defaultValue.addressPrefixSize` è **null**.
- Specificare `constraints.minAddressPrefixSize`. Le reti virtuali esistenti con uno spazio indirizzi inferiore al valore specificato non sono disponibili per la selezione.
- Specificare `subnets` e specificare `constraints.minAddressPrefixSize` per ogni subnet.
- Quando si crea una nuova rete virtuale, il prefisso dell'indirizzo di ogni subnet viene calcolato automaticamente in base al prefisso dell'indirizzo della rete virtuale e al rispettivo `addressPrefixSize`.
- Quando si usa una rete virtuale esistente, le subnet di dimensioni inferiori al rispettivo `constraints.minAddressPrefixSize` non sono disponibili per la selezione. Se specificato, le subnet che non contengono almeno `minAddressCount` indirizzi disponibili non sono disponibili per la selezione. Il valore predefinito è **0**. Per assicurarsi che gli indirizzi disponibili siano contigui, specificare **true** per `requireContiguousAddresses`. Il valore predefinito è **true**.
- La creazione di subnet in una rete virtuale esistente non è supportata.
- Se `options.hideExisting` è **true**, l'utente non può scegliere una rete virtuale esistente. Il valore predefinito è **false**.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
