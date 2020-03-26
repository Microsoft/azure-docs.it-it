---
title: File di inclusione
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67180763"
---
## <a name="enable-event-grid-resource-provider"></a>Abilitare il provider di risorse di Griglia di eventi

Se è la prima volta che si usa Griglia di eventi nella sottoscrizione di Azure, è necessario registrare il provider di risorse di Griglia di eventi. Eseguire il comando seguente:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

La registrazione può richiedere qualche minuto. Per controllare lo stato, eseguire:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Quando `RegistrationStatus` è `Registered`, è possibile continuare.
