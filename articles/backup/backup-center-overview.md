---
title: Panoramica di Centro backup
description: Questo articolo fornisce una panoramica di backup Center per Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506055"
---
# <a name="overview-of-backup-center"></a>Panoramica di Centro backup

Il centro di backup offre una **singola esperienza di gestione unificata** in Azure per le aziende che governano, monitorano, operano e analizzano i backup su larga scala. Di conseguenza, è coerente con le esperienze di gestione native di Azure.

Di seguito sono riportati alcuni dei principali vantaggi offerti da backup Center:

* **Singolo riquadro di vetro per la gestione dei backup** : il centro di backup è progettato per funzionare correttamente in un ambiente di Azure di grandi dimensioni e distribuito. È possibile usare il centro di backup per gestire in modo efficiente i backup che si estendono su più tipi di carico di lavoro, insiemi di credenziali, sottoscrizioni, aree e tenant del [Faro di Azure](../lighthouse/overview.md) .
* **Gestione incentrata** sull'origine dati: il centro di backup offre visualizzazioni e filtri centrati sulle origini dati di cui si sta eseguendo il backup, ad esempio macchine virtuali e database. Questo consente a un proprietario di risorse o a un amministratore di backup di monitorare e gestire i backup degli elementi senza dover concentrarsi sull'insieme di credenziali in cui viene eseguito il backup di un elemento. Una funzionalità chiave di questa progettazione è la possibilità di filtrare le visualizzazioni in base alle proprietà specifiche dell'origine dati, ad esempio la sottoscrizione DataSource, il gruppo di risorse DataSource e i tag DataSource. Ad esempio, se l'organizzazione segue una procedura per l'assegnazione di tag diversi alle macchine virtuali appartenenti a reparti diversi, è possibile usare il centro di backup per filtrare le informazioni di backup in base ai tag delle macchine virtuali sottostanti di cui viene eseguito il backup senza dover concentrarsi sul tag dell'insieme di credenziali.
* **Esperienze connesse** : il centro di backup offre integrazioni native per i servizi di Azure esistenti che consentono la gestione su larga scala. Ad esempio, Backup Center USA l'esperienza dei [criteri di Azure](../governance/policy/overview.md) per gestire i backup. Sfrutta anche le [cartelle di lavoro di Azure](../azure-monitor/visualize/workbooks-overview.md) e i log di monitoraggio di [Azure](../azure-monitor/logs/data-platform-logs.md) per semplificare la visualizzazione di report dettagliati sui backup. Non è quindi necessario apprendere i nuovi principi per usare le varie funzionalità offerte dal centro di backup. È anche possibile individuare le risorse della community dal centro di backup.

## <a name="supported-scenarios"></a>Scenari supportati

* Il centro di backup è attualmente supportato per il backup di macchine virtuali di Azure, SQL nel backup di VM di Azure, SAP HANA nel backup di macchine virtuali di Azure, Backup File di Azure, backup BLOB di Azure, backup di Azure Managed Disks e backup del server di database di Azure per PostgreSQL.
* Per un elenco dettagliato degli scenari supportati e non supportati, vedere la [matrice di supporto](backup-center-support-matrix.md) .

## <a name="get-started"></a>Introduzione

Per iniziare a usare il centro di backup, cercare **centro di backup** nella portale di Azure e passare al dashboard del **centro di backup** .

![Ricerca nel centro di backup](./media/backup-center-overview/backup-center-search.png)

La prima schermata visualizzata è la **Panoramica**. Contiene due riquadri: **processi** e **istanze di backup**.

![Riquadri del centro di backup](./media/backup-center-overview/backup-center-overview-widgets.png)

Nel riquadro **Jobs (processi** ) si ottiene una vista riepilogativa di tutti i processi correlati al backup e al ripristino che sono stati attivati nella proprietà backup nelle ultime 24 ore. È possibile visualizzare informazioni sul numero di processi completati, non riusciti e in corso. Selezionando uno dei numeri in questo riquadro è possibile visualizzare altre informazioni sui processi per un tipo di origine dati, un tipo di operazione e uno stato specifici.

Nel riquadro **istanze di backup** si ottiene una vista riepilogativa di tutte le istanze di backup nell'area di backup. Ad esempio, è possibile visualizzare il numero di istanze di backup in stato di eliminazione temporanea rispetto al numero di istanze ancora configurate per la protezione. Selezionando uno dei numeri in questo riquadro è possibile visualizzare altre informazioni sulle istanze di backup per un tipo di origine dati e uno stato di protezione specifici. È anche possibile visualizzare tutte le istanze di backup con origine dati sottostante non trovata (l'origine dati potrebbe essere eliminata o potrebbe non essere possibile accedere all'origine dati).

Guardare il video seguente per comprendere le funzionalità di backup Center:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Segui i [passaggi successivi](#next-steps) per comprendere le diverse funzionalità offerte da backup Center e come usare queste funzionalità per gestire in modo efficiente il tuo patrimonio di backup.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire i backup](backup-center-monitor-operate.md)
* [Governare la proprietà di backup](backup-center-govern-environment.md)
* [Ottenere informazioni dettagliate sui backup](backup-center-obtain-insights.md)
* [Eseguire azioni con backup Center](backup-center-actions.md)