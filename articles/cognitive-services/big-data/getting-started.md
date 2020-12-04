---
title: Introduzione a Servizi cognitivi per Big Data
description: Configurare la pipeline MMLSpark con Servizi cognitivi in Azure Databricks ed eseguire un esempio.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460503"
---
# <a name="getting-started"></a>Introduzione

La configurazione dell'ambiente è il primo passaggio per la creazione di una pipeline per i dati. Quando l'ambiente è pronto, l'esecuzione di un esempio è un'operazione rapida e semplice.

In questo articolo verranno eseguite queste operazioni per iniziare:

1. [Creare una risorsa Servizi cognitivi](#create-a-cognitive-services-resource)
1. [Creare un cluster Apache Spark](#create-an-apache-spark-cluster)
1. [Provare un esempio](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

Per usare Servizi cognitivi per Big Data, è necessario prima creare un servizio cognitivo per il flusso di lavoro. Esistono due tipi principali di Servizi cognitivi: servizi cloud ospitati in Azure e servizi in contenitori gestiti dagli utenti. È consigliabile iniziare con distribuzioni di Servizi cognitivi basate sul cloud, il tipo più semplice.

### <a name="cloud-services"></a>Servizi cloud

Le distribuzioni di Servizi cognitivi basate sul cloud sono algoritmi intelligenti ospitati in Azure. Questi servizi sono pronti per l'uso senza training, ma è necessaria una connessione Internet. È possibile [creare un servizio cognitivo nel portale di Azure](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) o con l'[interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>Servizi in contenitori (facoltativo)

Se l'applicazione o il carico di lavoro usa set di dati di grandi dimensioni, richiede una rete privata o non può contattare il cloud, la comunicazione con i servizi cloud potrebbe risultare impossibile. In questa situazione, le distribuzioni di Servizi cognitivi in contenitori offrono i vantaggi seguenti:

* **Bassa connettività**: è possibile distribuire Servizi cognitivi in contenitori in qualsiasi ambiente informatico, sia sul cloud che non. Se l'applicazione non riesce a contattare il cloud, è consigliabile distribuire Servizi cognitivi in contenitori.

* **Bassa latenza**: poiché i servizi in contenitori non richiedono la comunicazione roundtrip verso/dal cloud, le risposte vengono restituite con latenze molto inferiori.

* **Privacy e sicurezza dei dati**: è possibile distribuire i servizi in contenitori in reti private, in modo che i dati sensibili non lascino la rete.

* **Scalabilità elevata**: i servizi in contenitori non prevedono "limiti di velocità" e vengono eseguiti in computer gestiti dall'utente. Quindi, è possibile raggiungere dimensioni illimitate di Servizi cognitivi per gestire carichi di lavoro molto più grandi.

Seguire [questa guida](../cognitive-services-container-support.md?tabs=luis) creare un servizio cognitivo in contenitori.

## <a name="create-an-apache-spark-cluster"></a>Creare un cluster Apache Spark

[Apache Spark&trade;](http://spark.apache.org/) è un framework di elaborazione distribuito progettato per l'elaborazione di Big Data. Gli utenti possono usare Apache Spark in Azure con servizi come Azure Databricks, Azure Synapse Analytics, HDInsight e i servizi Azure Kubernetes. Per usare Servizi cognitivi per Big Data, è necessario prima creare un cluster. Se è già disponibile un cluster Spark, provare a eseguire un esempio.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks è una piattaforma di analisi basata su Apache Spark configurabile con un clic, con flussi di lavoro semplificati e un'area di lavoro interattiva. Si usa spesso per la collaborazione tra scienziati dei dati, ingegneri e analisti aziendali. Per usare Servizi cognitivi per Big Data in Azure Databricks, seguire questa procedura:

1. [Creare un'area di lavoro di Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Creare un cluster Spark in Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Installare Servizi cognitivi per Big Data
    * Creare una nuova libreria nell'area di lavoro di Databricks  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Immettere le coordinate Maven seguenti:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` repository: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Installare la libreria in un cluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (facoltativo)

Facoltativamente, è possibile usare Synapse Analytics per creare un cluster Spark. Azure Synapse Analytics riunisce funzionalità aziendali di data warehousing e analisi di Big Data. Offre la libertà di eseguire query sui dati in base alle proprie esigenze, usando risorse serverless su richiesta o con provisioning, su scala. Per iniziare a usare Azure Synapse Analytics, seguire questa procedura:

1. [Creare un'area di lavoro di Synapse (anteprima)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Creare un nuovo pool di Apache Spark serverless (anteprima) con il portale di Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

In Azure Synapse Analytics l'opzione Servizi cognitivi per Big Data è installata per impostazione predefinita.

### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Nel caso di Servizi cognitivi in contenitori, un'opzione comune per la distribuzione di Spark insieme ai contenitori è il servizio Azure Kubernetes.

Per iniziare a usare il servizio Azure Kubernetes, seguire questa procedura:

1. [Distribuire un cluster del servizio Azure Kubernetes usando il portale di Azure](../../aks/kubernetes-walkthrough-portal.md)
1. [Installare il grafico Helm Apache Spark 2.4.0](https://hub.helm.sh/charts/microsoft/spark)
1. [Installare un contenitore di servizi cognitivi usando Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Provare un esempio

Dopo aver configurato il cluster Spark e l'ambiente, è possibile eseguire un breve esempio. Questa sezione illustra come usare Servizi cognitivi per Big Data in Azure Databricks.

Creare prima di tutto un notebook in Azure Databricks. Per altri provider di cluster Spark, usare i loro notebook o Spark Submit.

1. Creare un nuovo notebook di Databricks scegliendo **Nuovo notebook** dal menu **Azure Databricks**.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. Nella finestra di dialogo **Crea blocco appunti** immettere un nome, selezionare **Python** come linguaggio e selezionare il cluster Spark creato in precedenza.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Selezionare **Crea**.

1. Incollare questo frammento di codice nel nuovo notebook.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Ottenere la chiave di sottoscrizione dal menu **Chiavi ed endpoint** del dashboard di Analisi del testo nel portale di Azure.
1. Sostituire il segnaposto della chiave di sottoscrizione nel codice del notebook di Databricks con la propria chiave di sottoscrizione.
1. Selezionare il simbolo di riproduzione, o triangolo, nell'angolo in alto a destra della cella del notebook per eseguire l'esempio. Facoltativamente, selezionare **Esegui tutti** nella parte superiore del notebook per eseguire tutte le celle. Le risposte vengono visualizzate sotto la cella in una tabella.

### <a name="expected-results"></a>Risultati previsti

| text                                      |   Valutazione |
|:------------------------------------------|------------:|
| I am so happy today, its sunny!           |   0,978959  |
| I am frustrated by this rush hour traffic |   0,0237956 |
| The cognitive services on spark aint bad  |   0,888896  |

## <a name="next-steps"></a>Passaggi successivi

- [Esempi brevi di Python](samples-python.md)
- [Esempi brevi di Scala](samples-scala.md)
- [Recipe: Manutenzione predittiva](recipes/anomaly-detection.md)
- [Recipe: Esplorazione intelligente di opere d'arte](recipes/art-explorer.md)