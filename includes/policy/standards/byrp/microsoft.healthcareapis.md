---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 72fb4efc7a30ebaa3299f4c479bc02ad4b6f5386
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862139"
---
## <a name="cmmc-level-3"></a>CMMC Livello 3

Per esaminare il mapping delle Criteri di Azure disponibili per tutti i servizi di Azure a questo standard di conformità, vedere Criteri di Azure [Regulatory Compliance - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Per altre informazioni su questo standard di conformità, vedere [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |AC.1.001 |Limitare l'accesso al sistema di informazioni agli utenti autorizzati, ai processi che agiscono per conto di utenti autorizzati e ai dispositivi (inclusi altri sistemi informatici). |[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Controllo di accesso |AC.1.002 |Limitare l'accesso del sistema in informazioni ai tipi di transazioni e funzioni che gli utenti autorizzati possono eseguire. |[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Controllo di accesso |AC.2.016 |Controllare il flusso di CUI in conformità alle autorizzazioni approvate. |[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Gestione della configurazione |CM.3.068 |Limitare, disabilitare o impedire l'uso di programmi, funzioni, porte, protocolli e servizi non essenziali. |[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Protezione del sistema e delle comunicazioni |SC.3.177 |Usare la crittografia convalidata da FIPS quando viene usata per proteggere la riservatezza di CUI. |[API di Azure per FHIR usare una chiave gestita dal cliente per crittografare i dati in pausa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Protezione del sistema e delle comunicazioni |SC.3.183 |Negare il traffico di comunicazione di rete per impostazione predefinita e consentire il traffico di comunicazione di rete in base all'eccezione( ad esempio, negare tutto, consentire in base all'eccezione). |[CORS non deve consentire a tutti i domini di accedere all'API per FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

