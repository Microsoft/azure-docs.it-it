---
title: Benchmark di sicurezza di Azure V2-gestione delle risorse
description: Gestione delle risorse di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbeb88b6aa542666481458fde97d7c63f467fa30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051532"
---
# <a name="security-control-v2-asset-management"></a>Controllo di sicurezza V2: gestione delle risorse

Gestione asset copre i controlli per garantire la visibilità e la governance della sicurezza sulle risorse di Azure. Sono incluse le raccomandazioni sulle autorizzazioni per il personale di sicurezza, l'accesso alla sicurezza per l'inventario degli asset e la gestione delle approvazioni per servizi e risorse (inventario, traccia e corrette).

Per visualizzare i criteri di Azure predefiniti applicabili, vedere la pagina relativa ai [Dettagli dell'iniziativa incorporata relativa alla conformità normativa per il benchmark di sicurezza di Azure: sicurezza di rete](../../governance/policy/samples/azure-security-benchmark.md#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: assicurarsi che il team di sicurezza abbia visibilità dei rischi per gli asset

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Assicurarsi che i team di sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza

A seconda del modo in cui sono strutturate le responsabilità del team di sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabile di un team di sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi.

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../../governance/management-groups/overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Assicurarsi che i team di sicurezza abbiano accesso a un inventario continuo aggiornato delle risorse in Azure. I team di sicurezza spesso necessitano di questo inventario per valutare la potenziale esposizione dell'organizzazione ai rischi emergenti e come input per miglioramenti continui della sicurezza. 

La funzionalità di inventario del Centro sicurezza di Azure e il grafico risorse di Azure possono eseguire query e individuare tutte le risorse nelle sottoscrizioni, inclusi i servizi, le applicazioni e le risorse di rete di Azure.

Organizzare logicamente gli asset in base alla tassonomia dell'organizzazione usando i tag, nonché altri metadati in Azure (nome, descrizione e categoria).

- [Come creare query con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Gestione inventario asset del Centro sicurezza di Azure](../../security-center/asset-inventory.md)

- [Per ulteriori informazioni sull'assegnazione di tag agli asset, vedere la guida alla decisione relativa alla denominazione delle risorse e all'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-3 | 2.3, 2.4 | CM-7, CM-8 |

Usare Criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Configurare e gestire i criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../../governance/policy/samples/index.md)

- [Come creare query con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Definire o aggiornare i criteri di sicurezza che affrontano i processi di gestione del ciclo di vita delle risorse per apportare modifiche potenzialmente elevate come ad esempio modifiche a: provider di identità e accessi, sensibilità dei dati, configurazione della rete e assegnazione dei privilegi amministrativi.

Rimuovere le risorse di Azure quando non sono più necessarie.

- [Eliminare un gruppo di risorse di Azure e le relative risorse](../../azure-resource-manager/management/delete-resource-group.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-5 | 2,9 | AC-3 |

Usare Azure AD accesso condizionale per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usare solo le applicazioni approvate nelle risorse di calcolo

| ID Azure | Controlli CIS v 7.1 ID/i | ID del NIST SP 800-53 R4 |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato non venga eseguita nelle macchine virtuali di Azure.

Usare i controlli applicazione adattivi del Centro sicurezza di Azure (ASC) per individuare e generare un elenco di applicazioni consentite. È anche possibile usare ASC Adaptive Application Controls per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

Usare Rilevamento modifiche e inventario di automazione di Azure per automatizzare la raccolta di informazioni di inventario dalle macchine virtuali Windows e Linux. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere la data di installazione del software e altre informazioni, abilitare la diagnostica a livello di Guest e indirizzare i registri eventi di Windows in Log Analytics area di lavoro.

A seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure.

È anche possibile usare una soluzione di terze parti per individuare e identificare il software non approvato.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

- [Informazioni su Rilevamento modifiche e inventario di automazione di Azure](../../automation/change-tracking/overview.md)

- [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
