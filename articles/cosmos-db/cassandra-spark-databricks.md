---
title: Accedere all'API Cassandra di Azure Cosmos DB da Azure Databricks
description: Questo articolo illustra come usare l'API Cassandra di Azure Cosmos DB da Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 0a83dd143ae626108fdf8d2645b8cc368a3f3e05
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516567"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Accedere ai dati dell'API Cassandra di Azure Cosmos DB da Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Questo articolo illustra come usare Azure Cosmos DB API Cassandra da Spark in [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks).

## <a name="prerequisites"></a>Prerequisiti

* [Effettuare il provisioning di un account API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Esaminare le informazioni di base della connessione all'API Cassandra di Azure Cosmos DB](cassandra-spark-generic.md)

* [Effettuare il provisioning di un cluster di Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)

* [Esaminare gli esempi di codice per l'uso dell'API Cassandra](cassandra-spark-generic.md#next-steps)

* [Usare cqlsh per la convalida, se si preferisce](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configurazione dell'istanza API Cassandra per il connettore Cassandra:**

  Il connettore per l'API Cassandra richiede che i dettagli della connessione a Cassandra vengano inizializzati nel contesto Spark. Quando si avvia un notebook Databricks, il contesto Spark è già inizializzato e non è consigliabile arrestarlo e reinizializzarlo. Una soluzione consiste nell'aggiungere la configurazione dell'istanza API Cassandra a un livello di cluster, nella configurazione del cluster Spark. Si tratta di un'attività una tantum per ogni cluster. Aggiungere il codice seguente alla configurazione Spark come coppia chiave-valore separata da spazio:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

* **Connettore Cassandra per Spark:** per eseguire l'integrazione dell'API Cassandra di Azure Cosmos DB con Spark, il connettore Cassandra deve essere collegato al cluster Azure Databricks. Per collegare il cluster:

  * Esaminare la versione di runtime di Databricks, la versione di Spark. Trovare quindi le [coordinate Maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) compatibili con il connettore Cassandra per Spark e collegarle al cluster. Vedere l'articolo [Upload a Maven package or Spark package](https://docs.databricks.com/user-guide/libraries.html) (Caricare un pacchetto Maven o un pacchetto Spark) per collegare la libreria del connettore al cluster. Ad esempio, la coordinata Maven per "Databricks Runtime versione 4.3", "Spark 2.3.1" e "Scala 2.11" è `spark-cassandra-connector_2.11-2.3.1`.

* **Libreria specifica dell'API Cassandra di Azure Cosmos DB:** è necessaria una factory di connessione personalizzata per configurare i criteri di ripetizione dal connettore Cassandra per Spark all'API Cassandra di Azure Cosmos DB. Aggiungere le `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`[coordinate Maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) per collegare la libreria al cluster.

## <a name="sample-notebooks"></a>Notebook di esempio

Un elenco di Azure Databricks [notebook di esempio](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/notebooks/scala) è disponibile nel repository GitHub da scaricare. Questi esempi includono come connettersi all'API Cassandra di Azure Cosmos DB da Spark ed eseguire diverse operazioni CRUD sui dati. È anche possibile [importare tutti i notebook](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/dbc) nell'area di lavoro del cluster Databricks ed eseguirli. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Accesso all'API Cassandra di Azure Cosmos DB da programmi Spark in Scala

I programmi Spark da eseguire come processi automatici in Azure Databricks vengono inviati al cluster usando [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) e pianificati per l'esecuzione tramite processi di Azure Databricks.

I collegamenti seguenti consentono di iniziare a compilare programmi Spark in Scala per l'interazione con l'API Cassandra di Azure Cosmos DB.
* [Come connettersi all'API Cassandra di Azure Cosmos DB da un programma Spark in Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Come eseguire un programma Spark in Scala come processo automatico in Azure Databricks](/azure/databricks/jobs)
* [Elenco completo di esempi di codice per l'uso dell'API Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Passaggi successivi

Introduzione alla [creazione di un account, un database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java.
