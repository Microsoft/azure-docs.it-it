---
title: Eseguire la migrazione di un cluster HBase a una nuova versione - Azure HDInsight
description: Come eseguire la migrazione di cluster Apache HBase a una versione più recente in Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: d7fb7b6b409a4e24be97ee61fc7ba1f0c0a93202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82792633"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Eseguire la migrazione di cluster Apache HBase a una nuova versione

Questo articolo illustra i passaggi necessari per aggiornare il cluster Apache HBase in Azure HDInsight a una versione più recente.

Il tempo di inattività durante l'aggiornamento dovrebbe essere minimo, nell'ordine di minuti. Questo tempo di inattività è dovuto ai passaggi per scaricare tutti i dati in memoria e quindi al tempo necessario per configurare e riavviare i servizi nel nuovo cluster. I risultati variano a seconda del numero di nodi, della quantità di dati e di altre variabili.

## <a name="review-apache-hbase-compatibility"></a>Esaminare la compatibilità di Apache HBase

Prima di aggiornare Apache HBase, verificare che le versioni di HBase nel cluster di origine e in quello di destinazione siano compatibili. Per altre informazioni, vedere [Componenti e versioni di Apache Hadoop disponibili in HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> È consigliabile esaminare la matrice di compatibilità delle versioni nel [manuale di HBase](https://hbase.apache.org/book.html#upgrading). Eventuali incompatibilità di rilievo sono indicate nelle note sulla versione di HBase.

Di seguito è riportato un esempio di matrice di compatibilità delle versioni. Y indica la compatibilità e N indica una potenziale incompatibilità:

| Tipo di compatibilità | Versione principale| Versione secondaria | Patch |
| --- | --- | --- | --- |
| Compatibilità di rete client-server | N | S | S |
| Compatibilità server-server | N | S | S |
| Compatibilità tra formati di file | N | S | S |
| Compatibilità API client | N | S | S |
| Compatibilità binaria client | N | N | S |
| **Compatibilità API limitata sul lato server** |  |  |  |
| Stable | N | S | S |
| In evoluzione | N | N | S |
| Instabile | N | N | N |
| Compatibilità tra dipendenze | N | S | S |
| Compatibilità operativa | N | N | S |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Eseguire l'aggiornamento con la stessa versione principale di Apache HBase

Per aggiornare il cluster Apache HBase in Azure HDInsight, seguire questa procedura:

1. Assicurarsi che l'applicazione sia compatibile con la nuova versione, come illustrato nella matrice di compatibilità e nelle note sulla versione di HBase. Testare l'applicazione in un cluster che esegue la versione di destinazione di HDInsight e HBase.

1. [Configurare un nuovo cluster di destinazione HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) usando lo stesso account di archiviazione, ma con un nome di contenitore diverso:

    ![Usare lo stesso account di archiviazione, ma creare un contenitore diverso](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Scaricare il cluster HBase di origine, ovvero il cluster che si sta aggiornando. HBase scrive i dati in ingresso in un archivio in memoria, detto _memstore_. Quando il memstore raggiunge una determinata dimensione, HBase lo scarica su disco per l'archiviazione a lungo termine nell'account di archiviazione del cluster. Quando si elimina il cluster precedente, gli archivi memstore vengono riciclati, con la potenziale perdita di dati. Per scaricare manualmente l'archivio memstore per ogni tabella su disco, eseguire lo script seguente. La versione più recente di questo script è in [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) di Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Arrestare l'inserimento nel cluster HBase precedente.

1. Per assicurarsi che i dati recenti nell'archivio memstore vengano scaricati, eseguire di nuovo lo script precedente.

1. Accedere ad [Apache Ambari](https://ambari.apache.org/) nel cluster precedente ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) e arrestare i servizi di HBase. Quando viene richiesto di confermare che si desidera arrestare i servizi, selezionare la casella per attivare la modalità di manutenzione per HBase. Per altre informazioni sulla connessione ad Ambari e sul suo uso, vedere [Gestire i cluster HDInsight con l'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![In Ambari fare clic su servizi > HBase > arresta in azioni servizio](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Selezionare la casella di controllo per attivare la modalità manutenzione per HBase e quindi confermare](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Accedere a Ambari nel nuovo cluster HDInsight. Modificare l'impostazione HDFS `fs.defaultFS` in modo che punti al nome del contenitore usato dal cluster originale. Questa impostazione si trova in **HDFS > Configs > Advanced > Advanced core-site** (HDFS > Configurazioni > Avanzate > Impostazioni avanzate core-site).

    ![In Ambari fare clic su servizi > HDFS > config > avanzate](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![In Ambari modificare il nome del contenitore](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Se non si usano cluster HBase con la funzionalità di scrittura avanzata, ignorare questo passaggio. È necessario solo per i cluster HBase con funzionalità di scrittura avanzate.

   Modificare il `hbase.rootdir` percorso in modo che punti al contenitore del cluster originale.

    ![In Ambari modificare il nome del contenitore per HBase RootDir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Se si sta eseguendo l'aggiornamento di HDInsight 3,6 a 4,0, seguire questa procedura. in caso contrario, andare al passaggio 10:
    1. Riavviare tutti i servizi necessari in Ambari selezionando **Servizi**  >  **Riavvia tutti necessari**.
    1. Arrestare il servizio HBase.
    1. Connettersi tramite SSH al nodo Zookeeper ed eseguire il [zkCli](https://github.com/go-zkcli/zkcli) comando zkCli `rmr /hbase-unsecure` per rimuovere il elemento znode radice HBase da Zookeeper.
    1. Riavviare HBase.

1. Se si esegue l'aggiornamento a qualsiasi altra versione di HDInsight oltre a 4,0, attenersi alla procedura seguente:
    1. Salvare le modifiche.
    1. Riavviare tutti i servizi richiesti, come indicato da Ambari.

1. Impostare l'applicazione in modo che punti al nuovo cluster.

    > [!NOTE]  
    > Il DNS statico per l'applicazione cambia quando si esegue l'aggiornamento. Invece di impostare come hardcoded il DNS, è possibile configurare un record CNAME nelle impostazioni DNS del nome di dominio che punta al nome del cluster. Un'altra opzione consiste nell'usare un file di configurazione per l'applicazione che è possibile aggiornare senza eseguire di nuovo la distribuzione.

1. Avviare l'operazione di inserimento per verificare se tutto funziona come previsto.

1. Se il nuovo cluster è soddisfacente, eliminare il cluster originale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su [Apache HBase](https://hbase.apache.org/) e sull'aggiornamento dei cluster HDInsight, vedere gli articoli seguenti:

* [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md)
* [Gestire i cluster HDInsight con l'interfaccia utente Web di Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componenti e versioni di Apache Hadoop](../hdinsight-component-versioning.md)
* [Ottimizzare Apache HBase](../optimize-hbase-ambari.md)
