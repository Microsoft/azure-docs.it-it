---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a4fe06d3ef31c9e19d5c721e75e9ca75a9cbd06
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498857"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Anteprima: \] Controlla le Azure Spring Cloud in cui la traccia distribuita non è abilitata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Gli strumenti di traccia distribuita in Azure Spring Cloud consentono adi eseguire il debug e il monitoraggio delle complesse interconnessioni tra i microservizi in un'applicazione. Gli strumenti di traccia distribuita devono essere abilitati e in uno stato integro. |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud deve usare l'aggiunta alla rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Le istanze di Azure Spring Cloud devono usare l'aggiunta alla rete virtuale per gli scopi seguenti: 1. Isolare Azure Spring Cloud da Internet. 2. Consentire ad Azure Spring Cloud di interagire con i sistemi nei data center locali o con il servizio Azure in altre reti virtuali. 3. Consentire ai clienti di controllare le comunicazioni di rete in ingresso e in uscita per Azure Spring Cloud. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
