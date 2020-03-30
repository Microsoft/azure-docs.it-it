---
title: Creare pipeline di dati predittiveCreate predictive data pipelines
description: Informazioni su come creare una pipeline predittiva mediante Azure Machine Learning - Attività di esecuzione batch in Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 44371c78a4d02588eef21aae5a4bba3d033763d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030000"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Creare pipeline predittive tramite Azure Machine Learning e Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versione corrente](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) consente di creare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Eseguire questo passaggio usando Azure Machine Learning Studio (classico). Azure Machine Learning Studio (classico) è un ambiente di sviluppo visivo collaborativo usato per eseguire il training e testare un modello di analisi predittiva usando i dati di training.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. È possibile inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web di [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) pubblicato per l'analisi predittiva. Usando **l'attività** di esecuzione batch in una pipeline di Azure Data Factory, è possibile richiamare un servizio Web di Azure Machine Learning Studio (classico) per eseguire stime sui dati in batch.

Nel corso del tempo, i modelli predittivi in Azure Machine Learning Studio (classico) gli esperimenti di assegnazione del punteggio devono essere riaddestrati usando nuovi set di dati di input. È possibile ripetere il training di un modello da una pipeline di Data Factory seguendo questa procedura:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Questo passaggio in Azure Machine Learning Studio (classico) viene eseguito come per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare l'attività di esecuzione batch di Azure Machine Learning Studio (classica) per richiamare il servizio Web per l'esperimento di training. Fondamentalmente, è possibile usare l'attività di esecuzione batch di Azure Machine Learning Studio (classica) per richiamare sia il servizio Web di formazione che il servizio Web di punteggio.

Dopo aver completato la riqualificazione, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l'attività di aggiornamento delle risorse di **Azure Machine Learning Studio (classico).** Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="azure-machine-learning-linked-service"></a>Servizio collegato di Azure Machine Learning

Si crea un servizio collegato di **Azure Machine Learning** per collegare un servizio web di Azure Machine Learning a una data factory di Azure. Il servizio collegato viene usato dall'attività di esecuzione batch di Azure Machine Learning e [dall'attività di risorsa](update-machine-learning-models.md)di aggiornamento .

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md) per le descrizioni sulle proprietà nella definizione JSON.

Azure Machine Learning supporta sia i servizi Web classici che i nuovi servizi Web per l'esperimento predittivo. È possibile scegliere quello corretto da usare da Data Factory. Per ottenere le informazioni necessarie per creare il servizio collegato di Azure Machine Learning, andare a https://services.azureml.net, dove sono elencati tutti i servizi Web nuovi e i servizi Web classici. Fare clic sul servizio Web cui si desidera accedere e fare clic sulla pagina **Consume (Uso)**. Copiare la **chiave primaria** per la proprietà **apiKey** e le **richieste batch** per la proprietà **mlEndpoint**.

![Servizi Web di Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Attività di esecuzione batch di Azure Machine Learning

Il frammento JSON seguente definisce un'attività Esecuzione batch di Azure Machine Learning. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Machine Learning creato in precedenza.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Proprietà          | Descrizione                              | Obbligatoria |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nome dell'attività nella pipeline     | Sì      |
| description       | Testo che descrive l'attività.  | No       |
| type              | Per l'attività U-SQL di Data Lake Analytics, il tipo di attività è **AzureMLBatchExecution**. | Sì      |
| linkedServiceName | Servizi collegati al servizio collegato di Azure Machine Learning. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| webServiceInputs  | Coppie valore, chiave, che eseguono il mapping dei nomi di input dei servizi Web di Azure Machine Learning. La chiave deve corrispondere ai parametri di input definiti nel servizio Web pubblicato di Azure Machine Learning. Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di input. | No       |
| webServiceOutputs | Coppie valore, chiave, che eseguono il mapping dei nomi di output dei servizi Web di Azure Machine Learning. La chiave deve corrispondere ai parametri di output definiti nel servizio Web pubblicato di Azure Machine Learning. Il valore è una coppia di proprietà FilePath e dei servizi collegati di archiviazione di Azure che specifica i percorsi BLOB di output. | No       |
| globalParameters  | Coppie chiave e valore da passare all'endpoint del servizio di esecuzione batch di Azure Machine Learning Studio (classico). Le chiavi devono corrispondere ai nomi dei parametri del servizio Web definiti nel servizio Web di Azure Machine Learning Studio (classico) pubblicato. I valori vengono passati nella proprietà GlobalParameters della richiesta di esecuzione batch di Azure Machine Learning Studio (classica) | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure

Il questo scenario il servizio Web Azure Machine Learning esegue stime usando dati provenienti da un file dell'archiviazione BLOB di Azure e archivia i risultati nell'archiviazione BLOB. Il frammento di codice JSON seguente definisce una pipeline di Data factory con un'attività AzureMLBatchExecution. Viene fatto riferimento ai dati di input e output nell'archiviazione BLOB di Azure tramite una coppia di LinkedName e FilePath. Nell'esempio Linked Service di input e output sono diversi, è possibile usare servizi collegati diversi per ogni input/output per Data Factory per poter prelevare i file corretti e inviarli al servizio Web di Azure Machine Learning Studio (classico).

> [!IMPORTANT]
> Nell'esperimento di Azure Machine Learning Studio (classico), le porte di input e output del servizio Web e i parametri globali hanno nomi predefiniti ("input1", "input2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. È possibile visualizzare il payload della richiesta di esempio nella pagina della Guida sull'esecuzione batch per l'endpoint di Azure Machine Learning Studio (classico) per verificare il mapping previsto.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi
Un altro scenario comune durante la creazione di esperimenti di Azure Machine Learning Studio (classico) consiste nell'usare i moduli Importa dati e Dati di output. Il modulo Import Data consente di caricare i dati in un esperimento, mentre il modulo Output Data consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Import Data e Output Data, vedere gli argomenti [Import Data](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Output Data](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.

Quando si usano i moduli Import Data e Output Data, è consigliabile usare un parametro del servizio Web per ogni proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Import Data che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si intende consentire ai consumer del servizio Web di specificare un altro server SQL di Azure denominato `YYY.database.windows.net`. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [!NOTE]
> L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come specificare un input e un output per un servizio Web di Azure Machine Learning Studio (classico). In questo scenario si passano i parametri per un servizio Web corrispondenti alle proprietà dei moduli Import Data e Output Data.
>
>

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. È stato distribuito un servizio Web di Azure Machine Learning che usa un modulo Reader per leggere i dati da una delle origini dati di Azure Machine Learning supportate, ad esempio un database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure).  Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, è consigliabile configurare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Ciò consente la configurazione dei moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>

Dopo aver completato la riqualificazione, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l'attività di aggiornamento delle risorse di **Azure Machine Learning Studio (classico).** Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](update-machine-learning-models.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività suina](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
