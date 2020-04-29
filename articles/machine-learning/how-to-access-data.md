---
title: Connettersi ai servizi di archiviazione di Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare gli archivi dati per connettersi in modo sicuro ai servizi di archiviazione di Azure durante il training con Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231204"
---
# <a name="connect-to-azure-storage-services"></a>Connettersi ai servizi di archiviazione di Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come connettersi ai servizi di archiviazione di Azure tramite Azure Machine Learning archivi dati. Gli archivi dati archiviano le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token nell' [Key Vault](https://azure.microsoft.com/services/key-vault/) associata all'area di lavoro, in modo da poter accedere in modo sicuro alla risorsa di archiviazione senza doverli codificare negli script. Per comprendere dove archiviare i dati nel flusso di lavoro globale di accesso ai dati di Azure Machine Learning, vedere l'articolo [accesso sicuro ai dati](concept-data.md#data-workflow) .

È possibile creare archivi dati da [queste soluzioni di archiviazione di Azure](#matrix). Per le soluzioni di archiviazione non supportate e per risparmiare i costi in uscita durante gli esperimenti di Machine Learning, è consigliabile [spostare i dati](#move) in soluzioni di archiviazione di Azure supportate. 

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova la [versione gratuita o a pagamento del Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) o una [condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md) o utilizzarne una esistente tramite Python SDK. Importare la `Workspace` classe `Datastore` e e caricare le informazioni sulla sottoscrizione dal file `config.json` utilizzando la funzione. `from_config()` Per impostazione predefinita, Cerca il file JSON nella directory corrente, ma è anche possibile specificare un parametro Path per puntare al file usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipi di servizi di archiviazione dati supportati

Gli archivi dati supportano attualmente l'archiviazione delle informazioni di connessione nei servizi di archiviazione elencati nella matrice seguente.

| Tipo&nbsp;di archiviazione | Tipo&nbsp;di autenticazione | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [SDK&nbsp;Python&nbsp;di&nbsp; Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [INTERFACCIA&nbsp;della&nbsp;riga di comando di Azure Machine Learning](reference-azure-machine-learning-cli.md) | [API&nbsp;Rest&nbsp;di&nbsp; Azure Machine Learning](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Archiviazione&nbsp;BLOB&nbsp;di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chiave account <br> Token SAS | ✓ | ✓ | ✓ |✓
[Condivisione&nbsp;file&nbsp;di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chiave account <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;data Lake&nbsp;storage gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Entità servizio| ✓ | ✓ | ✓ |✓
[Archiviazione&nbsp;di&nbsp;Azure Data Lake&nbsp;generazione 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entità servizio| ✓ | ✓ | ✓ |✓
[Database&nbsp;SQL&nbsp;di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticazione SQL <br>Entità servizio| ✓ | ✓ | ✓ |✓
[PostgreSQL&nbsp;di Azure](https://docs.microsoft.com/azure/postgresql/overview) | Autenticazione SQL| ✓ | ✓ | ✓ |✓
[Database&nbsp;&nbsp;di Azure&nbsp;per MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticazione SQL|  | ✓* | ✓* |✓*
[&nbsp;File&nbsp;System di databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nessuna autenticazione | | ✓** | ✓ ** |✓** 

* MySQL è supportato solo per la pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Databricks è supportato solo per la pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Linee guida per l'archiviazione

Si consiglia di creare un archivio dati per un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Per i BLOB sono disponibili sia l'archiviazione standard che Premium. Sebbene archiviazione Premium sia più costosa, la velocità effettiva più veloce potrebbe migliorare la velocità delle esecuzioni di training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sul costo degli account di archiviazione, vedere il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) è basato sull'archivio BLOB di Azure e progettato per l'analisi Big Data aziendale. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente.

Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati automaticamente nell'area di lavoro. Sono rispettivamente denominati `workspaceblobstore` e `workspacefilestore`. `workspaceblobstore`viene usato per archiviare gli elementi dell'area di lavoro e i log dell'esperimento di machine learning. `workspacefilestore`viene usato per archiviare notebook e script R autorizzati tramite l' [istanza di calcolo](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Il `workspaceblobstore` contenitore viene impostato come archivio dati predefinito.

> [!IMPORTANT]
> Azure Machine Learning Designer (anteprima) creerà automaticamente un archivio dati denominato **azureml_globaldatasets** quando si apre un esempio nella Home page della finestra di progettazione. Questo archivio dati contiene solo set di dati di esempio. **Non** usare questo archivio dati per l'accesso ai dati riservati.
> ![Archivio dati creato automaticamente per i set di dati di esempio della finestra di progettazione](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Creare e registrare archivi dati

Quando si registra una soluzione di archiviazione di Azure come archivio dati, l'archivio dati viene creato e registrato automaticamente in un'area di lavoro specifica. È possibile creare e registrare archivi dati in un'area di lavoro usando [Python SDK](#python-sdk) o [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> Come parte del processo di creazione e registrazione dell'archivio dati iniziale, Azure Machine Learning verifica che il servizio di archiviazione sottostante esista e che l'entità fornita dall'utente (nome utente, entità servizio o token SAS) abbia accesso a tale archiviazione. Per Azure Data Lake Storage archivi dati di generazione 1 e 2, tuttavia, questa convalida viene eseguita in un secondo momento, quando [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) vengono [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) chiamati i metodi di accesso ai dati come o. 
<br><br>
Dopo la creazione dell'archivio dati, questa convalida viene eseguita solo per i metodi che richiedono l'accesso al contenitore di archiviazione sottostante, **non** ogni volta che vengono recuperati gli oggetti dell'archivio dati. Ad esempio, la convalida si verifica se si vuole scaricare i file dall'archivio dati; Tuttavia, se si vuole solo modificare l'archivio dati predefinito, la convalida non viene eseguita.

### <a name="python-sdk"></a>Python SDK

Tutti i metodi Register si trovano nella [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e hanno il formato `register_azure_*`.
> [!IMPORTANT]
> Se l'account di archiviazione si trova in una rete virtuale, è supportata solo la creazione di archivi dati **tramite l'SDK** .

È possibile trovare le informazioni necessarie per popolare il `register_azure_*()` metodo sul [portale di Azure](https://portal.azure.com).

* Se si prevede di usare una chiave dell'account o un token di firma di accesso condiviso per l'autenticazione, selezionare **account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione che si vuole registrare. 
  * Nella pagina **Panoramica** sono disponibili informazioni quali il nome dell'account, il contenitore e il nome della condivisione file. 
      1. Per le chiavi dell'account, passare a **chiavi di accesso** nel riquadro **Impostazioni** . 
      1. Per i token SAS, passare a **firme di accesso condiviso** nel riquadro **Impostazioni** .

* Se si prevede di usare un'entità servizio per l'autenticazione, passare alla **registrazioni app** e selezionare l'app che si vuole usare. 
    * La pagina di **Panoramica** corrispondente conterrà informazioni obbligatorie come ID tenant e ID client.

Gli esempi seguenti illustrano come registrare un contenitore BLOB di Azure, una condivisione file di Azure e Azure Data Lake Storage generazione 2 come archivio dati. I parametri forniti in questi esempi sono i **parametri obbligatori** per creare e registrare un archivio dati. 

Per creare archivi dati per altri servizi di archiviazione e visualizzare i parametri facoltativi per questi metodi, vedere la [documentazione di riferimento relativa `register_azure_*` ai metodi applicabili](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Contenitore BLOB

Per registrare un contenitore BLOB di Azure come archivio dati, usare [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Il codice seguente crea e registra l' `blob_datastore_name` archivio dati nell' `ws` area di lavoro. Questo archivio dati accede al `my-container-name` contenitore BLOB nell'account di `my-account-name` archiviazione usando la chiave di accesso dell'account fornita.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Se il contenitore BLOB si trova in una rete virtuale, includere `skip_validation=True` il parametro [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) nel metodo. 

#### <a name="file-share"></a>Condivisione file

Per registrare una condivisione file di Azure come archivio dati, usare [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Il codice seguente crea e registra l' `file_datastore_name` archivio dati nell' `ws` area di lavoro. Questo archivio dati accede alla `my-fileshare-name` condivisione file nell'account di `my-account-name` archiviazione usando la chiave di accesso dell'account fornita.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Se la condivisione file si trova in una rete virtuale, includere `skip_validation=True` il parametro [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) nel metodo. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generazione 2

Per un archivio dati di Azure Data Lake Storage generazione 2 (ADLS gen 2), usare [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati delle credenziali connesso a una risorsa di archiviazione di Azure datalake gen 2 con le [autorizzazioni dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Per usare l'entità servizio, è necessario [registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e concedere all'entità servizio l'accesso al *proprietario dei dati del BLOB di archiviazione* . Altre informazioni sulla [configurazione del controllo di accesso per ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Per usare l'entità servizio, è necessario [registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e concedere all'entità servizio l'accesso ai dati appropriato. Altre informazioni sulla [configurazione del controllo di accesso per ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Il codice seguente crea e registra l' `adlsgen2_datastore_name` archivio dati nell' `ws` area di lavoro. Questo archivio dati accede al file system `test` nell'account di `account_name` archiviazione, usando le credenziali dell'entità servizio fornite.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Creare un nuovo archivio dati in pochi passaggi in Azure Machine Learning Studio:

> [!IMPORTANT]
> Se l'account di archiviazione si trova in una rete virtuale, è supportata solo la creazione di archivi dati [tramite l'SDK](#python-sdk) . 

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **archivi dati** nel riquadro sinistro in **Gestisci**.
1. Selezionare **+ Nuovo archivio dati**.
1. Completare il modulo per un nuovo archivio dati. Il modulo si aggiorna in modo intelligente in base alle selezioni effettuate per tipo di archiviazione di Azure e tipo di autenticazione.
  
È possibile trovare le informazioni necessarie per popolare il modulo nella [portale di Azure](https://portal.azure.com). Selezionare **account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione che si desidera registrare. Nella pagina **Panoramica** sono disponibili informazioni quali il nome dell'account, il contenitore e il nome della condivisione file. 

* Per gli elementi di autenticazione, ad esempio la chiave dell'account o il token di firma di accesso condiviso, passare a **chiavi di accesso** nel riquadro **Impostazioni** . 

* Per gli elementi dell'entità servizio, ad esempio ID tenant e ID client, passare alla **registrazioni app** e selezionare l'app che si vuole usare. La pagina di **Panoramica** corrispondente conterrà questi elementi. 

Nell'esempio seguente viene illustrato l'aspetto del form quando si crea un archivio dati BLOB di Azure: 
    
![Modulo per un nuovo archivio dati](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Ottenere gli archivi dati dall'area di lavoro

Per ottenere un archivio dati specifico registrato nell'area di lavoro corrente, usare [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) il metodo statico sulla `Datastore` classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Per ottenere l'elenco degli archivi dati registrati con una determinata area di lavoro, è possibile usare [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) la proprietà in un oggetto dell'area di lavoro:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Per ottenere l'archivio dati predefinito dell'area di lavoro, usare questa riga:

```Python
datastore = ws.get_default_datastore()
```
È anche possibile modificare l'archivio dati predefinito con il codice seguente. Questa funzionalità è supportata solo tramite l'SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Caricamento e download dei dati

I [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) metodi [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritti negli esempi seguenti sono specifici di e funzionano in modo identico per le classi [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

> [!NOTE]
> Il caricamento in archivi dati AzureDataLakeGen2 non è supportato in questo momento.

### <a name="upload"></a>Caricamento

Caricare una directory o singoli file nell'archivio dati usando Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Il `target_path` parametro specifica il percorso nella condivisione file o nel contenitore BLOB da caricare. Il valore predefinito è `None`, quindi i dati vengono caricati nella radice. Se `overwrite=True`, tutti i dati esistenti `target_path` in vengono sovrascritti.

È anche possibile caricare un elenco di singoli file nell'archivio dati tramite il `upload_files()` metodo.

### <a name="download"></a>Download

Scaricare i dati da un archivio dati all'file system locale:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Il `target_path` parametro è il percorso della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore BLOB) verrà scaricato.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Accedi ai dati durante il training

Per interagire con i dati negli archivi dati o per comprimere i dati in un oggetto utilizzabile per le attività di Machine Learning, ad esempio il training, [creare un set di dati Azure Machine Learning](how-to-create-register-datasets.md). I set di dati forniscono funzioni che caricano dati tabulari in un dataframe Pandas o Spark. I set di dati offrono inoltre la possibilità di scaricare o montare file di qualsiasi formato dall'archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL. [Altre informazioni su come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Accesso al codice sorgente durante il training

Archiviazione BLOB di Azure offre velocità di velocità effettiva più elevate rispetto a una condivisione file di Azure e scalabilità a un numero elevato di processi avviati in parallelo. Per questo motivo, è consigliabile configurare le esecuzioni in modo da usare l'archiviazione BLOB per il trasferimento dei file di codice sorgente.

Nell'esempio di codice seguente viene specificato nella configurazione di esecuzione quale archivio dati BLOB utilizzare per i trasferimenti del codice sorgente.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accedere ai dati durante il Punteggio

Azure Machine Learning offre diversi modi per usare i modelli per l'assegnazione dei punteggi. Alcuni di questi metodi non forniscono l'accesso agli archivi dati. Usare la tabella seguente per informazioni sui metodi che consentono di accedere agli archivi dati durante il Punteggio:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-use-parallel-run-step.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli come servizio Web. |
| [Modulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli nei dispositivi IoT Edge. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando Azure SDK pertinente per accedere ai dati. [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python) , ad esempio, è una libreria client che è possibile usare per accedere ai dati archiviati in BLOB o file.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Spostare i dati in soluzioni di archiviazione di Azure supportate

Azure Machine Learning supporta l'accesso ai dati di archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e database di Azure per PostgreSQL. Se si usa una risorsa di archiviazione non supportata, è consigliabile spostare i dati in soluzioni di archiviazione di Azure supportate usando [Azure Data Factory e questi passaggi](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Lo stato di trasferimento dei dati nell'archiviazione supportata può consentire di risparmiare i costi di uscita dei dati durante gli esperimenti di machine learning. 

Azure Data Factory offre un trasferimento dati efficiente e resiliente con più di 80 connettori predefiniti senza costi aggiuntivi. Questi connettori includono i servizi dati di Azure, le origini dati locali, Amazon S3 e lo spostamento verso il proprio e Google BigQuery.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md)
* [Eseguire il training di un modello](how-to-train-ml-models.md)
* [Distribuire un modello](how-to-deploy-and-where.md)
