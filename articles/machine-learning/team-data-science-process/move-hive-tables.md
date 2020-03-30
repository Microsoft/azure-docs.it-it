---
title: Creare tabelle Hive e caricare dati dall'archiviazione BLOB - Processo di analisi scientifica dei dati per i team
description: Usare le query Hive per creare tabelle Hive e caricare i dati dall'archiviazione BLOB di Azure. Partizionare le tabelle Hive e usare la formattazione ORC per migliorare le prestazioni delle query.
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
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251661"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Creare tabelle Hive e caricare i dati dall'archiviazione BLOB di Azure

Questo articolo presenta le query Hive generiche che consentono di creare tabelle Hive e di caricare i dati dall'archivio BLOB di Azure. Vengono inoltre fornite alcune linee guida sul partizionamento delle tabelle Hive e sull'uso della formattazione ORC per migliorare le prestazioni delle query.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure.Created an Azure Storage account. Per istruzioni, vedere Informazioni sugli account di archiviazione di [Azure.](../../storage/common/storage-introduction.md)
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight.  Per istruzioni, vedere [Setup Clusters in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) (Configurazione di cluster in HDInsight).
* Abilitato l'accesso remoto al cluster, eseguito l'accesso e aperto la console della riga di comando di Hadoop. Per istruzioni, vedere [Gestire cluster Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Caricare dati nell'archivio BLOB di Azure
Se è stata creata una macchina virtuale di Azure seguendo le istruzioni fornite nell'articolo [Configurare una macchina virtuale di Azure per l'analisi avanzata](../../machine-learning/data-science-virtual-machine/overview.md), questo file di script deve essere stato scaricato nella directory *C:\\Users\\\<nome utente name\>\\Documents\\Data Science Scripts* della macchina virtuale. Queste query Hive richiedono solo di fornire uno schema di dati e una configurazione di archiviazione BLOB di Azure nei campi appropriati per essere pronti per l'invio.

Si supponga che i dati delle tabelle Hive siano in un formato tabulare **non compresso** e che i dati siano stati caricati nel contenitore predefinito (o in uno aggiuntivo) dell'account di archiviazione usato dal cluster Hadoop.

Se si desidera far pratica con i **dati relativi alle corse dei Taxi di NYC**, è necessario:

