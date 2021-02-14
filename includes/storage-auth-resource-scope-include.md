---
title: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373752"
---
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo di Azure a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. In base alle procedure consigliate, è sempre preferibile concedere solo l'ambito più restrittivo possibile. I ruoli di controllo degli accessi in base al ruolo definiti in un ambito più ampio vengono ereditati dalle risorse sottostanti.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse BLOB e code di Azure, a partire dall'ambito più restrittivo:

- **Un singolo contenitore.** In questo ambito un'assegnazione di ruolo si applica a tutti i BLOB nel contenitore, nonché alle proprietà e ai metadati del contenitore.
- **Una singola coda.** In questo ambito un'assegnazione di ruolo si applica a tutti i messaggi nella coda, nonché alle proprietà e ai metadati della coda.
- **L'account di archiviazione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori e ai relativi BLOB oppure a tutte le code e ai relativi messaggi.
- **Il gruppo di risorse.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione del gruppo di risorse.
- **La sottoscrizione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione di tutti i gruppi di risorse della sottoscrizione.
- **Un gruppo di gestione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione di tutti i gruppi di risorse di tutte le sottoscrizioni nel gruppo di risorse.

Per altre informazioni sulle assegnazioni di ruoli e l'ambito, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../articles/role-based-access-control/overview.md)
