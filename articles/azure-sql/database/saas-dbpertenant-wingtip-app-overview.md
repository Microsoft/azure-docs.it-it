---
title: Esempio di app multi-tenant-SaaS Wingtip
description: Uso dell'esempio SaaS Wingtip, un'applicazione multi-tenant di esempio basata sul database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 0efc8105295ca7f97e6f465071c31b193173c5cd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588736"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Introduzione a un'app SaaS multi-tenant che usa il modello di database per tenant con il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

L'applicazione SaaS Wingtip è un'app multi-tenant di esempio. L'app usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'app presenta le funzionalità del database SQL di Azure che supportano gli scenari SaaS usando diversi modelli di progettazione e gestione SaaS. Per iniziare è sufficiente distribuire l'app SaaS Wingtip, operazione che richiede meno di cinque minuti.

Il codice sorgente dell'applicazione e gli script di gestione sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Prima di iniziare, leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script di gestione di Wingtip Tickets.

## <a name="application-architecture"></a>Architettura dell'applicazione

L'app SaaS Wingtip usa il modello di database per tenant. Essa usa pool elastici SQL per massimizzare l'efficienza. Per il provisioning e il mapping dei tenant ai dati, viene usato un database di catalogo. L'applicazione SaaS Wingtip principale usa un pool con tre tenant di esempio, oltre al database del catalogo. Per i server di catalogo e tenant è stato effettuato il provisioning con alias DNS. Questi alias vengono usati per mantenere un riferimento alle risorse attive usate dall'applicazione Wingtip. Tali alias sono aggiornati per puntare alle risorse di ripristino nelle esercitazioni sul ripristino di emergenza. Completando la maggior parte delle esercitazioni su Wingtip SaaS si potranno inserire dei componenti aggiuntivi all'implementazione iniziale. Vengono illustrati componenti aggiuntivi come i database analitici e la gestione dello schema del database.


![Architettura SaaS Wingtip](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Mentre si eseguono le esercitazioni e si usa l'app, è importante concentrarsi sui criteri SaaS perché sono correlati al livello dati. In altre parole, è necessario concentrarsi sul livello dati senza dedicare troppo tempo all'analisi dell'app stessa. Comprendere l'implementazione di questi criteri SaaS è fondamentale per l'implementazione degli stessi criteri nelle applicazioni. Considerare anche eventuali modifiche necessarie in base ai requisiti aziendali specifici.

## <a name="sql-database-wingtip-saas-tutorials"></a>Esercitazioni su SaaS con l'applicazione Wingtip e database SQL

Dopo avere distribuito l'app, sfogliare le esercitazioni seguenti, che si basano sull'implementazione iniziale. Queste esercitazioni illustrano i modelli SaaS comuni che sfruttano le funzionalità predefinite del database SQL, dell'analisi delle sinapsi di Azure e di altri servizi di Azure. Le esercitazioni includono script di PowerShell con spiegazioni dettagliate. Le spiegazioni semplificano la comprensione e l'implementazione degli stessi criteri di gestione SaaS nelle applicazioni.


| Esercitazione | Descrizione |
|:--|:--|
| [Materiale sussidiario e suggerimenti per l'esempio di app SaaS multi-tenant del database SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Scaricare ed eseguire gli script di PowerShell per preparare le parti dell'applicazione. |
|[Distribuire ed esplorare l'applicazione SaaS Wingtip](./saas-dbpertenant-get-started-deploy.md)|  Implementare e sfogliare l'applicazione SaaS Wingtip nella sottoscrizione di Azure. |
|[Effettuare il provisioning di tenant e registrarli nel catalogo](./saas-dbpertenant-provision-and-catalog.md)| Informazioni su come l'applicazione si connette ai tenant usando un database di catalogo e su come il catalogo esegue il mapping dei tenant ai dati. |
|[Monitorare e gestire le prestazioni](./saas-dbpertenant-performance-monitoring.md)| Informazioni su come usare le funzionalità di monitoraggio del database SQL e impostare avvisi quando vengono superate le soglie per le prestazioni. |
|[Monitorare con i log di Monitoraggio di Azure](./saas-dbpertenant-log-analytics.md) | Informazioni su come usare i [log di monitoraggio di Azure](../../azure-monitor/logs/log-query-overview.md) per monitorare grandi quantità di risorse in più pool. |
|[Ripristinare un tenant singolo](./saas-dbpertenant-restore-single-tenant.md)| Informazioni su come eseguire un ripristino temporizzato di un database tenant. È inclusa anche la procedura per ripristinare un database parallelo, lasciando il database tenant esistente online. |
|[Gestire lo schema del database tenant](saas-tenancy-schema-management.md)| Informazioni su come aggiornare lo schema e i dati di riferimento in tutti i database tenant. |
|[Eseguire query distribuite tra tenant](saas-tenancy-cross-tenant-reporting.md) | Creare un database di analisi ad hoc ed eseguire query distribuite in tempo reale su tutti i tenant.  |
|[Eseguire analisi sui dati del tenant estratti](saas-tenancy-tenant-analytics.md) | Estrarre i dati del tenant in un database di analisi o in un data warehouse per query di analisi offline. |


## <a name="next-steps"></a>Passaggi successivi

- [Indicazioni generali e suggerimenti per la distribuzione e l'uso dell'esempio di app SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Distribuire l'applicazione SaaS Wingtip](./saas-dbpertenant-get-started-deploy.md)