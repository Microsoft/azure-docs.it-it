---
title: Apache Sqoop con Apache Hadoop - Azure HDInsight
description: Informazioni su come usare Apache Sqoop per importare ed esportare tra Apache Hadoop in HDInsight e nel database SQL di Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943141"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Usare Apache Sqoop per importare ed esportare dati tra Apache Hadoop in HDInsight e nel database SQL di Azure

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop per importare ed esportare tra un cluster Apache Hadoop in Azure HDInsight e il database SQL di Azure o Microsoft SQL Server. La procedura descritta in questo documento usa il comando `sqoop` direttamente dal nodo head del cluster Hadoop. Usare SSH per connettersi al nodo head ed eseguire i comandi di questo documento. Questo articolo riprende l'articolo [Usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Prerequisiti

* Il completamento della [configurazione dell'ambiente di test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database), come indicato nell'articolo [Usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiarità con Sqoop. Per altre informazioni, vedere il [Manuale dell'utente di Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configurazione

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Per semplicità d'uso, impostare le variabili. Sostituire `PASSWORD` , `MYSQLSERVER` e `MYDATABASE` con i valori rilevanti, quindi immettere i comandi seguenti:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Esportazione con Sqoop

Da hive a SQL.

1. Per verificare che Sqoop possa visualizzare il database, immettere il comando seguente nella connessione SSH aperta. Questo comando restituisce un elenco di database.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Immettere il comando seguente per visualizzare un elenco di tabelle per il database specificato:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Per esportare i dati dalla `hivesampletable` tabella hive alla `mobiledata` tabella nel database, immettere il comando seguente nella connessione ssh aperta:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Per verificare che i dati siano stati esportati, usare le query seguenti dalla connessione SSH per visualizzare i dati esportati:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importazione con Sqoop

Da SQL ad archiviazione di Azure.

1. Immettere il comando seguente nella connessione SSH aperta per importare i dati dalla `mobiledata` tabella in SQL alla `wasbs:///tutorials/usesqoop/importeddata` directory in HDInsight. I campi nei dati sono separati da un carattere di tabulazione e le righe terminano con un carattere di nuova riga.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. In alternativa, è possibile specificare anche una tabella hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Al termine dell'importazione, immettere il comando seguente nella connessione SSH aperta per elencare i dati nella nuova directory:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Usare [l'](./apache-hadoop-use-hive-beeline.md) oggetto per verificare che la tabella sia stata creata in hive.

    1. Connessione

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Eseguire ogni query al di sotto di una alla volta ed esaminare l'output:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Uscire da con `!exit` .

## <a name="limitations"></a>Limitazioni

* Esportazione in blocco: con HDInsight basato su Linux, il connettore Sqoop usato per esportare i dati in SQL non supporta gli inserimenti bulk.

* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="important-considerations"></a>Considerazioni importanti

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure.

    Per avere un esempio, vedere il documento [Connettere HDInsight alla rete locale](./../connect-on-premises-network.md).

    Per altre informazioni sull'uso di HDInsight con le reti virtuali di Azure, vedere il documento [Estendere HDInsight con la rete virtuale di Azure](../hdinsight-plan-virtual-network-deployment.md). Per altre informazioni su Rete virtuale di Azure, vedere il documenti di [panoramica sulle reti virtuali](../../virtual-network/virtual-networks-overview.md).

* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, consultare il documento [Scegliere una modalità di autenticazione](/sql/relational-databases/security/choose-an-authentication-mode).

* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere il documento [Come risolvere i problemi di connessione al motore di database di SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache Oozie con HDInsight](../hdinsight-use-oozie-linux-mac.md): Usare l'azione Sqoop in un flusso di lavoro Oozie.
* [Analizzare i dati sui ritardi dei voli usando HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): usare query interattive per analizzare i dati sui ritardi dei voli e quindi usare Sqoop per esportare i dati in un database in Azure.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.