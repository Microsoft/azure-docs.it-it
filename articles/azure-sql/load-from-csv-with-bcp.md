---
title: Caricare dati da un file CSV in un database (BCP)
description: Per dati di piccole dimensioni, usa bcp per importare dati nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 216410d423a70037588a6fb596771a702a13c003
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472247"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Caricare dati da CSV nel database SQL di Azure o in SQL Istanza gestita (file flat)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

È possibile usare l'utilità da riga di comando bcp per importare dati da un file CSV nel database SQL di Azure o in Azure SQL Istanza gestita.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari:

* Un database nel database SQL di Azure
* Utilità della riga di comando bcp installata
* Utilità della riga di comando sqlcmd installata

È possibile scaricare le utilità bcp e SQLCMD dalla [documentazione di Microsoft sqlcmd] [ https://docs.microsoft.com/sql/tools/sqlcmd-utility?view=sql-server-ver15 ].

### <a name="data-in-ascii-or-utf-16-format"></a>Dati in formato ASCII o UTF-16

Se si prova a eseguire questa esercitazione con dati personalizzati, è necessario che i dati usino la codifica ASCII o UTF-16, perché bcp non supporta UTF-8.

## <a name="1-create-a-destination-table"></a>1. creare una tabella di destinazione

Definire una tabella nel database SQL come tabella di destinazione. Le colonne della tabella devono corrispondere ai dati in ogni riga del file di dati.

Per creare una tabella, aprire un prompt dei comandi e usare sqlcmd.exe per eseguire i comandi seguenti:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. creare un file di dati di origine

Aprire il Blocco note, copiare le righe di dati seguenti in un nuovo file di testo e quindi salvare il file nella directory temporanea locale, C:\Temp\DimDate2.txt. I dati hanno formato ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Facoltativo) Per esportare i dati personalizzati da un database di SQL Server, aprire un prompt dei comandi ed eseguire il comando seguente. Sostituire TableName, ServerName, DatabaseName, Username e Password con le informazioni personalizzate.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. caricare i dati

Per caricare i dati, aprire un prompt dei comandi ed eseguire il comando seguente, sostituendo i valori per nome server, nome database, nome utente e password con le informazioni personalizzate.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Usare questo comando per verificare che i dati siano stati caricati correttamente.

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

I risultati dovrebbero avere l'aspetto seguente:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Passaggi successivi

Per eseguire la migrazione di un database SQL Server, vedere [Migrazione di un database SQL Server al database SQL nel cloud](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
