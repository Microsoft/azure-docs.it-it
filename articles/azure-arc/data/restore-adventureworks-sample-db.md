---
title: Ripristinare il database di esempio AdventureWorks in SQL Istanza gestita
description: Ripristinare il database di esempio AdventureWorks in SQL Istanza gestita
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641801"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>Ripristinare il database di esempio AdventureWorks in SQL Istanza gestita-Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure) è un database di esempio contenente un database OLTP spesso usato nelle esercitazioni ed esempi. Viene fornita e gestita da Microsoft come parte del [repository GitHub SQL Server Samples](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Questo documento descrive un processo semplice per ripristinare il database di esempio AdventureWorks in SQL Istanza gestita-Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Scaricare il file di backup AdventureWorks

Scaricare il file di backup (con estensione bak) di AdventureWorks nel contenitore SQL Istanza gestita. In questo esempio, usare il `kubectl exec` comando per eseguire in modalità remota un comando all'interno del contenitore SQL istanza gestita per scaricare il file con estensione bak nel contenitore. Scaricare questo file da qualsiasi percorso accessibile da `wget` se sono presenti altri file di backup del database che si desidera inserire nel contenitore SQL istanza gestita. Una volta che si trova all'interno del contenitore SQL Istanza gestita, è facile eseguire il ripristino usando `RESTORE DATABASE` T-SQL standard.

Eseguire un comando simile al seguente per scaricare il file con estensione bak sostituendo il valore del nome del Pod e il nome dello spazio dei nomi prima di eseguirlo.
> [!NOTE]
>  Il contenitore dovrà avere connettività Internet superiore a 443 per scaricare il file da GitHub

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Esempio

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>Ripristinare il database AdventureWorks

Analogamente, è possibile eseguire un `kubectl` comando exec per usare lo strumento dell'interfaccia della riga di comando `sqlcmd` incluso nel contenitore SQL istanza gestita per eseguire il comando T-SQL per ripristinare il database.

Eseguire un comando simile al seguente per ripristinare il database. Sostituire il valore del nome del Pod, la password e il nome dello spazio dei nomi prima di eseguirlo.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Esempio

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
