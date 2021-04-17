---
title: Eseguire la migrazione di dati relazionali uno-a-pochi nell Azure Cosmos DB API SQL
description: Informazioni su come gestire la migrazione di dati complessi per relazioni uno-a-pochi nell'API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589619"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Eseguire la migrazione di dati relazionali uno-a-pochi nell'account Azure Cosmos DB dell'API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Per eseguire la migrazione da un database relazionale Azure Cosmos DB'API SQL, può essere necessario apportare modifiche al modello di dati per l'ottimizzazione.

Una trasformazione comune è la denormalizzazione dei dati incorporando elementi secondari correlati all'interno di un documento JSON. Ecco alcune opzioni per questa operazione usando Azure Data Factory o Azure Databricks. Per indicazioni generali sulla modellazione dei dati per Cosmos DB, vedere [Modellazione dei dati in Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Scenario di esempio

Si supponga di avere le due tabelle seguenti nel database SQL, Orders e OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Screenshot che mostra le tabelle Orders e OrderDetails nel database SQL." border="false" :::

Si vuole combinare questa relazione uno-a-pochi in un unico documento JSON durante la migrazione. A tale scopo, è possibile creare una query T-SQL usando "FOR JSON" come indicato di seguito:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

I risultati di questa query sono i seguenti: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Dettagli dell'ordine" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Idealmente, si vuole usare una singola attività di copia Azure Data Factory (ADF) per eseguire query sui dati SQL come origine e scrivere l'output direttamente Azure Cosmos DB sink come oggetti JSON adeguati. Attualmente non è possibile eseguire la trasformazione JSON necessaria in un'unica attività di copia. Se si prova a copiare i risultati della query precedente in un contenitore dell'API SQL Azure Cosmos DB, il campo OrderDetails verrà visualizzato come proprietà stringa del documento, anziché come matrice JSON prevista.

È possibile aggirare questa limitazione corrente in uno dei modi seguenti:

* **Usare Azure Data Factory con due attività di copia:** 
  1. Ottenere dati in formato JSON da SQL in un file di testo in un percorso di archiviazione BLOB intermedio e 
  2. Caricare i dati dal file di testo JSON in un contenitore in Azure Cosmos DB.

* **Usare Azure Databricks per leggere da SQL e scrivere** in Azure Cosmos DB. In questo caso verranno presenti due opzioni.


Questi approcci vengono ora descritti in modo più dettagliato:

## <a name="azure-data-factory"></a>Azure Data Factory

Anche se non è possibile incorporare OrderDetails come matrice JSON nel documento di destinazione Cosmos DB, è possibile risolvere il problema usando due attività di copia separate.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Attività di copia #1: SqlJsonToBlobText

