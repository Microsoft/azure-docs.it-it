---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179956"
---
### <a name="cacheskuname"></a>cacheSKUName

Piano tariffario del nuovo servizio Cache Redis di Azure.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Il modello definisce i valori consentiti per questo parametro (Basic, Standard o Premium) e assegna un valore predefinito (Basic) se non viene specificato alcun valore. Basic fornisce un singolo nodo con più dimensioni disponibili fino a 53 GB. Standard fornisce due nodi di replica/primario con più dimensioni disponibili fino a 53 GB e contratto di servizio con disponibilità del 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily

Famiglia dello SKU.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Dimensione della nuova istanza di Cache Redis di Azure.

Per le famiglie Basic e Standard:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

La capacità della cache dei valori Premium è definita uguale, ad eccezione dei valori consentiti compresi tra 1 e 5 anziché da 0 a 6.

Il modello definisce i valori interi consentiti per questo parametro (da 0 a 6 per le famiglie Basic e Standard; da 1 a 5 per la famiglia Premium). Se non viene specificato alcun valore, il modello assegna un valore predefinito pari a 0 per Basic e Standard, 1 per Premium.

I valori corrispondono alle seguenti dimensioni della cache:

| valore | Di base e standard<br>dimensione della cache | Premium<br>dimensione della cache |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (predefinito)                 | n/d                   |
| 1     | 1 GB                             | 6 GB (predefinito)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/d                   |
