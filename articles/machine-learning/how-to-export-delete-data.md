---
title: Esportare o eliminare i dati dell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come esportare o eliminare l'area di lavoro con Azure Machine Learning Studio, l'interfaccia della riga di comando, l'SDK e le API REST autenticate.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 657f6dd32c18b5b0745883da02563e9f5257d92d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87307217"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Esportare o eliminare i dati dell'area di lavoro nel servizio Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning è possibile esportare o eliminare i dati dell'area di lavoro con l'API REST autenticata. Questo articolo descrive come esportare o eliminare i dati.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controllare i dati dell'area di lavoro

I dati del prodotto archiviati da Azure Machine Learning sono disponibili per l'esportazione e l'eliminazione tramite Azure Machine Learning Studio, l'interfaccia della riga di comando, l'SDK e le API REST autenticate. I dati di telemetria sono accessibili tramite il portale Privacy di Azure. 

In Azure Machine Learning i dati personali sono costituiti dalle informazioni utente dei documenti della cronologia di esecuzione e dai record dei dati di telemetria di alcune interazioni dell'utente con il servizio.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminare i dati dell'area di lavoro con l'API REST

Per eliminare i dati, è possibile effettuare le chiamate API seguenti con il verbo HTTP DELETE. L'autorizzazione delle chiamate API deriva dalla presenza di un'intestazione `Authorization: Bearer <arm-token>` nella richiesta, dove `<arm-token>` è il token di accesso AAD per l'endpoint `https://management.core.windows.net/`.  

Per informazioni su come ottenere questo token e chiamare gli endpoint di Azure, vedere [usare REST per gestire le risorse ml](how-to-manage-rest.md) e la [documentazione sull'API REST di Azure](https://docs.microsoft.com/rest/api/azure/).  

Negli esempi seguenti sostituire il testo in {} con i nomi delle istanze che determinano la risorsa associata.

### <a name="delete-an-entire-workspace"></a>Eliminare un'intera area di lavoro

Usare questa chiamata per eliminare un'intera area di lavoro.  
> [!WARNING]
> Verranno eliminate tutte le informazioni e l'area di lavoro non sarà più utilizzabile.

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="delete-models"></a>Eliminare i modelli

Usare questa chiamata per ottenere un elenco dei modelli e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

I singoli modelli possono essere eliminati con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="delete-assets"></a>Eliminare gli asset

Usare questa chiamata per ottenere un elenco degli asset e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

I singoli asset possono essere eliminati con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="delete-images"></a>Eliminare le immagini

Usare questa chiamata per ottenere un elenco delle immagini e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Le singole immagini possono essere eliminate con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="delete-services"></a>Eliminare i servizi

Usare questa chiamata per ottenere un elenco dei servizi e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

I singoli servizi possono essere eliminati con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

## <a name="export-service-data-with-the-rest-api"></a>Esportare i dati del servizio con l'API REST

Per esportare i dati, è possibile effettuare le chiamate API seguenti con il verbo HTTP GET. L'autorizzazione delle chiamate API deriva dalla presenza di un'intestazione`Authorization: Bearer <arm-token>` nella richiesta, dove `<arm-token>` è il token di accesso AAD per l'endpoint `https://management.core.windows.net/`.  

Per informazioni su come ottenere questo token e chiamare gli endpoint di Azure, vedere [usare REST per gestire le risorse ml](how-to-manage-rest.md) e la [documentazione sull'API REST di Azure](https://docs.microsoft.com/rest/api/azure/).   

Negli esempi seguenti sostituire il testo in {} con i nomi delle istanze che determinano la risorsa associata.

### <a name="export-workspace-information"></a>Esportare le informazioni sull'area di lavoro

Usare questa chiamata per ottenere un elenco di tutte le aree di lavoro:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01`

Le informazioni su una singola area di lavoro possono essere ottenute con:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="export-compute-information"></a>Esportare le informazioni di calcolo

Tutte le destinazioni di calcolo collegate a un'area di lavoro possono essere ottenute con:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01`

Le informazioni su una singola destinazione di calcolo possono essere ottenute con:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01`

### <a name="export-run-history-data"></a>Esportare i dati della cronologia di esecuzione

Usare questa chiamata per ottenere un elenco di tutti gli esperimenti e le relative informazioni:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments`

Tutte le esecuzioni per un determinato esperimento possono essere ottenute con:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs`

Gli elementi della cronologia di esecuzione possono essere ottenuti con:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId}`

Tutte le metriche di esecuzione per un esperimento possono essere ottenute con:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics`

Una singola metrica di esecuzione può essere ottenuta con:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}`

### <a name="export-artifacts"></a>Esportare gli elementi

Usare questa chiamata per ottenere un elenco degli elementi e i relativi percorsi:

`https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}`

### <a name="export-notifications"></a>Esportare le notifiche

Usare questa chiamata per ottenere un elenco di tutte le attività archiviate:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks`

Le notifiche per una singola attività possono essere ottenute con:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}`

### <a name="export-data-stores"></a>Esportare gli archivi dati

Usare questa chiamata per ottenere un elenco di tutti gli archivi dati:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores`

I singoli archivi dati possono essere ottenuti con:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}`

### <a name="export-models"></a>Esportare i modelli

Usare questa chiamata per ottenere un elenco dei modelli e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

I singoli modelli possono essere ottenuti con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="export-assets"></a>Esportare gli asset

Usare questa chiamata per ottenere un elenco degli asset e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

I singoli asset possono essere ottenuti con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="export-images"></a>Esportare le immagini

Usare questa chiamata per ottenere un elenco delle immagini e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Le singole immagini possono essere ottenute con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="export-services"></a>Esportare i servizi

Usare questa chiamata per ottenere un elenco dei servizi e i relativi ID:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

I singoli servizi possono essere ottenuti con:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

### <a name="export-pipeline-experiments"></a>Esportare gli esperimenti della pipeline

I singoli esperimenti possono essere ottenuti con:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}`

### <a name="export-pipeline-graphs"></a>Esportare i grafici della pipeline

I singoli grafici possono essere ottenuti con:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}`

### <a name="export-pipeline-modules"></a>Esportare i moduli della pipeline

I moduli possono essere ottenuti con:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}`

### <a name="export-pipeline-templates"></a>Esportare i modelli della pipeline

I modelli possono essere ottenuti con:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}`

### <a name="export-pipeline-data-sources"></a>Esportare le origini dati della pipeline

Le origini dati possono essere ottenute con:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}`

## <a name="delete-assets-in-the-designer"></a>Elimina asset nella finestra di progettazione

Nella finestra di progettazione in cui è stato creato l'esperimento, eliminare i singoli asset:

1. Vai a finestra di progettazione

    ![Eliminare gli asset](./media/how-to-export-delete-data/delete-experiment.png)

1. Nell'elenco selezionare la bozza della singola pipeline da eliminare.

1. Selezionare **Elimina**.

### <a name="delete-datasets-in-the-designer"></a>Eliminare i set di impostazioni nella finestra di progettazione

Per eliminare i set di impostazioni nella finestra di progettazione, usare il portale di Azure o Storage Explorer per passare agli account di archiviazione connessi ed eliminare i set di impostazioni. L'annullamento della registrazione dei set di impostazioni nella finestra di progettazione rimuove solo il punto di riferimento nell'archivio.

