---
title: Connettersi ad Azure SQL Data Warehouse
description: Connettersi ad Azure SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72825f588ff92383858020cdbcd92c7de3078ed5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350649"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Connettersi ad Azure SQL Data Warehouse
Connettersi ad Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Trovare il nome del server
Il nome del server nell'esempio seguente è samplesvr.database.windows.net. Per trovare il nome completo del server, procedere come segue:

1. Passare al [portale di Azure][Azure portal].
2. Fare clic su **SQL Data Warehouse**.
3. Fare clic sul data warehouse a cui connettersi.
4. Individuare il nome completo del server.
   
    ![Nome completo del server](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Driver supportati e stringhe di connessione
Azure SQL Data Warehouse supporta [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] e [JDBC][JDBC]. Per trovare la versione e la documentazione più recenti, fare clic su uno dei driver precedenti. Per generare automaticamente la stringa di connessione per il driver in uso dal portale di Azure, fare clic su **Mostra stringhe di connessione del database** nell'esempio precedente. Di seguito sono riportati alcuni esempi di come si presenta la stringa di connessione per ogni driver.

> [!NOTE]
> Per preservare la connessione in caso di brevi periodi di indisponibilità, può essere opportuno impostare il timeout di connessione su 300 secondi.
> 
> 

### <a name="adonet-connection-string-example"></a>Esempio di stringa di connessione ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Esempio di stringa di connessione ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Esempio di stringa di connessione PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Esempio di stringa di connessione JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Impostazioni di connessione
SQL Data Warehouse standardizza alcune impostazioni durante la connessione e la creazione di oggetti. Queste impostazioni, di cui non è possibile eseguire l'override, includono:

| Impostazione del database | valore |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ATTIVA |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ATTIVA |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Passaggi successivi
Per connettersi ed eseguire query con Visual Studio, vedere [Eseguire query con Visual Studio][Query with Visual Studio]. Per altre informazioni sulle opzioni di autenticazione, vedere [Autenticazione in Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]:sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]:sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


