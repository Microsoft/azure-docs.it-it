---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b4a89e39ec0be07a600fca4d9cf8bce32bf9bff9
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094788"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Accesso con privilegi |PA-7 |Segui l'amministrazione sufficiente (principio dei privilegi minimi) |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Accesso con privilegi |PA-7 |Segui l'amministrazione sufficiente (principio dei privilegi minimi) |[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Protezione dei dati |4.6 |Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-110"></a>CIS Microsoft Azure Foundations Benchmark 1.1.0

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestione delle identità e dell'accesso |1.23 |Assicurarsi che non siano stati creati ruoli di proprietario della sottoscrizione personalizzati |[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>1.3.0 benchmark di base di CIS Microsoft Azure

Per esaminare il modo in cui i criteri di Azure disponibili predefiniti per tutti i servizi di Azure vengono mappati a questo standard di conformità, vedere [conformità normativa ai criteri di Azure-Microsoft Azure benchmark di](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)base di 1.3.0.
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestione delle identità e dell'accesso |1,21 |Assicurarsi che non siano stati creati ruoli di proprietario della sottoscrizione personalizzati |[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="cmmc-level-3"></a>Livello CMMC 3

Per verificare in che modo i criteri di Azure disponibili predefiniti per tutti i servizi di Azure siano mappati a questo standard di conformità, vedere conformità normativa per i criteri di Azure [-CMMC livello 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Per ulteriori informazioni su questo standard di conformità, vedere [Cybersecurity maturità Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |AC. 3.018 |Impedire agli utenti senza privilegi di eseguire funzioni con privilegi e acquisire l'esecuzione di tali funzioni nei log di controllo. |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa predefinita di conformità alle normative per HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Per altre informazioni su questo standard di conformità, vedere [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestione dei privilegi |1148.01c2System.78 - 01.c |L'organizzazione limita l'accesso a funzioni con privilegi e a tutte le informazioni pertinenti per la sicurezza. |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Separazione dei compiti |1230.09c2Organizational.1 - 09.c |Nessun utente singolo può accedere, modificare o usare i sistemi informatici senza autorizzazione o senza che venga rilevato. |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Separazione dei compiti |1276.09c2Organizational.2 - 09.c |Le attività di controllo della sicurezza sono indipendenti. |[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|Separazione dei compiti |1278.09c2Organizational.56 - 09.c |L'organizzazione identifica i compiti che richiedono la separazione e definisce le autorizzazioni di accesso al sistema informativo per supportare la separazione dei compiti; i compiti incompatibili sono separati tra più utenti per ridurre al minimo la possibilità di abusi o frodi. |[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Per esaminare il mapping tra i criteri di Azure disponibili predefiniti per tutti i servizi di Azure e questo standard di conformità, vedere [conformità alle normative di criteri di Azure-ISO 27001:2013](../../../../articles/governance/policy/samples/iso-27001.md).
Per ulteriori informazioni su questo standard di conformità, vedere [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |9.2.3 |Gestione dei diritti di accesso con privilegi |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Per altre informazioni su questo standard di conformità, vedere [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |AC-2 (7) |Gestione degli account \| Schemi basati sui ruoli |[Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

