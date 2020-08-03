---
title: Importare un file BACPAC per creare un database nel database SQL di Azure
description: Creare un nuovo database nel database SQL di Azure o in Azure SQL Istanza gestita da un file BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: a1e349d284d8f2c0c9c5194ac9f52534cd1ab973
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503243"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Guida introduttiva: importare un file BACPAC in un database nel database SQL di Azure o in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

È possibile importare un database SQL Server nel database SQL di Azure o in SQL Istanza gestita usando un file [BacPac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . È possibile importare i dati da un file BACPAC archiviato in un archiviazione BLOB di Azure (solo archiviazione Standard) o da una risorsa di archiviazione locale in una posizione locale. Per ottimizzare la velocità di importazione fornendo un maggior numero di risorse più veloci, ridimensionare il database a un livello di servizio e dimensioni di calcolo superiori durante il processo di importazione. Al termine dell'importazione, sarà quindi possibile ridurre le caratteristiche.

> [!NOTE]
> Il livello di compatibilità del database importato si basa sul livello di compatibilità del database di origine.

> [!IMPORTANT]
> Dopo aver importato il database, è possibile scegliere di usare il database al livello di compatibilità corrente (livello 100 per il database AdventureWorks2008R2) o a un livello superiore. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.

## <a name="using-azure-portal"></a>Uso del portale di Azure

Guardare questo video per informazioni su come importare da un file BACPAC nel portale di Azure o continuare a leggere di seguito:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Il [portale di Azure supporta ](https://portal.azure.com) *solo* la creazione di un database singolo nel database SQL di Azure e *solo* da un file BACPAC salvato nell'archivio BLOB di Azure.

Per eseguire la migrazione di un database in un [istanza gestita SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) da un file BACPAC, usare SQL Server Management Studio o SqlPackage, usando la portale di Azure o la Azure PowerShell non è attualmente supportata.

> [!NOTE]
> I computer che elaborano le richieste di importazione/esportazione inviate tramite il portale di Azure o PowerShell devono archiviare il file BACPAC e i file temporanei generati dal framework dell'applicazione livello dati (DacFX). Lo spazio su disco richiesto varia significativamente tra i database con le stesse dimensioni e può richiedere spazio su disco fino a 3 volte la dimensione del database. I computer che eseguono la richiesta di importazione/esportazione hanno solo spazio su disco locale 450GB. Di conseguenza, alcune richieste potrebbero non riuscire con l'errore `There is not enough space on the disk` . In questo caso, la soluzione alternativa consiste nell'eseguire sqlpackage.exe in un computer con sufficiente spazio su disco locale. Si consiglia di usare SqlPackage per importare/esportare database di dimensioni superiori a 150 GB per evitare questo problema.

1. Per importare da un file BACPAC in un nuovo database singolo utilizzando il portale di Azure, aprire la pagina Server appropriata e quindi sulla barra degli strumenti selezionare **Importa database**.  

   ![Importazione database 1](./media/database-import/sql-server-import-database.png)

1. Selezionare l'account di archiviazione e il contenitore per il file BACPAC e quindi selezionare il file BACPAC da cui eseguire l'importazione.

1. Specificare la nuova dimensione del database (generalmente uguale all'origine) e specificare le credenziali dell'istanza SQL Server di destinazione. Per un elenco dei valori possibili per un nuovo database nel database SQL di Azure, vedere [create database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importazione database 2](./media/database-import/sql-server-import-database-settings.png)

1. Fare clic su **OK**.

1. Per monitorare lo stato di avanzamento dell'importazione, aprire la pagina del server del database e in **Impostazioni** selezionare **Cronologia importazioni/esportazioni**. Se l'operazione ha esito positivo, l'importazione visualizzerà lo stato **Completato**.

   ![Stato di importazione del database](./media/database-import/sql-server-import-database-history.png)

1. Per verificare che il database sia attivo sul server, selezionare **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="using-sqlpackage"></a>Utilizzo di SqlPackage

Per importare un database SQL Server tramite l'utilità della riga di comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), vedere la sezione relativa a [parametri e proprietà dell'importazione](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) includono SqlPackage. È possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) anche dall'Area download Microsoft.

Per la scalabilità e le prestazioni, è consigliabile usare SqlPackage, anziché il portale di Azure, nella maggior parte degli ambienti di produzione. Per informazioni da parte del team di consulenza clienti di SQL Server sull'uso di file `BACPAC` per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.

Il comando SqlPackage seguente importa il database **AdventureWorks2008R2** dalla risorsa di archiviazione locale a un server SQL logico denominato **mynewserver20170403**. Crea un nuovo database denominato **myMigratedDatabase** con un livello di servizio **Premium** e un obiettivo di servizio **P6**. Modificare questi valori in base alle esigenze specifiche dell'ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Per connettersi al database SQL di Azure da un firewall aziendale, è necessario che la porta 1433 sia aperta nel firewall. Per connettersi a SQL Istanza gestita, è necessario disporre di una connessione da [punto a sito](../managed-instance/point-to-site-p2s-configure.md) o di una connessione Express route.

Questo esempio illustra come importare un database usando SqlPackage con l'autenticazione universale di Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Utilizzo di PowerShell

> [!NOTE]
> [Un istanza gestita SQL](../managed-instance/sql-managed-instance-paas-overview.md) attualmente non supporta la migrazione di un database in un database di istanza da un file BACPAC tramite Azure PowerShell. Per eseguire l'importazione in un Istanza gestita SQL, utilizzare SQL Server Management Studio o SqlPackage.

> [!NOTE]
> I computer che elaborano le richieste di importazione/esportazione inviate tramite il portale o PowerShell devono archiviare il file BACPAC e i file temporanei generati da Data-Tier Application Framework (DacFX). Lo spazio su disco richiesto varia significativamente tra i database con le stesse dimensioni e può richiedere fino a 3 volte le dimensioni del database. I computer che eseguono la richiesta di importazione/esportazione hanno solo spazio su disco locale 450GB. Di conseguenza, alcune richieste potrebbero non riuscire con l'errore "lo spazio su disco non è sufficiente". In questo caso, la soluzione alternativa consiste nell'eseguire sqlpackage.exe in un computer con sufficiente spazio su disco locale. Quando si importano o esportano database di dimensioni maggiori di 150 GB, utilizzare SqlPackage per evitare questo problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato, ma tutto lo sviluppo futuro riguarda il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Usare il cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) per inviare una richiesta di importazione del database ad Azure. A seconda delle dimensioni del database, l'importazione può richiedere del tempo.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

