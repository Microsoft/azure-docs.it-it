---
title: "Avvio rapido: Introduzione all'analisi con Spark"
description: Questa esercitazione illustra come analizzare i dati con Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363853"
---
# <a name="analyze-with-apache-spark"></a>Analizzare dati con Apache Spark

Questa esercitazione illustra i passaggi di base per caricare e analizzare i dati con Apache Spark per Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Creare un pool di Apache Spark serverless

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool di Apache Spark**.
1. Selezionare **Nuovo** 
1. Per **Nome del pool di Apache Spark**, immettere **Spark1**.
1. Per **Dimensioni del nodo**, immettere **Piccole**.
1. Per **Numero di nodi**, impostare il valore minimo su 3 e il valore massimo su 3
1. Selezionare **Rivedi e crea** > **Crea**. Il pool di Apache Spark è pronto in pochi secondi.

## <a name="understanding-serverless-apache-spark-pools"></a>Informazioni sui pool di Apache Spark serverless

Un pool di Spark serverless è un modo per indicare come un utente vuole usare Spark. Quando si inizia a usare un pool, viene creata una sessione Spark, se necessario. Il pool controlla il numero di risorse Spark che verranno usate da tale sessione e la durata della sessione prima della sospensione automatica. Si paga per le risorse Spark usate durante la sessione e non per il pool stesso. In questo modo, un pool di Spark consente di usare Spark senza doversi preoccupare della gestione dei cluster. È simile al funzionamento di un pool SQL serverless.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Analizzare i dati dei taxi di New York con un pool di Spark

1. In Synapse Studio passare all'hub **Sviluppo**
2. Creare un nuovo notebook
3. Creare una nuova cella di codice e incollare il codice seguente in tale cella.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. Nel notebook scegliere **il** pool Spark serverless Spark **Spark1** creato in precedenza nel menu Collega a.
1. Selezionare **Esegui** nella cella. Synapse avvia una nuova sessione spark per eseguire questa cella, se necessario. Se è necessaria una nuova sessione spark, inizialmente saranno necessari circa due secondi per la creazione. 
1. Per vedere semplicemente lo schema del dataframe, eseguire una cella con il codice seguente:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili tramite il frame di dati **denominato df**. Caricarli in un database Spark denominato **nyctaxi**.

1. Aggiungere una nuova cella di codice al notebook e quindi immettere il codice seguente:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Creare una nuova cella di codice e immettere il codice seguente. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire la cella per visualizzare i dati di NYC Taxi caricati nel database **Nyctaxi** Spark.
1. Creare una nuova cella di codice e immettere il codice seguente. Questi dati verranno a analisi e i risultati verranno salvati in una tabella denominata **nyctaxi.passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nei risultati della cella selezionare **Grafico** per visualizzare i dati.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool SQL dedicato](get-started-analyze-sql-pool.md)
