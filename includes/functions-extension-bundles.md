---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010476"
---
Il modo più semplice per installare le estensioni di binding è consentire [aggregazioni di estensione](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Quando si abilitano i bundle, viene installato automaticamente un set predefinito di pacchetti di estensioni.

Per abilitare i bundle di estensioni, aprire il file host.json e aggiornarne il contenuto in modo che corrisponda al codice seguente:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```