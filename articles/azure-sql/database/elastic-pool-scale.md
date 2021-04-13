---
title: Ridimensionare le risorse del pool elastico
description: Questa pagina descrive come ridimensionare le risorse per i pool elastici nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 3d935332854816ae62dea8e30f08bee2b92a4eab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302982"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Ridimensionare le risorse dei pool elastici nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per i pool elastici e i database in pool nel database SQL di Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Modificare le risorse di calcolo (VCore o DTU)

Dopo aver selezionato inizialmente il numero di Vcore o edtu, è possibile ridimensionare in modo dinamico un pool elastico in base all'esperienza effettiva usando:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)
* [REST API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effetti della modifica delle dimensioni di calcolo del livello di servizio o di ridimensionamento

La modifica del livello di servizio o delle dimensioni di calcolo di un pool elastico segue un modello simile a quello per i database singoli e comporta principalmente il servizio che esegue i passaggi seguenti:

1. Creare una nuova istanza di calcolo per il pool elastico  

    Viene creata una nuova istanza di calcolo per il pool elastico con il livello di servizio e le dimensioni di calcolo richiesti. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, è necessario creare una replica di ogni database nella nuova istanza di calcolo, che prevede la copia dei dati e può influenzare fortemente la latenza complessiva. Indipendentemente dal fatto che i database rimangano online durante questo passaggio, le connessioni continuano a essere indirizzate ai database nell'istanza di calcolo originale.

2. Passare il routing delle connessioni a una nuova istanza di calcolo

    Vengono eliminate le connessioni esistenti ai database nell'istanza di calcolo originale. Vengono stabilite nuove connessioni ai database nella nuova istanza di calcolo. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, i file di database vengono scollegati e ricollegati durante l'opzione.  Indipendentemente dall'opzione, l'opzione può causare una breve interruzione del servizio quando i database non sono disponibili in genere per meno di 30 secondi e spesso solo per pochi secondi. Se sono in esecuzione transazioni con esecuzione prolungata quando si eliminano le connessioni, la durata di questo passaggio potrebbe richiedere più tempo per recuperare le transazioni interrotte. Il [recupero accelerato del database](../accelerated-database-recovery.md) può ridurre l'effetto di un'interruzione delle transazioni a esecuzione prolungata.

> [!IMPORTANT]
> Durante qualsiasi passaggio del flusso di lavoro non viene perso alcun dato.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latenza di modifica del livello di servizio o di ridimensionamento delle dimensioni di calcolo

La latenza stimata per modificare il livello di servizio, ridimensionare le dimensioni di calcolo di un singolo database o di un pool elastico, spostare un database all'interno o all'esterno di un pool elastico oppure spostare un database tra pool elastici è parametrizzato come indicato di seguito:

|Livello di servizio|Database singolo di base,</br>Standard (S0-S1)|Pool elastico di base,</br>Standard (S2-S12), </br>per utilizzo generico database singolo o il pool elastico|Premium o business critical database singolo o pool elastico|Hyperscale
|:---|:---|:---|:---|:---|
|**Database singolo Basic, </br> standard (S0-S1)**|&bull;&nbsp;Latenza temporale costante indipendente dallo spazio usato</br>&bull;&nbsp;In genere, meno di 5 minuti|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Pool elastico Basic, </br> standard (S2-S12), </br> per utilizzo generico database singolo o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Per i database singoli, latenza temporale costante indipendente dallo spazio usato</br>&bull;&nbsp;In genere, meno di 5 minuti per database singoli</br>&bull;&nbsp;Per i pool elastici, proporzionale al numero di database|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Premium o business critical database singolo o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|&bull;&nbsp;Latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio usato|
|**Hyperscale**|N/D|N/D|N/D|&bull;&nbsp;Latenza temporale costante indipendente dallo spazio usato</br>&bull;&nbsp;In genere, meno di 2 minuti|

> [!NOTE]
>
> - In caso di modifica del livello di servizio o di ridimensionamento del calcolo per un pool elastico, è necessario usare la somma dello spazio usato in tutti i database nel pool per calcolare la stima.
> - Nel caso in cui si sposti un database da e verso un pool elastico, solo lo spazio usato dal database influisca sulla latenza, non sullo spazio usato dal pool elastico.
> - Per i pool elastici standard e per utilizzo generico, la latenza di trasferimento di un database all'interno o all'esterno di un pool elastico o tra pool elastici sarà proporzionale alle dimensioni del database se il pool elastico usa l'archiviazione di condivisione file Premium ([PFS](../../storage/files/storage-files-introduction.md)). Per determinare se un pool utilizza l'archiviazione PFS, eseguire la query seguente nel contesto di qualsiasi database nel pool. Se il valore nella colonna AccountType è `PremiumFileStorage` o `PremiumFileStorage-ZRS` , il pool utilizza l'archiviazione PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Per monitorare le operazioni in corso, vedere: [gestire le operazioni tramite l'API REST di SQL](/rest/api/sql/operations/list), [gestire le operazioni tramite l'interfaccia](/cli/azure/sql/db/op)della riga di comando, [monitorare le operazioni usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e questi due comandi di PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerazioni aggiuntive quando si modificano le dimensioni di calcolo del livello di servizio o di ridimensionamento

- Quando si esegue il ridimensionamento di Vcore o edtu per un pool elastico, lo spazio usato nel pool deve essere inferiore alla dimensione massima consentita per il livello di servizio e il pool edtu di destinazione.
- Durante il ridimensionamento delle eDTU di un pool elastico viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime delle risorse di archiviazione del pool sono supportate dal pool di destinazione e (2) la dimensione massima delle risorse di archiviazione supera lo spazio di archiviazione incluso del pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e lo spazio di archiviazione incluso è solo di 50 GB. Lo spazio di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.

### <a name="billing-during-rescaling"></a>Fatturazione durante il ridimensionamento

Viene fatturata ogni ora per cui un database esiste usando il livello di servizio più elevato e le dimensioni di calcolo applicati in quell'ora, indipendentemente dall'uso o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-elastic-pool-storage-size"></a>modifica delle dimensioni di archiviazione del pool elastico

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo di dimensioni:

  - Per l'archiviazione nei livelli di servizio per utilizzo generico o standard, aumentare o ridurre le dimensioni in incrementi di 10 GB
  - Per l'archiviazione nei livelli di servizio premium o business critical, aumentare o ridurre le dimensioni in incrementi di 250 GB
- Il provisioning dell'archiviazione per un pool elastico può essere effettuato aumentandone o diminuendone le dimensioni massime.
- Il prezzo dell'archiviazione per un pool elastico corrisponde allo spazio di archiviazione moltiplicato per il prezzo unitario dell'archiviazione del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo eDTU per un pool elastico include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per le quantità di risorse di archiviazione incluse e i limiti di dimensioni massime, vedere [limiti delle risorse per i pool elastici usando il modello di acquisto DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) o [limiti delle risorse per i pool elastici usando il modello di acquisto vCore](resource-limits-vcore-elastic-pools.md).
- Le risorse di archiviazione extra per un pool elastico possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](elastic-pool-manage.md#azure-portal), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) o l'[API REST](/rest/api/sql/elasticpools/update).
- Il prezzo delle risorse di archiviazione extra per un pool elastico corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [gestire lo spazio di file nel database SQL di Azure](file-space-manage.md).

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore per il database SQL - pool elastici](resource-limits-vcore-elastic-pools.md) e [Limiti delle risorse basate su DTU per il database SQL - pool elastici](resource-limits-dtu-elastic-pools.md).