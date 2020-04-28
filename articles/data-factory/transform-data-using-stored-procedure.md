---
title: Trasformare i dati tramite l'attività stored procedure
description: Illustra l'uso dell'attività stored procedure di SQL Server per richiamare una stored procedure in un database SQL di Azure o in Data Warehouse da una pipeline di Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418814"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività stored procedure di SQL Server in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-stored-proc-activity.md)
> * [Versione corrente](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le attività di trasformazione dei dati in una [pipeline](concepts-pipelines-activities.md) di Data factory trasformano ed elaborano i dati non elaborati in stime e approfondimenti. L'attività di stored procedure è una delle attività di trasformazione supportate da Data factory. Questo articolo si basa sull'articolo relativo alla [trasformazione dei dati](transform-data.md), che offre una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate in Data Factory.

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati). 

È possibile usare l'attività stored procedure per richiamare una stored procedure in uno dei seguenti archivi dati presenti in azienda o in una macchina virtuale di Azure: 

- database SQL di Azure
- Azure SQL Data Warehouse
- Database di SQL Server.  Se si usa SQL Server, è necessario installare Runtime di integrazione (self-hosted) nello stesso computer che ospita il database o in un computer separato che ha accesso al database. Runtime di integrazione (self-hosted) è un componente che connette in modo sicuro e gestito origini dati presenti in locale o in macchine virtuali di Azure ai servizi cloud. Per informazioni dettagliate, vedere l'articolo [runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md) .

> [!IMPORTANT]
> Quando si copiano dati in SQL Server o nel Database SQL di Azure, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure tramite la proprietà **sqlWriterStoredProcedureName**. Per informazioni dettagliate sulla proprietà, vedere gli articoli connettore seguenti: [Database SQL di Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Non è possibile richiamare una stored procedure durante la copia dei dati in un Azure SQL Data Warehouse tramite un'attività di copia. Tuttavia, è possibile usare l'attività di stored procedure per richiamare una stored procedure in un SQL Data Warehouse. 
>
> Quando si copiano dati in SQL Server, nel Database SQL di Azure o in Azure SQL Data Warehouse, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure che consenta di leggere i dati dal database di origine tramite la proprietà **sqlReaderStoredProcedureName**. Per altre informazioni, vedere gli articoli connettore seguenti: [Database SQL di Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Dettagli sintassi
Di seguito è riportato il formato JSON per la definizione di un'attività di Stored Procedure:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

La tabella seguente illustra queste proprietà JSON:

| Proprietà                  | Descrizione                              | Obbligatoria |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nome dell'attività                     | Sì      |
| description               | Testo descrittivo per lo scopo dell'attività | No       |
| type                      | Per l'attività di stored procedure, il tipo di attività è **SqlServerStoredProcedure** | Sì      |
| linkedServiceName         | Riferimento al **database SQL di Azure**, ad **Azure SQL Data Warehouse** o a **SQL Server** registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| storedProcedureName       | Specificare il nome della stored procedure da richiamare. | Sì      |
| storedProcedureParameters | Specificare i valori dei parametri della stored procedure. Usare `"param1": { "value": "param1Value","type":"param1Type" }` per passare i valori dei parametri e i tipi nativi corrispondenti supportati dall'origine dati. Se per un parametro è necessario passare Null, usare `"param1": { "value": null }` (tutte lettere minuscole). | No       |

## <a name="parameter-data-type-mapping"></a>Mapping dei tipi di dati dei parametri
Il tipo di dati specificato per il parametro è il tipo di Azure Data Factory che esegue il mapping al tipo di dati nell'origine dati in uso. È possibile trovare i mapping dei tipi di dati per l'origine dati nell'area connettori. Alcuni esempi sono

| origine dati          | Mapping dei tipi di dati |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| database SQL di Azure   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informazioni sull'errore

Quando una stored procedure ha esito negativo e restituisce i dettagli dell'errore, non è possibile acquisire le informazioni sull'errore direttamente nell'output dell'attività. Tuttavia, Data Factory invia tutti gli eventi di esecuzione attività a Monitoraggio di Azure. Oltre a inviare gli eventi a Monitoraggio di Azure, Data Factory esegue il push dei dettagli degli errori. È possibile, ad esempio, configurare avvisi tramite posta elettronica da questi eventi. Per altre informazioni, vedere [Avvisi e monitoraggio delle data factory con Monitoraggio di Azure](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività hive](transform-data-using-hadoop-hive.md)
* [Attività di Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
