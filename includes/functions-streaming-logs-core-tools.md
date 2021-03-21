---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020357"
---
#### <a name="built-in-log-streaming"></a>Streaming dei log predefinito

Usare l'opzione `logstream` per iniziare a ricevere i log in streaming di una specifica app per le funzioni in esecuzione in Azure, come nell'esempio seguente:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Lo streaming di log predefinito non è ancora abilitato in Core Tools per le app per le funzioni in esecuzione in Linux con un piano a consumo. Per questi piani di hosting, è invece necessario usare Live Metrics Stream per visualizzare i log quasi in tempo reale.

#### <a name="live-metrics-stream"></a>Flusso di metriche live

È possibile visualizzare [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) per l'app per le funzioni in una nuova finestra del browser includendo l'opzione `--browser`, come nell'esempio seguente:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
