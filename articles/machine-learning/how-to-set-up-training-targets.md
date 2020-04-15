---
title: Usare gli obiettivi di calcolo per il training del modelloUse compute targets for model training
titleSuffix: Azure Machine Learning
description: Configurare gli ambienti di training (destinazioni di calcolo) per il training del modello di Machine Learning. Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 7fcfac923da1c0daee58b10d92cbc6a6ad5e7910
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383415"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Impostare e usare gli obiettivi di calcolo per il training del modelloSet up and use compute targets for model training 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Con Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, denominati collettivamente destinazioni di [__calcolo.__](concept-azure-machine-learning-architecture.md#compute-targets) Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Machine Learning, Azure HDInsight o una macchina virtuale remota.  È possibile anche creare destinazioni di calcolo per la distribuzione del modello, come descritto in ["Dove e come distribuire i modelli"](how-to-deploy-and-where.md).

È possibile creare e gestire una destinazione di calcolo usando Azure Machine Learning SDK, Azure Machine Learning Studio, l'interfaccia della riga di comando di Azure o l'estensione Codice VS di Azure Machine Learning.You can create and manage a compute target using the Azure Machine Learning SDK, Azure Machine Learning studio, Azure CLI or Azure Machine Learning VS Code extension. Se si hanno destinazioni di calcolo create tramite un altro servizio, ad esempio un cluster HDInsight, è possibile usarle collegandole all'area di lavoro di Azure Machine Learning.If you have compute targets that were created through another service (for example, an HDInsight cluster), you can use them by attaching them to your Azure Machine Learning workspace.
 
Questo articolo illustra come usare diverse destinazioni di calcolo per il training del modello.  I passaggi per tutte le destinazioni di calcolo seguono lo stesso flusso di lavoro:
1. __Creare__ una destinazione di calcolo se non ne è già presente.
2. __Collegare__ la destinazione di calcolo all'area di lavoro.
3. __Configurare__ le destinazioni di calcolo affinché contengano l'ambiente Python e le dipendenze del pacchetto necessari per lo script.


>[!NOTE]
> Il codice in questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.74.Code in this article was tested with Azure Machine Learning SDK version 1.0.74.

## <a name="compute-targets-for-training"></a>Destinazioni di calcolo per il training

Azure Machine Learning offre un supporto variabile tra destinazioni di elaborazione diverse. Un tipico ciclo di vita di sviluppo modello inizia con lo sviluppo e la sperimentazione su una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, ad esempio il computer locale o una macchina virtuale basata sul cloud. Quando il training viene eseguito su set di dati più grandi, o quando si esegue il training distribuito, è consigliabile usare l'ambiente di calcolo di Azure Machine Learning per creare un cluster a uno o più nodi che viene ridimensionato automaticamente ogni volta che viene inviata un'esecuzione. È possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato di seguito:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> L'ambiente di calcolo di Azure Machine Learning può essere creato come risorsa permanente o in modo dinamico quando si richiede un'esecuzione. Al termine del training, la creazione basata sull'esecuzione rimuove la destinazione di calcolo. Non è quindi possibile riutilizzare le destinazioni di calcolo create in questo modo.

## <a name="whats-a-run-configuration"></a>Che cos'è una configurazione di esecuzione?

Solitamente il training si avvia nel computer locale e in un secondo momento si esegue tale script di training in una destinazione di calcolo diversa. Con Azure Machine Learning è possibile eseguire lo script in varie destinazioni di calcolo senza dover modificare lo script.

È sufficiente definire l'ambiente per ogni destinazione di calcolo all'interno di una configurazione di **esecuzione.**  Quindi, quando si vuole eseguire l'esperimento di training in una destinazione di calcolo diversa, specificare la configurazione di esecuzione per tale ambiente di calcolo. Per informazioni dettagliate sulla specifica di un ambiente e sull'associazione per l'esecuzione della configurazione, vedere Creare e gestire ambienti per la formazione e la [distribuzione.](how-to-use-environments.md)

