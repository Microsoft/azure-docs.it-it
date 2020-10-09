---
title: Trasformare i dati con il notebook di databricks
description: Informazioni su come elaborare o trasformare i dati eseguendo un notebook di Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81419018"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Trasformare i dati eseguendo un notebook di Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività Azure Databricks notebook in una [pipeline Data Factory](concepts-pipelines-activities.md) esegue un notebook di databricks nell'area di lavoro Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md)   , che offre una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate.Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definizione di attività dei notebook di Databricks

Ecco la definizione JSON di esempio di un'attività dei notebook di Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Proprietà dell'attività dei notebook di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|Descrizione|Obbligatoria|
|---|---|---|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No|
|type|Per l'attività dei notebook di Databricks il tipo di attività è DatabricksNotebook.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione il notebook di Databricks. Per informazioni su questo servizio collegato, vedere l'articolo [servizi collegati di calcolo](compute-linked-services.md)   .|Sì|
|notebookPath|Percorso assoluto del notebook da eseguire nell'area di lavoro di Databricks. Questo percorso deve iniziare con una barra.|Sì|
|baseParameters|Matrice di coppie chiave-valore. I parametri base possono essere usati per ogni esecuzione attività. Se il notebook accetta un parametro non specificato, verrà usato il valore predefinito del notebook. Per altre informazioni sui parametri, vedere [Notebook di Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|No|
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di \<string, object> .|No|


## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di Databricks

Nella definizione di attività databricks precedente specificare questi tipi di libreria: *jar*, *Egg*, *WHL*, *Maven*, *pypi*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Per altre informazioni, consultare la [documentazione di Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) per i tipi di libreria.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passaggio di parametri tra notebook e Data Factory

È possibile passare data factory parametri ai notebook usando la proprietà *baseParameters* nell'attività databricks. 

In alcuni casi potrebbe essere necessario passare di nuovo alcuni valori dal notebook al data factory, che può essere usato per il flusso di controllo (controlli condizionali) in data factory o essere utilizzato dalle attività downstream (il limite di dimensioni è 2MB). 

1. Nel notebook è possibile chiamare [dbutils. notebook. Exit ("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) e il corrispondente "returnValue" verrà restituito data factory.

2. È possibile utilizzare l'output in data factory utilizzando un'espressione, ad esempio `'@activity('databricks notebook activity name').output.runOutput'` . 

   > [!IMPORTANT]
   > Se si passa un oggetto JSON, è possibile recuperare i valori accodando i nomi di proprietà. Esempio: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Uso dell'interfaccia utente dell'area di lavoro di Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Per ottenere il percorso dbfs della libreria aggiunto tramite l'interfaccia utente, è possibile usare [Databricks CLI (installazione)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencare tutti tramite l'interfaccia della riga di comando: *databricks FS LS dBFS:/FileStore/jar*.



#### <a name="copy-library-using-databricks-cli"></a>[Copia della libreria con Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Esempio: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