Per i dati di origine, viene utilizzata una query SQL per ottenere il set di risultati come una singola colonna con un oggetto JSON (che rappresenta l'ordine) per riga usando le funzionalità SQL Server OPENJSON e FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Copia di AdF":::


Per il sink dell'attività di copia SqlJsonToBlobText, si sceglie "Testo delimitato" e lo si punta a una cartella specifica in Archiviazione BLOB di Azure con un nome file univoco generato dinamicamente (ad esempio, ' @concat (pipeline(). RunId,'.json').
Poiché il file di testo non è effettivamente "delimitato" e non si vuole analizzarlo in colonne separate usando virgole e si vogliono mantenere le virgolette doppie ("), si imposta "Delimitatore di colonna" su una tabulazione ("\t") - o su un altro carattere non presente nei dati - e "Virgolette" su "Nessun carattere virgolette".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Screenshot che evidenzia le impostazioni Delimitatore di colonna e Carattere virgoletta.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Attività di copia #2: BlobJsonToCosmos

Successivamente, si modifica la pipeline di AdF aggiungendo la seconda attività di copia che cerca nel Archiviazione BLOB di Azure il file di testo creato dalla prima attività. Lo elabora come origine "JSON" da inserire Cosmos DB sink come un documento per ogni riga JSON trovata nel file di testo.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Screenshot che evidenzia il file di origine JSON e i campi Percorso file.":::

Facoltativamente, si aggiunge anche un'attività "Delete" alla pipeline in modo che elimini tutti i file precedenti rimanenti nella cartella /Orders/ prima di ogni esecuzione. La pipeline di AdF ha ora un aspetto simile al seguente:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Screenshot che evidenzia l'attività Elimina.":::

Dopo aver attivato la pipeline precedente, viene visualizzato un file creato nel percorso Archiviazione BLOB di Azure intermedio contenente un oggetto JSON per riga:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Screenshot che mostra il file creato che contiene gli oggetti JSON.":::

Vengono anche visualizzati documenti Orders con OrderDetails correttamente incorporati inseriti nella raccolta Cosmos DB raccolta:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Screenshot che mostra i dettagli dell'ordine come parte del documento Cosmos DB":::


## <a name="azure-databricks"></a>Azure Databricks

È anche possibile usare Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) per copiare i dati dall'origine del database SQL alla destinazione Azure Cosmos DB senza creare i file di testo/JSON intermedi in Archiviazione BLOB di Azure. 

> [!NOTE]
> Per maggiore chiarezza e semplicità, i frammenti di codice seguenti includono password di database fittizie inline in modo esplicito, ma è consigliabile usare sempre Azure Databricks segreti.
>

In primo luogo, si creano e collegano le librerie del connettore [SQL](/connectors/sql/) e [Azure Cosmos DB connettore](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) al cluster Azure Databricks cluster. Riavviare il cluster per assicurarsi che le librerie siano caricate.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Screenshot che mostra dove creare e collegare il connettore SQL e le librerie del connettore Azure Cosmos DB necessarie al cluster Azure Databricks cluster.":::

Vengono ora forniti due esempi, per Scala e Python. 

### <a name="scala"></a>Scala
Qui si ottengono i risultati della query SQL con l'output "FOR JSON" in un dataframe:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Screenshot che mostra l'output della query SQL in un dataframe.":::

Successivamente, ci si connette al database Cosmos DB e alla raccolta:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Infine, si definisce lo schema e si usa from_json per applicare il dataframe prima di salvarlo nella raccolta CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Screenshot che evidenzia la matrice appropriata per il salvataggio in una Cosmos DB raccolta.":::


### <a name="python"></a>Python

Come approccio alternativo, potrebbe essere necessario eseguire trasformazioni JSON in Spark (se il database di origine non supporta "FOR JSON" o un'operazione simile) oppure è possibile usare operazioni parallele per un set di dati di dimensioni molto grandi. Qui viene presentato un esempio di PySpark. Per iniziare, configurare le connessioni di database di origine e di destinazione nella prima cella:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Verrà quindi eseguita una query sul database di origine (in questo caso SQL Server) per i record di dettaglio ordine e ordine, inserendo i risultati nei dataframe Spark. Si creerà anche un elenco contenente tutti gli ID ordine e un pool di thread per le operazioni parallele:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Creare quindi una funzione per la scrittura di Orders nella raccolta di API SQL di destinazione. Questa funzione filtra tutti i dettagli dell'ordine per l'ID ordine specificato, li converte in una matrice JSON e inserisce la matrice in un documento JSON che verrà scritto nella raccolta di API SQL di destinazione per tale ordine:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Infine, si chiamerà l'oggetto precedente usando una funzione map nel pool di thread, per l'esecuzione in parallelo, passando l'elenco degli ID ordine creati in precedenza:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
In entrambi gli approcci, alla fine, si dovrebbe salvare correttamente orderDetails incorporato all'interno di ogni documento Order Cosmos DB raccolta:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [modellazione dei dati in Azure Cosmos DB](./modeling-data.md)
* Informazioni [su come modellare e partizionare i dati in Azure Cosmos DB](./how-to-model-partition-example.md)
