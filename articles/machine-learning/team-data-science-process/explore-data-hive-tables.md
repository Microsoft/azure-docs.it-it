---
title: Esplorare i dati nelle tabelle Hive con query Hive - Processo di data science per i team
description: Usare script Hive di esempio che sono usati per esplorare i dati nelle tabelle Hive in un cluster HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722170"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Esplorare i dati nelle tabelle Hive con  query Hive.

Questo articolo fornisce alcuni script Hive di esempio che sono usati per esplorare i dati nelle tabelle Hive in un cluster HDInsight Hadoop.

Questa attività è un passaggio del [processo di data science per i team](overview.md).

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md)
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight. Per istruzioni, vedere [Personalizzare i cluster Hadoop di Azure HDInsight per l'analisi avanzata.](customize-hadoop-cluster.md)
* Caricato i dati nelle tabelle Hive dei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata eseguita, seguire le istruzioni in [Creazione e caricamento di dati nelle tabelle Hive](move-hive-tables.md) per caricare prima di tutto i dati nelle tabelle Hive.
* Abilitato l'accesso remoto al cluster. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](customize-hadoop-cluster.md).
* Per istruzioni su come inviare query Hive, vedere [Come inviare query Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Script delle query Hive di esempio per l'esplorazione dei dati
1. Visualizzare il numero di osservazioni per partizione `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Visualizzare il numero di osservazioni per giorno `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Visualizzare i livelli in una colonna di categoria   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Visualizzare il numero di livelli in una combinazione di due colonne di categoria `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Visualizzare la distribuzione per colonne numeriche  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Estrarre i record dall'unione di due tabelle
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Script delle query aggiuntive per gli scenari relativi ai dati delle corse dei taxi
Nell'[archivio GitHub](https://chriswhong.com/open-data/foil_nyc_taxi/) sono disponibili anche alcuni esempi di query specifiche per gli scenari relativi ai [dati dei tragitti dei taxi di NYC](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tali query dispongono già di un determinato schema dei dati e possono essere inviate e usate immediatamente.

