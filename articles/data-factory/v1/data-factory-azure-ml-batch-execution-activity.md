---
title: Creare pipeline di dati predittivi usando Azure Data Factory
description: Viene descritto come creare pipeline predittive usando Azure Data Factory e Azure Machine Learning Studio (classico)
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: c65ef2eb25f330f645048cdc73371d98d8c2ce91
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508473"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Creare pipeline predittive usando Azure Machine Learning Studio (classico) e Azure Data Factory

> [!div class="op_single_selector" title1="Attività di trasformazione"]
> * [Attività hive](data-factory-hive-activity.md)
> * [Attività Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di streaming di Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Azure Machine Learning Studio (versione classica)](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività di aggiornamento risorse di Azure Machine Learning Studio (versione classica)](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduzione
> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere [Trasformare dati tramite Machine Learning in Data Factory](../transform-data-using-machine-learning.md).

### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (versione classica)
[Azure Machine Learning Studio (classico)](https://azure.microsoft.com/documentation/services/machine-learning/) consente di compilare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Per eseguire questa operazione, è possibile usare Azure Machine Learning Studio (classico). Studio (classico) è un ambiente di sviluppo visivo collaborativo che consente di eseguire il training e il test di un modello di analisi predittiva usando i dati di training.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. È possibile inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory è un servizio di integrazione dei dati basato sul cloud che orchestra e automatizza lo **spostamento** e la **trasformazione** dei dati. È possibile creare soluzioni di integrazione dei dati usando Azure Data Factory che può inserire dati da diversi archivi dati, trasformare/elaborare i dati e pubblicare i dati risultanti negli archivi dati.

Il servizio Data Factory consente di creare pipeline di dati che spostano e trasformano i dati e quindi di eseguire le pipeline in base a una pianificazione specificata (ogni ora, ogni giorno, ogni settimana e così via). Offre anche viste avanzate per visualizzare la derivazione e le dipendenze tra le pipeline di dati e monitorare tutte le pipeline di dati da una singola visualizzazione unificata per individuare facilmente i problemi e configurare avvisi di monitoraggio.

Per una rapida introduzione al servizio Azure Data Factory, vedere gli articoli [Introduzione al servizio Azure Data Factory](data-factory-introduction.md) e [Creare la prima pipeline](data-factory-build-your-first-pipeline.md).

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory e Machine Learning Studio (classico) insieme
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato [Azure Machine Learning Studio (classico)][azure-machine-learning] per l'analisi predittiva. Utilizzando l' **attività di esecuzione batch** in una pipeline di Azure Data Factory, è possibile richiamare un servizio Web Studio (classico) per eseguire stime sui dati in batch. Per informazioni dettagliate, vedere richiamata di un servizio Web di Azure Machine Learning Studio (classico) utilizzando la sezione attività esecuzione batch.

Nel corso del tempo, è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi in studio (classico) usando nuovi set di dati di input. È possibile ripetere il training di un modello Studio (classico) da una pipeline di Data Factory attenendosi alla procedura seguente:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Questa operazione viene eseguita in studio (classico), come è stato fatto per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare l'attività di esecuzione batch di studio (classica) per richiamare il servizio Web per l'esperimento di training. In pratica, è possibile usare l'attività di esecuzione batch di studio (classica) per richiamare il servizio Web di training e il servizio Web di assegnazione dei punteggi.

Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Richiamo di un servizio Web tramite Attività di esecuzione batch
Usare Azure Data Factory per orchestrare lo spostamento e l'elaborazione dei dati e quindi eseguire l'esecuzione del batch con studio (versione classica). Ecco i passaggi principali:

1. Creare un servizio collegato Azure Machine Learning Studio (classico). Sono necessari i valori seguenti:

   1. **URI della richiesta** per l’API di esecuzione batch. È possibile trovare l'URI della richiesta facendo clic sul collegamento **ESECUZIONE BATCH** nella pagina dei servizi Web.
   2. **Chiave API** per il servizio Web pubblicato in studio (classico). È possibile trovare la chiave API facendo clic sul servizio Web pubblicato.
   3. Usare l'attività **AzureMLBatchExecution** .

      ![Dashboard Machine Learning Studio (classico)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure
In questo scenario, il servizio Web Studio (classico) esegue stime usando i dati di un file in un archivio BLOB di Azure e archivia i risultati della stima nell'archivio BLOB. Il frammento di codice JSON seguente definisce una pipeline di Data factory con un'attività AzureMLBatchExecution. L'attività include il set di dati **DecisionTreeInputBlob** come input e il set di dati **DecisionTreeResultBlob** come output. Il set di dati **DecisionTreeInputBlob** viene passato come input al servizio Web usando la proprietà JSON **webServiceInput**. Il set di dati **DecisionTreeResultBlob** viene passato come output al servizio Web usando la proprietà JSON **webServiceOutputs**.

> [!IMPORTANT]
> Se il servizio Web accetta più input, usare la proprietà **webServiceInputs** invece di **webServiceInput**. Vedere la sezione [Il servizio Web richiede più input](#web-service-requires-multiple-inputs) per un esempio di uso della proprietà webServiceInputs.
>
> I set di elementi a cui fanno riferimento le  / **Proprietà webServiceInputs** e **webServiceOutputs** di webServiceInput (in **typeProperties**) devono essere inclusi anche negli **input** e negli **output** dell'attività.
>
> Nell'esperimento di studio (classico), le porte e i parametri globali di input e output del servizio Web hanno nomi predefiniti ("INPUT1", "INPUT2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. È possibile visualizzare il payload della richiesta di esempio nella pagina della Guida esecuzione batch per l'endpoint di studio (classico) per verificare il mapping previsto.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Possono essere passati come parametri per il servizio Web solo input e output dell'attività AzureMLBatchExecution. Nel precedente snippet JSON, ad esempio, DecisionTreeInputBlob è un input per l'attività AzureMLBatchExecution e viene passato come input al servizio Web tramite il parametro webServiceInput.
>
>

### <a name="example"></a>Esempio
Questo esempio usa Archiviazione di Azure per archiviare i dati di input e di output.

Prima di procedere con questo esempio, è consigliabile eseguire l'esercitazione relativa alla [creazione della prima pipeline con Data Factory][adf-build-1st-pipeline]. Usare l'editor di Data Factory per creare elementi di Data Factory, come servizi collegati, set di dati e pipeline, in questo esempio.

1. Creare un **servizio collegato** per **Archiviazione di Azure**. Se i file di input e output si trovano in account di archiviazione diversi, sono necessari due servizi collegati. Di seguito è fornito un esempio JSON:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Creare il **set di dati** di **input** di Azure Data Factory. A differenza di altri set di dati di Data Factory, questi devono includere entrambi i valori **folderPath** e **fileName**. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch (ogni sezione di dati) elabori o produca file di input e di output univoci. Può essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e inserirlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni **external** ed **externalData** illustrate nell'esempio seguente e DecisionTreeInputBlob sarà il set di dati di output di un'attività diversa.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
         "interval": 1
       },
       "policy": {
         "externalData": {
           "retryInterval": "00:01:00",
           "retryTimeout": "00:10:00",
           "maximumRetry": 3
         }
       }
     }
   }
   ```

   Il file con estensione csv di input deve avere una riga di intestazione di colonna. Se si usa l'**attività di copia** per creare/spostare il file CSV nell'archivio BLOB, è consigliabile impostare la proprietà **blobWriterAddHeader** del sink su **true**. ad esempio:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   Se il file CSV non ha la riga di intestazione, potrebbe essere visualizzato un messaggio di errore analogo al seguente: **Errore nell'attività: Errore di lettura della stringa. Token imprevisto: StartObject. Percorso '', riga 1, posizione 1**.

3. Creare il **set di dati** di **output** di Azure Data Factory. In questo esempio il file di output usa il partizionamento per creare un percorso di output univoco per l'esecuzione di ciascuna sezione. Senza il partizionamento l'attività sovrascriverà il file.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Creare un **servizio collegato** di tipo **AzureMLLinkedService** che fornisce la chiave API e un modello di URL per l'esecuzione batch.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Creare infine una pipeline contenente un'attività **AzureMLBatchExecution** . In fase di esecuzione la pipeline segue questa procedura:

   1. Ottiene il percorso del file di input dal set di dati di input.
   2. Richiama l'API di esecuzione batch di studio (classica)
   3. Copia l'output di esecuzione batch nel BLOB specificato nel set di dati di output.

      > [!NOTE]
      > L'attività AzureMLBatchExecution può avere zero o più input e uno o più output.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
              {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
          }
      }
      ```

      I valori DateTime di **inizio** e di **fine** devono essere in [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Se non si specifica un valore per la proprietà **Inizio + 48 ore** ". Se non si specifica un valore per la proprietà **end** , questo viene calcolato come "**Start + 48 hours".** Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](/previous-versions/azure/dn835050(v=azure.100)) .

      > [!NOTE]
      > La specifica dell'input per l'attività AzureMLBatchExecution è opzionale.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi
Un altro scenario comune durante la creazione di esperimenti Studio (classico) consiste nell'usare i moduli Reader e writer. Il modulo Reader consente di caricare i dati in un esperimento, mentre il modulo Writer consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Reader e Writer, vedere gli argomenti [Reader](/azure/machine-learning/studio-module-reference/import-data) e [Writer](/azure/machine-learning/studio-module-reference/export-data) in MSDN Library.

Quando si usano i moduli Reader e Writer, è consigliabile usare un parametro del servizio Web per ciascuna proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Reader che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si desidera consentire ai consumer del servizio Web di specificare un altro server logico SQL denominato YYY.database.windows.net. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [!NOTE]
> L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web di studio (classico). In questo scenario si passano i parametri per un servizio Web corrispondenti alle proprietà dei moduli Reader e Writer.
>
>

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. Si dispone di un servizio Web Studio distribuito (classico) che usa un modulo Reader per leggere i dati da una delle origini dati supportate da studio (classico), ad esempio il database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure).  Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, è consigliabile configurare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Ciò consente la configurazione dei moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

È anche possibile usare le [funzioni di Data factory](data-factory-functions-variables.md) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso di un modulo Reader per leggere dati da più file del BLOB di Azure
Le pipeline di Big Data, con attività come Pig e Hive, possono generare uno o più file di output senza estensioni. Ad esempio, quando si specifica una tabella Hive esterna, i dati per tale tabella possono essere archiviati nell'archiviazione BLOB di Azure con il nome 000000_0. È possibile usare il modulo Reader in un esperimento per la lettura di più file e usare questi ultimi per creare delle stime.

Quando si usa il modulo Reader in un esperimento di studio (classico), è possibile specificare il BLOB di Azure come input. I file nell'archivio BLOB di Azure possono essere file di output, ad esempio 000000_0, generati da uno script Pig e Hive in esecuzione in HDInsight. Il modulo Reader consente di leggere i file, senza estensioni, configurando la voce **Path to container, directory or blob**(Percorso del contenitore, della directory o del BLOB). La parte relativa al **percorso del contenitore** punta al contenitore, mentre **directory o BLOB** punta alla cartella che contiene i file, come illustrato nell'immagine seguente. L'asterisco (\*) **specifica che tutti i file nel contenitore o nella cartella, ovvero data/aggregateddata/year=2014/month-6/\***, vengono letti come parte dell'esperimento.

![Proprietà del Blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Esempio
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline con l'attività AzureMLBatchExecution con parametri del servizio Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

Nell'esempio JSON precedente:

* Il servizio Web distribuito in studio (classico) usa un modulo Reader e writer per leggere/scrivere dati da/in un database SQL di Azure. Il servizio Web espone i quattro parametri seguenti: Database server name, Database name, Server user account name e Server user account password.
* I valori DateTime di **inizio** e di **fine** devono essere in [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Se non si specifica un valore per la proprietà **Inizio + 48 ore** ". Se non si specifica un valore per la proprietà **end** , questo viene calcolato come "**Start + 48 hours".** Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](/previous-versions/azure/dn835050(v=azure.100)) .

### <a name="other-scenarios"></a>Altri scenari
#### <a name="web-service-requires-multiple-inputs"></a>Il servizio Web richiede più input
Se il servizio Web accetta più input, usare la proprietà **webServiceInputs** invece di **webServiceInput**. Includere anche i set di dati a cui **webServiceInputs** fa riferimento negli **input** dell'attività.

Nell'esperimento di Azure Machine Learning Studio (classico), le porte e i parametri globali di input e output del servizio Web hanno nomi predefiniti ("INPUT1", "INPUT2") che è possibile personalizzare. I nomi scelti per le impostazioni webServiceInputs, webServiceOutputs e globalParameters devono corrispondere esattamente ai nomi negli esperimenti. È possibile visualizzare il payload della richiesta di esempio nella pagina della Guida esecuzione batch per l'endpoint di studio (classico) per verificare il mapping previsto.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Servizio Web non richiede un input
È possibile usare i servizi Web di esecuzione batch Azure Machine Learning Studio (classico) per eseguire qualsiasi flusso di lavoro, ad esempio script R o Python, che potrebbero non richiedere input. In alternativa, l'esperimento può essere configurato con un modulo Reader che non espone proprietà GlobalParameters. In tal caso l'attività AzureMLBatchExecution verrà configurata come segue:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Servizio Web non richiede un input/output
Per il servizio Web di esecuzione batch Azure Machine Learning Studio (classico) potrebbe non essere configurato alcun output del servizio Web. In questo esempio non sono disponibili input o output del servizio Web, né alcuna proprietà GlobalParameters configurata. È ancora presente un output configurato nell'attività stessa, ma non viene fornito come webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Il servizio Web usa moduli Reader e Writer e l'attività viene eseguita solo quando le altre attività hanno avuto esito positivo
I moduli Reader e writer del servizio Web Azure Machine Learning Studio (classico) potrebbero essere configurati per l'esecuzione con o senza GlobalParameters. Tuttavia, è possibile incorporare chiamate al servizio in una pipeline che usa le dipendenze del set di dati per richiamare il servizio solo dopo il completamento di alcune operazioni di elaborazione upstream. Si possono anche attivare altre azioni dopo il completamento dell'esecuzione batch usando questo approccio. In tal caso è possibile esprimere le dipendenze tramite gli input e gli output dell'attività, senza denominarli input o output del servizio Web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Ecco i **punti chiave** :

* Se l'endpoint dell'esperimento usa la proprietà webServiceInput, questa è rappresentata da un set di dati del BLOB ed è inclusa negli input dell'attività e nella proprietà webServiceInput. In caso contrario, la proprietà webServiceInput viene omessa.
* Se l'endpoint dell'esperimento usa le proprietà webServiceOutputs, queste sono rappresentate da un set di dati del BLOB e sono incluse negli output dell'attività e nella proprietà webServiceOutputs. Il mapping degli output dell'attività e di webServiceOutputs viene eseguito in base al nome di ogni output nell'esperimento. In caso contrario, la proprietà webServiceOutputs viene omessa.
* Se l'endpoint dell'esperimento espone le proprietà globalParameters, vengono assegnate alla proprietà globalParameters dell'attività come coppie chiave-valore. In caso contrario, la proprietà globalParameters viene omessa. Le chiavi distinguono tra maiuscole e minuscole. [funzioni di Data factory di Azure](data-factory-functions-variables.md) possono essere usate nei valori.
* Nelle proprietà di input e output delle dell'attività possono essere inclusi set di dati aggiuntivi, senza che vi si faccia riferimento nella sezione typeProperties dell'attività. Questi set di dati regolano l'esecuzione tramite le dipendenze delle sezioni, ma in caso contrario vengono ignorati dall'attività AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aggiornamento dei modelli con Attività della risorsa di aggiornamento
Al termine della ripetizione del training, aggiornare il servizio Web di assegnazione dei punteggi (esperimento predittivo esposto come servizio Web) con il modello appena sottoposto a training usando l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)**. Per informazioni dettagliate, vedere l'articolo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Aggiornamento dei modelli con Attività della risorsa di aggiornamento).

### <a name="reader-and-writer-modules"></a>Moduli Reader e Writer 
Uno scenario comune per l'uso dei parametri del servizio Web è costituito dai reader e dai writer SQL di Azure. Il modulo Reader viene usato per caricare i dati in un esperimento da servizi di gestione dati all'esterno di studio (classico). Il modulo Writer consente di salvare i dati degli esperimenti in servizi di gestione dati all'esterno di studio (classico).

Per informazioni dettagliate sui BLOB di Azure o sui moduli Reader e Writer SQL di Azure, vedere gli argomenti [Reader](/azure/machine-learning/studio-module-reference/import-data) e [Writer](/azure/machine-learning/studio-module-reference/export-data) in MSDN Library. L'esempio della sezione precedente usa un reader e un writer di BLOB di Azure. Questa sezione illustra l'uso di un reader e un writer SQL di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti
**D:** Ho più file generati dalle pipeline di Big Data. Posso usare l'attività AzureMLBatchExecution per lavorare con tali file?

**R:** Sì. Per informazioni dettagliate, vedere la sezione **Uso di un modulo Reader per leggere dati da più file del BLOB di Azure** .

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Attività di Punteggio batch Azure Machine Learning Studio (classico)
Se si usa l'attività **AzureMLBatchScoring** per l'integrazione con Azure Machine Learning Studio (classico), si consiglia di usare l'attività **AzureMLBatchExecution** più recente.

L'attività AzureMLBatchExecution è stata introdotta nella versione di Azure SDK e Azure PowerShell di agosto 2015.

Se si vuole continuare a usare l'attività AzureMLBatchScoring, continuare a leggere questa sezione.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Attività di Punteggio batch Azure Machine Learning Studio (classica) con archiviazione di Azure per input/output

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parametri del servizio Web
Per specificare i valori dei parametri del servizio Web, aggiungere una sezione **typeProperties** alla sezione **AzureMLBatchScoringActivity** nel file JSON della pipeline, come illustrato nell'esempio seguente:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
È anche possibile usare le [funzioni di Data factory](data-factory-functions-variables.md) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.
>
>

## <a name="see-also"></a>Vedi anche
* [Post di blog di Azure: Guida introduttiva a Data Factory di Azure e ad Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/