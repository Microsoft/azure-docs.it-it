---
title: Eseguire l'aggiornamento alla generazione più recente del pool SQL dedicato (in precedenza SQL DW)
description: Aggiornare il pool SQL dedicato di Azure sinapsi Analytics (in precedenza SQL DW) all'ultima generazione dell'architettura hardware e di archiviazione di Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459253"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Ottimizzare le prestazioni tramite l'aggiornamento di un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

Aggiornare il pool SQL dedicato (in precedenza SQL DW) all'ultima generazione dell'architettura hardware e di archiviazione di Azure.

## <a name="why-upgrade"></a>Ragioni dell'aggiornamento

È ora possibile eseguire facilmente l'aggiornamento al livello Gen2 ottimizzato per il pool SQL dedicato (in precedenza SQL DW) nell'portale di Azure per le [aree supportate](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se l'area non supporta l'aggiornamento automatico, è possibile eseguire l'aggiornamento a un'area supportata o attendere la disponibilità dell'aggiornamento autonomo nella propria area. Eseguire subito l'aggiornamento per sfruttare i vantaggi della generazione più recente di hardware e architettura di archiviazione migliorata di Azure, che include prestazioni più veloci, una maggiore scalabilità e archiviazione a colonne illimitata.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Questo aggiornamento si applica ai pool SQL dedicati al livello Gen1 ottimizzato per il calcolo (fornmerly SQL DW) nelle [aree supportate](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Prima di iniziare

1. Controllare se l'[area](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) è supportata per la migrazione da Gen1 a Gen2. Tenere presente le date di migrazione automatica. Per evitare conflitti con il processo automatizzato, pianificare la migrazione manuale prima della data di inizio processo automatizzato.
2. Se ci si trova in un'area non è ancora supportata, continuare a controllare che la propria area venga aggiunta oppure [eseguire l'aggiornamento tramite ripristino](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) in un'area supportata.
3. Se l'area è supportata, [eseguire l'aggiornamento tramite il portale di Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selezionare il livello di prestazioni consigliato** per il pool SQL dedicato (in precedenza SQL DW) in base al livello di prestazioni corrente nel livello di Gen1 ottimizzato per il calcolo usando il mapping seguente:

   | Livello Gen1 con ottimizzazione per il calcolo | Livello Gen2 con ottimizzazione per il calcolo |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> I livelli di prestazioni consigliati non sono una conversione diretta. Ad esempio, è consigliabile passare da DW600 DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Eseguire l'aggiornamento in un'area supportata tramite portale di Azure

- La migrazione da Gen1 a Gen2 tramite il portale di Azure è permanente. Non esiste un processo per tornare a Gen1.
- Il pool SQL dedicato (in precedenza SQL DW) deve essere in esecuzione per eseguire la migrazione a Gen2

### <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Accedere al [portale di Azure](https://portal.azure.com/).
- Verificare che il pool SQL dedicato (in precedenza SQL DW) sia in esecuzione. è necessario eseguire la migrazione a Gen2

### <a name="powershell-upgrade-commands"></a>Comandi di aggiornamento di PowerShell

1. Se il pool SQL dedicato del livello Gen1 ottimizzato per il calcolo (in precedenza SQL DW) da aggiornare è sospeso, [riprendere il pool SQL dedicato (in precedenza SQL DW)](pause-and-resume-compute-portal.md).

2. Questa operazione potrebbe comportare alcuni minuti di inattività.

3. Identificare tutti i riferimenti del codice ai livelli di prestazioni Gen1 con ottimizzazione per il calcolo e modificarli impostando il livello di prestazioni equivalente Gen2 con ottimizzazione per il calcolo. Di seguito sono riportati due esempi di in cui è necessario aggiornare i riferimenti del codice prima dell'aggiornamento:

   Comando Gen1 PowerShell originale:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificato come segue:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" diventa RequestedServiceObjectiveName "DW300 **c**"
   >

   Comando Gen1 T-SQL originale:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificato come segue:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = 'DW300' diventa SERVICE_OBJECTIVE = 'DW300 **c**'

## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

1. Passare al pool SQL dedicato Gen1 ottimizzato per il calcolo (denominato in precedenza SQL DW) nell'portale di Azure. Se il pool SQL dedicato del livello Gen1 ottimizzato per il calcolo (in precedenza SQL DW) da aggiornare è sospeso, [riprendere il pool SQL dedicato](pause-and-resume-compute-portal.md).
2. Selezionare **Aggiorna alla scheda Gen2** nella scheda attività: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Se la scheda **Aggiorna alla seconda generazione** non è visualizzata sotto la scheda Attività, il tipo di sottoscrizione è limitato nell'area corrente.
   > [Invia un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) per richiedere l'approvazione della sottoscrizione.

3. Assicurarsi che il carico di lavoro abbia completato l'esecuzione e sia inattivo prima dell'aggiornamento. Si verificheranno tempi di inattività per alcuni minuti prima che il pool SQL dedicato (noto in precedenza come SQL DW) sia di nuovo online come pool SQL dedicato di calcolo ottimizzato per il livello Gen2 (in precedenza SQL DW). **Selezionare Aggiorna**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitorare l'aggiornamento** controllando lo stato nel portale di Azure:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Il primo passaggio del processo di aggiornamento prevede l'operazione di ridimensionamento ("Aggiornamento - Offline") in cui verranno terminate tutte le sessioni ed interrotte tutte le connessioni.

   Il secondo passaggio del processo di aggiornamento è la migrazione dei dati ("Aggiornamento - Online"). La migrazione dei dati è un processo in background con flusso irregolare. Questo processo sposta lentamente i dati a colonne dall'architettura di archiviazione precedente alla nuova architettura di archiviazione usando la cache SSD locale. Durante questo periodo, il pool SQL dedicato (in precedenza SQL DW) sarà online per l'esecuzione di query e il caricamento. I dati saranno disponibili per le query indipendentemente dal fatto che sia stata o meno completata la migrazione. La migrazione dei dati avviene a velocità variabili a seconda delle dimensioni dei dati, del livello di prestazioni e del numero di segmenti columnstore.

5. **Raccomandazione facoltativa:** Al termine dell'operazione di ridimensionamento, è possibile velocizzare il processo in background per la migrazione dei dati. È possibile forzare immediatamente lo spostamento dei dati eseguendo [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) su tutte le tabelle columnstore primarie su cui si prevede di eseguire query, con SLO e classe di risorse più grandi. Questa operazione è **offline**, confrontata con il processo un processo in background con flusso irregolare, che può richiedere ore a seconda di numero e dimensione delle tabelle. Tuttavia, al termine dell'operazione la migrazione dei dati sarà molto più veloce per via della nuova architettura di archiviazione migliorata con gruppi di righe di qualità elevata.

> [!NOTE]
> ALTER INDEX REBUILD è un'operazione offline, pertanto le tabelle non saranno disponibili fino al completamento della ricompilazione.

La query seguente genera i comandi Alter Index Rebuild necessari per velocizzare la migrazione dei dati:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Eseguire l'aggiornamento da un'area geografica di Azure con l'esecuzione del ripristino tramite il portale di Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Creare un il punto di ripristino definito dall'utente usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare al pool SQL dedicato (in precedenza SQL DW) per il quale si desidera creare un punto di ripristino.

3. Nella parte superiore della sezione della panoramica, selezionare **+ Nuovo punto di ripristino**.

    ![Nuovo punto di ripristino](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Specificare un nome per il punto di ripristino.

    ![Nome del punto di ripristino](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Ripristinare un database attivo o sospeso con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pool SQL dedicato (in precedenza SQL DW) da cui si desidera eseguire il ripristino.
3. Nella parte superiore della sezione della panoramica, selezionare **+ Ripristino**.

    ![ Panoramica del servizio di ripristino](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selezionare **punti di ripristino automatici** o **punti di ripristino definiti dall'utente**. Per i punti di ripristino definiti dall'utente, **selezionare un punto di ripristino definito** dall'utente o **creare un nuovo punto di ripristino definito dall'** utente. Per il server selezionare **Crea nuovo** e scegliere un server in un'area geografica supportata da Gen2.

    ![Punti di ripristino automatici](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Eseguire il ripristino da un'area geografica di Azure con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per ripristinare un database, usare il cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000 **c**) come parametro facoltativo.

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database, specificando un parametro ServiceObjectiveName Gen2.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.

Se si verificano problemi con il pool SQL dedicato, creare una [richiesta di supporto](sql-data-warehouse-get-started-create-support-ticket.md) e fare riferimento all'aggiornamento di Gen2 come possibile.

## <a name="next-steps"></a>Passaggi successivi

Il pool SQL dedicato aggiornato (in precedenza SQL DW) è online. Per sfruttare i vantaggi dell'architettura avanzata, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
