---
title: Gestione dello spazio file del database SQL di Azure
description: Questa pagina descrive come gestire lo spazio file con database singoli e in pool nel database SQL di Azure e fornisce esempi di codice per determinare se sia necessario compattare un database singolo o in pool e come eseguire un'operazione di compattazione del database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 12/22/2020
ms.openlocfilehash: 7bb754b892715adffc6ead99f3d866f9f9d8af9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99096492"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Gestire lo spazio file per i database nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo descrive i diversi tipi di spazio di archiviazione per i database nel database SQL di Azure e i passaggi che possono essere eseguiti quando lo spazio file allocato deve essere gestito in modo esplicito.

> [!NOTE]
> Questo articolo non si applica a Istanza gestita di SQL di Azure.

## <a name="overview"></a>Panoramica

Con il database SQL di Azure sono disponibili modelli di carico di lavoro in cui l'allocazione dei file di dati sottostanti per i database può diventare maggiore della quantità di pagine di dati utilizzate. Questa condizione si può verificare quando lo spazio usato aumenta e i dati vengono successivamente eliminati. Ciò è dovuto al fatto che lo spazio file allocato non viene recuperato automaticamente quando i dati vengono eliminati.

Può essere necessario monitorare l'utilizzo dello spazio file e compattare i file di dati per:

- Consentire l'aumento delle dimensioni dei dati in un pool elastico quando lo spazio file allocato per i relativi database raggiunge le dimensioni massime del pool.
- Consentire la riduzione delle dimensioni massime di un database singolo o di un pool elastico.
- Consentire il passaggio di un database singolo o di un pool elastico a un livello di servizio o a un livello di prestazioni diverso con dimensioni massime inferiori.

### <a name="monitoring-file-space-usage"></a>Monitoraggio dell'utilizzo dello spazio file

La maggior parte delle metriche per lo spazio di archiviazione visualizzate nel portale di Azure e delle API seguenti misura solo le dimensioni delle pagine di dati usate:

- API per le metriche basate su Azure Resource Manager tra cui [get-metrics](/powershell/module/az.monitor/get-azmetric) di PowerShell
- T-SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Le API seguenti misurano invece anche le dimensioni dello spazio allocato per i database e i pool elastici:

- T-SQL: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Compattazione dei file di dati

Il database SQL di Azure non compatta automaticamente i file di dati per recuperare lo spazio allocato inutilizzato a causa dell'impatto potenziale sulle prestazioni del database.  Tuttavia, i clienti possono compattare i file di dati tramite self-service in un momento specifico attenendosi alla procedura descritta in [recuperare lo spazio allocato inutilizzato](#reclaim-unused-allocated-space).

> [!NOTE]
> Diversamente dai file di dati, il database SQL di Azure compatta automaticamente i file di log poiché tale operazione non influisca sulle prestazioni del database.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Informazioni sui tipi di spazio di archiviazione per un database

La comprensione delle quantità di spazio di archiviazione seguenti è importante per la gestione dello spazio file di un database.

|Quantità di database|Definizione|Commenti|
|---|---|---|
|**Spazio dati usato**|Quantità di spazio usato per archiviare i dati del database in pagine da 8 KB.|In genere, lo spazio usato aumenta quando vengono inseriti i dati e diminuisce quando vengono eliminati. In alcuni casi, lo spazio usato non cambia in caso di inserimenti o eliminazioni, a seconda della quantità e del modello di dati coinvolti nell'operazione e dell'eventuale frammentazione. Ad esempio, se si elimina una riga da ogni pagina di dati, non si riduce necessariamente lo spazio usato.|
|**Spazio dati allocato**|Quantità di spazio file formattato messo a disposizione per l'archiviazione dei dati del database.|La quantità di spazio allocato aumenta automaticamente, ma non diminuisce mai dopo le eliminazioni. Questo comportamento assicura che gli inserimenti futuri avvengano più velocemente, perché non è necessario riformattare lo spazio.|
|**Spazio dati allocato ma non usato**|Differenza tra la quantità di spazio dati allocato e lo spazio dati usato.|Questa quantità rappresenta la quantità massima di spazio libero che può essere recuperata compattando i file di dati del database.|
|**Dimensioni massime dei dati**|Quantità massima di spazio che può essere usata per l'archiviazione dei dati del database.|La quantità di spazio dati allocato non può superare le dimensioni massime dei dati.|

Il diagramma seguente illustra la relazione tra i diversi tipi di spazio di archiviazione per un database.

