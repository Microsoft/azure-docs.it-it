---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "72329568"
---
È stata già creata un'app per le funzioni in Azure, oltre al necessario account di archiviazione. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. In questo articolo verranno scritti messaggi in una coda di archiviazione dello stesso account. Per connettersi all'account di archiviazione durante l'esecuzione della funzione in locale, è necessario scaricare le impostazioni dell'app nel file local.settings.json. 

Dalla radice del progetto eseguire il comando di Azure Functions Core Tools seguente per scaricare le impostazioni nel file local.settings.json, sostituendo `<APP_NAME>` con il nome dell'app per le funzioni specificato nell'articolo precedente:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Potrebbe essere necessario accedere all'account Azure.

> [!IMPORTANT]  
> Questo comando sovrascrive le impostazioni esistenti con i valori dell'app per le funzioni in Azure.  
>
> Il file local.settings.json contiene segreti, quindi non viene mai pubblicato e dovrà essere escluso dal controllo del codice sorgente.  
> 

È necessario il valore `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Usare questa connessione per verificare se il binding di output funziona come previsto.
