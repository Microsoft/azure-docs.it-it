---
title: Pipeline e attività in Azure Data Factory | Microsoft Docs
description: Informazioni sulle pipeline e le attività in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 4cf62e609505487961dbfbab3f46c37d54e8f50b
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259055"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipeline e attività in Azure Data Factory
> [!div class="op_single_selector" title1="Seleziona la versione del servizio Data Factory che stai utilizzando
:"]
> * [Versione 1](v1/data-factory-create-pipelines.md)
> * [Versione corrente](concepts-pipelines-activities.md)

Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per creare flussi di lavoro completi basati sui dati per gli scenari di elaborazione e trasferimento dei dati.

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività che insieme eseguono un compito. Ad esempio, una pipeline può contenere un set di attività che inseriscono ed eseguono la pulizia dei dati di log e quindi avviano un processo Spark in un cluster HDInsight per analizzare i dati di log. La pipeline offre il vantaggio di poter gestire le attività come un set anziché singolarmente. È ad esempio possibile distribuire e pianificare la pipeline anziché distribuire e pianificare le attività separatamente.

Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare o trasformare i dati dall'archivio BLOB per produrre dati di output. Infine, usare una seconda attività di copia per copiare i dati di output in un Azure SQL Data Warehouse in cui vengono compilate le soluzioni di report di business intelligence, BI.

Il servizio Data Factory supporta tre tipi di attività: [attività di spostamento dei dati](copy-activity-overview.md), [attività di trasformazione dei dati](transform-data.md) e [attività di controllo](control-flow-web-activity.md). Un'attività può non avere alcun [set di dati](concepts-datasets-linked-services.md) di input o può averne più di uno e generare uno o più [set di dati di output](concepts-datasets-linked-services.md). Nel diagramma seguente viene illustrata la relazione tra attività, set di dati e pipeline in Data Factory:

![Relazione tra set di dati, attività e pipeline](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Dopo aver creato un set di dati, è possibile usarlo con le attività in una pipeline. Ad esempio, un set di dati può essere configurato come set di dati di input o di output di un'attività di copia o un'attività HDInsightHive. Per altre informazioni sui set di dati, vedere l'articolo [Set di dati in Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Attività di spostamento dei dati
L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Il servizio Data Factory supporta gli archivi dati elencati nella tabella in questa sezione. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Per altre informazioni, vedere l'articolo [Copy Activity in Azure Data Factory](copy-activity-overview.md) (Attività di copia in Azure Data Factory).

## <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
Azure Data Factory supporta le seguenti attività di trasformazione che possono essere aggiunte alle pipeline singolarmente o con un'altra attività concatenata.

Attività di trasformazione dei dati | Ambiente di calcolo
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Attività di Machine Learning: esecuzione batch e aggiornamento risorse](transform-data-using-machine-learning.md) | Macchina virtuale di Azure
[Stored procedure](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse o SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics.
[Codice personalizzato](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Notebook di Databricks](transform-data-databricks-notebook.md) | Azure Databricks

Per altre informazioni, vedere l'articolo [Attività di trasformazione dei dati](transform-data.md).

## <a name="control-activities"></a>Attività di controllo
Sono supportate le seguenti attività del flusso di controllo:

Attività di controllo | DESCRIZIONE
---------------- | -----------
[Attività ExecutePipeline](control-flow-execute-pipeline-activity.md) | L'attività di esecuzione pipeline consente a una pipeline di Data Factory di richiamare un'altra pipeline.
[ForEachActivity](control-flow-for-each-activity.md) | L'attività ForEach definisce un flusso di controllo ripetuto nella pipeline. Questa attività viene usata per eseguire l'iterazione su una raccolta ed esegue attività specificate in un ciclo. L'implementazione di cicli di questa attività è simile alla struttura di esecuzione cicli Foreach nei linguaggi di programmazione.
[WebActivity](control-flow-web-activity.md) | L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi.
[Attività di ricerca](control-flow-lookup-activity.md) | L'attività di ricerca può essere usata per la lettura o la ricerca di un nome/valore di record/tabella da qualsiasi origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive.
[Attività GetMetadata](control-flow-get-metadata-activity.md) | Questa attività può essere usata per recuperare i metadati di tutti i dati in Azure Data Factory.
[Attività Until](control-flow-until-activity.md) | Implementa il ciclo Do-Until che è simile alla struttura di esecuzione cicli Do-Until nei linguaggi di programmazione. Esegue infatti un set di attività in un ciclo finché la condizione associata con l'attività restituisce true. È possibile specificare un valore di timeout per l'attività Until in Data Factory.
[Attività della condizione If](control-flow-if-condition-activity.md) | Può essere usata per creare un ramo in base alla condizione che il valore restituito sia true o false. L'attività IfCondition svolge la stessa funzione dell'istruzione If nei linguaggi di programmazione. Valuta un set di attività se la condizione restituisce `true` e un altro set di attività se la condizione restituisce `false`.
[Attività Wait](control-flow-wait-activity.md) | Quando si usa un'attività Wait in una pipeline, la pipeline attende per il periodo di tempo specificato prima di proseguire con l'esecuzione delle attività successive.

## <a name="pipeline-json"></a>Pipeline JSON
Ecco come una pipeline viene definita in formato JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Tag | DESCRIZIONE | type | Obbligatoria
--- | ----------- | ---- | --------
name | Nome della pipeline. Specificare un nome che rappresenti l'azione eseguita dalla pipeline. <br/><ul><li>Numero massimo di caratteri: 140</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\"</li></ul> | string | Yes
description | Specificare il testo descrittivo che illustra lo scopo della pipeline. | string | No 
attività | Nella sezione delle **attività** possono essere definite una o più attività. Vedere la sezione relativa al formato [JSON delle attività](#activity-json) per informazioni dettagliate sull'elemento JSON delle attività. | Array | Yes
Parametri | La sezione **parameters** può avere uno o più parametri definiti all'interno della pipeline, assicurando la flessibilità per il riutilizzo della pipeline. | Elenco | No 

## <a name="activity-json"></a>Attività JSON
Nella sezione delle **attività** possono essere definite una o più attività. Esistono due tipi principali di attività: attività di esecuzione e di controllo.

### <a name="execution-activities"></a>Attività di esecuzione
Includono le [attività di spostamento dei dati](#data-movement-activities) e di [ trasformazione dei dati](#data-transformation-activities). Presentano la seguente struttura di primo livello:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

La tabella seguente descrive le proprietà all'interno della definizione JSON dell'attività:

Tag | DESCRIZIONE | Obbligatoria
--- | ----------- | ---------
name | Nome dell'attività. Specificare un nome che rappresenti l'azione eseguita dall'attività. <br/><ul><li>Numero massimo di caratteri: 55</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\" | Yes</li></ul>
description | Testo descrittivo per il tipo o lo scopo dell'attività | Yes
type | Tipo di attività. Per informazioni sui diversi tipi di attività, vedere le sezioni [Attività di spostamento dei dati](#data-movement-activities), [Attività di trasformazione dei dati](#data-transformation-activities) e [Attività di controllo](#control-activities). | Yes
linkedServiceName | Nome del servizio collegato usato dall'attività.<br/><br/>Per un'attività può essere necessario specificare il servizio collegato che collega all'ambiente di calcolo richiesto. | Sì per l'attività HDInsight, l'attività di assegnazione di punteggio Batch di Azure Machine Learning e l'attività stored procedure. <br/><br/>No per tutto il resto
typeProperties | Le proprietà nella sezione typeProperties dipendono da ogni tipo di attività. Per visualizzare le proprietà del tipo per un'attività, fare clic sui collegamenti all'attività nella sezione precedente. | No 
policy | Criteri che influiscono sul comportamento di runtime dell'attività. Questa proprietà include il comportamento di timeout e ripetizione. Se queste impostazioni non vengono specificate, vengono usati i valori predefiniti. Per altre informazioni, vedere la sezione [Criteri di attività](#activity-policy). | No 
dependsOn | Questa proprietà viene usata per definire le dipendenze delle attività e come le attività successive dipendono dalle attività precedenti. Per altre informazioni, vedere [Dipendenza delle attività](#activity-dependency). | No 

### <a name="activity-policy"></a>Criteri di attività
Criteri che influiscono sul comportamento runtime di un'attività, offrendo le opzioni di configurabilità. I criteri delle attività sono disponibili solo per le attività di esecuzione.

### <a name="activity-policy-json-definition"></a>Definizione JSON dei criteri di attività

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
Nome JSON | DESCRIZIONE | Valori consentiti | Obbligatoria
--------- | ----------- | -------------- | --------
timeout | Specifica il timeout per l'attività da eseguire. | TimeSpan | No. Il timeout predefinito è 7 giorni.
retry | Numero massimo di tentativi | Integer | No. Il valore predefinito è 0
retryIntervalInSeconds | Il ritardo tra tentativi di ripetizione espresso in secondi | Integer | No. Il valore predefinito è 20 secondi.
secureOutput | Se impostato su true, l'output dall'attività viene considerato protetto e non viene registrato per il monitoraggio. | boolean | No. Il valore predefinito è false.

### <a name="control-activity"></a>Attività di controllo
Le attività di controllo presentano la seguente struttura di primo livello:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tag | DESCRIZIONE | Obbligatoria
--- | ----------- | --------
name | Nome dell'attività. Specificare un nome che rappresenti l'azione eseguita dall'attività.<br/><ul><li>Numero massimo di caratteri: 55</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\" | Yes</li><ul>
description | Testo descrittivo per il tipo o lo scopo dell'attività | Yes
type | Tipo di attività. Per informazioni sui diversi tipi di attività, vedere le sezioni [Attività di spostamento dei dati](#data-movement-activities), [Attività di trasformazione dei dati](#data-transformation-activities) e [Attività di controllo](#control-activities). | Yes
typeProperties | Le proprietà nella sezione typeProperties dipendono da ogni tipo di attività. Per visualizzare le proprietà del tipo per un'attività, fare clic sui collegamenti all'attività nella sezione precedente. | No 
dependsOn | Questa proprietà viene usata per definire la dipendenza delle attività e come le attività successive dipendono dalle attività precedenti. Per altre informazioni, vedere la [Dipendenza delle attività](#activity-dependency). | No 

### <a name="activity-dependency"></a>Dipendenza di attività
La dipendenza delle attività definisce in che modo le attività successive dipendono dalle attività precedenti, determinando la condizione dell'eventuale esecuzione dell'attività successiva. Un'attività può dipendere da una o più attività precedenti con condizioni di dipendenza diverse.

Le diverse condizioni di dipendenza sono: Succeeded (esito positivo), Failed (esito negativo), Skipped (operazione ignorata), Completed (operazione completata).

Ad esempio, se una pipeline dispone di un'attività A-> attività B, i diversi scenari possibili sono:

- L'attività B ha una condizione di dipendenza dall'attività A con **succeeded**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito positivo.
- L'attività B ha una condizione di dipendenza dall'attività A con **failed**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito negativo.
- L'attività B ha una condizione di dipendenza dall'attività A con **completed**: l'attività B viene eseguita solo se lo stato finale dell'attività A è con esito positivo o negativo.
- L'attività B ha una condizione di dipendenza dall'attività A con **skipped**: l'attività B viene eseguita se lo stato finale dell'attività A è di operazione ignorata. Lo stato skipped (operazione ignorata) si verifica nello scenario Attività X -> Attività Y -> Attività Z, in cui ogni attività viene eseguita solo se l'attività precedente ha esito positivo. Se l'attività X ha esito negativo, lo stato dell'attività Y è "skipped" perché non viene mai eseguita. Analogamente, anche lo stato dell'attività Z è "skipped".

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Esempio: l'attività 2 dipende dall'esito positivo dell'attività 1

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Esempio di una pipeline di copia
In questa pipeline di esempio è presente un'attività di tipo **Copy** in the **attività** . In questo esempio, l'[attività di copia](copy-activity-overview.md) consente di copiare i dati da un'archiviazione BLOB di Azure a un database SQL di Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Tenere presente quanto segue:

- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
- L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. Vedere l'articolo [Set di dati](concepts-datasets-linked-services.md) per la definizione di set di dati in JSON.
- Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Nella sezione [Attività di spostamento dati](#data-movement-activities) scegliere l'archivio dati che si vuole usare come origine o sink per avere altre informazioni sullo spostamento dei dati da e verso tale archivio dati.

Per la procedura dettagliata sulla creazione di questa pipeline, vedere [Avvio rapido: Creare una data factory](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Esempio di una pipeline di trasformazione
In questa pipeline di esempio è presente un'attività di tipo **HDInsightHive** in the **attività** . In questo esempio, l' [attività Hive di HDInsight](transform-data-using-hadoop-hive.md) trasforma i dati da un archivio BLOB di Azure tramite l'esecuzione di un file di script Hive in un cluster Hadoop di HDInsight.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Tenere presente quanto segue:

- Nella sezione attività esiste una sola attività con l'oggetto **type** impostato su **HDInsightHive**.
- Il file di script Hive, **partitionweblogs.hql**, viene archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato AzureStorageLinkedService) e nella cartella script nel contenitore `adfgetstarted`.
- La sezione `defines` viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio `{hiveconf:inputtable}` e `${hiveconf:partitionedtable}`.

La sezione **typeProperties** è diversa per ogni attività di trasformazione. Per altre informazioni sulle proprietà del tipo supportate per un'attività di trasformazione, fare clic sull'attività di trasformazione nelle [attività di trasformazione dei dati](#data-transformation-activities).

Per la procedura dettagliata sulla creazione di questa pipeline, vedere [Esercitazione: Trasformare i dati usando Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Attività multiple in una pipeline
Le due pipeline di due esempio precedenti contengono una sola attività. È possibile avere più di un'attività in una pipeline. Se sono disponibili più attività in una pipeline e le attività successive non dipendono da quelle precedenti, le attività possono essere eseguite in parallelo.

È possibile concatenare due attività usando la [dipendenza delle attività](#activity-dependency), che definisce in che modo le attività successive dipendono dalle attività precedenti, determinando la condizione se si passerà all'esecuzione dell'attività successiva. Un'attività può dipendere da una o più attività precedenti con condizioni di dipendenza diverse.

## <a name="scheduling-pipelines"></a>Pianificazione delle pipeline
Le pipeline vengono pianificate da trigger. Esistono diversi tipi di trigger: il trigger dell'utilità di pianificazione che consente di attivare le pipeline con una pianificazione basata sul tempo reale e il trigger manuale che attiva le pipeline su richiesta. Per altre informazioni sui trigger, vedere l'articolo [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

Per fare in modo che il trigger attivi l'esecuzione di una pipeline, è necessario includere un riferimento di pipeline della pipeline specifica nella definizione del trigger. Pipeline e trigger hanno una relazione n-m. Più trigger possono avviare una singola pipeline e lo stesso trigger può avviare più pipeline. Dopo che il trigger è stato definito è necessario avviarlo per iniziare ad attivare la pipeline. Per altre informazioni sui trigger, vedere l'articolo [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

Ad esempio, si può disporre di un trigger dell'utilità di pianificazione, "Trigger A", per avviare la pipeline, "MyCopyPipeline". Definire il trigger come illustrato nell'esempio seguente:

### <a name="trigger-a-definition"></a>Definizione del trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline con le attività:

- [Creare una pipeline con un'attività di copia](quickstart-create-data-factory-powershell.md)
- [Creare una pipeline con un'attività di trasformazione dati](tutorial-transform-data-spark-powershell.md)
