---
title: espressione Resource () nella query del log di monitoraggio di Azure | Microsoft Docs
description: L'espressione di risorsa viene usata in una query di log di monitoraggio di Azure incentrata sulle risorse per recuperare dati da più risorse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619517"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>espressione Resource () nella query del log di monitoraggio di Azure

L' `resource` espressione viene usata in una query di monitoraggio di Azure con [ambito a una risorsa](scope.md#query-scope) per recuperare i dati da altre risorse. 


## <a name="syntax"></a>Sintassi

`resource(`*Identificatore*`)`

## <a name="arguments"></a>Argomenti

- *Identificatore*: ID risorsa di una risorsa.

| Identificatore | Descrizione | Esempio
|:---|:---|:---|
| Risorsa | Include i dati per la risorsa. | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Gruppo di risorse o sottoscrizione | Include i dati per la risorsa e tutte le risorse in esso contenute.  | risorsa ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Note

* È necessario avere accesso in lettura alla risorsa.


## <a name="examples"></a>Esempio

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate su un ambito di query [, vedere ambito della query di log e intervallo di tempo in monitoraggio di Azure log Analytics](scope.md) .
- Accedere alla documentazione completa relativa al [linguaggio di query Kusto](/azure/kusto/query/).
