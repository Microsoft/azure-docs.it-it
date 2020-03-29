---
title: Aggiornamento dello schema di analisi del traffico di Azure - Marzo 2020 Documenti Microsoft
description: Query di esempio con nuovi campi nello schema Analisi del traffico.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969069"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Query di esempio con nuovi campi nello schema Analisi del traffico (aggiornamento dello schema di agosto 2019)

Lo [schema del registro di analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) è stato aggiornato per includere i seguenti nuovi campi: **SrcPublicIPs_s** , **DestPublicIPs_s** **, NSGRule_s**. Nei prossimi mesi i seguenti campi meno recenti saranno deprecati: **VMIP_s**, **Subscription_g**, **Region_s** **, NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s** **, FlowCount_d**.
I nuovi campi forniscono informazioni sugli indirizzi IP di origine e di destinazione e semplificano le query.

Di seguito sono riportati tre esempi che mostrano come sostituire i vecchi campi con quelli nuovi.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Esempio 1 - VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Non è necessario dedurre i casi di origine e di destinazione per i flussi pubblici di Azure ed esterni da FlowDirection_s campo per i flussi AzurePublic ed ExternalPublic in modo specifico. Nel caso di un NVA (Network Virtual Appliance), il campo FlowDirection_s può essere inappropriato per essere utilizzato.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Esempio 2 - NSGRules_s

Il campo precedente era del formato: <Valore di indice 0)>>NSG_RULENAME<<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

In precedenza abbiamo usato per aggregare i dati tra NSG e NSGRules. Ora non aggregiamo. Pertanto, NSGList_s contiene un solo gruppo di sicurezza di base e NSGRules_s utilizzati anche per contenere una sola regola. Così abbiamo rimosso la formattazione complicata qui e lo stesso può essere trovato in altri campi come accennato di seguito:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Esempio 3 - FlowCount_d

Dal momento che non trattiamo i dati in tutto il gruppo di sicurezza, il FlowCount_d è semplicemente AllowedInFlows_d, DeniedInFlows_d, AllowedOutFlows_d e DeniedOutFlows_d.
Solo 1 dei precedenti 4 sarà diverso da zero e il resto tre sarà 0. E indicherebbe lo stato e il conteggio nella scheda di interfaccia di rete in cui è stato acquisito il flusso.

Se il flusso è stato consentito, verrà popolato uno dei campi con il prefisso "Consentito". Altrimenti verrà popolato un campo con il prefisso "Negato".
Se il flusso era in ingresso, verrà\_popolato uno dei campi con suffisso "d" come "InFlows_d". Else "OutFlows_d" verrà popolato.

A seconda delle 2 condizioni sopra, sappiamo quale su 4 sarà popolato.


## <a name="next-steps"></a>Passaggi successivi
Per ottenere risposte alle domande frequenti, vedere [Domande frequenti sull'analisi del traffico](traffic-analytics-faq.md) Per informazioni dettagliate sulle funzionalità, vedere la documentazione relativa all'analisi del [traffico](traffic-analytics.md)
