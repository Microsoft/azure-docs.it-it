---
title: Controllo della sicurezza di Azure-Gestione inventario e asset
description: Inventario e gestione delle risorse di controllo della sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3079ea475c20e3ae0e78319d6c6b24ee579fd0c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521306"
---
# <a name="security-control-inventory-and-asset-management"></a>Controllo di sicurezza: inventario e gestione delle risorse

I consigli relativi all'inventario e alla gestione degli asset sono incentrati sulla risoluzione dei problemi relativi alla gestione attiva (inventario, verifica e correzione) di tutte le risorse di Azure, in modo da consentire l'accesso solo alle risorse autorizzate e le risorse non autorizzate e non gestite vengono identificate e rimosse.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Customer |

Usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.2 | 1.5 | Customer |

Applicare i tag alle risorse di Azure fornendo i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.3 | 1.6 | Customer |

Usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.4 | 2.1 | Customer |

Creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.7 | 2.5 | Customer |

Usare il monitoraggio dell'integrità dei file (Rilevamento modifiche) e l'inventario delle macchine virtuali del Centro sicurezza di Azure per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato per la rimozione di software non autorizzato. È anche possibile usare una soluzione di terze parti per identificare il software non approvato.

- [Come usare il monitoraggio dell'integrità dei file](../../security-center/security-center-file-integrity-monitoring.md)

- [Informazioni su Rilevamento modifiche di Azure](../../automation/change-tracking/overview.md)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.8 | 2.6 | Customer |

Usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.9 | 2.6 | Customer |

Usare i criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.10 | 2.7 | Customer |

Usare i controlli applicazione adattivi del Centro sicurezza di Azure per specificare i tipi di file a cui può essere applicata una regola.

Implementare una soluzione di terze parti se non soddisfa il requisito.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.11 | 2,9 | Customer |

Usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.12 | 2,9 | Customer |

A seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure.  È anche possibile usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

- [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 6.13 | 2,9 | Customer |

Il software necessario per le operazioni aziendali, ma può comportare un rischio maggiore per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o della rete virtuale e sufficientemente protetto con un firewall di Azure o un gruppo di sicurezza di rete.

- [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

- [Come creare una NSG con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [configurazione sicura](security-control-secure-configuration.md)