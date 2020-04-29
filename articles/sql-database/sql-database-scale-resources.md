---
title: Ridimensionare le risorse
description: Questo articolo spiega come ridimensionare il database aggiungendo o eliminando le risorse allocate.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76835913"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Ridimensionare in modo dinamico le risorse di database con tempo di inattività minimo

Il database SQL di Azure consente di aggiungere in modo dinamico altre risorse al database con [tempi di inattività](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)minimi. Tuttavia, esiste un passaggio rispetto al periodo in cui la connettività viene persa nel database per un breve intervallo di tempo, che può essere mitigato usando la logica di ripetizione dei tentativi.

## <a name="overview"></a>Panoramica

Quando la domanda per la tua app cresce da qualche dispositivo e qualche cliente al milione di richieste, il database SQL di Azure viene ridimensionato immediatamente, con tempi di inattività minimi. La scalabilità è una delle caratteristiche più importanti del modello PaaS che consente di aggiungere in modo dinamico altre risorse al servizio quando necessario. Database SQL di Azure consente di modificare facilmente le risorse allocate per i database (potenza della CPU, memoria, velocità effettiva IO e storage).

È possibile limitare i problemi di prestazione, causati da un maggiore utilizzo dell'applicazione, che non possono essere risolti utilizzando l'indicizzazione o con i metodi di query. L'aggiunta di altre risorse consente di una reazione rapida quando il database raggiunge i limiti delle risorse correnti e richiede più energia per gestire il carico di lavoro in ingresso. Database SQL di Azure consente inoltre di ridurre le risorse non necessarie all'abbassamento dei costi.

Non è necessario acquistare un hardware o modificare o l'infrastruttura di base. Il ridimensionamento del database può essere eseguito con facilità tramite il portale di Azure usando un dispositivo di scorrimento.

![Prestazioni del ridimensionamento del database](media/sql-database-scalability/scale-performance.svg)

Per il database SQL di Azure è disponibile il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

- Il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) offre un insieme di risorse di calcolo, memoria e risorse IO in tre livelli di servizio per supportare carichi di lavoro di database da leggeri a pesanti: Basic, Standard e Premium. I livelli delle prestazioni di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
- Il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) consente di scegliere il numero di vCore, la quantità di memoria e la quantità e la velocità della risorsa di archiviazione. Questo modello di acquisto offre tre livelli di servizio: per utilizzo generico, business critical e iperscalabilità.

È possibile creare la prima app in un singolo database di piccole dimensioni a un costo mensile contenuto nel livello di servizio Basic, Standard o Utilizzo generico e quindi modificare il livello di servizio manualmente o tramite codice in qualsiasi momento passando al livello di servizio Premium o Business Critical in base alle esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

> [!NOTE]
> La scalabilità dinamica è diversa dalla scalabilità automatica. La scalabilità automatica è quando un servizio viene ridimensionato automaticamente in base ai criteri, mentre la scalabilità dinamica consente la scalabilità manuale con tempi di inattività minimi.

Un singolo database SQL di Azure supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più *automatica*, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database.
Esistono comunque script utili per automatizzare la scalabilità per un singolo database SQL di Azure. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un singolo database SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).

È possibile modificare i [livelli di servizio delle DTU](sql-database-service-tiers-dtu.md) o le [caratteristiche vCore](sql-database-vcore-resource-limits-single-databases.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario usare un pool elastico con un determinato numero di eDTU condivise tra più database nel pool.

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Tutte le tre versioni di Database SQL di Azure offrono la capacità di ridimensionare in modo dinamico i database:

- Con un [Database singolo](sql-database-single-database-scale.md) è possibile usare sia i modelli [DTU](sql-database-dtu-resource-limits-single-databases.md) o [vCore](sql-database-vcore-resource-limits-single-databases.md) per definire la quantità massima di risorse che verranno assegnate a ogni database.
- Un'[Istanza gestita](sql-database-managed-instance.md) usa la modalità [vCore](sql-database-managed-instance.md#vcore-based-purchasing-model) e consente di definire il massimo di core CPU e il massimo della memoria allocata per l'istanza. Tutti i database all'interno dell'istanza condivideranno le risorse allocate per l'istanza.
- I [pool elastici](sql-database-elastic-pool-scale.md) consentono di definire il limite massimo di risorse per ogni gruppo di database nel pool.

Quando si avvia l'azione di aumento o riduzione delle prestazioni in una qualsiasi delle varianti, il processo del motore di database viene riavviato e spostato in una macchina virtuale diversa, se necessario. Lo sviluppo del processo del motore di database in una nuova macchina virtuale è un **processo online** in cui è possibile continuare a usare il servizio database SQL di Azure esistente mentre il processo è in corso. Una volta che il motore di database di destinazione è stato completamente inizializzato ed è pronto per l'elaborazione delle query, le connessioni verranno [passate dal motore di database di origine a quello di destinazione](sql-database-single-database-scale.md#impact). 


> [!NOTE]
> È possibile che si verifichi un breve intervallo di connessione al termine del processo di scalabilità verticale/orizzontale. Se è stata implementata la [logica di ripetizione dei tentativi per gli errori temporanei standard](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), il failover non sarà noto.

## <a name="alternative-scale-methods"></a>Metodi alternativi di scalabilità

Il ridimensionamento delle risorse è il modo più semplice ed efficace per migliorare le prestazioni del database senza modificare il codice del database o dell'applicazione. In alcuni casi, anche i livelli di servizio più elevati, le dimensioni di calcolo e le ottimizzazioni delle prestazioni potrebbero non gestire il carico di lavoro in modo efficace ed economicamente conveniente. In tal caso sono disponibili opzioni aggiuntive per la scalabilità del database:

- La [scalabilità in lettura](sql-database-read-scale-out.md) è una funzionalità disponibile che consente di ottenere una replica di sola lettura dei dati in cui è possibile eseguire query di sola lettura complesse, ad esempio i report. Replica di sola lettura gestirà il carico di lavoro di sola lettura senza influire sull'utilizzo delle risorse nel database primario.
- Il [Partizionamento di database](sql-database-elastic-scale-introduction.md) è un set di tecniche che consente di dividere i dati in diversi database e di ridimensionarli in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come migliorare le prestazioni del database tramite la modifica del codice del database, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
- Per informazioni su come consentire alle intelligence integrate nel database di ottimizzare il database, vedere [Ottimizzazione automatica](sql-database-automatic-tuning.md).
- Per informazioni sulla Scalabilità in lettura nel servizio Database SQL di Azure, vedere come [Usare le repliche di sola lettura per bilanciare i carichi di lavoro di query di sola lettura](sql-database-read-scale-out.md).
- Per informazioni sul Partizionamento del database, vedere [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md).
