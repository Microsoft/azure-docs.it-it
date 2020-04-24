---
title: Eseguire la migrazione di carichi di lavoro hive di Azure HDInsight 3,6 a HDInsight 4,0
description: Informazioni su come eseguire la migrazione di carichi di lavoro Apache Hive in HDInsight 3,6 in HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214653"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro hive di Azure HDInsight 3,6 a HDInsight 4,0

Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Hive e LLAP in HDInsight 3,6 a HDInsight 4,0. HDInsight 4,0 fornisce le più recenti funzionalità hive e LLAP, ad esempio viste materializzate e Caching risultati query. Quando si esegue la migrazione dei carichi di lavoro a HDInsight 4,0, è possibile usare molte funzionalità più recenti di hive 3 che non sono disponibili in HDInsight 3,6.

In questo articolo vengono trattati gli argomenti seguenti:

* Migrazione di metadati hive a HDInsight 4,0
* Migrazione sicura di tabelle ACID e non ACID
* Conservazione dei criteri di sicurezza hive tra le versioni di HDInsight
* Esecuzione di query e debug da HDInsight 3,6 a HDInsight 4,0

Un vantaggio di hive è la possibilità di esportare metadati in un database esterno (denominato Metastore hive). Il **Metastore hive** è responsabile dell'archiviazione delle statistiche della tabella, inclusi il percorso di archiviazione delle tabelle, i nomi delle colonne e le informazioni sull'indice della tabella. Lo schema del database del Metastore è diverso tra le versioni di hive. Il metodo consigliato per aggiornare la metastore Hive in modo sicuro consiste nel creare una copia e aggiornare la copia anziché l'ambiente di produzione corrente.

## <a name="copy-metastore"></a>Copia Metastore

HDInsight 3,6 e HDInsight 4,0 richiedono schemi di Metastore diversi e non possono condividere un singolo Metastore.

### <a name="external-metastore"></a>Metastore esterno

Creare una nuova copia del Metastore esterno. Se si usa un Metastore esterno, uno dei modi più semplici e sicuri per creare una copia del Metastore consiste nel [ripristinare il database](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) con un nome diverso usando la funzione di ripristino del database SQL.  Vedere [usare gli archivi di metadati esterni in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) per altre informazioni sul fissaggio di un Metastore esterno a un cluster HDInsight.

### <a name="internal-metastore"></a>Metastore interno

Se si usa il Metastore interno, è possibile usare le query per esportare le definizioni degli oggetti nel metastore Hive e importarle in un nuovo database.

Una volta completato lo script, si presuppone che il cluster precedente non verrà più utilizzato per l'accesso a una delle tabelle o dei database a cui si fa riferimento nello script.

> [!NOTE]
> Nel caso di tabelle ACID, viene creata una nuova copia dei dati sotto la tabella.

