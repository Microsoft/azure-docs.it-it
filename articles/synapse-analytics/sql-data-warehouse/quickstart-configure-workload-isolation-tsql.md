---
title: "Guida introduttiva: configurare l'isolamento del carico di lavoro - T-SQL"
description: Usare T-SQL per configurare l'isolamento del carico di lavoro.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679755"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Avvio rapido: Configurare l'isolamento del carico di lavoro in un pool SQL dedicato con T-SQL

In questa Guida introduttiva verranno creati rapidamente un gruppo di carico di lavoro e un classificatore per prenotare risorse per il caricamento dei dati. Il gruppo di carico di lavoro allocherà il 20% delle risorse di sistema al carico di dati.  Il classificatore del carico di lavoro assegnerà le richieste al gruppo di carico di lavoro per i dati.  Con un isolamento del 20% per il caricamento dei dati, si tratta di risorse garantite per soddisfare i contratti di servizio.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!NOTE]
> La creazione di un'istanza Synapse SQL in Azure Synapse Analytics può tradursi in un nuovo servizio fatturabile.  Per altre informazioni, vedere [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido presuppone che l'utente abbia già un'istanza di Synapse SQL in Azure Synapse con autorizzazioni CONTROL DATABASE. Se è necessario crearne uno, vedere [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un pool SQL dedicato denominato **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Creare un account di accesso per i DataLoads

Creare un account di accesso con autenticazione di SQL Server nel database `master` con l’istruzione [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per "ELTLogin".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Create user

[Create user](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ed "ELTLogin" in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Creare un gruppo di carico di lavoro

Creare un [gruppo di carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per DataLoads con il 20% di isolamento.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Creare un classificatore del carico di lavoro

Creare un [classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per eseguire il mapping di ELTLogin al gruppo di carico di lavoro DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Visualizzare i gruppi di carico di lavoro, i classificatori e i valori di runtime esistenti

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Pulire le risorse

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Per le unità del data warehouse e i dati archiviati nel pool SQL dedicato vengono addebitati costi. Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il pool SQL dedicato non è in uso. In questo modo, vengono addebitati solo i costi per l'archiviazione dei dati. Quando si è pronti a lavorare con i dati, riprendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il pool SQL dedicato.

## <a name="next-steps"></a>Passaggi successivi

- A questo punto è stato creato un gruppo di carico di lavoro. Eseguire alcune query come ELTLogin per verificarne le prestazioni. Vedere [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per visualizzare le query e il gruppo di carico di lavoro assegnato.
- Per altre informazioni sulla gestione dei carichi di lavoro di SQL Synapse, vedere [Gestione dei carichi di lavoro](sql-data-warehouse-workload-management.md) e [Isolamento dei carichi di lavoro](sql-data-warehouse-workload-isolation.md).
