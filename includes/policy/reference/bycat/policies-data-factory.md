---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a608d1c31b1b68d0d02a1dc5cef3cc370887e57e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866753"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le data factory di Azure devono essere crittografate con una chiave gestita dal cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Usare le chiavi gestite dal cliente per gestire la crittografia nel resto del Azure Data Factory. Per impostazione predefinita, i dati dei clienti vengono crittografati con chiavi gestite dal servizio, ma le chiavi gestite dal cliente sono in genere necessarie per soddisfare gli standard di conformità alle normative. Le chiavi gestite dal cliente consentono di crittografare i dati con una chiave Azure Key Vault creata e di proprietà dell'utente. L'utente dispone del controllo completo e della piena responsabilità in merito al ciclo di vita della chiave, incluse le operazioni di rotazione e gestione. Per altre informazioni, vedere [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) . |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[\[Anteprima: \] Azure Data Factory runtime di integrazione deve avere un limite per il numero di core](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85bb39b5-2f66-49f8-9306-77da3ac5130f) |Per gestire le risorse e i costi, limitare il numero di core per un runtime di integrazione. |Audit, Deny, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/IR_Core_Count_Exceeds_Audit.json) |
|[\[Anteprima: \] Azure Data Factory tipo di risorsa del servizio collegato deve essere in elenco consenti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6809a3d0-d354-42fb-b955-783d207c62a8) |Definire l'elenco consenti di Azure Data Factory di servizi collegati. La limitazione dei tipi di risorse consentiti consente il controllo sul limite dello spostamento dei dati. Ad esempio, limitare un ambito per consentire solo l'archiviazione BLOB con Data Lake Storage Gen1 e Gen2 per l'analisi o un ambito per consentire solo l'accesso a SQL e Kusto per le query in tempo reale. |Audit, Deny, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_ResourceType_Audit.json) |
|[\[Anteprima: \] i Azure Data Factory collegati devono usare Key Vault per l'archiviazione dei segreti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F127ef6d7-242f-43b3-9eef-947faf1725d0) |Per garantire che i segreti(ad esempio le stringhe di connessione) siano gestiti in modo sicuro, richiedere agli utenti di fornire segreti usando un Azure Key Vault anziché specificarli inline nei servizi collegati. |Audit, Deny, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_InlineSecrets_Audit.json) |
|[\[Anteprima: Azure Data Factory servizi collegati devono usare l'autenticazione dell'identità gestita assegnata dal sistema \] quando è supportata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff78ccdb4-7bf4-4106-8647-270491d2978a) |L'uso dell'identità gestita assegnata dal sistema durante la comunicazione con gli archivi dati tramite servizi collegati evita l'uso di credenziali meno protette, ad esempio password o stringhe di connessione. |Audit, Deny, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_All_Auth_Audit_except_MSI.json) |
|[\[Anteprima: \] Azure Data Factory usare un repository Git per il controllo del codice sorgente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77d40665-3120-4348-b539-3192ec808307) |Abilitare il controllo del codice sorgente nelle data factory per ottenere funzionalità quali rilevamento delle modifiche, collaborazione, integrazione continua e distribuzione. |Audit, Deny, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/Factory_None_GIT_Audit.json) |
|[Azure Data Factory usare il collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Collegamento privato di Azure consente di connettere la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione. La piattaforma Collegamento privato gestisce la connettività tra il consumer e i servizi attraverso la rete backbone di Azure. Tramite il mapping di endpoint privati Azure Data Factory, i rischi di perdita dei dati vengono ridotti. Per altre informazioni sui collegamenti privati, vedere: [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link) . |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Configurare data factory per disabilitare l'accesso alla rete pubblica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08b1442b-7789-4130-8506-4f99a97226a7) |Disabilitare l'accesso alla rete pubblica Data Factory in modo che non sia accessibile tramite Internet pubblico. Ciò può ridurre i rischi di perdita dei dati. Per altre informazioni, vedere [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link). |Modify, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Modify.json) |
|[Configurare zone DNS private per endpoint privati che si connettono a Azure Data Factory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86cd96e1-1745-420d-94d4-d3f2fe415aa4) |DNS privato record consentono connessioni private a endpoint privati. Le connessioni endpoint privato consentono comunicazioni sicure abilitando la connettività privata al Azure Data Factory senza la necessità di indirizzi IP pubblici nell'origine o nella destinazione. Per altre informazioni sugli endpoint privati e le zone DNS in Azure Data Factory, vedere [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link) . |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateDnsZones_DeployIfNotExists.json) |
|[Configurare endpoint privati per data factory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496ca26b-f669-4322-a1ad-06b7b5e41882) |Gli endpoint privati connettono la rete virtuale ai servizi di Azure senza un indirizzo IP pubblico nell'origine o nella destinazione.  Tramite il mapping di endpoint privati al Azure Data Factory, è possibile ridurre i rischi di perdita dei dati.  Per altre informazioni, vedere [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link). |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_DeployIfNotExists.json) |
|[L'accesso alla rete pubblica Azure Data Factory deve essere disabilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cf164be-6819-4a50-b8fa-4bcaa4f98fb6) |La disabilitazione della proprietà di accesso alla rete pubblica migliora la sicurezza assicurando che Azure Data Factory sia accessibile solo da un endpoint privato. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Audit.json) |
|[SQL Server Integration Services runtime di integrazione Azure Data Factory essere aggiunti a una rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0088bc63-6dee-4a9c-9d29-91cfdc848952) |La distribuzione di Rete virtuale di Azure offre sicurezza e isolamento ottimizzati per i runtime di integrazione di SQL Server Integration Services in Azure Data Factory, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/SSISIR_JoinVirtualNetwork_Audit.json) |