![Tipi di spazio di archiviazione e relazioni](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Eseguire una query su un database singolo per ottenere informazioni sullo spazio di archiviazione

Per determinare le quantità di spazio di archiviazione per un database singolo, è possibile usare le query seguenti.  

### <a name="database-data-space-used"></a>Spazio dati del database usato

Modificare la query seguente per restituire la quantità di spazio dati del database usato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Spazio di dati del database allocato e spazio allocato non usato

Usare la query seguente per restituire la quantità di spazio dati di database allocato e la quantità di spazio non usato allocato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Dimensioni massime dei dati del database

Modificare la query seguente per restituire le dimensioni massime dei dati del database.  L'unità di misura dei risultati di query è costituita da byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Informazioni sui tipi di spazio di archiviazione per un pool elastico

La comprensione delle quantità di spazio di archiviazione seguenti è importante per la gestione dello spazio file di un pool elastico.

|Quantità di pool elastico|Definizione|Commenti|
|---|---|---|
|**Spazio dati usato**|Somma dello spazio dati usato da tutti i database nel pool elastico.||
|**Spazio dati allocato**|Somma dello spazio dati allocato da tutti i database nel pool elastico.||
|**Spazio dati allocato ma non usato**|Differenza tra la quantità di spazio dati allocato e lo spazio dati usato da tutti i database nel pool elastico.|Questa quantità rappresenta la quantità massima di spazio allocato per il pool elastico che può essere recuperata compattando i file di dati del database.|
|**Dimensioni massime dei dati**|Quantità massima di spazio dati che può essere usata dal pool elastico per tutti i rispettivi database.|Lo spazio allocato per il pool elastico non deve superare le dimensioni massime del pool elastico.  Se si verifica questa condizione, lo spazio allocato e non usato può essere recuperato compattando i file di dati del database.|

> [!NOTE]
> Il messaggio di errore "il pool elastico ha raggiunto il limite di archiviazione" indica che agli oggetti di database è stato allocato spazio sufficiente per soddisfare il limite di archiviazione del pool elastico, ma potrebbe esserci spazio inutilizzato nell'allocazione dello spazio dati. Si consiglia di aumentare il limite di archiviazione del pool elastico o come soluzione a breve termine, liberando lo spazio dei dati usando la sezione [**recuperare lo spazio allocato non usato**](#reclaim-unused-allocated-space) riportato di seguito. È inoltre necessario essere a conoscenza dell'impatto potenziale negativo sulle prestazioni della compattazione dei file di database. vedere la sezione [**ricompilazione degli indici**](#rebuild-indexes) .

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Eseguire una query su un pool elastico per ottenere informazioni sullo spazio di archiviazione

Le query seguenti possono essere usate per determinare le quantità di spazio di archiviazione per un pool elastico.  

### <a name="elastic-pool-data-space-used"></a>Spazio dati del pool elastico usato

Modificare la query seguente per restituire la quantità di spazio dati del pool elastico usato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Spazio di dati del pool elastico allocato e spazio allocato non usato

Modificare gli esempi seguenti per restituire una tabella che elenca lo spazio allocato e lo spazio allocato non usato per ogni database in un pool elastico. La tabella dispone i database in ordine decrescente in base alla quantità di spazio allocato non usato.  L'unità di misura dei risultati di query è costituita da MB.  

I risultati delle query per determinare lo spazio allocato per ogni database nel pool possono essere sommati per determinare lo spazio totale allocato per il pool elastico. Lo spazio allocato del pool elastico non deve superare le dimensioni massime del pool elastico.  

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

Lo script di PowerShell richiede il modulo SQL Server PowerShell. Vedere [Scaricare il modulo PowerShell](/sql/powershell/download-sql-server-ps-module) per l'installazione.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

Lo screenshot seguente mostra un esempio di output dello script:

![Esempio di spazio allocato del pool elastico e spazio allocato non usato](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Dimensioni massime dei dati del pool elastico

Modificare la query T-SQL seguente per restituire le dimensioni massime massime dei dati del pool elastico registrato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Recuperare lo spazio allocato non usato

> [!NOTE]
> I comandi Shrink influiscano sulle prestazioni del database durante l'esecuzione e, se possibile, devono essere eseguiti durante i periodi di basso utilizzo.

### <a name="dbcc-shrink"></a>Compattazione tramite DBCC

Dopo l'identificazione dei database per il recupero di spazio allocato non usato, modificare il nome del database nel comando seguente per compattare i file di dati di ogni database.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1');
```

I comandi Shrink influiscano sulle prestazioni del database durante l'esecuzione e, se possibile, devono essere eseguiti durante i periodi di basso utilizzo.  

È inoltre necessario essere a conoscenza dell'impatto potenziale negativo sulle prestazioni della compattazione dei file di database. vedere la sezione [**ricompilazione degli indici**](#rebuild-indexes) .

Per altre informazioni su questo comando, vedere [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Compattazione automatica

In alternativa, è possibile abilitare la compattazione automatica per un database.  La compattazione automatica riduce la complessità della gestione dei file e il suo impatto sulle prestazioni del database è inferiore rispetto a `SHRINKDATABASE` o `SHRINKFILE`.  Può essere particolarmente utile per la gestione dei pool elastici con molti database.  Può essere però meno efficace nel recupero dello spazio file rispetto a `SHRINKDATABASE` e `SHRINKFILE`.
Per impostazione predefinita, compattazione automatica è disabilitato come consigliato per la maggior parte dei database. Per ulteriori informazioni, vedere [considerazioni per AUTO_SHRINK](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink).

Per abilitare la compattazione automatica, modificare il nome del database nel comando seguente.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON;
```

Per altre informazioni su questo comando, vedere [Opzioni ALTER DATABASE SET](/sql/t-sql/statements/alter-database-transact-sql-set-options).

### <a name="rebuild-indexes"></a>Ricompilazione degli indici

Dopo la compattazione dei file di dati del database, gli indici possono diventare frammentati e perdere l'efficacia di ottimizzazione delle prestazioni. In caso di riduzione del livello delle prestazioni, provare a ricompilare gli indici del database. Per altre informazioni sulla frammentazione e sulla ricompilazione degli indici, vedere [Riorganizzare e ricompilare gli indici](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle dimensioni massime dei database, vedere:
  - [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](resource-limits-vcore-single-databases.md)
  - [Limiti di risorse per i database singoli usando il modello di acquisto basato su DTU](resource-limits-dtu-single-databases.md)
  - [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per i pool elastici](resource-limits-vcore-elastic-pools.md)
  - [Limiti di risorse per i pool elastici usando il modello di acquisto basato su DTU](resource-limits-dtu-elastic-pools.md)
- Per altre informazioni sul comando `SHRINKDATABASE`, vedere [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Per altre informazioni sulla frammentazione e sulla ricompilazione degli indici, vedere [Riorganizzare e ricompilare gli indici](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).