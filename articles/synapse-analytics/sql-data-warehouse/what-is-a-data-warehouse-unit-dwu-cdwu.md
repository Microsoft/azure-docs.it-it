---
title: Unità di data warehouse (DKU) in Azure Synapse Analytics (in precedenza SQL DW)
description: Consigli sulla scelta del numero ideale di unità di data warehouse (DKU) per ottimizzare il prezzo e le prestazioni e su come modificare il numero di unità.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a7f27215c7ba053933d9d12658253ea92aefa526
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351119"
---
# <a name="data-warehouse-units-dwus"></a>Unità data warehouse (DKU)

Consigli sulla scelta del numero ideale di unità di data warehouse (DKU) per ottimizzare il prezzo e le prestazioni e su come modificare il numero di unità.

## <a name="what-are-data-warehouse-units"></a>Che cosa sono le unità di data warehouse

Un [pool SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) rappresenta una raccolta di risorse analitiche di cui viene eseguito il provisioning quando si utilizza SQL [Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Le risorse analitiche sono definite come una combinazione di CPU, memoria e I/O. Queste tre risorse sono raggruppate in unità di scala di calcolo denominate Unità di data warehouse (DKU). Un'unità DWU rappresenta una misura astratta e normalizzata delle risorse e delle prestazioni di calcolo. Una modifica al livello di servizio altera il numero di DKU disponibili per il sistema, che a sua volta regola le prestazioni e il costo del sistema.

Per migliorare le prestazioni, è possibile aumentare il numero di unità del data warehouse. Per una riduzione delle prestazioni, ridurre le unità del data warehouse. I costi di archiviazione e calcolo vengono fatturati separatamente, pertanto la modifica delle unità Data Warehouse non influisce sui costi di archiviazione.

Le prestazioni per le unità del data warehouse si basano su queste metriche del carico di lavoro:Performance for data warehouse units is based on these workload metrics:

- Velocità con cui una query di data warehousing standard può eseguire la scansione di un numero elevato di righe e quindi eseguire un'aggregazione complessa. Questa è un'operazione con uso intensivo di I/O e CPU.
- Velocità con cui il data warehouse può creare dati dai BLOB di Archiviazione di Azure o da Azure Data Lake.How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. Questa è un'operazione con uso intensivo di rete e CPU.
- Velocità con [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) cui il comando T-SQL può copiare una tabella. Questa operazione comporta la lettura dei dati dall'archiviazione, la relativa distribuzione tra tutti i nodi dell'appliance e la riscrittura nella risorsa di archiviazione. È un'operazione con uso intensivo di CPU, I/O e rete.

L'aumento delle DWU:

- Modifica in modo lineare le prestazioni del sistema per le analisi, l'aggregazione e le istruzioni CTAS
- Aumenta il numero di lettori e writer per operazioni di caricamento di PolyBase
- Aumenta il numero massimo di query simultanee e slot di concorrenza.

## <a name="service-level-objective"></a>Obiettivo del livello di servizio

L'obiettivo del livello di servizio (SLO) è l'impostazione di scalabilità che determina il livello di costi e prestazioni del data warehouse. I livelli di servizio per il pool SQL Gen2 sono misurati in unità di data warehouse (DWU), ad esempio DW2000c.

In T-SQL l'impostazione SERVICE_OBJECTIVE determina il livello di servizio per il pool SQL.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Limiti di capacità

Ogni server SQL (ad esempio, myserver.database.windows.net) ha una quota di [unità di transazione di database (DTU)](../../sql-database/sql-database-service-tiers-dtu.md) che consente un numero specifico di unità di data warehouse. Per altre informazioni, vedere i [limiti della capacità di gestione del carico di lavoro](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quante unità di data warehouse sono necessarie

Il numero ideale di unità Data Warehouse dipende molto dal carico di lavoro e dalla quantità di dati che sono stati caricati nel sistema.

Procedure per individuare l'impostazione DWU ottimale per il carico di lavoro:

1. Iniziare selezionando un'unità data warehouse più piccola.
2. Monitorare le prestazioni dell'applicazione durante il caricamento dei dati di test nel sistema, osservando il numero di DWU selezionato in relazione alle prestazioni rilevate.
3. Identificare eventuali requisiti aggiuntivi per i periodici periodi di punta delle attività. I carichi di lavoro che mostrano picchi e minimi significativi nell'attività potrebbero dover essere scalati frequentemente.

Analisi SQL è un sistema con scalabilità orizzontale in grado di eseguire il provisioning di grandi quantità di dati di dimensioni elevate e di query. Per verificare le effettive capacità nell'ottica della scalabilità, in particolare per le maggiori quantità di DWU, è consigliabile ridimensionare il set di dati durante gli interventi di scalabilità per assicurarsi che siano disponibili dati sufficienti per le CPU. Per i test di scalabilità è consigliabile usare almeno 1 TB.

> [!NOTE]
>
> Le prestazioni delle query aumentano con maggiore parallelizzazione solo se il lavoro può essere suddivise tra i nodi di calcolo. Se si riscontra che gli interventi di scalabilità non hanno effetti sulle prestazioni, potrebbe essere necessario ottimizzare la progettazione delle tabelle e/o le query. Per informazioni sull'ottimizzazione delle query, vedere [Gestire le query utente](cheat-sheet.md).

## <a name="permissions"></a>Autorizzazioni

Per modificare le unità Data Warehouse sono necessarie le autorizzazioni descritte in [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

I ruoli predefiniti per le risorse di Azure, ad esempio Collaboratore database SQL e Collaboratore SQL Server, possono modificare le impostazioni DWU.

## <a name="view-current-dwu-settings"></a>Visualizzare le impostazioni DWU correnti

Per visualizzare l'impostazione corrente per le unità DWU:

1. Aprire Esplora oggetti di SQL Server in Visual Studio.
2. Connettersi al database master associato al server di database SQL logico.
3. Effettuare una selezione nella vista a gestione dinamica sys.database_service_objectives. Esempio:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Modificare le unità Data Warehouse

### <a name="azure-portal"></a>Portale di Azure

Per modificare i DKU:

1. Aprire il [portale di Azure](https://portal.azure.com), aprire il database e quindi fare clic su **Ridimensiona**.

2. In **Ridimensiona** spostare il dispositivo di scorrimento verso sinistra o destra per modificare l'impostazione delle DWU.

3. Fare clic su **Salva**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per modificare le DKU, utilizzare il cmdlet [PowerShell Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) Nell'esempio seguente l'obiettivo del livello di servizio viene impostato su DW1000c per il database MySQLDW ospitato nel server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Per altre informazioni, vedere i [cmdlet di PowerShell per SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Con T-SQL è possibile visualizzare le impostazioni DWU correnti, modificare le impostazioni e controllare lo stato di avanzamento.

Per modificare le DWU:

1. Connettersi al database master associato al server di database SQL logico.
2. Usare l'istruzione T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). Nell'esempio seguente l'obiettivo del livello di servizio viene impostato su DW1000c per il database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>API REST

Per modificare le DWU, usare l'API REST descritta in [Create or Update Database](/rest/api/sql/databases/createorupdate) (Creare o aggiornare il database). L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000c per il database MySQLDW, ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

Per altri esempi di API REST, vedere [API REST per SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Controllare lo stato delle modifiche alle DWU

Il completamento delle modifiche alle DWU può richiedere alcuni minuti. In caso di operazioni automatiche di ridimensionamento, valutare l'implementazione della logica per assicurare il completamento di determinate operazioni prima di procedere con altre.

Verificando lo stato del database in vari endpoint sarà possibile implementare correttamente l'automazione. Il portale invia notifiche una volta completata un'operazione e indica lo stato corrente del database. Tuttavia non è possibile verificare lo stato in maniera programmatica.

Non è possibile verificare lo stato del database per le operazioni di scalabilità orizzontale con il portale di Azure.

Per controllare lo stato delle modifiche alle DWU:

1. Connettersi al database master associato al server di database SQL logico.

1. Inviare la query seguente per controllare lo stato del database.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Inviare la query seguente per controllare lo stato dell'operazione.

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
Questa DMV restituisce informazioni sulle varie operazioni di gestione nel pool SQL, ad esempio l'operazione e lo stato dell'operazione, ovvero IN_PROGRESS o COMPLETED.

## <a name="the-scaling-workflow"></a>Flusso di lavoro del ridimensionamento

Quando si avvia un'operazione di ridimensionamento, il sistema interrompe prima tutte le sessioni aperte, eseguendo il rollback di tutte le transazioni aperte per garantire uno stato coerente. Le operazioni di ridimensionamento vengono eseguite solo dopo il completamento del rollback delle transazioni.  

- Per un'operazione di scalabilità verticale, il sistema scollega tutti i nodi di calcolo, esegue il provisioning dei nodi di calcolo aggiuntivi e quindi si ricollega al livello di archiviazione.
- Per un'operazione di ridimensionamento, il sistema scollega tutti i nodi di calcolo e quindi ricollega solo i nodi necessari al livello di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle prestazioni, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md) e [Limiti di memoria e concorrenza](memory-concurrency-limits.md).
