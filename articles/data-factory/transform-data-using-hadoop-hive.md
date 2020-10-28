---
title: Trasformare i dati usando l'attività Hive di Hadoop
description: Informazioni su come usare l'attività Hive in una data factory di Azure per eseguire query Hive in un cluster HDInsight su richiesta o nel proprio cluster HDInsight.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 20deb1a235843bf5fe5c42c513e1d8d2117c65c5
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637905"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Trasformare i dati usando l'attività Hive di Hadoop in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-hive-activity.md)
> * [Versione corrente](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività Hive di HDInsight in una [pipeline](concepts-pipelines-activities.md) di Data Factory esegue query Hive sul [proprio](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight o sul cluster HDInsight [on demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati). 

## <a name="syntax"></a>Sintassi

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Dettagli sintassi
| Proprietà            | Descrizione                                                  | Obbligatoria |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nome dell'attività                                         | Sì      |
| description         | Testo descrittivo per lo scopo dell'attività                | No       |
| type                | Per l'Attività Hive, il tipo di attività è HDinsightHive        | Sì      |
| linkedServiceName   | Riferimento al cluster HDInsight registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| scriptLinkedService | Riferimento a un servizio collegato Archiviazione di Azure usato per memorizzare lo script Hive da eseguire. Qui sono supportati solo i servizi collegati **[Archiviazione BLOB di Azure](./connector-azure-blob-storage.md)** e **[ADLS Gen2](./connector-azure-data-lake-storage.md)** . Se non si specifica questo servizio collegato, viene usato il servizio collegato Archiviazione di Azure definito nel servizio collegato HDInsight.  | No       |
| scriptPath          | Specificare il percorso del file di script archiviato nel servizio Archiviazione di Azure indicato da scriptLinkedService. Il nome del file distingue tra maiuscole e minuscole. | Sì      |
| getDebugInfo        | Specifica quando i file di log vengono copiati nell'Archiviazione di Azure usata dal cluster HDInsight (o) indicata da scriptLinkedService. Valori consentiti: None, Always e Failure. Valore predefinito: No. | No       |
| argomenti           | Specifica una matrice di argomenti per un processo Hadoop. Gli argomenti vengono passati a ogni attività come argomenti della riga di comando. | No       |
| defines             | Specificare i parametri come coppie chiave/valore per fare riferimento a essi nello script Hive. | No       |
| queryTimeout        | Valore di timeout di query (in minuti). Applicabile se il cluster HDInsight è abilitato per Enterprise Security Package. | No       |

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività di esecuzione batch Azure Machine Learning Studio (classica)](transform-data-using-machine-learning.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)