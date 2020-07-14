---
title: Panoramica della gestione di Azure - Governance di Azure
description: Panoramica delle aree di gestione di applicazioni e risorse di Azure con collegamenti a contenuti sugli strumenti di gestione di Azure.
ms.date: 07/06/2020
ms.topic: overview
ms.openlocfilehash: 81d655db706a7330fc541724d490a4885cc2fe8b
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041915"
---
# <a name="what-are-the-azure-management-areas"></a>Che cosa sono le aree di gestione di Azure?

La governance in Azure è un aspetto della gestione di Azure. Questo articolo illustra le diverse aree di gestione per la distribuzione e la manutenzione delle risorse in Azure.

Per gestione si intendono le attività e i processi necessari per gestire le applicazioni aziendali e le risorse che le supportano. Azure offre molti strumenti e servizi che interagiscono per offrire la gestione completa. Questi servizi non sono solo per le risorse di Azure, ma anche in altri cloud e risorse locali. La comprensione dei diversi strumenti e del modo in cui possono essere usati in combinazione costituisce il primo passaggio della progettazione di un ambiente di gestione completo.

Il diagramma seguente illustra le diverse aree della gestione necessarie per gestire eventuali applicazioni o risorse. Tali diverse aree possono essere considerate come un ciclo di vita. Ogni area è necessaria in successione continua per l'intera durata di una risorsa. A partire dalla distribuzione iniziale, fino al funzionamento continuo e infine al ritiro della risorsa.

:::image type="content" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Discipline della gestione in Azure" border="false":::

Nessun singolo servizio di Azure soddisfa completamente i requisiti di un'area di gestione specifica. Al contrario, ognuno viene realizzato mediante numerosi servizi che interagiscono l’uno con l’altro. Alcuni servizi, come Application Insights, offrono funzionalità mirate di monitoraggio per le applicazioni Web. Altri, come i log di Monitoraggio di Azure, archiviano dati di gestione per altri servizi. Questa funzionalità consente di analizzare i dati di diverso tipo raccolti dai molteplici servizi.

Le sezioni seguenti descrivono brevemente le diverse aree di gestione e includono collegamenti a contenuti dettagliati sui servizi di Azure principali specifici per ogni area.

## <a name="monitor"></a>Monitorare

Il monitoraggio comporta la raccolta e l'analisi dei dati per controllare le prestazioni, l'integrità e la disponibilità delle risorse. Una strategia di monitoraggio efficace aiuta a comprendere l'operazione dei componenti e ad aumentare il tempo di attività con le notifiche. Leggere una panoramica del Monitoraggio che illustra i diversi servizi usati per [Monitoraggio di applicazioni e risorse di Azure](../azure-monitor/overview.md).

## <a name="configure"></a>Configurare

Per configurazione si intendono la distribuzione e la configurazione iniziali di risorse e manutenzione continuativa.
L'automazione di queste attività consente di eliminare la ridondanza, riducendo al minimo il tempo e il lavoro richiesti, aumentando la precisione e l'efficienza. [Automazione di Azure](../automation/automation-intro.md) fornisce la maggior parte dei servizi per l'automazione delle attività di configurazione. Mentre i runbook gestiscono l'automazione dei processi, la configurazione e la gestione dell'aggiornamento assistono nel gestire la configurazione.

## <a name="govern"></a>Governance

La governance offre meccanismi e processi per mantenere il controllo delle applicazioni e delle risorse in Azure. Include la pianificazione delle iniziative e la definizione delle priorità strategiche.
La governance in Azure viene implementata principalmente tramite due servizi. [Criteri di Azure](./policy/overview.md) consente di creare, assegnare e gestire le definizioni dei criteri per applicare le regole alle risorse.
Questa funzionalità consente di mantenere tali risorse conformi agli standard dell'azienda.
[Gestione costi di Azure](../cost-management-billing/cost-management-billing-overview.md) consente di tenere traccia dell'uso del cloud e delle spese per le risorse di Azure e altri provider di servizi cloud.

## <a name="secure"></a>Sicurezza

Valutare la sicurezza delle risorse e dei dati. Un programma di sicurezza comprende la valutazione delle minacce, la raccolta e l'analisi dei dati e la conformità di applicazioni e risorse. Il monitoraggio della sicurezza e l'analisi delle minacce vengono forniti dal [Centro sicurezza di Azure](../security-center/security-center-intro.md), che include la gestione unificata della sicurezza e la protezione avanzata dalle minacce in carichi di lavoro cloud ibridi. Per informazioni complete sulla sicurezza e indicazioni sulla configurazione sicura delle risorse di Azure, vedere [Introduzione alla sicurezza in Azure](../security/fundamentals/overview.md).

## <a name="protect"></a>Proteggere

La protezione si riferisce al mantenimento delle applicazioni e dei dati disponibili, anche in caso di interruzioni che sfuggono al controllo. La protezione in Azure viene fornita da due servizi. [Backup di Azure](../backup/backup-overview.md) fornisce il backup e il ripristino dei dati, nel cloud o in locale. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) fornisce continuità aziendale e ripristino immediato durante un'emergenza.

## <a name="migrate"></a>Migrazione

Per migrazione si intende la transizione nel cloud di Azure dei carichi di lavoro attualmente in esecuzione in locale.
[Azure Migrate](../migrate/migrate-services-overview.md) è un servizio che consente di valutare l'idoneità della migrazione in Azure delle macchine virtuali locali. Azure Site Recovery effettua la migrazione delle macchine virtuali [dall'ambiente locale](../site-recovery/migrate-tutorial-on-premises-azure.md) o [da Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migrazione del database di Azure](../dms/dms-overview.md) fornisce supporto per la migrazione di più origini di database nelle piattaforme dati di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla governance di Azure, vedere gli articoli seguenti:

- [Hub di Governance di Azure](./index.yml).
- [Governance in Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/govern/)
