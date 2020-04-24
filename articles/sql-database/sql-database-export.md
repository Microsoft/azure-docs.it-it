---
title: Esportare un database singolo o in pool in un file BACPAC
description: Esportare un database SQL di Azure in un file BACPAC usando il portale di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061637"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Esportare un database SQL di Azure in un file BACPAC

Quando è necessario esportare un database per l'archiviazione o lo spostamento in un'altra piattaforma, è possibile esportare lo schema di database e i dati in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un file BACPAC è un file ZIP con un'estensione bacpac contenente i metadati e dati da un database di SQL Server. È possibile memorizzare questo tipo di file in Archivio BLOB di Azure o in un archivio locale e successivamente importarlo nuovamente nel database SQL di Azure o in un'installazione locale di SQL Server.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerazioni relative all'esportazione di un database SQL di Azure

- Affinché un'esportazione sia coerente a livello di transazione, è necessario assicurarsi che non si verifichi alcuna attività di scrittura durante l'esportazione o che si stia esportando da una [copia coerente](sql-database-copy.md) dal punto di transazione del database SQL di Azure.
- Se si sta eseguendo l'esportazione nell'archiviazione BLOB, la dimensione massima di un file BACPAC è 200 GB. Per archiviare un file BACPAC di dimensioni maggiori, eseguire l'esportazione in una risorsa di archiviazione locale.
- L'esportazione di un file BACPAC in Archiviazione Premium di Azure usando i metodi descritti in questo articolo non è supportata.
- L'archiviazione dietro un firewall non è attualmente supportata.
- Se l'operazione di esportazione da un database SQL di Azure dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:

  - Aumentare temporaneamente le dimensioni di calcolo.
  - Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  - Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW_STATISTICS** e verificare che il parametro *RANGE_HI_KEY* non sia null e il relativo valore abbia distribuzione valida. Per i dettagli, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell'utente. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md) e [Backup del database SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eseguire l'esportazione in un file BACPAC con il portale di Azure

L'esportazione di un BACPAC di un database da un' [istanza gestita](sql-database-managed-instance.md) usando Azure PowerShell non è attualmente supportata. In alternativa, usare SQL Server Management Studio o SqlPackage.

> [!NOTE]
> I computer che elaborano le richieste di importazione/esportazione inviate tramite il portale di Azure o PowerShell devono archiviare il file BACPAC e i file temporanei generati dal framework dell'applicazione livello dati (DacFX). Lo spazio su disco richiesto varia significativamente tra i database con le stesse dimensioni e può richiedere spazio su disco fino a 3 volte la dimensione del database. I computer che eseguono la richiesta di importazione/esportazione hanno solo spazio su disco locale 450GB. Di conseguenza, alcune richieste potrebbero non riuscire con l' `There is not enough space on the disk`errore. In questo caso, la soluzione alternativa consiste nell'eseguire SqlPackage. exe su un computer con sufficiente spazio su disco locale. Si consiglia di usare [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) per importare/esportare database di dimensioni superiori a 150 GB per evitare questo problema.

1. Per esportare un database con il [portale di Azure](https://portal.azure.com), aprire la pagina relativa al database e fare clic su **Esporta** sulla barra degli strumenti.

   ![Esportazione di un database](./media/sql-database-export/database-export1.png)

2. Specificare il nome del file BACPAC, selezionare un account di archiviazione di Azure esistente e un contenitore per l'esportazione e quindi fornire le credenziali appropriate per l'accesso al database di origine. È necessario un **account di accesso amministratore** di SQL Server anche se si è l'amministratore di Azure, perché un amministratore di Azure non equivale a avere SQL Server autorizzazioni di amministratore.

    ![Esportazione di un database](./media/sql-database-export/database-export2.png)

3. Fare clic su **OK**.

4. Per monitorare lo stato di avanzamento dell'operazione di esportazione, aprire la pagina per il server di database SQL contenente il database da esportare. Passare a **Impostazioni** e quindi fare clic su **Cronologia importazioni/esportazioni**.

   ![Cronologia esportazioni](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eseguire l'esportazione in un file BACPAC con l'utilità SQLPackage

Per esportare un database SQL tramite l'utilità della riga di comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), vedere la sezione relativa ai [parametri e proprietà dell'importazione](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). L'utilità SQLPackage viene offerta con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). È tuttavia possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

È consigliabile usare l'utilità SQLPackage per la scalabilità e le prestazioni nella maggior parte degli ambienti di produzione. Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Questo esempio illustra come esportare un database usando SqlPackage.exe con l'autenticazione universale di Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eseguire l'esportazione in un file BACPAC con SQL Server Management Studio (SSMS)

Le versioni più recenti di SQL Server Management Studio includono una procedura guidata per l'esportazione di un database SQL di Azure in un file BACPAC. Vedere [Esportazione guidata applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eseguire l'esportazione in un file BACPAC con PowerShell

> [!NOTE]
> [Un'istanza gestita](sql-database-managed-instance.md) non supporta attualmente l'esportazione di un database in un file BACPAC tramite PowerShell. Per esportare un'istanza gestita in un file BACPAC, usare SQL Server Management Studio o SQLPackage.

Usare il cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) per inviare una richiesta di esportazione del database al servizio database SQL di Azure. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Per controllare lo stato della richiesta di esportazione, usare il cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . L'esecuzione di questo cmdlet subito dopo la richiesta restituisce in genere **Status: InProgress**. Al termine dell'esportazione, il messaggio restituito è **Status: Succeeded**.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla conservazione dei backup a lungo termine dei database singoli e dei database in pool come alternativa all'esportazione di un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md). È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione dei backup a lungo termine.
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC in un database di SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Per altre informazioni sull'esportazione di un file con estensione bacpac da un database di SQL Server, vedere [Esportare un'applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).
- Per altre informazioni sull'uso del Servizio Migrazione del database per eseguire la migrazione di un database, vedere [Eseguire la migrazione di SQL Server al database SQL di Azure offline con Servizio Migrazione del database](../dms/tutorial-sql-server-to-azure-sql.md).
- Se si sta eseguendo l'esportazione da SQL Server come preparazione alla migrazione nel database SQL di Azure, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-single-database-migrate.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
