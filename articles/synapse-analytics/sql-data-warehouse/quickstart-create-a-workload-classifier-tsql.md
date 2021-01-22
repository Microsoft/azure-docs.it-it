---
title: 'Guida di avvio rapido: Creare un classificatore del carico di lavoro - T-SQL'
description: Usare T-SQL per creare un classificatore di carico di lavoro con priorità alta.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677369"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Guida di avvio rapido: Creare un classificatore di carico di lavoro con T-SQL

In questa Guida di avvio rapido si creerà rapidamente un classificatore di carico di lavoro con priorità alta per il direttore generale dell'organizzazione. Il classificatore di carico di lavoro consentirà alle query del direttore generale di avere la precedenza su quelle con priorità inferiore nella coda.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!NOTE]
> La creazione di un'istanza del pool SQL dedicato in Azure Synapse Analytics può tradursi in un nuovo servizio fatturabile.  Per altre informazioni, vedere [Prezzi di Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido presuppone che sia già stato effettuato il provisioning di un pool SQL dedicato in Azure Synapse Analytics e che si abbiano le autorizzazioni CONTROL DATABASE. Se è necessario crearne uno, vedere [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un pool SQL dedicato denominato **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Creare un accesso per IlCEO

Creare un account di accesso con autenticazione di SQL Server nel database `master` con l’istruzione [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per “IlCEO”.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Create user

[Creare l'utente](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) "TheCEO" in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Creare un classificatore del carico di lavoro

Creare un [classificatore di carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per "TheCEO" con priorità alta.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Visualizzare i classificatori esistenti

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Pulire le risorse

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Per le unità del data warehouse e i dati archiviati nel pool SQL dedicato vengono addebitati costi. Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il pool SQL dedicato non è in uso. In questo modo, vengono addebitati solo i costi per l'archiviazione dei dati. Quando si è pronti a lavorare con i dati, riprendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il pool SQL dedicato.

Seguire questa procedura per pulire le risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il pool SQL dedicato.

    ![Pulire le risorse](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Per sospendere il calcolo, selezionare il pulsante **Pausa**. Quando il pool SQL dedicato è sospeso, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, selezionare **Avvia**.

3. Per rimuovere il pool SQL dedicato in modo da non ricevere addebiti per operazioni di calcolo o archiviazione, selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- È stato creato un classificatore di carico di lavoro. Eseguire alcune query come IlCEO per verificarne le prestazioni. Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) per visualizzare le query e la loro priorità.
- Per altre informazioni sulla gestione dei carichi di lavoro del pool SQL dedicato, vedere [Priorità del carico di lavoro](sql-data-warehouse-workload-importance.md) e [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md).
- Vedere gli articoli sulle procedure per [configurare la priorità del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e per [gestire e monitorare la priorità del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
