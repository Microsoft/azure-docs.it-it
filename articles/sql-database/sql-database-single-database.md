---
title: Che cos'è un database singolo
description: Informazioni sull'opzione database singolo del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79500751"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Informazioni sull'opzione database singolo del database SQL di Azure

L'opzione di distribuzione database singolo crea un database nel database SQL di Azure con il proprio set di risorse e viene gestita tramite un server di database SQL. Con un database singolo, ogni database è isolato dagli altri e portabile e a ognuno viene assegnato uno specifico livello di servizio all'interno del [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o del [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md), con dimensioni di calcolo garantite.

> [!IMPORTANT]
> Il database singolo è una delle tre opzioni di distribuzione per il database SQL di Azure. Le altre due sono i [pool elastici](sql-database-elastic-pool.md) e l'[istanza gestita](sql-database-managed-instance.md).
> [!NOTE]
> Per un glossario di termini relativi al database SQL di Azure, vedere [Glossario dei termini relativi al database SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Scalabilità dinamica

Puoi creare la tua prima app in un database di piccole dimensioni, a basso costo nel livello di calcolo senza server o in una piccola dimensione di calcolo nel livello di calcolo di cui è stato effettuato il provisioning. È possibile modificare il [livello di calcolo o di servizio](sql-database-single-database-scale.md) manualmente o a livello di codice in qualsiasi momento per soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

## <a name="single-databases-and-elastic-pools"></a>Database singoli e pool elastici

Un database singolo può essere spostato all'interno o all'esterno di un [pool elastico](sql-database-elastic-pool.md) per la condivisione delle risorse. Per molte aziende e applicazioni, la possibilità di creare singoli database e aumentare o ridurre le prestazioni all'occorrenza è sufficiente, specialmente se i modelli d'utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. I pool elastici sono stati progettati per risolvere questo problema. Il concetto è semplice. Si allocano le risorse relative alle prestazioni a un pool invece che a un database singolo e quindi si pagano le risorse relative alle prestazioni collettive del pool invece di pagare le prestazioni del database singolo.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

Usare gli strumenti di [monitoraggio delle prestazioni](sql-database-performance-guidance.md) e di [avviso](sql-database-insights-alerts-portal.md)predefiniti, combinati con le valutazioni delle prestazioni. Usando questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle prestazioni in base alle esigenze correnti o previste relative alle prestazioni. Inoltre, il database SQL può [emettere metriche e log delle risorse](sql-database-metrics-diag-logging.md) per semplificare il monitoraggio.

## <a name="availability-capabilities"></a>Funzionalità per la disponibilità

Tutti i database singoli, i pool elastici e le istanze gestite offrono molte caratteristiche di disponibilità. Per informazioni, vedere [Caratteristiche di disponibilità](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Differenze di Transact-SQL

La maggior parte delle funzionalità Transact-SQL usate dalle applicazioni è supportata in Microsoft SQL Server e nel database SQL di Azure. Ad esempio, i componenti SQL principali, come tipi di dati, operatori, funzioni di stringa, funzioni aritmetiche, logiche e del cursore funzionano allo stesso modo in SQL Server e nel database SQL. T-SQL presenta, tuttavia, alcune differenze negli elementi DDL (Data Definition Language) e DML (Data Manipulation Language), di conseguenza alcune istruzioni e query T-SQL sono supportate solo in parte (questo verrà descritto più avanti in questo articolo).
Inoltre, alcune funzionalità e sintassi non sono affatto supportate, perché il database SQL di Azure è progettato in modo da isolare le funzionalità dalle dipendenze nel database master e nel sistema operativo. Di conseguenza, la maggior parte delle attività a livello di server non è appropriata al database SQL. Le istruzioni T-SQL non sono disponibili se configurano opzioni a livello di server e componenti del sistema operativo o se specificano una configurazione del file system. Quando sono necessarie tali funzionalità, spesso è disponibile un'alternativa appropriata dal database SQL o da un'altra funzionalità o un altro servizio di Azure.

Per altre informazioni, vedere [Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Sicurezza

Il database SQL offre un'ampia gamma di [funzionalità predefinite per sicurezza e conformità](sql-database-security-overview.md) utili per fare in modo che le applicazioni possano soddisfare svariati requisiti di sicurezza e conformità.

> [!IMPORTANT]
> Il database SQL di Azure (tutte le opzioni di distribuzione) è stato certificato rispetto a una serie di standard di conformità. Per ulteriori informazioni, vedere la [Microsoft Azure Centro protezione](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare rapidamente a usare un singolo database, iniziare con la [Guida introduttiva a database singolo](sql-database-single-database-quickstart-guide.md).
- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](sql-database-single-database-migrate.md).
- Per informazioni sulle funzionalità supportate, vedere [funzionalità](sql-database-features.md).
