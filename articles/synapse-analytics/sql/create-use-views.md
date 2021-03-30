---
title: Creare e usare le viste nel pool SQL serverless
description: Questa sezione illustra come creare e usare le viste per il wrapping delle query del pool SQL serverless. Le viste consentono di riutilizzare tali query. Sono anche necessarie se si vogliono usare strumenti, come Power BI, in combinazione con il pool SQL serverless.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462292"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Creare e usare le viste usando il pool SQL serverless in Azure Synapse Analytics

Questa sezione illustra come creare e usare le viste per il wrapping delle query del pool SQL serverless. Le viste consentono di riutilizzare tali query. Sono anche necessarie se si vogliono usare strumenti, come Power BI, in combinazione con il pool SQL serverless.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel creare un database in cui verrà creata la vista e inizializzare gli oggetti necessari per l'autenticazione in archiviazione di Azure mediante l’esecuzione di [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Tutte le query in questo articolo verranno eseguite nel database di esempio.

## <a name="create-a-view"></a>Creare una vista

È possibile creare viste nello stesso modo in cui si creano le normali viste di SQL Server. La query seguente crea una vista che legge il file *population.csv*.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

La vista in questo esempio applica la funzione `OPENROWSET` che usa il percorso assoluto dei file sottostanti. Se si dispone di `EXTERNAL DATA SOURCE` con un URL radice della risorsa di archiviazione, è possibile usare `OPENROWSET` con `DATA_SOURCE` e relativo il percorso di file:

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Usare una vista

È possibile usare viste nelle query nello stesso modo in cui vengono usate nelle query di SQL Server.

La query seguente illustra l'uso della vista *population* creata nella sezione [Creare una vista](#create-a-view). Restituisce i nomi dei paesi/aree geografiche con la popolazione del 2019 in ordine decrescente.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire una query su tipi di file diversi, vedere gli articoli [Eseguire query su un singolo file CSV](query-single-csv-file.md), [Eseguire query su file Per](query-parquet-files.md) e [Eseguire query su file JSON](query-json-files.md).
