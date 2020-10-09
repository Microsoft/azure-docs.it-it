---
title: Utilizzo dell'API Cassandra di Azure Cosmos DB da Spark
description: Questo articolo è la pagina principale per l'integrazione dell'API Cassandra di Cosmos DB da Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: ffe9167bb155826eea3a1e7994469d378e5925fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260492"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Connettersi all'API Cassandra di Azure Cosmos DB da Spark

Questo articolo è uno di una serie di articoli sull'integrazione dell'API Cassandra di Azure Cosmos DB da Spark. Gli articoli trattano la connettività, le operazioni DDL (Data Definition Language), le operazioni DML (Data Manipulation Language) e l'integrazione avanzata dell'API Cassandra di Azure Cosmos DB da Spark. 

## <a name="prerequisites"></a>Prerequisiti
* [Effettuare il provisioning di un account di API Cassandra Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Effettuare il provisioning dell'ambiente Spark scelto [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Altre].

## <a name="dependencies-for-connectivity"></a>Dipendenze per la connettività
* **Connettore Spark per Cassandra:** il connettore Spark viene usato per connettersi all'API Cassandra di Azure Cosmos DB.  Identificare e usare la versione del connettore nel [repository Maven]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) compatibile con le versioni di Spark e Scala dell'ambiente Spark.

* **Libreria helper di Azure Cosmos DB per l'API Cassandra:** oltre al connettore Spark, è necessaria un'altra libreria chiamata [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) da Azure Cosmos DB. Questa libreria contiene una classe factory di connessione e una classe per i criteri di ripetizione, entrambe personalizzate.

  I criteri di ripetizione in Azure Cosmos DB sono configurati per gestire le eccezioni con codice stato HTTP 429 ("La frequenza delle richieste è troppo elevata"). L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra ed è possibile riprovare con backoff. Dato che Azure Cosmos DB usa il modello di velocità effettiva con provisioning, quando il traffico in ingresso/uscita aumenta si verificano eccezioni di limitazione della frequenza delle richieste. Il criterio di ripetizione dei tentativi protegge i processi Spark rispetto ai picchi di dati che superano momentaneamente la velocità effettiva allocata per il contenitore.

  > [!NOTE] 
  > I criteri di ripetizione possono proteggere i processi di Spark solo da picchi momentanei. Se non è stato configurato un numero sufficiente di UR per eseguire il carico di lavoro, i criteri di ripetizione non sono applicabili e la classe dei criteri di ripetizione genera nuovamente l'eccezione.

* **Dettagli della connessione all'account Azure Cosmos DB:** nome dell'account dell'API Cassandra di Azure, endpoint dell'account e chiave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametri di configurazione della velocità effettiva del connettore Spark

La tabella seguente elenca i parametri di configurazione della velocità effettiva specifici dell'API Cassandra di Azure Cosmos DB forniti dal connettore. Per un elenco dettagliato di tutti i parametri di configurazione, vedere la pagina di [riferimento per la configurazione](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) del repository GitHub del connettore Cassandra Spark.

| **Nome proprietà** | **Valore predefinito** | **Descrizione** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Numero di righe per ogni singolo batch. Impostare questo parametro su 1. Questo parametro viene usato per ottenere una velocità effettiva maggiore per carichi di lavoro pesanti. |
| spark.cassandra.connection.connections_per_executor_max  | nessuno | Numero massimo di connessioni per ogni nodo per ogni executor. 10*n equivale a 10 connessioni per nodo in un cluster Cassandra con n-nodi. Pertanto, se sono necessarie 5 connessioni per nodo per ogni executor per un cluster Cassandra a 5 nodi, è necessario impostare questa configurazione su 25. Modificare questo valore in base al grado di parallelismo o al numero di executor per cui sono configurati i processi Spark.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definisce il numero di scritture parallele che possono verificarsi per ogni executor. Dato che "batch.size.rows" è impostato su 1, assicurarsi di aumentare questo valore di conseguenza. Modificare questo valore in base al grado di parallelismo o alla velocità effettiva che si vuole ottenere per il carico di lavoro. |
| spark.cassandra.concurrent.reads |  512 | Definisce il numero di letture parallele che possono verificarsi per ogni executor. Modificare questo valore in base al grado di parallelismo o alla velocità effettiva che si vuole ottenere per il carico di lavoro.  |
| spark.cassandra.output.throughput_mb_per_sec  | nessuno | Definisce la velocità effettiva di scrittura totale per ogni executor. Questo parametro può essere usato come limite superiore per la velocità effettiva del processo Spark e basarlo sulla velocità effettiva con provisioning del contenitore Cosmos.   |
| spark.cassandra.input.reads_per_sec| nessuno   | Definisce la velocità effettiva di lettura totale per ogni executor. Questo parametro può essere usato come limite superiore per la velocità effettiva del processo Spark e basarlo sulla velocità effettiva con provisioning del contenitore Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Definisce il numero di batch per ogni singola attività Spark che possono essere archiviati in memoria prima dell'invio all'API Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definisce il periodo di tempo fino a quando sono disponibili connessioni inutilizzate. | 

Regolare la velocità effettiva e il grado di parallelismo di questi parametri in base al carico di lavoro previsto per i processi Spark e alla velocità effettiva di cui è stato effettuato il provisioning per l'account Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Connessione all'API Cassandra di Azure Cosmos DB da Spark

### <a name="cqlsh"></a>cqlsh
I comandi seguenti illustrano in dettaglio come connettersi all'API Cassandra di Azure Cosmos DB da cqlsh.  Queste informazioni sono utili per la convalida durante l'esecuzione degli esempi in Spark.<br>
**Da Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
L'articolo seguente descrive il provisioning di cluster Azure Databricks, la configurazione del cluster per la connessione all'API Cassandra di Azure Cosmos DB e vari notebook di esempio che illustrano le operazioni DDL, le operazioni DML e altro.<BR>
[Accedere all'API Cassandra di Azure Cosmos DB da Azure Databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. HDInsight-Spark di Azure
L'articolo seguente descrive il servizio HDinsight-Spark, il provisioning, la configurazione del cluster per la connessione all'API Cassandra di Azure Cosmos DB e vari notebook di esempio che illustrano le operazioni DDL, le operazioni DML e altro.<BR>
[Accedere all'API Cassandra di Azure Cosmos DB da Spark in YARN con HDInsight](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. ambiente Spark in generale
Mentre le sezioni precedenti sono specifiche per servizi PaaS basati su Spark di Azure, questa sezione è dedicata agli ambienti Spark generali.  Di seguito sono indicate in dettaglio le dipendenze del connettore, le importazioni e la configurazione della sessione Spark. La sezione "Passaggi successivi" include collegamenti a esempi di codice per operazioni DDL, operazioni DML e altro.  

#### <a name="connector-dependencies"></a>Dipendenze del connettore:

1. Aggiungere le coordinate di maven per ottenere il [connettore Cassandra per Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Aggiungere le coordinate di maven per la [libreria helper di Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) per l'API Cassandra

#### <a name="imports"></a>Importazioni:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configurazione della sessione Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano l'integrazione di Spark con l'API Cassandra di Azure Cosmos DB. 
 
* [Operazioni DDL](cassandra-spark-ddl-ops.md)
* [Operazioni di creazione e inserimento](cassandra-spark-create-ops.md)
* [Operazioni di lettura](cassandra-spark-read-ops.md)
* [Operazioni di upsert](cassandra-spark-upsert-ops.md)
* [Operazioni Delete](cassandra-spark-delete-ops.md)
* [Operazioni di aggregazione](cassandra-spark-aggregation-ops.md)
* [Operazioni di copia di tabelle](cassandra-spark-table-copy-ops.md)