1. Connettersi al cluster HDInsight usando un [client Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Connettersi a HiveServer2 con il [client](../hadoop/apache-hadoop-use-hive-beeline.md) dell'utente corrente dalla sessione ssh aperta immettendo il comando seguente:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Questo comando genera un file denominato **AllTables. HQL**.

1. Chiudere la sessione SSH. Immettere quindi un comando SCP per scaricare **AllTables. HQL** in locale.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Caricare **AllTables. HQL** nel *nuovo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Quindi usare SSH per connettersi al *nuovo* cluster HDInsight. Eseguire il codice seguente dalla sessione SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Aggiorna Metastore

Una volta completata la **copia** del Metastore, eseguire uno script di aggiornamento dello schema nell' [azione script](../hdinsight-hadoop-customize-cluster-linux.md) nel cluster HDInsight 3,6 esistente per aggiornare il nuovo Metastore allo schema hive 3. Ciò consente di collegare il database come Metastore HDInsight 4,0.

Usare i valori nella tabella più avanti. Sostituire `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` con i valori appropriati per il Metastore hive **copiato** , separati da spazi. Non includere ". database.windows.net" quando si specifica il nome del server SQL.

|Proprietà | Valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome|Aggiornamento hive|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo/i di nodo|Head|
|Parametri|PASSWORD NOME UTENTE DATASERVERNAME DATABASENAME|

> [!Warning]  
> L'aggiornamento che converte lo schema di metadati HDInsight 3,6 nello schema HDInsight 4,0 non può essere annullato.

Per verificare l'aggiornamento, è possibile eseguire la query SQL seguente sul database:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Eseguire la migrazione di tabelle hive a HDInsight 4,0

Dopo aver completato il set di passaggi precedente per eseguire la migrazione del Metastore hive a HDInsight 4,0, le tabelle e i database registrati nel Metastore saranno visibili all'interno del cluster HDInsight 4,0 eseguendo `show tables` o `show databases` dall'interno del cluster. Per informazioni sull'esecuzione di query nei cluster HDInsight 4,0, vedere [esecuzione di query tra versioni di HDInsight](#query-execution-across-hdinsight-versions) .

Tuttavia, i dati effettivi delle tabelle non sono accessibili fino a quando il cluster non ha accesso agli account di archiviazione necessari. Per assicurarsi che il cluster HDInsight 4,0 possa accedere agli stessi dati del cluster HDInsight 3,6 precedente, completare i passaggi seguenti:

1. Determinare l'account di archiviazione di Azure della tabella o del database.

1. Se il cluster HDInsight 4,0 è già in esecuzione, allegare l'account di archiviazione di Azure al cluster tramite Ambari. Se non è ancora stato creato il cluster HDInsight 4,0, verificare che l'account di archiviazione di Azure sia specificato come account di archiviazione del cluster primario o secondario. Per altre informazioni sull'aggiunta di account di archiviazione ai cluster HDInsight, vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Distribuire il nuovo HDInsight 4,0 e connettersi al nuovo Metastore

Al termine dell'aggiornamento dello schema, distribuire un nuovo cluster HDInsight 4,0 e connettere il Metastore aggiornato. Se è già stato distribuito 4,0, impostarlo in modo da potersi connettere al Metastore da Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Eseguire lo script di migrazione dello schema da HDInsight 4,0

Le tabelle sono gestite in modo diverso in HDInsight 3,6 e HDInsight 4,0. Per questo motivo, non è possibile condividere le stesse tabelle per i cluster di versioni diverse. Se si desidera utilizzare HDInsight 3,6 allo stesso tempo di HDInsight 4,0, è necessario disporre di copie separate dei dati per ogni versione.

Il carico di lavoro hive può includere una combinazione di tabelle ACID e non ACID. Una differenza fondamentale tra hive in HDInsight 3,6 (hive 2) e hive in HDInsight 4,0 (hive 3) è la conformità ACID per le tabelle. In HDInsight 3,6, per abilitare la conformità ACID hive è necessaria una configurazione aggiuntiva, ma nelle tabelle HDInsight 4,0 sono conformi agli ACID per impostazione predefinita. L'unica azione necessaria prima della migrazione consiste nell'eseguire una compattazione principale sulla tabella ACID nel cluster 3,6. Dalla vista hive o da l'oggetto con cui eseguire la query seguente:

```sql
alter table myacidtable compact 'major';
```

Questa compattazione è necessaria perché le tabelle ACID HDInsight 3,6 e HDInsight 4,0 comprendono i Delta ACID in modo diverso. La compattazione impone uno Slate pulito che garantisce la coerenza. La sezione 4 della [documentazione relativa alla migrazione di hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contiene informazioni aggiuntive per la compattazione delle tabelle HDINSIGHT 3,6 acid.

Una volta completati i passaggi per la migrazione e la compressione dei Metastore, è possibile eseguire la migrazione del warehouse effettivo. Dopo aver completato la migrazione del warehouse di hive, il warehouse HDInsight 4,0 avrà le proprietà seguenti:

|3.6 |4.0 |
|---|---|
|Tabelle esterne|Tabelle esterne|
|Tabelle gestite non transazionali|Tabelle esterne|
|Tabelle gestite transazionali|Tabelle gestite|

Prima di eseguire la migrazione, potrebbe essere necessario modificare le proprietà del data warehouse. Se, ad esempio, si prevede che una tabella sarà accessibile da terze parti, ad esempio un cluster HDInsight 3,6, tale tabella deve essere esterna al termine della migrazione. In HDInsight 4,0 tutte le tabelle gestite sono transazionali. Pertanto, le tabelle gestite in HDInsight 4,0 devono essere accessibili solo dai cluster HDInsight 4,0.

Una volta impostate correttamente le proprietà della tabella, eseguire lo strumento di migrazione del warehouse di hive da uno dei nodi head del cluster usando la shell SSH:

1. Connettersi al cluster nodo head usando SSH. Per istruzioni, vedere [connettersi a HDInsight tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Aprire una shell di accesso come utente hive eseguendo`sudo su - hive`
1. Determinare la versione dello stack della piattaforma dati eseguendo `ls /usr/hdp`. Verrà visualizzata una stringa di versione da usare nel comando successivo.
1. Eseguire il comando seguente dalla Shell. Sostituire `STACK_VERSION` con la stringa di versione del passaggio precedente:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Al termine dell'esecuzione dello strumento di migrazione, il warehouse di hive sarà pronto per HDInsight 4,0.

> [!Important]  
> Le tabelle gestite in HDInsight 4,0 (incluse le tabelle migrate da 3,6) non devono essere accessibili da altri servizi o applicazioni, inclusi i cluster HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Sicurezza hive tra le versioni di HDInsight

Dal momento che HDInsight 3,6, HDInsight si integra con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP USA Kerberos e Apache Ranger per gestire le autorizzazioni di risorse specifiche all'interno del cluster. È possibile eseguire la migrazione dei criteri Ranger distribuiti in hive in HDInsight 3,6 in HDInsight 4,0 con i passaggi seguenti:

1. Passare al pannello Service Manager Ranger nel cluster HDInsight 3,6.
2. Passare al criterio denominato **hive** ed esportare il criterio in un file JSON.
3. Assicurarsi che tutti gli utenti a cui viene fatto riferimento nel file JSON dei criteri esportati esistano nel nuovo cluster. Se a un utente viene fatto riferimento nel codice JSON dei criteri ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster o rimuovere il riferimento dal criterio.
4. Passare al pannello **Service Manager Ranger** nel cluster HDInsight 4,0.
5. Passare al criterio denominato **hive** e importare il codice JSON dei criteri Ranger dal passaggio 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Controllare la compatibilità e modificare i codici in base alle esigenze nell'app di test

Quando si esegue la migrazione di carichi di lavoro, ad esempio programmi e query esistenti, controllare le note sulla versione e la documentazione per le modifiche e applicare le modifiche secondo necessità. Se il cluster HDInsight 3,6 usa una Spark condivisa e una metastore Hive, è necessaria una [configurazione aggiuntiva con il connettore di hive warehouse](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Distribuire una nuova app per la produzione

Per passare al nuovo cluster, ad esempio, è possibile installare una nuova applicazione client e usarla come nuovo ambiente di produzione oppure è possibile aggiornare l'applicazione client esistente e passare a HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Passare HDInsight 4,0 alla produzione

Se durante il testing sono state create differenze nel Metastore, sarà necessario aggiornare le modifiche immediatamente prima del cambio. In questo caso, è possibile esportare & importare il Metastore e quindi eseguire di nuovo l'aggiornamento.

## <a name="remove-the-old-production"></a>Rimuovere la produzione precedente

Una volta confermato che la versione è completa e completamente operativa, è possibile rimuovere la versione 3,6 e il Metastore precedente. Verificare che sia stata eseguita la migrazione di tutti gli elementi prima di eliminare l'ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Esecuzione di query tra versioni di HDInsight

Esistono due modi per eseguire ed eseguire il debug di query hive/LLAP all'interno di un cluster HDInsight 3,6. HiveCLI offre un'esperienza da riga di comando e la visualizzazione Tez/hive fornisce un flusso di lavoro basato su GUI.

In HDInsight 4,0, HiveCLI è stato sostituito da. HiveCLI è un client di parsimonia per del 1 e è un client JDBC che fornisce l'accesso a del 2. È inoltre possibile utilizzare l'estensione per connettersi a qualsiasi altro endpoint del database compatibile con JDBC. È possibile usare l'installazione predefinita di HDInsight 4,0 senza alcuna installazione necessaria.

In HDInsight 3,6, il client GUI per interagire con il server hive è la visualizzazione hive di Ambari. HDInsight 4,0 non viene fornito con la visualizzazione Ambari. Per i clienti è stato fornito un modo per usare Data Analytics Studio (DAS), che non è un servizio HDInsight di base. DAS non viene fornito con i cluster HDInsight predefiniti e non è un pacchetto supportato ufficialmente. Tuttavia, è possibile installare DAS nel cluster usando un' [azione script](../hdinsight-hadoop-customize-cluster-linux.md) come indicato di seguito:

|Proprietà | Valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome|DAS|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Tipo/i di nodo|Head|

Attendere da 10 a 15 minuti, quindi avviare Data Analytics studio usando questo URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

Prima di accedere a DAS, potrebbe essere necessario aggiornare l'interfaccia utente di Ambari e/o riavviare tutti i componenti di Ambari.

Dopo aver installato DAS, se non vengono visualizzate le query eseguite nel Visualizzatore query, seguire questa procedura:

1. Impostare le configurazioni per hive, Tez e DAS come descritto in [questa guida per la risoluzione dei problemi relativi all'installazione di das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Assicurarsi che le seguenti configurazioni di directory di archiviazione di Azure siano BLOB di pagine e che siano elencate in `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Riavviare HDFS, hive, Tez e DAS in entrambi nodi head.

## <a name="next-steps"></a>Passaggi successivi

* [Annuncio HDInsight 4,0](../hdinsight-version-release.md)
* [Approfondimento su HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabelle ACID hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
