---
title: Configurare le destinazioni di calcolo di training & inferenza
titleSuffix: Azure Machine Learning
description: Aggiungere risorse di calcolo (destinazioni di calcolo) all'area di lavoro da usare per il training e l'inferenza di Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a3a70ac5d5603cad98c199cbd8e3b98bb095d131
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167669"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Configurare le destinazioni di calcolo per il training e la distribuzione dei modelli

Informazioni su come aggiungere risorse di calcolo di Azure all'area di lavoro Azure Machine Learning.  È quindi possibile usare queste risorse come [destinazioni di calcolo](concept-compute-target.md) per il training e l'inferenza nelle attività di machine learning.

Questo articolo illustra come configurare l'area di lavoro per l'uso di queste risorse di calcolo:

* Il computer locale
* Macchine virtuali remote
* HDInsight di Azure
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics.
* Istanza di contenitore di Azure

Per usare le destinazioni di calcolo gestite da Azure Machine Learning, vedere:


* [Istanza di calcolo di Azure Machine Learning](how-to-create-manage-compute-instance.md)
* [Cluster di calcolo di Azure Machine Learning](how-to-create-attach-compute-cluster.md)
* [Cluster del servizio Azure Kubernetes](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Limitazioni

* **Non creare più allegati simultanei nello stesso calcolo** dall'area di lavoro. Ad esempio, se si connette un cluster del servizio Kubernetes di Azure a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

    Se si vuole ricollegare una destinazione di calcolo, ad esempio per modificare TLS o altre impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente.

## <a name="whats-a-compute-target"></a>Che cos'è una destinazione di calcolo?

Con Azure Machine Learning, è possibile eseguire il training del modello su varie risorse o ambienti, chiamati collettivamente come [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.  Si usano anche le destinazioni di calcolo per la distribuzione del modello, come descritto in ["dove e come distribuire i modelli"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Computer locale

Quando si usa il computer locale per il **Training**, non è necessario creare una destinazione di calcolo.  È sufficiente [inviare l'esecuzione del training](how-to-set-up-training-targets.md) dal computer locale.

Quando si usa il computer locale per l' **inferenza**, è necessario che Docker sia installato. Per eseguire la distribuzione, utilizzare [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) per definire la porta che il servizio Web utilizzerà. Usare quindi il normale processo di distribuzione come descritto in [distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Macchine virtuali remote

Azure Machine Learning supporta anche il fissaggio di una macchina virtuale di Azure. La macchina virtuale deve essere un Data Science Virtual Machine di Azure (DSVM). La macchina virtuale offre una scelta dettagliata di strumenti e framework per l'intero ciclo di vita dello sviluppo dell'apprendimento automatico. Per altre informazioni su come usare una Data Science Virtual Machine, consultare [Configurare un ambiente di sviluppo per Azure Machine Learning](./how-to-configure-environment.md#dsvm).

> [!TIP]
> Anziché una macchina virtuale remota, è consigliabile usare l' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md). Si tratta di una soluzione di calcolo basata sul cloud completamente gestita e specifica per Azure Machine Learning. Per altre informazioni, vedere [creare e gestire Azure Machine Learning istanza di calcolo](how-to-create-manage-compute-instance.md).

1. **Creazione**: Azure Machine Learning non è possibile creare una macchina virtuale remota. Al contrario, è necessario creare la macchina virtuale e quindi collegarla all'area di lavoro Azure Machine Learning. Per informazioni sulla creazione di un DSVM, vedere effettuare [il provisioning del Data Science Virtual Machine per Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning supporta solo le macchine virtuali che eseguono **Ubuntu**. Quando si crea una macchina virtuale o se ne sceglie una esistente, è necessario selezionare una macchina virtuale che usa Ubuntu.
    > 
    > Azure Machine Learning richiede anche che la macchina virtuale disponga di un __indirizzo IP pubblico__.

1. **Collegare**: Per associare una macchina virtuale esistente come destinazione di calcolo, è necessario specificare l'ID risorsa, il nome utente e la password per la macchina virtuale. L'ID risorsa della macchina virtuale può essere creato usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome della macchina virtuale nel formato stringa seguente: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   In alternativa, è possibile collegare la Data Science VM all'area di lavoro [usando Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Non creare più allegati simultanei nello stesso DSVM dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo Data Science Virtual Machine. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

> [!TIP]
> Se si vuole __rimuovere__ (scollegare) una macchina virtuale dall'area di lavoro, usare il metodo [RemoteCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remotecompute#detach--) .
>
> Azure Machine Learning non elimina la macchina virtuale. È necessario eliminare manualmente la macchina virtuale usando il portale di Azure, l'interfaccia della riga di comando o l'SDK per la macchina virtuale di Azure.

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight è una piattaforma comune per l'analisi dei Big Data. La piattaforma include Apache Spark, che può essere usato per il training del modello.

1. **Creazione**: Azure Machine Learning non è possibile creare un cluster HDInsight. Al contrario, è necessario creare il cluster e quindi collegarlo all'area di lavoro Azure Machine Learning. Per altre informazioni, vedere [creare un cluster Spark in HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning richiede che il cluster HDInsight disponga di un __indirizzo IP pubblico__.

    Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
    
    Dopo la creazione del cluster, connettersi con il \<clustername> nome host-SSH.azurehdinsight.NET, dove \<clustername> è il nome fornito per il cluster. 

1. **Collegare**: Per collegare un cluster HDInsight come destinazione di calcolo, è necessario specificare l'ID risorsa, il nome utente e la password per il cluster HDInsight. L'ID risorsa del cluster HDInsight può essere creato usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome del cluster HDInsight nel formato stringa seguente: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   In alternativa, è possibile collegare il cluster HDInsight all'area di lavoro [usando Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Non creare più allegati simultanei nello stesso HDInsight dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

1. **Configurare**: Creare una configurazione di esecuzione per la destinazione di calcolo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]

> [!TIP]
> Se si vuole __rimuovere__ (scollegare) un cluster HDInsight dall'area di lavoro, usare il metodo [HDInsightCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.hdinsight.hdinsightcompute#detach--) .
>
> Azure Machine Learning non elimina il cluster HDInsight. È necessario eliminarlo manualmente usando il portale di Azure, l'interfaccia della riga di comando o l'SDK per Azure HDInsight.

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch viene usato per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. AzureBatchStep può essere usato in una pipeline di Azure Machine Learning per inviare processi a un pool di computer di Azure Batch.

Per collegare Azure Batch come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

-    **Nome dell'ambiente di calcolo di Azure Batch**: un nome descrittivo da usare per l'ambiente di calcolo nell'area di lavoro.
-    **Nome dell'account Azure Batch**: il nome dell'account Azure Batch.
-    **Gruppo di risorse**: il gruppo di risorse che contiene l'account Azure Batch.

Il codice seguente illustra come collegare Azure Batch come destinazione di calcolo:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Non creare più allegati simultanei per lo stesso Azure Batch dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks è un ambiente basato su Apache Spark nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

> [! IMPORTANTE} Azure Machine Learning non è possibile creare una destinazione di calcolo Azure Databricks. Al contrario, è necessario creare un'area di lavoro Azure Databricks e quindi collegarla all'area di lavoro di Azure Machine Learning. Per creare una risorsa dell'area di lavoro, vedere il documento [eseguire un processo Spark in Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) .

Per collegare Azure Databricks come destinazione di calcolo, fornire le informazioni seguenti:

* __Databricks nome di calcolo__: il nome che si vuole assegnare a questa risorsa di calcolo.
* __Nome area di lavoro databricks__: nome dell'area di lavoro Azure Databricks.
* __Token di accesso di databricks__: il token di accesso usato per l'autenticazione Azure Databricks. Per generare un token di accesso vedere il documento [Authentication](/azure/databricks/dev-tools/api/latest/authentication) (Autenticazione).

Il codice seguente illustra come associare Azure Databricks come destinazione di calcolo con Azure Machine Learning SDK (__l'area di lavoro databricks deve essere presente nella stessa sottoscrizione dell'area di lavoro di AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-databricks) in GitHub.

> [!WARNING]
> Non creare più allegati simultanei per lo stesso Azure Databricks dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics.

Azure Data Lake Analytics è una piattaforma di analisi dei Big Data nel cloud di Azure. Può essere usata come destinazione di calcolo con una pipeline di Azure Machine Learning.

Creare un account di Azure Data Lake Analytics prima di usarla. Per creare questa risorsa vedere il documento [Introduzione ad Azure Data Lake Analytics con il portale di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Per connettere Data Lake Analytics come destinazione di calcolo è necessario usare Azure Machine Learning SDK e specificare le informazioni seguenti:

* __Nome del calcolo__: il nome da assegnare a questa risorsa di calcolo.
* __Gruppo di risorse__: il gruppo di risorse contenente l'account data Lake Analytics.
* __Nome account__: il nome dell'account data Lake Analytics.

Il codice seguente illustra in che modo connettere Data Lake Analytics come destinazione di calcolo:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Per un esempio più dettagliato, vedere un [notebook di esempio](https://aka.ms/pl-adla) in GitHub.

> [!WARNING]
> Non creare più allegati simultanei nello stesso Anna dall'area di lavoro. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

> [!TIP]
> Le pipeline di Azure Machine Learning possono funzionare solo con i dati archiviati nell'archivio dati predefinito dell'account Data Lake Analytics. Se i dati che è necessario utilizzare si trova in un archivio non predefinito, è possibile utilizzare un oggetto [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) per copiare i dati prima del training.

## <a name="azure-container-instance"></a><a id="aci"></a>Istanza di contenitore di Azure

Le istanze di contenitore di Azure (ACI) vengono create dinamicamente quando si distribuisce un modello. Non è possibile creare o alporre l'associazione ACI all'area di lavoro in qualsiasi altro modo. Per altre informazioni, vedere [distribuire un modello in istanze di contenitore di Azure](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Azure Kubernetes Service (AKS) consente diverse opzioni di configurazione quando vengono usate con Azure Machine Learning. Per altre informazioni, vedere [come creare e alleghi il servizio Azure Kubernetes](how-to-create-attach-kubernetes.md).

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare la risorsa di calcolo per [configurare e inviare un'esecuzione di training](how-to-set-up-training-targets.md).
* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* [Usare Azure Machine Learning con le reti virtuali di Azure](./how-to-network-security-overview.md)