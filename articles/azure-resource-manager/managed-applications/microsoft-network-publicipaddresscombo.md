---
title: Elemento PublicIpAddressCombo dell'interfaccia utente
description: Illustra l'elemento Microsoft.Network.PublicIpAddressCombo dell'interfaccia utente per il portale di Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651917"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento Microsoft.Network.PublicIpAddressCombo dell'interfaccia utente

Gruppo di controlli per la selezione di un indirizzo IP pubblico nuovo o esistente.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se l'utente seleziona "Nessuno" come indirizzo IP pubblico, la casella di testo dell'etichetta del nome di dominio viene nascosta.
- Se l'utente seleziona un indirizzo IP pubblico esistente, la casella di testo dell'etichetta del nome di dominio viene disabilitata e il valore corrisponderà all'etichetta del nome di dominio dell'indirizzo IP selezionato.
- Il suffisso del nome di dominio (ad esempio, westus.cloudapp.azure.com) viene aggiornato automaticamente in base alla posizione selezionata.

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

Se l'utente non seleziona alcun indirizzo IP pubblico, il controllo restituisce l'output seguente:

```json
{
  "newOrExistingOrNone": "none"
}
```

Se l'utente seleziona un indirizzo IP nuovo o esistente, il controllo restituisce l'output seguente:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Quando `options.hideNone` viene specificato **true**, `newOrExistingOrNone` avrà un valore **nuovo** o **esistente**.
- Quando `options.hideDomainNameLabel` viene specificato **true**, non `domainNameLabel` è dichiarato.

## <a name="remarks"></a>Osservazioni

- Se `constraints.required.domainNameLabel` è impostato su **true**, l'utente deve specificare un'etichetta di nome di dominio quando crea un nuovo indirizzo IP pubblico. Gli indirizzi IP pubblici esistenti senza etichetta non sono disponibili per la selezione.
- Se `options.hideNone` è impostato su **true**, l'opzione per selezionare **Nessuno** per l'indirizzo IP pubblico viene nascosta. Il valore predefinito è **false**.
- Se `options.hideDomainNameLabel` è impostato su **true**, la casella di testo per l'etichetta del nome di dominio viene nascosta. Il valore predefinito è **false**.
- Se `options.hideExisting` è true, l'utente non può scegliere un indirizzo IP pubblico esistente. Il valore predefinito è **false**.
- Per `zone`, sono disponibili solo gli indirizzi IP pubblici per la zona specificata o gli indirizzi IP pubblici resilienti per la zona.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