* **scaricare** i 24 file di [dati elativi alle corse dei Taxi di NYC](https://www.andresmh.com/nyctaxitrips) (12 file relativi alle corse e 12 file delle tariffe);
* **decomprimere** tutti i file in file con estensione .csv;
* **caricarli** nell'account predefinito (o appropriato) dell'account di archiviazione di Azure. Le opzioni per un account di questo tipo vengono visualizzate nell'argomento Usare Archiviazione di Azure con i cluster di Azure HDInsight.options for such an account appear at [Use Azure Storage with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) topic. Il processo per caricare i file con estensione csv per il contenitore predefinito nell'account di archiviazione sono disponibili in questa [pagina](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Come inviare query Hive
È possibile inviare query Hive mediante:

* [Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop](#headnode)
* [Inviare le query Hive con l'editor Hive](#hive-editor)
* [Inviare le query Hive con i comandi di Azure PowerShell](#ps)

Le query Hive sono simili a SQL. Se si ha già familiarità con SQL, i [fogli informativi per gli utenti di Hive](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) possono rivelarsi utili.

Quando si invia una query Hive, è possibile controllare la destinazione dell'output, sia questo visualizzato sullo schermo o incluso in un file locale nel nodo head o in un BLOB di Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop
Se la query Hive è complessa e la si invia direttamente al nodo head del cluster Hadoop, il completamento dell'operazione sarà più rapido rispetto a quando l'invio viene effettuato con l'editor Hive o gli script di Azure PowerShell.

Accedere al nodo head del cluster Hadoop, aprire la riga di comando di Hadoop sul desktop del nodo head e immettere il comando `cd %hive_home%\bin`.

Esistono tre modi per inviare query Hive nella riga di comando di Hadoop:

* Direttamente
* utilizzo di file '.hql'
* Con la console dei comandi di Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Inviare query Hive direttamente nella riga di comando di Hadoop
È possibile eseguire comandi come `hive -e "<your hive query>;` per inviare query Hive semplici direttamente nella riga di comando di Hadoop. In questo esempio, la casella rossa evidenzia il comando che consente di inviare la query Hive, mentre quella verde evidenzia l'output della query.

![Comando per inviare query Hive con l'output dalla query Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Inviare le query Hive nei file '.hql'
Quando la query Hive è più complicata e presenta più righe, modificare le query nella riga di comando o nella console dei comandi di Hive non è pratico. Un'alternativa consiste nell'utilizzare un editor di testo nel nodo head del cluster Hadoop per salvare le query Hive in un file '.hql' in una directory locale del nodo head. È quindi possibile inviare la query Hive nel file `-f` '.hql' utilizzando l'argomento come segue:

    hive -f "<path to the '.hql' file>"

![Query Hive in un file '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Eliminare la visualizzazione relativa allo stato di avanzamento delle query Hive sullo schermo**

Per impostazione predefinita, dopo l'invio della query Hive nella riga di comando di Hadoop, lo stato di avanzamento del processo di mapping e riduzione viene mostrato sullo schermo. Per eliminare la stampa della schermata di avanzamento del processo di mapping e riduzione, è possibile usare un argomento `-S` ("S" in lettere maiuscole) nella riga di comando nel modo seguente:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Inviare query Hive nella console dei comandi di Hive
Innanzitutto è possibile immettere la console dei comandi di Hive eseguendo il comando `hive` nella riga di comando di Hadoop, quindi inviare query Hive nella console dei comandi di Hive. Ecco un esempio. In questo esempio, le due caselle rosse evidenziano i comandi usati per inserire la console dei comandi di Hive e la query Hive inviata nella console dei comandi. La casella verde evidenzia l'output dalla query Hive.

![Aprire la console dei comandi di Hive, immettere il comando e visualizzare l'output della query Hive](./media/move-hive-tables/run-hive-queries-2.png)

Negli esempi precedenti, i risultati della query Hive vengono visualizzati direttamente sullo schermo. È anche possibile salvare l'output su un file locale nel nodo head o in un BLOB di Azure. Quindi, è possibile usare altri strumenti per analizzare ulteriormente l'output delle query Hive.

**Restituire i risultati delle query Hive in un file locale.**
Per restituire i risultati delle query Hive in una directory locale nel nodo head, si deve inviare la query Hive nella riga di comando di Hadoop come indicato di seguito:

    hive -e "<hive query>" > <local path in the head node>

Nell'esempio seguente, l'output della query Hive viene scritto in un file `hivequeryoutput.txt` all'interno della directory `C:\apps\temp`.

![Output della query Hive](./media/move-hive-tables/output-hive-results-1.png)

**Restituire i risultati delle query Hive in un BLOB di Azure**

Si può anche restituire i risultati della query Hive in un BLOB di Azure, nel contenitore predefinito del cluster Hadoop. In questo caso la query Hive del client è la seguente:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Nell'esempio seguente, l'output della query Hive viene scritto in una directory del BLOB `queryoutputdir` nel contenitore predefinito del cluster Hadoop. In questo caso, è sufficiente fornire il nome della directory, senza il nome del BLOB. Viene generato un errore se si specificano i nomi della directory e del BLOB, ad esempio `wasb:///queryoutputdir/queryoutput.txt`.

![Output della query Hive](./media/move-hive-tables/output-hive-results-2.png)

Se si apre il contenitore predefinito del cluster Hadoop usando Esplora archivi di Azure, è possibile visualizzare l'output della query Hive come indicato nell'immagine di seguito. È possibile applicare il filtro (evidenziato da una casella rossa) per recuperare solo il BLOB con le lettere specificate nei nomi.

![Visualizzazione Azure Storage Explorer dell'output della query Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Inviare le query Hive con l'editor Hive
È anche possibile usare la console di query (Hive Editor) immettendo un URL nel formato *https:\//\<nome del cluster Hadoop>.azurehdinsight.net/Home/HiveEditor* in un Web browser. La console è visualizzabile solo dopo aver eseguito l'accesso, pertanto è necessario disporre delle proprie credenziali del cluster Hadoop.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Inviare le query Hive con i comandi di Azure PowerShell
È possibile usare anche PowerShell per inviare le query Hive. Per istruzioni, vedere [Invio di processi Hive tramite PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Creare database e tabelle HiveCreate Hive database and tables
Le query Hive sono condivise nel [repository GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e possono essere scaricate da lì.

Di seguito è presentata la query Hive che crea una tabella Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Di seguito sono presentate le descrizioni dei campi da collegare e altre configurazioni:

* nome database : il nome del database che si desidera creare. ** \<\>** Se si desidera utilizzare solo il database predefinito, la query "*create database...*" può essere omessa.
* nome tabella : il nome della tabella che si desidera creare all'interno del database specificato. ** \<\>** Se si desidera utilizzare il database predefinito, è possibile \<fare\>riferimento direttamente alla tabella in base al * \<nome\> * della tabella senza nome database .
* **separatore\>di campo : il separatore che delimita i campi nel file di dati da caricare nella tabella Hive. \<**
* **separatore\>di riga : il separatore che delimita le righe nel file di dati. \<**
* percorso di archiviazione: il percorso di Archiviazione di Azure per salvare i dati delle tabelle Hive. ** \<\>** Se il parametro *LOCATION \<storage location\>* non viene specificato, il database e le tabelle vengono archiviati per impostazione predefinita nella directory *hive/warehouse/* nel contenitore predefinito del cluster Hive. Se si desidera specificare il percorso di archiviazione, questo deve trovarsi nel contenitore predefinito per database e tabelle. Questo percorso deve essere indicato come percorso relativo al contenitore predefinito del cluster nel formato *'wasb:///\<directory 1>/'* o *\<'wasb:/// directory 1>/\<directory 2>/'* e così via. Dopo l'esecuzione della query, le directory relative vengono create all'interno del contenitore predefinito.
* **TBLPROPERTIES("skip.header.line.count"="1")**: se il file di dati presenta una riga di intestazione, si deve aggiungere questa proprietà **alla fine** della query di *creazione della tabella*. In caso contrario, la riga di intestazione verrà caricata come un record nella tabella. Se il file di dati non presenta una riga di intestazione, questa configurazione può essere omessa nella query.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Caricare dati nelle tabelle Hive
Di seguito è presentata la query Hive che carica i dati in una tabella Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* path **to\>blob data : If the blob file to be uploaded to the Hive table is in the default container of the HDInsight Hadoop cluster, the path to blob data should be in the format 'wasb:// directory in this container>/ blob file name>' . \<** * \<\> * *\<\<* Il file BLOB può trovarsi inoltre in un contenitore aggiuntivo del cluster Hadoop di HDInsight. In questo * \<\> * caso, il percorso dei dati BLOB deve essere nel formato 'wasb:// *\<nome \<del\<contenitore>nome del file di archiviazione>.blob.core.windows.net/nome file BLOB>'.*

  > [!NOTE]
  > I dati BLOB da caricare nella tabella Hive devono trovarsi nel contenitore predefinito o aggiuntivo dell'account di archiviazione del cluster Hadoop. In caso contrario, la query di *LOAD DATA* avrà esito negativo perché non può accedere ai dati.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Argomenti avanzati: Tabella partizionata e archiviazione dei dati Hive in formato ORC
Se i dati sono di grandi dimensioni, il partizionamento della tabella è utile per le query che devono solo eseguire l'analisi di alcune partizioni della tabella. Ad esempio, è ragionevole partizionare i dati di log di un sito Web per date.

Oltre al partizionamento delle tabelle Hive, è inoltre utile archiviare i dati Hive in formato ORC. Per altre informazioni sulla formattazione ORC, vedere l’<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">uso dei file ORC per migliorare le prestazioni quando Hive legge, scrive ed elabora dati</a>.

### <a name="partitioned-table"></a>Tabella partizionata
Di seguito è presentata la query Hive che crea una tabella partizionata e vi carica i dati.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Quando si eseguono query sulle tabelle partizionate, è `where` consigliabile aggiungere la condizione di partizione **all'inizio** della clausola, che migliora l'efficienza della ricerca.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Archiviare i dati Hive in formato ORC
Non è possibile caricare direttamente i dati del BLOB nelle tabelle Hive nel formato di archiviazione ORC. Di seguito sono presentati i passaggi da eseguire per caricare dati dai BLOB Azure nelle tabelle Hive archiviate in formato ORC.

Creare una tabella esterna **ARCHIVIATA COME TEXTFILE** e caricare i dati dall'archiviazione BLOB nella tabella.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Creare una tabella interna con lo stesso schema della tabella esterna nel passaggio 1, lo stesso delimitatore dei campi e archiviare i dati Hive in formato ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Selezionare i dati della tabella esterna nel passaggio 1 e inserirli nella tabella ORC.

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Se il * \<nome\>del\< database della tabella TEXTFILE . nome\> di tabella file di testo esterno* `SELECT * FROM <database name>.<external textfile table name>` ha partizioni, in STEP 3, il comando seleziona la variabile di partizione come un campo nel set di dati restituito. Inserendolo nel * \<nome\>del\< database. \> * Il nome della tabella ORC ha esito negativo poiché * \<il nome\>del database .\< Il nome\> * della tabella ORC non ha la variabile di partizione come campo nello schema della tabella. In questo caso, è necessario selezionare in modo specifico i campi da inserire nel * \<nome\>del database.\< Nome della\> tabella ORC* come segue:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

È possibile eliminare * \<\> * il nome della tabella di file di testo esterno quando si utilizza la query seguente dopo che tutti i dati sono stati inseriti nel * \<nome\>del database.\< Nome\>tabella ORC*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Al termine della procedura, si disporrà di una tabella con i dati nel formato ORC pronta per l'uso.  