È possibile usare il cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) per controllare lo stato dell'importazione. L'esecuzione del cmdlet immediatamente dopo la richiesta restituisce in genere `Status: InProgress` . L'importazione è completa quando viene visualizzato `Status: Succeeded` .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [AZ-SQL-DB-Import](/cli/azure/sql/db#az-sql-db-import) per inviare una richiesta di importazione del database ad Azure. A seconda delle dimensioni del database, l'importazione può richiedere del tempo.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Per un altro esempio di script, vedere [Importare un database da un file BACPAC](scripts/import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitazioni

- L'importazione in un database nel pool elastico non è supportata. È possibile importare i dati in un database singolo e quindi spostare quest'ultimo in un pool elastico.
- Il servizio importazione/esportazione non funziona quando l'opzione Consenti l'accesso a servizi di Azure è impostata su disattivato. Tuttavia, è possibile aggirare il problema eseguendo manualmente sqlpackage.exe da una macchina virtuale di Azure o eseguendo l'esportazione direttamente nel codice usando l'API DACFx.

## <a name="import-using-wizards"></a>Importazione con procedure guidate

È anche possibile usare queste procedure guidate.

- [Procedura guidata Importa applicazione livello dati in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Importazione/esportazione guidata SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come connettersi ed eseguire query su un database nel database SQL di Azure, vedere [Guida introduttiva: database SQL di Azure: usare SQL Server Management Studio per connettersi ai dati ed eseguire query](connect-query-ssms.md)su di essi.
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per una descrizione dell'intero processo di migrazione del database SQL Server, con raccomandazioni sulle prestazioni, vedere [Migrazione di un database SQL Server al database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni su come gestire e condividere chiavi di archiviazione e firme di accesso condiviso in modo sicuro, vedere [Guida alla sicurezza di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
