---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4f9711ddf6501273c9ed9ebb2c5c0e142d8e0096
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098580"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La cache di Azure per Redis deve risiedere all'interno di una rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |La distribuzione di rete virtuale di Azure offre sicurezza e isolamento avanzati per la cache di Azure per Redis, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Quando una cache di Azure per l'istanza di redis è configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo dalle macchine virtuali e dalle applicazioni all'interno della rete virtuale. |Audit, Deny, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Devono essere abilitate solo le connessioni sicure alla cache di Azure per Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Controllare l'abilitazione delle sole connessioni tramite SSL a Cache Redis. L'uso di connessioni sicure garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