Altre informazioni sull'[invio degli esperimenti](#submit) alla fine di questo articolo.

## <a name="whats-an-estimator"></a>Cos'è uno stimatore?

Per facilitare il training dei modelli usando framework popolari, Azure Machine Learning Python SDK offre un'astrazione alternativa di livello superiore, la classe di stima.  Questa classe consente di costruire facilmente configurazioni di esecuzione. È possibile creare e usare un [Stimatore](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) generico per inviare script di formazione che utilizzano qualsiasi framework di apprendimento scelto (ad esempio scikit-learn). È consigliabile usare uno stimatore per il training quando crea automaticamente oggetti incorporati come un ambiente o oggetti RunConfiguration per l'utente. Se si vuole avere un maggiore controllo sulla modalità di creazione di questi oggetti e specificare i pacchetti da installare per l'esecuzione dell'esperimento, seguire questi passaggi per inviare gli esperimenti di training usando un oggetto RunConfiguration in un calcolo di Azure Machine Learning.If you wish have more control on how these objects are created and specify what packages to install for your experiment run, follow [these steps](#amlcompute) to submit your training experiments using a RunConfiguration object on an Azure Machine Learning Compute.

Per le attività PyTorch, TensorFlow e Chainer, Azure Machine Learning fornisce anche gli strumenti di funzione [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) per semplificare l'utilizzo di questi framework.

Per ulteriori informazioni, vedere [Eseguire il training di modelli ML con stimatori](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Che cos'è una pipeline mL?

Con le pipeline di ML, è possibile ottimizzare il flusso di lavoro con semplicità, velocità, portabilità e riutilizzo. Quando si creano pipeline con Azure Machine Learning, è possibile concentrarsi sulle proprie competenze, sull'apprendimento automatico anziché sull'infrastruttura e sull'automazione.

Le pipeline ML vengono costruite da più **passaggi,** che sono unità di calcolo distinte nella pipeline. Ogni passaggio può essere eseguito in modo indipendente e usare risorse di calcolo isolate. Ciò consente a più data scientist di lavorare contemporaneamente sulla stessa pipeline senza sovraccaricare le risorse di calcolo e semplifica inoltre l'uso di tipi/dimensioni di calcolo diversi per ogni passaggio.

> [!TIP]
> Le pipeline di Machine Learning possono usare la configurazione di esecuzione o gli stimatori durante il training dei modelli.

Mentre le pipeline ML possono eseguire il training dei modelli, possono anche preparare i dati prima del training e distribuire i modelli dopo il training. Uno dei casi d'uso principali per le pipeline è il punteggio in batch. Per altre informazioni, vedere [Pipeline: Ottimizzare i flussi di lavoro](concept-ml-pipelines.md)di Apprendimento automatico.

## <a name="set-up-in-python"></a>Configurazione in Python

Usare le sezioni seguenti per configurare queste destinazioni di calcolo:

* [Computer locale](#local)
* [Ambiente di calcolo di Machine Learning](#amlcompute)
* [Macchine virtuali remote](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Computer locale

1. **Crea e attach:** non è necessario creare o collegare una destinazione di calcolo per usare il computer locale come ambiente di training.  

1. **Configura**: quando si utilizza il computer locale come destinazione di calcolo, il codice di training viene eseguito nell'ambiente di [sviluppo.](how-to-configure-environment.md)  Se l'ambiente contiene già i pacchetti di Python necessari, usare l'ambiente gestito dall'utente.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Dopo aver collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste [nell'inviare l'esecuzione del training](#submit).

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Ambiente di calcolo di Machine Learning

L'ambiente di calcolo di Azure Machine Learning è un'infrastruttura di calcolo gestito che consente all'utente di creare facilmente un ambiente di calcolo a uno o più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente in contenitori e comprime le dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

È possibile usare un ambiente di calcolo di Azure Machine Learning per distribuire il processo di training in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


È possibile creare l'ambiente di calcolo di Azure Machine Learning su richiesta quando si pianifica un'esecuzione, oppure come risorsa permanente.

#### <a name="run-based-creation"></a>Creazione basata su esecuzione

È possibile creare l'ambiente di calcolo di Machine Learning come destinazione di calcolo in fase di esecuzione. Il calcolo viene creato automaticamente per l'esecuzione. Il calcolo viene eliminato automaticamente dopo il completamento dell'esecuzione. 

> [!IMPORTANT]
> La creazione basata su esecuzione dell'ambiente di calcolo di Azure Machine Learning è attualmente disponibile in anteprima. Non usare la creazione basata su esecuzione se si usa l'ottimizzazione degli iperparametri o le funzionalità automatizzate di Machine Learning. Per usare l'ottimizzazione degli iperparametri o le funzionalità automatizzate di Machine Learning, creare una destinazione di [calcolo permanente](#persistent).

1.  **Crea, collega e configura:** la creazione basata su esecuzione esegue tutti i passaggi necessari per creare, collegare e configurare la destinazione di calcolo con la configurazione di esecuzione.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Dopo aver collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste [nell'inviare l'esecuzione del training](#submit).

#### <a name="persistent-compute"></a><a id="persistent"></a>Calcolo persistente

Un ambiente di calcolo di Machine Learning permanente può essere usato nuovamente su più processi. Il calcolo può anche essere condiviso con altri utenti nell'area di lavoro e viene mantenuto tra i processi.

1. **Creare e collegare:** per creare una risorsa di calcolo di Azure Machine Learning persistente in Python, specificare le **proprietà vm_size** e **max_nodes.** Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà. Il calcolo viene ridimensionato automaticamente fino a zero nodi quando non viene usato.   Le macchine virtuali dedicate vengono create per eseguire i processi in base alle esigenze.
    
    * **vm_size:** la famiglia di macchine virtuali dei nodi creati da Azure Machine Learning Compute.The Machine Name family of the nodes created by Azure Machine Learning Compute.
    * **max_nodes:** il numero massimo di nodi da scalare automaticamente fino a quando si esegue un processo in Azure Machine Learning Compute.Max_nodes: The max number of nodes to autoscale up to when you run a job on Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Durante la creazione di un ambiente di calcolo di Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente di dimensione fissa o all'interno di una Rete virtuale di Azure esistente nella sottoscrizione.  Per informazioni dettagliate, consultare [AmlCompute class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) (Classe AmlCompute).
    
   In alternativa, è possibile creare e collegare una risorsa di calcolo di Azure Machine Learning persistente in [Azure Machine Learning Studio.](#portal-create)

1. **Configura:** crea una configurazione di esecuzione per la destinazione di calcolo persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Dopo aver collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste [nell'inviare l'esecuzione del training](#submit).


### <a name="remote-virtual-machines"></a><a id="vm"></a>Macchine virtuali remote

Azure Machine Learning consente di usare la risorsa di calcolo personale e di associarla all'area di lavoro. Uno di questi tipi di risorse è una macchina virtuale remota arbitraria, purché sia accessibile da Azure Machine Learning.One such resource type is an arbitrary remote VM, as long as it's accessible from Azure Machine Learning. La risorsa può essere una macchina virtuale di Azure, un server remoto nell'organizzazione o in locale. In particolare, se si dispone dell'indirizzo IP e delle credenziali (nome utente e password o chiave SSH), è possibile usare qualsiasi macchina virtuale accessibile per le esecuzioni remote.

È possibile usare un ambiente conda integrato nel sistema, un ambiente Python già esistente o un contenitore Docker. Per l'esecuzione in un contenitore Docker, è necessario che il motore Docker sia in esecuzione nella macchina virtuale. Questa funzionalità è particolarmente utile quando si preferisce un ambiente di sviluppo o sperimentazione basato sul cloud più flessibile rispetto al computer locale.

Per questo scenario usare Data Science Virtual Machine (DSVM) come macchina virtuale di Azure. Questa macchina virtuale è un ambiente preconfigurato di data science e di sviluppo per l'intelligenza artificiale in Azure. La macchina virtuale offre una scelta dettagliata di strumenti e framework per l'intero ciclo di vita dello sviluppo dell'apprendimento automatico. Per altre informazioni su come usare una Data Science Virtual Machine, consultare [Configurare un ambiente di sviluppo per Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Create**: Creare una DSVM prima di usarla per eseguire il training del modello. Per creare questa risorsa consultare [Effettuare il provisioning della Data Science Virtual Machine per Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu. Quando si crea una macchina virtuale o se ne sceglie una esistente, è necessario selezionare una macchina virtuale che usa Ubuntu.

1. **Connetti:** per collegare una macchina virtuale esistente come destinazione di calcolo, è necessario fornire l'ID risorsa, il nome utente e la password per la macchina virtuale. L'ID risorsa della macchina virtuale può essere costruito usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome della macchina virtuale usando il formato di stringa seguente:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   In alternativa, è possibile collegare la DSVM all'area di lavoro [usando Azure Machine Learning Studio.](#portal-reuse)

1. **Configura:** crea una configurazione di esecuzione per la destinazione di calcolo DSVM. Docker e conda vengono usati per creare e configurare l'ambiente di training nella Data Science Virtual Machine.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Dopo aver collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste [nell'inviare l'esecuzione del training](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight è una piattaforma comune per l'analisi dei Big Data. La piattaforma include Apache Spark, che può essere usato per il training del modello.

1. **Create:** creare il cluster HDInsight prima di usarlo per eseguire il training del modello. Per creare un cluster Spark in HDInsight, consultare [Creare un cluster Spark in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Quando si crea il cluster, è necessario specificare un nome utente e una password SSH. Prendere nota di questi valori, perché saranno necessari quando si usa HDInsight come destinazione di calcolo.
    
    Dopo aver creato il cluster, collegarlo al nome host \<clustername>-ssh.azurehdinsight.net, dove \<clustername> è il nome fornito per il cluster. 

1. **Attach:** per collegare un cluster HDInsight come destinazione di calcolo, è necessario fornire l'ID risorsa, il nome utente e la password per il cluster HDInsight.Attach : To attach an HDInsight cluster as a compute target, you must provide the resource ID, user name, and password for the HDInsight cluster. L'ID risorsa del cluster HDInsight può essere costruito usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome del cluster HDInsight usando il formato di stringa seguente:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   In alternativa, è possibile collegare il cluster HDInsight all'area di lavoro [usando Azure Machine Learning Studio.](#portal-reuse)

1. **Configura:** crea una configurazione di esecuzione per la destinazione di calcolo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Dopo aver collegato il calcolo e configurato l'esecuzione, il passaggio successivo consiste [nell'inviare l'esecuzione del training](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch viene usato per eseguire applicazioni HPC (parallele e HPC) su larga scala in modo efficiente nel cloud. AzureBatchStep può essere usato in una pipeline di Azure Machine Learning per inviare processi a un pool di macchine Azure Batch.AzureBatchStep can be used in an Azure Machine Learning Pipeline to submit jobs to an Azure Batch pool of machines.

Per collegare Azure Batch come destinazione di calcolo, è necessario usare Azure Machine Learning SDK e fornire le informazioni seguenti:To attach Azure Batch as a compute target, you must use the Azure Machine Learning SDK and provide the following information:

-    **Nome di calcolo di Azure Batch:** nome descrittivo da usare per il calcolo all'interno dell'area di lavoroAzure Batch compute name : A friendly name to be used for the compute within the workspace
-    **Nome account batch**di Azure: nome dell'account Batch di AzureAzure Batch account name : The name of the Azure Batch account
-    **Gruppo di risorse:** gruppo di risorse che contiene l'account Batch di Azure.Resource Group: The resource group that contains the Azure Batch account.

Il codice seguente illustra come collegare Azure Batch come destinazione di calcolo:The following code demonstrates how to attach Azure Batch as a compute target:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Configurazione in Azure Machine Learning studio

È possibile accedere alle destinazioni di calcolo associate all'area di lavoro in Azure Machine Learning Studio.You can access the compute targets that are associated with your workspace in the Azure Machine Learning studio.  È possibile utilizzare lo studio per:

* [Visualizzare le destinazioni di calcolo](#portal-view) collegate all'area di lavoro
* [Creare una destinazione di calcolo](#portal-create) nell'area di lavoro
* [Collegare una destinazione di calcolo](#portal-reuse) creata esternamente all'area di lavoro


Dopo la creazione e il collegamento di una destinazione all'area di lavoro, questa verrà usata nella configurazione di esecuzione con un oggetto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Vista destinazioni di calcolo


Per visualizzare le destinazioni di calcolo dell'area di lavoro, usare la procedura seguente:

1. Passare ad [Azure Machine Learning Studio](https://ml.azure.com).
 
1. In __Applicazioni__ selezionare __Ambiente di calcolo__.

    [![Visualizzare la scheda Calcolo](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per creare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo.

    ![Aggiungere una destinazione di calcolo](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Immettere un nome per la destinazione di calcolo. 

1. Selezionare l'**ambiente di calcolo di Machine Learning** come tipo di ambiente di calcolo da usare per il __training__. 

    >[!NOTE]
    >Azure Machine Learning Compute è l'unica risorsa di calcolo gestito che è possibile creare in Azure Machine Learning Studio.Azure Machine Learning Compute is the only managed-compute resource you can create in Azure Machine Learning studio.  Tutte le altre risorse di calcolo si possono collegare dopo averle create.

1. Compilare il modulo. Specificare i valori per le proprietà necessarie, in particolare la **famiglia di macchine virtuali**e il **numero massimo di nodi** da usare per creare rapidamente l'ambiente di calcolo.  

1. Selezionare __Create__ (Crea).


1. Visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco:

    ![Selezionare una destinazione di calcolo per visualizzare lo stato dell'operazione di creazione](./media/how-to-set-up-training-targets/View_list.png)

1. Verranno mostrati i dettagli della destinazione di calcolo: 

    ![Visualizzare i dettagli della destinazione di calcolo](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Collegare destinazioni di calcolo

Per usare le destinazioni di calcolo create all'esterno dell'area di lavoro di Azure Machine Learning, è necessario collegarle. Il collegamento di una destinazione di calcolo rende quest'ultima disponibile nell'area di lavoro.

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi i passaggi seguenti per collegare una destinazione di calcolo: 

1. Selezionare il segno più (+) per aggiungere una destinazione di calcolo. 
1. Immettere un nome per la destinazione di calcolo. 
1. Selezionare il tipo di calcolo da allegare per __la formazione__:

    > [!IMPORTANT]
    > Non tutti i tipi di calcolo possono essere collegati da Azure Machine Learning Studio.Not all compute types can be attached from Azure Machine Learning studio. I tipi di calcolo che possono essere allegati per il training sono:
    >
    > * Macchina virtuale remota
    > * Azure Databricks (per l'uso nelle pipeline di machine learning)
    > * Azure Data Lake Analytics (per l'uso nelle pipeline di machine learning)
    > * HDInsight di Azure

1. Compilare il modulo e fornire i valori per le proprietà necessarie.

    > [!NOTE]
    > Microsoft consiglia di usare le chiavi SSH, che sono più sicure rispetto alle password. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. Le chiavi SSH si basano sulle firme di crittografia. Per informazioni sulla creazione di chiavi SSH per l'uso con Macchine virtuali di Azure, vedere i documenti seguenti:
    >
    > * [Create and use SSH keys on Linux or macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Creare e usare chiavi SSH in Linux o macOS)
    > * [Create and use SSH keys on Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Creare e usare chiavi SSH in Windows)

1. Selezionare __Allega__. 
1. Visualizzare lo stato dell'operazione di collegamento selezionando la destinazione di calcolo dall'elenco.

## <a name="set-up-with-cli"></a>Configurazione con CLI

È possibile accedere alle destinazioni di calcolo associate all'area di lavoro usando [l'estensione CLI](reference-azure-machine-learning-cli.md) per Azure Machine Learning.You can access the compute targets that are associated with your workspace using the CLI extension for Azure Machine Learning.  È possibile usare l'interfaccia della riga di comando per:

* Creare una destinazione di calcolo gestita
* Aggiornare una destinazione di calcolo gestita
* Collegare una destinazione di calcolo non gestita

Per altre informazioni consultare [Gestione delle risorse](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurazione con codice VS

È possibile accedere, creare e gestire le destinazioni di calcolo associate all'area di lavoro usando [l'estensione Codice VS](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) per Azure Machine Learning.You can access, create, and manage the compute targets that are associated with your workspace using the VS Code extension for Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Inviare il training con Azure Machine Learning SDKSubmit training run using Azure Machine Learning SDK

Dopo aver creato una configurazione di esecuzione, questa si usa per l'esecuzione dell'esperimento.  Il criterio di codice per inviare l'esecuzione di un training è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un esperimento da eseguire
1. Inviare l'esecuzione.
1. Attendere il completamento dell'esecuzione.

> [!IMPORTANT]
> Quando si invia l'esecuzione del training, viene creato uno snapshot della directory che contiene gli script di training alla destinazione di calcolo. Viene inoltre archiviato come parte dell'esperimento nell'area di lavoro. Se si modificano i file e si invia di nuovo l'esecuzione, verranno caricati solo i file modificati.
>
> Per impedire che i file vengano inclusi nello `.amlignore` snapshot, creare un file con estensione [gitignore](https://git-scm.com/docs/gitignore) o nella directory e aggiungervi i file. Il `.amlignore` file utilizza la stessa sintassi e gli stessi modelli del file [.gitignore.](https://git-scm.com/docs/gitignore) Se entrambi i `.amlignore` file esistono, il file ha la precedenza.
> 
> Per ulteriori informazioni, vedere [Istantanee](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento nell'area di lavoro.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Inviare l'esperimento

Inviare l'esperimento con un oggetto `ScriptRunConfig`.  Questo oggetto include:

* **source_directory**: la directory di origine che contiene lo script di training
* **script**: Identificare lo script di formazione
* **run_config**: La configurazione di esecuzione, che a sua volta definisce dove si verificherà il training.

Ad esempio, per usare la configurazione [destinazione locale](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Eseguire lo stesso esperimento in una destinazione di calcolo diversa tramite una configurazione di esecuzione differente, come la [destinazione amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Per impostazione predefinita, in questo esempio viene utilizzato un solo nodo della destinazione di calcolo per il training. Per utilizzare più di un `node_count` nodo, impostare la configurazione di esecuzione sul numero di nodi desiderato. Ad esempio, il codice seguente imposta il numero di nodi utilizzati per il training su quattro:For example, the following code sets the number of nodes used for training to four:
>
> ```python
> src.run_config.node_count = 4
> ```

In alternativa, è possibile:

* Inviare l'esperimento con un oggetto `Estimator` come illustrato in [Eseguire il training di modelli di Machine Learning con oggetti Estimator](how-to-train-ml-models.md).
* Inviare un'esecuzione di HyperDrive per [l'ottimizzazione dei parametri iperparametri](how-to-tune-hyperparameters.md).
* Inviare un esperimento tramite [l'estensione del codice VS](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Per altre informazioni, vedere la documentazione di ScriptRunConfig e [RunConfiguration.For](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) more information, see the [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) and RunConfiguration documentation.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Creare la configurazione di esecuzione e inviare l'esecuzione usando l'interfaccia della riga di comando di Azure Machine LearningCreate run configuration and submit run run using Azure Machine Learning CLI

È possibile usare [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure e [l'estensione dell'interfaccia della riga](reference-azure-machine-learning-cli.md) di comando di Machine Learning per creare configurazioni di esecuzione e inviare esecuzioni su destinazioni di calcolo diverse. Gli esempi seguenti presuppongono che si disponga di un'area `az login` di lavoro di Azure Machine Learning esistente e che sia stato effettuato l'accesso ad Azure usando il comando dell'interfaccia della riga di comando. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

### <a name="create-run-configuration"></a>Creare la configurazione di esecuzioneCreate run configuration

Il modo più semplice per creare la configurazione di esecuzione consiste nell'esplorare la cartella che contiene gli script Python di Machine Learning e usare il comando CLI

```azurecli
az ml folder attach
```

Questo comando crea `.azureml` una sottocartella che contiene i file di configurazione di esecuzione dei modelli per destinazioni di calcolo diverse. È possibile copiare e modificare questi file per personalizzare la configurazione, ad esempio per aggiungere pacchetti Python o modificare le impostazioni di Docker.  

### <a name="structure-of-run-configuration-file"></a>Struttura del file di configurazione di esecuzione

Il file di configurazione dell'esecuzione è formattato YAML, con le seguenti sezioni
 * Lo script da eseguire e i relativi argomenti
 * Calcolare il nome della destinazione, "locale" o il nome di un calcolo nell'area di lavoro.
 * Parametri per l'esecuzione dell'esecuzione: framework, communicator per esecuzioni distribuite, durata massima e numero di nodi di calcolo.
 * Sezione Ambiente. Per informazioni dettagliate sui campi di questa sezione, vedere Creare e gestire ambienti per la formazione e la [distribuzione.](how-to-use-environments.md)
   * Per specificare i pacchetti Python da installare per l'esecuzione, creare il file di [ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e impostare il campo __condaDependenciesFile.__
 * Eseguire i dettagli della cronologia per specificare la cartella del file di log e per abilitare o disabilitare la raccolta di output e gli snapshot della cronologia di esecuzione.
 * Dettagli di configurazione specifici per il framework selezionato.
 * Dettagli di riferimento e archivio dati.
 * Dettagli di configurazione specifici per Machine Learning Compute per la creazione di un nuovo cluster.

Vedere il [file JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) di esempio per uno schema runconfig completo.

### <a name="create-an-experiment"></a>Creare un esperimento

Innanzitutto, crea un esperimento per le tue esecuzioni

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Esecuzione di script

Per inviare un'esecuzione di script, eseguire un comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Esecuzione di HyperDrive

È possibile usare HyperDrive con l'interfaccia della riga di comando di Azure per eseguire esecuzioni di ottimizzazione dei parametri. Creare innanzitutto un file di configurazione HyperDrive nel formato seguente. Per informazioni dettagliate sui parametri di ottimizzazione degli iperparametri, vedere [l'articolo Sugli iperparametri per il modello.](how-to-tune-hyperparameters.md)

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Aggiungere questo file insieme ai file di configurazione di esecuzione. Quindi inviare una corsa HyperDrive utilizzando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Si noti la sezione *degli argomenti* in runconfig e lo spazio *dei parametri* nella configurazione di HyperDrive. Contengono gli argomenti della riga di comando da passare allo script di training. Il valore in runconfig rimane invariato per ogni iterazione, mentre l'intervallo in HyperDrive config viene iterato. Non specificare lo stesso argomento in entrambi i file.

Per ulteriori informazioni ```az ml``` su questi comandi dell'interfaccia della riga di comando e sul set completo di argomenti, vedere [la documentazione](reference-azure-machine-learning-cli.md)di riferimento .

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Monitoraggio e integrazione Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione Git per Azure Machine Learning.For](concept-train-model-git-integration.md)more information, see Git integration for Azure Machine Learning .

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: eseguire il training](tutorial-train-models-with-aml.md) di un modello usa una destinazione di calcolo gestita per eseguire il training di un modello.
* Scopri come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Consultare le informazioni sull'SDK di [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usare Azure Machine Learning con le reti virtuali di AzureUse Azure Machine Learning with Azure Virtual Networks](how-to-enable-virtual-network.md)
