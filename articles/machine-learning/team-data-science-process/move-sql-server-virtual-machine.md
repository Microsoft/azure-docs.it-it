---
title: Spostare i dati in una macchina virtuale di SQL Server - Processo di data science per i team
description: Spostare i dati da file flat o da un Server SQL locale a SQL Server su VM di Azure
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721371"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Spostamento dei dati in SQL Server in una macchina virtuale di Azure

Questo articolo descrive le opzioni per lo spostamento dei dati da file flat con estensione csv o tsv o da SQL Server locale a SQL Server in una macchina virtuale di Azure. Queste attività per lo spostamento dei dati nel cloud fanno parte del Processo di analisi scientifica dei dati per i team.

Per un argomento che descrive le opzioni per lo spostamento dei dati a un database SQL di Azure per Machine Learning, vedere [Spostare i dati a un Database di SQL Azure per Azure Machine Learning](move-sql-azure.md).

Nella tabella seguente vengono riepilogate le opzioni per lo spostamento dei dati in SQL Server in una macchina virtuale Azure.

| <b>fonte</b> | <b>DESTINAZIONE: SQL Server in VM di Azure</b> |
| --- | --- |
| <b>File Flat</b> |1. Utilità di copia di massa della riga di comando <a href="#insert-tables-bcp">(BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Inserimento di massa query SQL </a><br> 3. <a href="#sql-builtin-utilities">Utilità grafiche integrate in SQL Server</a> |
| <b>Server SQL locale</b> |1. Distribuire un database di <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server in una procedura guidata di Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">Esportare in un file piatto</a><br> 3. <a href="#sql-migration">Migrazione guidata database SQL </a> <br> 4. <a href="#sql-backup">Backup e ripristino database </a><br> |

In questo documento si presuppone che i comandi SQL vengano eseguiti da SQL Server Management StudioSQL Server Management Studio o Da Esplora database di Visual Studio.This document assumes that SQL commands are executed from SQL Server Management Studio sql or Visual Studio Database Explorer.

> [!TIP]
> In alternativa, è possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per creare e pianificare una pipeline che sposta i dati a una macchina virtuale di SQL Server in Azure. Per altre informazioni, vedere [Copia di dati con Data factory di Azure (Attività di copia)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Prerequisiti
Il tutorial presuppone:

* Una **sottoscrizione di Azure.** Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un account di archiviazione di **Azure.** In questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md) . Dopo avere creato l'account di archiviazione, sarà necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere Gestire le chiavi di [accesso dell'account di archiviazione.](../../storage/common/storage-account-keys-manage.md)
* Provisioning di **SQL Server in una VM di Azure**. Per le istruzioni, vedere [Configurare una macchina virtuale SQL Server di Azure come server IPython Notebook per l'analisi avanzata](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Installazione e configurazione di **Azure PowerShell** in locale. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Spostamento di dati da un'origine di file flat a SQL Server su una VM di Azure
Se i dati si trovano in un file flat (organizzati in un formato righe/colonne), possono essere spostati a una macchina virtuale di SQL Server attraverso i seguenti metodi:

1. [Utilità di copia bulk della riga di comando (BCP)](#insert-tables-bcp)
2. [Inserimento di massa query SQL](#insert-tables-bulkquery)
3. [Utilità grafiche integrate in SQL Server (importazione/esportazione, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Utilità copia di massa della riga di comando (BCP) 
BCP è un'utilità della riga di comando installata con SQL Server e rappresenta uno dei metodi più rapidi per spostare i dati. Funziona in tutte e tre le varianti di SQL Server (SQL Server locale, SQL Azure e MACCHINA virtuale di SQL Server in Azure).

> [!NOTE]
> **Dove devono trovarsi i dati per eseguire la copia BCP?**  
> Anche se non è obbligatorio che i file contenenti i dati di origine si trovino nello stesso computer del server SQL di destinazione, questo garantisce trasferimenti più rapidi, a causa della differenza tra velocità di rete e velocità di I/O dei dischi locali. È possibile spostare i file flat contenenti i dati nel computer dove è installato SQL Server usando diversi strumenti per la copia dei file quali [AZCopy](../../storage/common/storage-use-azcopy.md), [Esplora archivi di Azure](https://storageexplorer.com/) o la funzione di copia/incolla di Windows tramite Remote Desktop Protocol (RDP).
>
>

1. Assicurarsi che il database e le tabelle vengano create nel database di SQL Server di destinazione. Ecco un esempio di come procedere utilizzando i comandi `Create Database` e `Create Table`:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Generare il file di formato che descrive lo schema per la tabella eseguendo il comando seguente dalla riga di comando del computer in cui è installato bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Inserire i dati nel database utilizzando il comando bcp, che dovrebbe funzionare dalla riga di comando quando SQL Server è installato nello stesso computer:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Ottimizzazione inserimenti BCP** Per ottimizzare gli inserimenti, fare riferimento al seguente articolo ["Linee guida per ottimizzare l'importazione di massa"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) .
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Parallelizzazione delle operazioni di inserimento per uno spostamento dei dati più veloce
Se i dati da spostare sono di grandi dimensioni, è possibile velocizzare gli elementi eseguendo contemporaneamente più comandi BCP in parallelo in uno script di PowerShell.If the data you are moving is large, you can speed up things by simultaneously executing multiple BCP commands in parallel in a PowerShell Script.

> [!NOTE]
> **Inserimento di Big Data** Per ottimizzare il caricamento dei dati per set di dati grandi e molto grandi, partizionare le tabelle dei database logici e fisici mediante più gruppi di file e tabelle di partizione. Per ulteriori informazioni sulla creazione e sul caricamento dei dati in tabelle di partizione, vedere [Caricamento parallelo di tabelle di partizione SQL](parallel-load-sql-partitioned-tables.md).
>
>

Lo script di esempio PowerShell seguente illustra gli inserimenti mediante bcp:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Inserimento di massa query SQL
L'[inserimento di massa di query SQL](https://msdn.microsoft.com/library/ms188365) può essere usato per importare dati nel database da file basati su righe/colonne (i tipi supportati sono indicati nell'argomento [Preparazione dei dati per l'importazione o l'esportazione bulk (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Ecco alcuni comandi di esempio per l'inserimento di massa:  

1. Analizzare i dati e impostare le opzioni personalizzate prima dell'importazione per assicurarsi che il database SQL Server presupponga lo stesso formato per tutti i campi speciali, ad esempio le date. Ecco un esempio di come impostare il formato della data come anno-mese-giorno (se i dati contengono la data in formato anno-mese-giorno):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. portare i dati utilizzando le istruzioni per eseguire importazioni di massa

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Utilità integrate in SQL Server
È possibile usare SQL Server Integration Services (SSIS) per importare dati nella macchina virtuale di SQL Server in Azure da un file flat.
SSIS è disponibile in due ambienti studio. Per ulteriori informazioni, vedere [Integration Services (SSIS) e ambienti Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Per informazioni dettagliate su SQL Server Data Tools, vedere [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Per informazioni dettagliate sull'importazione/esportazione guidata, vedere [Importazione/esportazione guidata di SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Spostamento dei dati da SQL Server locale a SQL Server in una VM di Azure
È inoltre possibile utilizzare le strategie di migrazione seguenti:

1. [Distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Esporta in file flat](#export-flat-file)
3. [Migrazione guidata database SQL](#sql-migration)
4. [Backup e ripristino del database](#sql-backup)

Descriviamo ognuna di queste opzioni di seguito:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure
La **Distribuzione di un Database SQL Server in una macchina virtuale di Microsoft Azure** è un modo semplice e consigliato per spostare dati da un'istanza di SQL Server locale a un SQL Server in una macchina virtuale di Azure. Per passaggi dettagliati, nonché per una descrizione delle altre alternative, vedere [Migrazione di un database a SQL Server su una macchina virtuale di Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Esportazione in un file flat
È possibile usare diversi metodi per l'esportazione di massa dei dati dal Server locale SQL come descritto nell'argomento [Importazione ed esportazione dei dati in massa (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . In questo documento si parla di Bulk Copy Program (BCP) come esempio. Una volta che i dati sono esportati in un file flat, possono essere importati in un altro server SQL mediante l'importazione di massa.

1. Esportare i dati dal Server locale SQL in un file mediante l'utilità bcp come indicato di seguito

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Creare il database e la tabella nella macchina virtuale di SQL Server in Azure tramite `create database` e `create table` per lo schema della tabella esportato nel passaggio 1.
3. Creare un file di formato per la descrizione dello schema della tabella dei dati da importare/esportare. I dettagli del file di formato sono descritti in [Creazione di un file di formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Creazione di file di formato quando si esegue BCP dal computer SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Creazione di file di formato quando si esegue BCP in remoto rispetto a SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilizzare uno dei metodi descritti nella sezione [Spostamento dei dati dall'origine file](#filesource_to_sqlonazurevm) per spostare i dati dai file flat in SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Migrazione guidata database SQL
[Migrazione guidata database SQL Server](https://sqlazuremw.codeplex.com/) fornisce un modo semplice per spostare i dati tra due istanze del server SQL. Consente all'utente di mappare lo schema dei dati tra origini e tabelle di destinazione, scegliere i tipi di colonna e varie altre funzionalità. Utilizza la copia di massa (BCP) dietro le quinte. Di seguito è riportata una schermata della schermata iniziale della procedura guidata di migrazione del database SQL.  

![Migrazione guidata in SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Backup e ripristino database
SQL Server supporta:

1. La [funzionalità di backup e ripristino del database](https://msdn.microsoft.com/library/ms187048.aspx) (sia in un file locale o in un'esportazione bacpac in un BLOB) e [applicazioni livello dati](https://msdn.microsoft.com/library/ee210546.aspx) (tramite bacpac).
2. Possibilità di creare direttamente le macchine virtuali SQL Server in Azure con un database copiato o di copiare in un database esistente di SQL Azure. Per altre informazioni, vedere [Use the Copy Database Wizard](https://msdn.microsoft.com/library/ms188664.aspx).

Di seguito è riportata una schermata delle opzioni di backup e ripristino del database da SQL Server Management Studio.

![Strumento di importazione di SQL Server][1]

## <a name="resources"></a>Risorse
[Eseguire la migrazione di un database a SQL Server in una macchina virtuale di AzureMigrate a Database to SQL Server on an Azure VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Panoramica di SQL Server in Macchine virtuali di AzureSQL Server on Azure Virtual Machines overview](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
