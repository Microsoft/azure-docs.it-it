---
title: Schema di eventi di Azure Event Grid Machine LearningAzure Event Grid Machine Learning event schema
description: Descrive le proprietà fornite per gli eventi dell'area di lavoro di Machine Learning con Griglia di eventi di AzureDescribes the properties that are provided for Machine Learning Workspace events with Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202145"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid event schema for Azure Machine Learning

Questo articolo fornisce le proprietà e lo schema per gli eventi dell'area di lavoro di Machine Learning.This article provides the properties and schema for machine learning workspace events. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco di script ed esercitazioni di esempio, vedere [Origine evento Di AzureML.](event-sources.md#azure-machine-learning)

## <a name="available-event-types"></a>Tipi di evento disponibili

Azure Machine Learning genera i tipi di evento seguenti:Azure Machine Learning emits the following event types:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | Generato quando una nuova versione del modello o del modello è stata registrata correttamente. |
| Microsoft.MachineLearningServices.ModelDeployed | Generato quando i modelli sono stati distribuiti correttamente in un endpoint. |
| Microsoft.MachineLearningServices.RunCompleted | Generato quando un'esecuzione è stata completata correttamente. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Generato quando un monitoraggio della deriva del set di dati rileva la deriva. |
| Microsoft.MachineLearningServices.RunStatusChanged | Generato quando lo stato di esecuzione diventa 'failed'. |

## <a name="the-contents-of-an-event-response"></a>Il contenuto di una risposta all'evento

Quando viene attivato un evento, il servizio Griglia di eventi invia i dati relativi a tale evento all'endpoint di sottoscrizione.

Questa sezione contiene un esempio dell'aspetto di tali dati per ogni evento.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Evento Microsoft.MachineLearningServices.ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed (evento)

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted (evento)

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Evento Microsoft.MachineLearningServices.DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Evento Microsoft.MachineLearningServices.RunStatusChanged

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

L'oggetto dati ha le seguenti proprietà per ogni tipo di evento:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| ModelName | string | Nome del modello registrato. |
| VersioneModello | string | Versione del modello registrata. |
| Modelli | object | Tag del modello registrato. |
| ModelProperties (Proprietà Modello) | object | Proprietà del modello registrato. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| ServiceName | string | Nome del servizio distribuito. |
| ServiceComputeType (Tipo di servizio) | string | Tipo di calcolo (ad esempio ACI, AKS) del servizio distribuito. |
  | ModelIds (Id modello) | string | Elenco separato da virgole di ID modello. ID dei modelli distribuiti nel servizio. |
| ServiceTag (Tag di servizio) | object | Tag del servizio distribuito. |
| ServiceProperties (Proprietà Servizio) | object | Proprietà del servizio distribuito. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Id Esperimento | string | ID dell'esperimento a cui appartiene l'esecuzione. |
| Nome Esperimento | string | Nome dell'esperimento a cui appartiene l'esecuzione. |
| Id esecuzione | string | ID dell'esecuzione completata. |
| Tipo Run | string | Tipo di esecuzione dell'esecuzione completata. |
| Tag Di Esecuzione | object | I tag dell'esecuzione completata. |
| Proprietà RunProperties | object | Proprietà dell'esecuzione completata. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| DataDriftId | string | ID del monitoraggio della deriva dei dati che ha attivato l'evento. |
| DataDriftName | string | Nome del monitoraggio della deriva dei dati che ha attivato l'evento. |
| Id esecuzione | string | ID dell'esecuzione che ha rilevato la deriva dei dati. |
| BaseDatasetId | string | ID del set di dati di base utilizzato per rilevare la deriva. |
| TargetDatasetId | string | ID del set di dati di destinazione utilizzato per rilevare la deriva. |
| DriftCoefficiente | double | Risultato del coefficiente che ha attivato l'evento. |
| StartTime | Datetime | Ora di inizio della serie temporale del set di dati di destinazione che ha determinato il rilevamento della deriva.  |
| EndTime | Datetime | Ora di fine della serie temporale del set di dati di destinazione che ha determinato il rilevamento della deriva. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Id Esperimento | string | ID dell'esperimento a cui appartiene l'esecuzione. |
| Nome Esperimento | string | Nome dell'esperimento a cui appartiene l'esecuzione. |
| Id esecuzione | string | ID dell'esecuzione completata. |
| Tipo Run | string | Tipo di esecuzione dell'esecuzione completata. |
| Tag Di Esecuzione | object | I tag dell'esecuzione completata. |
| Proprietà RunProperties | object | Proprietà dell'esecuzione completata. |
| RunStatus (Stato esecuzione) | string | Stato dell'esecuzione. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere Schema della sottoscrizione di Griglia di eventiFor more information about creating an Azure Event Grid subscription subscription, see [Event Grid subscription schema](subscription-creation-schema.md)
* Per un'introduzione all'uso di Griglia di eventi di Azure con Azure Machine Learning, vedere Usare gli eventi di [Azure Machine LearningFor](/azure/machine-learning/service/concept-event-grid-integration) an introduction to using Azure Event Grid with Azure Machine Learning, see Consume Azure Machine Learning events
* Per un esempio di utilizzo di Griglia di eventi di Azure con Azure Machine Learning, vedere Creare flussi di lavoro di Machine Learning basati su eventiFor an example of using Azure Event Grid with Azure Machine Learning, see [Create event driven Machine Learning workflows](/azure/machine-learning/service/how-to-use-event-grid)
