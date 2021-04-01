---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424716"
---
Eseguire il comando seguente per visualizzare i [log in streaming](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) quasi in tempo reale:

```console
func azure functionapp logstream <APP_NAME> 
```

In una finestra del terminale separata o nel browser chiamare di nuovo la funzione remota. Nel terminale viene visualizzato un log dettagliato dell'esecuzione della funzione in Azure. 