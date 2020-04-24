---
title: Configurare la replica in un database di istanza gestita
description: Informazioni sulla configurazione della replica transazionale tra un server di pubblicazione/distribuzione di istanza gestita di database SQL di Azure e un Sottoscrittore di istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299074"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurare la replica in un database dell'istanza gestita di database SQL di Azure

La replica transazionale consente di replicare i dati in un database dell'istanza gestita di database SQL di Azure da un database di SQL Server o da un altro database dell'istanza. 

Questo articolo illustra come configurare la replica tra un server di pubblicazione o un server di distribuzione di istanze gestite e un Sottoscrittore di istanza gestita. 

![Eseguire la replica tra due istanze gestite](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

È anche possibile usare la replica transazionale per eseguire il push delle modifiche apportate in un database di istanza nell'istanza gestita di database SQL di Azure per:

- Un database SQL Server.
- Un database singolo nel database SQL di Azure.
- Un database in pool in un pool elastico del database SQL di Azure.
 
La replica transazionale è in anteprima pubblica nell' [istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Un'istanza gestita può ospitare database di pubblicazione, distribuzione e sottoscrittore. Vedere [configurazioni di replica transazionale](sql-database-managed-instance-transactional-replication.md#common-configurations) per le configurazioni disponibili.

  > [!NOTE]
  > - Questo articolo ha lo scopo di guidare un utente nella configurazione della replica con un'istanza gestita di database di Azure da end-to-end, a partire dalla creazione del gruppo di risorse. Se sono già state distribuite istanze gestite, procedere con il [passaggio 4](#4---create-a-publisher-database) per creare il database del server di pubblicazione oppure il [passaggio 6](#6---configure-distribution) se si dispone già di un server di pubblicazione e di un database Sottoscrittore e si è pronti per iniziare la configurazione della replica.  
  > - Questo articolo consente di configurare il server di pubblicazione e il server di distribuzione nella stessa istanza gestita. Per inserire il server di distribuzione in un'istanza gestita separata, vedere l'esercitazione [configurare la replica tra un server di pubblicazione mi e un server di distribuzione mi](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Requisiti

La configurazione di un'istanza gestita per fungere da server di pubblicazione e/o da un server di distribuzione richiede:

- Che l'istanza gestita del server di pubblicazione si trovi nella stessa rete virtuale del server di distribuzione e nel Sottoscrittore oppure che sia stato stabilito il [peering vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) tra le reti virtuali di tutte e tre le entità. 
- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.
- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.
- La porta 445 (TCP in uscita) è aperta nelle regole di sicurezza di NSG per le istanze gestite per accedere alla condivisione file di Azure.  Se viene visualizzato l'errore "non è stato possibile connettersi al \<nome dell'account di archiviazione di archiviazione di Azure> con errore del sistema operativo 53", sarà necessario aggiungere una regola in uscita alla NSG della Subnet SQL istanza gestita appropriata.


 > [!NOTE]
 > I database singoli e in pool nel database SQL di Azure possono essere usati solo come sottoscrittori. 


## <a name="features"></a>Caratteristiche

Supporto:

- Combinazione di replica transazionale e replica snapshot di istanze locali e gestite di SQL Server nel database SQL di Azure.
- I sottoscrittori possono trovarsi in database SQL Server locali, in database singoli/istanze gestite nel database SQL di Azure o in pool di database in pool elastici del database SQL di Azure.
- Replica unidirezionale o bidirezionale.

In un'istanza gestita del database SQL di Azure non sono supportate le funzionalità seguenti:

- [Sottoscrizioni aggiornabili](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Replica geografica attiva](sql-database-active-geo-replication.md) con replica transazionale. Anziché la replica geografica attiva, usare i [gruppi di failover automatico](sql-database-auto-failover-group.md), ma si noti che la pubblicazione deve essere [eliminata manualmente](sql-database-managed-instance-transact-sql-information.md#replication) dall'istanza gestita primaria e ricreata nell'istanza gestita secondaria dopo il failover.  
 
## <a name="1---create-a-resource-group"></a>1-creare un gruppo di risorse

Usare il [portale di Azure](https://portal.azure.com) per creare un gruppo di risorse con il `SQLMI-Repl`nome.  

## <a name="2---create-managed-instances"></a>2-creare istanze gestite

Usare il [portale di Azure](https://portal.azure.com) per creare due [istanze gestite](sql-database-managed-instance-create-tutorial-portal.md) nella stessa rete virtuale e nella stessa subnet. Ad esempio, denominare le due istanze gestite:

- `sql-mi-pub`(insieme ad alcuni caratteri per la sequenza casuale)
- `sql-mi-sub`(insieme ad alcuni caratteri per la sequenza casuale)

Sarà anche necessario [configurare una macchina virtuale di Azure per la connessione](sql-database-managed-instance-configure-vm.md) alle istanze gestite del database SQL di Azure. 

## <a name="3---create-azure-storage-account"></a>3: creare un account di archiviazione di Azure

[Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../storage/files/storage-how-to-create-file-share.md) al suo interno. 

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare le chiavi di accesso alle archiviazione nel formato:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4-creare un database del server di pubblicazione

Connettersi all'istanza `sql-mi-pub` gestita usando SQL Server Management Studio ed eseguire il codice Transact-SQL (T-SQL) seguente per creare il database del server di pubblicazione:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-creare un database Sottoscrittore

Connettersi all'istanza `sql-mi-sub` gestita usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per creare il database del Sottoscrittore vuoto:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-configurare la distribuzione

Connettersi all'istanza `sql-mi-pub` gestita usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per configurare il database di distribuzione. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-configurare Publisher per l'utilizzo del server di distribuzione 

Nell'istanza `sql-mi-pub`gestita del server di pubblicazione modificare l'esecuzione della query in modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) ed eseguire il codice seguente per registrare il nuovo server di distribuzione nel server di pubblicazione. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Assicurarsi di usare solo le barre rovesciate (`\`) per il parametro file_storage. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file. 

Questo script configura un server di pubblicazione locale nell'istanza gestita, aggiunge un server collegato e crea un set di processi per la SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8-creare una pubblicazione e un Sottoscrittore

Utilizzando la modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , eseguire lo script T-SQL seguente per abilitare la replica per il database e configurare la replica tra server di pubblicazione, server di distribuzione e Sottoscrittore. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-modificare i parametri dell'agente

Istanza gestita di database SQL di Azure sta attualmente riscontrando problemi di back-end con la connettività con gli agenti di replica. Anche se il problema viene risolto, la soluzione alternativa per aumentare il valore di timeout di accesso per gli agenti di replica. 

Eseguire il comando T-SQL seguente nel server di pubblicazione per aumentare il timeout di accesso: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Eseguire di nuovo il comando T-SQL seguente per impostare di nuovo il timeout di accesso sul valore predefinito, se necessario:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Riavviare tutti e tre gli agenti per applicare le modifiche. 

## <a name="10---test-replication"></a>10-replica di test

Dopo aver configurato la replica, è possibile testarla inserendo nuovi elementi nel server di pubblicazione e osservando le modifiche che si propagano nel sottoscrittore. 

Eseguire il frammento di codice T-SQL seguente per visualizzare le righe nel sottoscrittore:

```sql
select * from dbo.ReplTest
```

Eseguire il frammento di codice T-SQL seguente per inserire righe aggiuntive nel server di pubblicazione, quindi controllarle di nuovo nel sottoscrittore. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare la pubblicazione, eseguire il comando T-SQL seguente:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Per rimuovere l'opzione di replica dal database, eseguire il comando T-SQL seguente:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Per disabilitare la pubblicazione e la distribuzione, eseguire il comando T-SQL seguente:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

È possibile eseguire la pulizia delle risorse di Azure eliminando [le risorse dell'istanza gestita dal gruppo di risorse](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e quindi eliminando il gruppo `SQLMI-Repl`di risorse. 

   
## <a name="see-also"></a>Vedi anche

- [Replica transazionale](sql-database-managed-instance-transactional-replication.md)
- [Esercitazione: configurare la replica transazionale tra un server di pubblicazione MI e SQL Server sottoscrittore](sql-database-managed-instance-configure-replication-tutorial.md)
- [Informazioni su Istanza gestita](sql-database-managed-instance.md)
