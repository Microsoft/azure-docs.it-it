---
title: "Guida introduttiva: Creare un'istanza di Azure Data Factory con Python"
description: Creare una data factory di Azure per copiare i dati da una posizione dell'archivio BLOB di Azure a un'altra posizione.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.custom: seo-python-october2019
ms.openlocfilehash: 85b61e03f1545b8a0e8263b799ac7a5b347be181
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419137"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Guida introduttiva: Creare una data factory e una pipeline con Python

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questo argomento di avvio rapido viene creata una data factory con Python. La pipeline in questa data factory copia i dati da una cartella a un'altra in archiviazione BLOB di Azure.

Azure Data Factory è un servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi. Con Azure Data Factory è possibile creare e pianificare flussi di lavoro basati sui dati, detti pipeline,

che inseriscono dati provenienti da archivi diversi. Le pipeline elaborano o trasformano i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. Le pipeline pubblicano i dati di output in archivi dati, ad esempio Azure SQL Data Warehouse per le applicazioni business intelligence (BI).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Un account di archiviazione di Azure](../storage/common/storage-account-create.md).

* [Azure Storage Explorer](https://storageexplorer.com/) (facoltativo).

* [Un'applicazione in Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Prendere nota dei valori seguenti da usare nei passaggi successivi: **ID applicazione**, **chiave di autenticazione** e **ID tenant**. Assegnare l'applicazione al ruolo **Collaboratore** seguendo le istruzioni riportate nello stesso articolo.

## <a name="create-and-upload-an-input-file"></a>Creare e caricare un file di input

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **input.txt** sul disco.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Usare strumenti come [Azure Storage Explorer](https://storageexplorer.com/) per creare il contenitore **adfv2tutorial** e la cartella **input** nel contenitore. Caricare quindi il file **input.txt** nella cartella **input**.

## <a name="install-the-python-package"></a>Installare il pacchetto Python

1. Aprire un terminale o un prompt dei comandi con privilegi di amministratore. 
2. Per prima cosa, installare il pacchetto Python per le risorse di gestione di Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Per installare il pacchetto Python per Data Factory, eseguire questo comando:

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Python SDK per Data Factory](https://github.com/Azure/azure-sdk-for-python) supporta Python 2.7, 3.3, 3.4, 3.5, 3.6 e 3.7.

## <a name="create-a-data-factory-client"></a>Creare un client di data factory

1. Creare un file denominato **datafactory.py**. Aggiungere le istruzioni seguenti per aggiungere riferimenti a spazi dei nomi.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Aggiungere le funzioni seguenti, che consentono di stampare informazioni.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe DataFactoryManagementClient. Usare questo oggetto per creare la data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline. Impostare la variabile **subscription_id** sull'ID della sottoscrizione di Azure. Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}
    ```

## <a name="create-a-data-factory"></a>Creare una data factory

Aggiungere il codice seguente al metodo **Main** per creare una **data factory**. Se il gruppo di risorse esiste già, impostare come commento la prima istruzione `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**.

Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida introduttiva è necessario creare solo un servizio collegato di Archiviazione di Azure come archivio di origine e sink della copia, denominato "AzureStorageLinkedService" nell'esempio. Sostituire `<storageaccountname>` e `<storageaccountkey>` con il nome e la chiave dell'account di archiviazione di Azure.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Creare set di dati

In questa sezione vengono creati due set di dati: uno per l'origine e l'altro per il sink.

### <a name="create-a-dataset-for-source-azure-blob"></a>Creare un set di dati per il BLOB di Azure di origine

Aggiungere il codice seguente al metodo Main per creare un set di dati BLOB di Azure. Per informazioni sulle proprietà del set di dati BLOB di Azure, vedere l'articolo [Azure blob connector](connector-azure-blob-storage.md#dataset-properties) (Connettore BLOB di Azure).

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati BLOB fa riferimento al servizio collegato di Archiviazione di Azure creato nel passaggio precedente.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(linked_service_name=ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Creare un set di dati per il BLOB di Azure sink

Aggiungere il codice seguente al metodo Main per creare un set di dati BLOB di Azure. Per informazioni sulle proprietà del set di dati BLOB di Azure, vedere l'articolo [Azure blob connector](connector-azure-blob-storage.md#dataset-properties) (Connettore BLOB di Azure).

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati BLOB fa riferimento al servizio collegato di Archiviazione di Azure creato nel passaggio precedente.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo **Main** per creare una **pipeline con un'attività di copia**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

Per monitorare l'esecuzione della pipeline, aggiungere il codice seguente al metodo **Main**:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Aggiungere quindi l'istruzione seguente per richiamare il metodo **main** quando viene eseguito il programma:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Ecco il codice Python completo:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time


def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")


def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")


def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(
            activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(
            activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(
            activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(
        client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location': 'eastus'}
    df_params = {'location': 'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString(
        value='DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(
        connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(
        rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob = AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.

Di seguito è riportato l'output di esempio:

```json
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare la data factory, aggiungere il codice seguente al programma:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md).
