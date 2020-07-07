---
title: Trasformare i dati con jar di databricks
description: Informazioni su come elaborare o trasformare i dati eseguendo un'attività JAR di Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414619"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Trasformare i dati eseguendo un'attività JAR in Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività JAR di Azure Databricks in una [pipeline di Data Factory](concepts-pipelines-activities.md) esegue un'attività JAR Spark nel cluster di Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md)   , che offre una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate.Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

Per un'introduzione di undici minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definizione di attività JAR di Databricks

Ecco la definizione JSON di esempio di un'attività JAR di Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Proprietà dell'attività JAR di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|Descrizione|Obbligatoria|
|:--|---|:-:|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No|
|type|Per l'attività JAR di Databricks il tipo di attività è DatabricksSparkJar.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione l'attività JAR. Per informazioni su questo servizio collegato, vedere l'articolo [servizi collegati di calcolo](compute-linked-services.md)   .|Sì|
|mainClassName|Il nome completo della classe che contiene il metodo Main deve essere eseguito. Questa classe deve essere contenuta in un file JAR fornito come libreria.|Sì|
|parametri|Parametri che verranno passati al metodo Main.  È una matrice di stringhe.|No|
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di <stringa, oggetto>|Sì (almeno una che contiene il metodo mainClassName)|

> [!NOTE]
> **Problema noto** : quando si usa lo stesso [cluster interattivo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) per l'esecuzione di attività jar di databricks simultanee (senza riavvio del cluster), si verifica un problema noto in databricks, in cui i parametri della prima attività verranno usati anche dalle attività seguenti. Di conseguenza, i parametri non corretti vengono passati ai processi successivi. Per ovviare a questo problema, usare invece un [cluster di processi](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di databricks

Nella definizione dell'attività di Databricks precedente si specificano questi tipi di libreria: *jar*, *egg*, *maven*, *pypi*, *cran*.

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

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Uso dell'interfaccia utente dell'area di lavoro di Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Per ottenere il percorso dbfs della libreria aggiunto tramite l'interfaccia utente, è possibile usare [Databricks CLI (installazione)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencarle tutte tramite l'interfaccia della riga di comando: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Copia della libreria con Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Usare l'interfaccia della riga di comando di Databricks [(procedura di installazione)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Esempio: copiare JAR in dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
