---
title: Apache Ambari per ottimizzare le configurazioni del cluster-Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 54f65f9ef4af2c0d96dd80156eab81c49e5e52a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232871"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Usare Apache Ambari per ottimizzare le configurazioni cluster HDInsight

HDInsight fornisce i cluster Apache Hadoop per le applicazioni di elaborazione dei dati su larga scala. La gestione, il monitoraggio e l'ottimizzazione di questi complessi cluster a più nodi possono essere difficili. Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio dei cluster Linux in HDInsight.  Per i cluster Windows, usare l'[API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Per un'introduzione all'uso dell'interfaccia utente Web Ambari, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Accedere ad Ambari all'indirizzo `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale visualizza un dashboard generale.

![Visualizzazione del dashboard utente di Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

L'interfaccia utente Web di Ambari viene utilizzata per gestire host, servizi, avvisi, configurazioni e visualizzazioni. Non è possibile usare Ambari per creare un cluster HDInsight o aggiornare i servizi. Non è inoltre in grado di gestire stack e versioni, rimuovere le autorizzazioni o ricommissionare host o aggiungere servizi al cluster.

## <a name="manage-your-clusters-configuration"></a>Gestire la configurazione del cluster

Le impostazioni di configurazione consentono di ottimizzare un determinato servizio. Per modificare le impostazioni di configurazione di un servizio, selezionare il servizio dalla barra laterale dei **Servizi** (a sinistra). Passare quindi alla scheda **configs (configurazioni** ) nella pagina dei dettagli del servizio.

![Barra laterale dei servizi Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificare le dimensioni dell'heap Java NameNode

Le dimensioni dell'heap Java NameNode dipendono da molti fattori, ad esempio il carico del cluster. Inoltre, il numero di file e il numero di blocchi. Le dimensioni predefinite, pari a 1 GB, vanno bene per la maggior parte dei cluster, anche se alcuni carichi di lavoro richiedono più o meno memoria.

Per modificare le dimensioni dell'heap Java NameNode:

1. Selezionare **HDFS** dalla barra laterale Services (Servizi) e passare alla scheda **Configs** (Configurazioni).

    ![Configurazione di Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Trovare l'impostazione **NameNode Java heap size** (Dimensioni dell'heap Java NameNode). È anche possibile usare la casella di testo **filter** (filtro) per digitare e trovare una determinata impostazione. Selezionare l'icona **pen** (penna) accanto al nome dell'impostazione.

    ![Dimensioni heap Java Ambari NameNode Apache](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digitare il nuovo valore nella casella di testo, quindi premere **INVIO** per salvare la modifica.

    ![Ambari modificare NameNode heap Java size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Le dimensioni dell'heap Java NameNode sono state modificate in 1 GB da 2 GB.

    ![Modificato NameNode heap Java size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Salvare le modifiche facendo clic sul pulsante verde **Save** (Salva) nella parte superiore della schermata di configurazione.

    ![' Apache Ambari Save Configurations '](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Ottimizzazione di Apache Hive

Le sezioni seguenti descrivono le opzioni di configurazione per ottimizzare le prestazioni generali di Apache Hive.

1. Per modificare i parametri di configurazione di Hive, selezionare **Hive** dalla barra laterale Services (Servizi).
1. Passare alla scheda **Configs** (Configurazioni).

### <a name="set-the-hive-execution-engine"></a>Impostare il motore di esecuzione di Hive

Hive offre due motori di esecuzione: Apache Hadoop MapReduce e Apache TEZ. Tez è più veloce di MapReduce. I cluster Linux in HDInsight usano Tez come motore di esecuzione predefinito. Per cambiare il motore di esecuzione:

1. Nella scheda **Configs** (Configurazioni) di Hive digitare **execution engine** nella casella di filtro.

    ![Motore di esecuzione della ricerca di Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Il valore predefinito della proprietà **Optimization** (Ottimizzazione) è **Tez**.

    ![Ottimizzazione-motore Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Ottimizzare i mapper

Hadoop prova a dividere (*eseguire il mapping*) di un singolo file in più file e a elaborare i file risultanti in parallelo. Il numero di mapper dipende dal numero di suddivisioni. I due parametri di configurazione seguenti determinano il numero di suddivisioni per il motore di esecuzione:

* `tez.grouping.min-size`: limite minimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 16 MB (16.777.216 byte).
* `tez.grouping.max-size`: limite massimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 1 GB (1.073.741.824 byte).

Come linea guida per le prestazioni, abbassare entrambi i parametri per migliorare la latenza, aumentando la velocità effettiva.

Ad esempio, per impostare quattro attività di mapper per dimensioni dei dati pari a 128 MB, impostare entrambi i parametri su 32 MB ognuno (33.554.432 byte).

1. Per modificare i parametri limite, passare alla scheda **Configs** (Configurazioni) del servizio Tez. Espandere il pannello **General** (Generale) e individuare i parametri `tez.grouping.max-size` e `tez.grouping.min-size`.

1. Impostare entrambi i parametri su **33.554.432** byte (32 MB).

    ![Dimensioni di raggruppamento di Apache Ambari Tez](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Queste modifiche interessano tutti i processi Tez nel server. Per ottenere un risultato ottimale, scegliere valori appropriati per i parametri.

### <a name="tune-reducers"></a>Ottimizzare i riduttori

Apache ORC e Snappy offrono entrambi prestazioni elevate. Hive potrebbe tuttavia avere un numero troppo basso di riduttori per impostazione predefinita e causare quindi colli di bottiglia.

Si supponga, ad esempio, di avere dati di input di dimensioni pari a 50 GB. Tali dati nel formato ORC con la compressione Snappy sono pari a 1 GB. Hive stima il numero di riduttori necessari nel modo seguente: (numero di byte di input per i mapper / `hive.exec.reducers.bytes.per.reducer`).

Con le impostazioni predefinite, questo esempio è quattro riduttori.

Il parametro `hive.exec.reducers.bytes.per.reducer` specifica il numero di byte elaborati per riduttore. Il valore predefinito è 64 MB. Se si diminuisce questo valore, il parallelismo aumenta e le prestazioni possono migliorare. Se lo si diminuisce troppo, potrebbero anche essere generati troppi riduttori, che possono influire negativamente sulle prestazioni. Questo parametro è basato sugli specifici requisiti per i dati, sulle impostazioni di compressione e su altri fattori ambientali.

1. Per modificare il parametro, passare alla scheda **Configs** (Configurazioni) di Hive e trovare il parametro **Data per Reducer** (Dati per riduttore) nella pagina Settings (Impostazioni).

    ![Dati Apache Ambari per riduttore](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Selezionare **Edit** (Modifica) per impostare il valore su 128 MB (134.217.728 byte) e quindi premere **INVIO** per salvare.

    ![Dati Ambari per riduttore-modificati](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Date le dimensioni di input di 1.024 MB, con 128 MB di dati per riduttore, sono presenti otto riduttori (1024/128).

1. Un valore non corretto per il parametro **Data per Reducer** (Dati per riduttore) può restituire un numero elevato di riduttori, che influisce negativamente sulle prestazioni della query. Per limitare il numero massimo di riduttori, impostare `hive.exec.reducers.max` su un valore appropriato. Il valore predefinito è 1009.

### <a name="enable-parallel-execution"></a>Abilitare l'esecuzione parallela

Una query Hive viene eseguita in una o più fasi. Se le fasi indipendenti possono essere eseguite in parallelo, le prestazioni della query miglioreranno.

1. Per abilitare l'esecuzione della query parallela, passare alla scheda **Config** (Configurazioni) di Hive e cercare la proprietà `hive.exec.parallel`. Il valore predefinito è false. Impostare il valore su true e quindi premere **INVIO** per salvare il valore.

1. Per limitare il numero di processi da eseguire in parallelo, modificare la `hive.exec.parallel.thread.number` proprietà. Il valore predefinito è 8.

    ![Visualizzazione di Apache Hive Exec Parallel](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Abilitare la vettorializzazione

Hive elabora i dati una riga alla volta. Con la vettorializzazione Hive elabora i dati in blocchi di 1.024 righe invece che una riga alla volta. La vettorializzazione è applicabile solo al formato di file ORC.

1. Per abilitare un'esecuzione di query vettorializzata, passare alla scheda **Configs** (Configurazioni) di Hive e cercare il parametro `hive.vectorized.execution.enabled`. Il valore predefinito è true per Hive 0.13.0 o versione successiva.

1. Per abilitare l'esecuzione vettorializzata per il lato reduce della query, impostare il parametro `hive.vectorized.execution.reduce.enabled` su true. Il valore predefinito è false.

    ![Apache Hive esecuzione vettoriale](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Abilitare l'ottimizzazione basata sui costi

Per impostazione predefinita, Hive segue un set di regole per trovare un piano di esecuzione della query ottimale. L'ottimizzazione basata sui costi valuta più piani per l'esecuzione di una query. E assegna un costo a ogni piano, quindi determina il piano più economico per l'esecuzione di una query.

Per abilitare la**configurazione**di base di base, passare a > **Impostazioni** di configurazione di **hive** > e trovare **Abilita ottimizzazione basata sui costi**, quindi impostare l'interruttore **su**attivato.

![Ottimizzatore basato sui costi di HDInsight](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

I parametri di configurazione aggiuntivi seguenti migliorano le prestazioni della query Hive quando l'ottimizzazione basata sui costi è abilitata:

* `hive.compute.query.using.stats`

    Se impostato su true, Hive usa le statistiche archiviate nel metastore per rispondere a query semplici, ad esempio `count(*)`.

    ![Apache Hive query di calcolo con statistiche](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Quando l'ottimizzazione basata sui costi è abilitata, vengono create statistiche di colonna. Hive usa le statistiche di colonna, archiviate nel metastore, per ottimizzare le query. Recuperare le statistiche per ogni colonna richiede più tempo quando il numero di colonne è elevato. Se impostata su false, questa impostazione disabilita il recupero delle statistiche di colonna dal metastore.

    ![Statistiche colonna set statistiche Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Le statistiche della partizione di base, ad esempio numero di righe, dimensioni dei dati e dimensioni dei file, vengono archiviate nel metastore. Se impostato su true, le statistiche della partizione vengono recuperate dal Metastore. Se è false, le dimensioni del file vengono recuperate dal file system. Il numero di righe viene recuperato dallo schema di riga.

    ![Statistiche della partizione impostate nelle statistiche di Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Abilitare la compressione intermedia

Le attività mappe creano file intermedi che vengono usati dalle attività riduttori. La compressione intermedia riduce le dimensioni dei file intermedi.

I processi Hadoop presentano in genere colli di bottiglia a causa dell'I/O. La compressione dei dati può velocizzare l'I/O e il trasferimento di rete complessivo.

I tipi di compressione disponibili sono:

| Format | Strumento | Algoritmo | Estensione nome del file | Divisibile |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Sì |
| LZO | `Lzop` | LZO | `.lzo` | Sì, se indicizzato |
| Snappy | N/D | Snappy | Snappy | No |

Come regola generale, la possibilità di suddividere il metodo di compressione è importante. in caso contrario, verranno creati pochi Mapper. Se i dati di input sono costituiti da testo, `bzip2` è l'opzione migliore. Per il formato ORC, Snappy è l'opzione di compressione più rapida.

1. Per abilitare la compressione intermedia, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.intermediate` su true. Il valore predefinito è false.

    ![' Hive Exec Compress Intermediate '](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Per comprimere i file intermedi, scegliere un codec di compressione con costi di CPU più bassi, anche se il codec non ha un output di compressione elevato.

1. Per impostare il codec di compressione intermedia, aggiungere la proprietà personalizzata `mapred.map.output.compression.codec` al file `hive-site.xml` o `mapred-site.xml`.

1. Per aggiungere un'impostazione personalizzata:

    a. Passare a **hive** > **configs** > **Advanced** > **Custom hive-site**.

    b. Selezionare **Aggiungi proprietà** nella parte inferiore del riquadro Custom hive-site.

    c. Nella finestra Add Property (Aggiungi proprietà) immettere `mapred.map.output.compression.codec` come chiave e `org.apache.hadoop.io.compress.SnappyCodec` come valore.

    d. Selezionare **Aggiungi**.

    ![' Apache Hive proprietà personalizzata Add '](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Questa impostazione comprimerà il file intermedio utilizzando la compressione a scatto. Dopo che la proprietà è stata aggiunta, viene visualizzata nel riquadro Custom hive-site (hive-site personalizzato).

    > [!NOTE]  
    > Questa procedura modifica il file `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Comprimere l'output finale

Anche l'output di Hive finale può essere compresso.

1. Per comprimere l'output di Hive finale, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.output` su true. Il valore predefinito è false.

1. Per scegliere il codec di compressione dell'output, aggiungere la proprietà personalizzata `mapred.output.compression.codec` al riquadro Custom hive-site (hive-site personalizzato), come descritto nel passaggio 3 della sezione precedente.

    ![Apache Hive proprietà personalizzata ADD2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Abilitare l'esecuzione speculativa

L'esecuzione speculativa avvia un certo numero di attività duplicate per rilevare e negare l'elenco dello strumento di rilevamento delle attività con esecuzione prolungata. Migliorando l'esecuzione complessiva del processo, ottimizzando i risultati delle singole attività.

È consigliabile non attivare l'esecuzione speculativa per le attività MapReduce a esecuzione prolungata con grandi quantità di input.

* Per abilitare l'esecuzione speculativa, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.mapred.reduce.tasks.speculative.execution` su true. Il valore predefinito è false.

    ![' Hive mapred ridurre le attività di esecuzione speculativa '](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Ottimizzare le partizioni dinamiche

Hive consente di creare partizioni dinamiche quando si inseriscono i record in una tabella, senza predefinire ogni partizione. Questa possibilità è una funzionalità potente. Sebbene possa comportare la creazione di un numero elevato di partizioni. E un numero elevato di file per ogni partizione.

1. Per creare partizioni dinamiche in Hive, il parametro `hive.exec.dynamic.partition` deve essere impostato su (impostazione predefinita).

1. Impostare la modalità di partizione dinamica su *strict*. Nella modalità strict almeno una partizione deve essere statica Questa impostazione impedisce le query senza il filtro di partizione nella clausola WHERE, ovvero *strict* impedisce le query che analizzano tutte le partizioni. Passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare `hive.exec.dynamic.partition.mode` su **strict**. Il valore predefinito è **nonstrict**.

1. Per limitare il numero di partizioni dinamiche da creare, modificare il parametro `hive.exec.max.dynamic.partitions`. Il valore predefinito è 5000.

1. Per limitare il numero totale di partizioni dinamiche per nodo, modificare `hive.exec.max.dynamic.partitions.pernode`. Il valore predefinito è 2000.

### <a name="enable-local-mode"></a>Abilitare la modalità locale

La modalità locale consente a hive di eseguire tutte le attività di un processo in un singolo computer. O a volte in un singolo processo. Questa impostazione migliora le prestazioni delle query se i dati di input sono di piccole dimensioni. E l'overhead di avvio delle attività per le query utilizza una percentuale significativa dell'esecuzione complessiva della query.

Per abilitare la modalità locale, aggiungere il parametro `hive.exec.mode.local.auto` al pannello Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Modalità di Apache Hive Exec auto locale](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Impostare una singola query MultiGROUP BY per MapReduce

Quando questa proprietà è impostata su true, una query MultiGROUP BY con chiavi group-by comuni genera un singolo processo MapReduce.  

Per abilitare questo comportamento, aggiungere il parametro `hive.multigroupby.singlereducer` al riquadro Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Query MultiGROUP BY singola per MapReduce impostata in Hive](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ottimizzazioni di Hive aggiuntive

Le sezioni seguenti descrivono altre ottimizzazioni relative a Hive che è possibile impostare.

#### <a name="join-optimizations"></a>Ottimizzazioni del join

Il tipo di join predefinito in Hive è un *join casuale*. In Hive speciali mapper leggono l'input e generano una coppia chiave/valore di join per un file intermedio. Hadoop ordina e unisce queste coppie in una fase casuale. Questa fase casuale è costosa. La scelta del join appropriato in base ai dati può migliorare considerevolmente le prestazioni.

| Tipo di join | Se | Come | Settings di Hive | Commenti |
| --- | --- | --- | --- | --- |
| Join casuale | <ul><li>Scelta predefinita</li><li>È sempre valido</li></ul> | <ul><li>Legge da parte di una delle tabelle</li><li>Raggruppa e ordina nella chiave di join</li><li>Invia un bucket a ogni elemento reduce</li><li>Il join viene eseguito sul lato Reduce</li></ul> | Non sono necessarie impostazioni di Hive significative | Funziona sempre |
| Map Join | <ul><li>La memoria disponibile è sufficiente per una tabella</li></ul> | <ul><li>Legge una tabella di piccole dimensioni nella tabella hash della memoria</li><li>Passa attraverso parte del file di grandi dimensioni</li><li>Crea un join con ogni record della tabella hash</li><li>I join vengono eseguiti tramite il solo mapper</li></ul> | `hive.auto.confvert.join=true` | Veloce, ma limitato |
| Sort Merge Bucket | Se entrambe le tabelle: <ul><li>Sono ordinate allo stesso modo</li><li>Sono raggruppate allo stesso modo</li><li>Creano un join con la colonna ordinata/raggruppata</li></ul> | Ogni processo: <ul><li>Legge un bucket da ogni tabella</li><li>Elabora la riga con il valore più basso</li></ul> | `hive.auto.convert.sortmerge.join=true` | Efficiente |

#### <a name="execution-engine-optimizations"></a>Ottimizzazioni del motore di esecuzione

Raccomandazioni aggiuntive per ottimizzare il motore di esecuzione Hive:

| Impostazione | Consigliato | Impostazione predefinita di HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = più sicuro, più lento; false = più veloce | false |
| `tez.am.resource.memory.mb` | limite superiore di 4 GB per la maggior parte | Ottimizzazione automatica |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Ottimizzazione di Apache Pig

Le proprietà di Apache Pig possono essere modificate dall'interfaccia utente Web Ambari per ottimizzare le query di Pig. Se le proprietà di Pig vengono modificate da Ambari, vengono direttamente modificate anche nel file `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Per modificare le proprietà di Pig, passare alla scheda **Configs** (Configurazioni) di Pig e quindi espandere il riquadro **Advanced pig-properties** (Proprietà Pig avanzate).

1. Trovare, rimuovere il commento e cambiare il valore della proprietà che si vuole modificare.

1. Selezionare **Salva** nel lato superiore destro della finestra per salvare il nuovo valore. Alcune proprietà possono richiedere un riavvio del servizio.

    ![Proprietà avanzate di Apache Pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Le impostazioni a livello di sessione eseguono l'override dei valori delle proprietà nel file `pig.properties`.

### <a name="tune-execution-engine"></a>Ottimizzare il motore di esecuzione

Per eseguire gli script di Pig, sono disponibili due motori di esecuzione: MapReduce e Tez. Tez è un motore ottimizzato ed è molto più veloce di MapReduce.

1. Per modificare il motore di esecuzione, nel riquadro **Advanced pig-properties** (Proprietà Pig avanzate) trovare la proprietà `exectype`.

1. Il valore predefinito è **MapReduce**. Impostarlo su **Tez**.

### <a name="enable-local-mode"></a>Abilitare la modalità locale

Come in Hive, la modalità locale viene usata per velocizzare i processi con quantità relativamente piccole di dati.

1. Per abilitare la modalità locale, impostare `pig.auto.local.enabled` su **true**. Il valore predefinito è false.

1. I processi con dati di input di dimensioni inferiori al valore della proprietà `pig.auto.local.input.maxbytes` sono considerati processi di piccole dimensioni. Il valore predefinito è 1 GB.

### <a name="copy-user-jar-cache"></a>Copiare la cache JAR dell'utente

Pig copia i file JAR necessari per le funzioni definite dall'utente in una cache distribuita per renderli disponibili per i nodi attività. Questi jar non cambiano di frequente. Se abilitata, l'impostazione `pig.user.cache.enabled` consente di inserire i file JAR in una cache per riutilizzarli per i processi eseguiti dallo stesso utente. Questa impostazione comporta un lieve aumento delle prestazioni del processo.

1. Per abilitarla, impostare `pig.user.cache.enabled` su true. Il valore predefinito è false.

1. Per impostare il percorso di base dei file JAR memorizzati nella cache, impostare `pig.user.cache.location` sul percorso di base. Il valore predefinito è `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Ottimizzare le prestazioni con le impostazioni della memoria

Le impostazioni della memoria seguenti consentono di ottimizzare le prestazioni di uno script di Pig.

* `pig.cachedbag.memusage`: Quantità di memoria assegnata a un contenitore. Un contenitore è una raccolta di tuple. Una tupla è un set ordinato di campi e un campo è costituito da dati. Se i dati in un contenitore superano la memoria specificata, vengono distribuiti su disco. Il valore predefinito è 0,2, che rappresenta il 20% della memoria disponibile. Questa memoria viene condivisa tra tutti i contenitori di un'applicazione.

* `pig.spill.size.threshold`: i contenitori con dimensioni superiori a questa soglia di distribuzione (in byte) vengono distribuiti su disco. Il valore predefinito è 5 MB.

### <a name="compress-temporary-files"></a>Comprimere i file temporanei

Pig genera file temporanei durante l'esecuzione dei processi. La compressione dei file temporanei produce un aumento delle prestazioni durante la lettura o la scrittura di file su disco. Per comprimere i file temporanei, si possono usare le impostazioni seguenti.

* `pig.tmpfilecompression`: se true, abilita la compressione dei file temporanei. Il valore predefinito è false.

* `pig.tmpfilecompression.codec`: codec di compressione da usare per comprimere i file temporanei. I codec di compressione consigliati sono LZO e snapy per un uso più basso della CPU.

### <a name="enable-split-combining"></a>Abilitare la combinazione per la suddivisione

Se abilitata, i file di piccole dimensioni vengono combinati per ottenere un numero inferiore di attività mappe, Questa impostazione migliora l'efficienza dei processi con molti file di piccole dimensioni. Per abilitarla, impostare `pig.noSplitCombination` su true. Il valore predefinito è false.

### <a name="tune-mappers"></a>Ottimizzare i mapper

Il numero di mapper viene controllato modificando la proprietà `pig.maxCombinedSplitSize`, Questa proprietà specifica le dimensioni dei dati che devono essere elaborati da una singola attività mappa. Il valore predefinito corrisponde alle dimensioni di blocco predefinite del file system. L'aumento di questo valore comporta un numero inferiore di attività del mapper.

### <a name="tune-reducers"></a>Ottimizzare i riduttori

Il numero di riduttori viene calcolato in base al parametro `pig.exec.reducers.bytes.per.reducer`. Il parametro specifica il numero di byte elaborati per riduttore. L'impostazione predefinita è 1 GB. Per limitare il numero massimo di riduttori, impostare la proprietà `pig.exec.reducers.max`. L'impostazione predefinita è 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Ottimizzazione di Apache HBase con l'interfaccia utente Web Ambari

La configurazione di Apache HBase viene modificata dalla scheda **configs di HBase** . Le sezioni seguenti descrivono alcune delle importanti impostazioni di configurazione che influiscono sulle prestazioni di HBase.

### <a name="set-hbase_heapsize"></a>Impostare HBASE_HEAPSIZE

Le dimensioni dell'heap HBase specificano, in megabyte, la quantità massima di heap che devono essere usati dai server di *area* e *master*. Il valore predefinito è 1.000 MB. Questo valore deve essere ottimizzato per il carico di lavoro del cluster.

1. Per modificarlo, passare al riquadro **Advanced HBase-env** (Ambiente HBase avanzato) nella scheda **Configs** (Configurazioni) e quindi trovare l'impostazione `HBASE_HEAPSIZE`.

1. Impostare il valore predefinito su 5.000 MB.

    ![' Apache Ambari HBase Memory heapsize '](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di letture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di letture.

#### <a name="block-cache-size"></a>Dimensioni della cache dei blocchi

La cache dei blocchi è la cache di lettura, le cui dimensioni sono controllate dal parametro `hfile.block.cache.size`. Il valore predefinito è 0,4, che corrisponde al 40% della memoria totale del server di area. La velocità delle letture casuali sarà direttamente proporzionale alle dimensioni della cache di blocchi.

1. Per modificare questo parametro, passare alla scheda **Settings** (Impostazioni) nella scheda **Configs** (Configurazioni) di HBase e quindi individuare **% of RegionServer Allocated to Read Buffers** (% di server di area allocata ai buffer di lettura).

    ![Dimensioni della cache del blocco di memoria Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Per modificare il valore, selezionare l'icona **Edit** (Modifica).

#### <a name="memstore-size"></a>Dimensioni del memstore

Tutte le modifiche vengono archiviate nel buffer di memoria, denominato *memstore*. Questo buffer aumenta la quantità totale di dati che possono essere scritti su disco in un'unica operazione. Consente inoltre di velocizzare l'accesso alle modifiche recenti. Le dimensioni del memstore sono definite dai due parametri seguenti:

* `hbase.regionserver.global.memstore.UpperLimit`: definisce la percentuale massima del server di area che può essere usata dal memstore combinato.

* `hbase.regionserver.global.memstore.LowerLimit`: definisce la percentuale minima del server di area che può essere usata dal memstore combinato.

Per ottimizzare le letture casuali, è possibile ridurre i limiti massimo e minimo del memstore.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Numero di righe recuperate quando si esegue l'analisi dal disco

L'impostazione `hbase.client.scanner.caching` definisce il numero di righe lette dal disco quando il metodo `next` viene chiamato su uno scanner.  Il valore predefinito è 100. Maggiore è il numero, meno saranno le chiamate remote eseguite dal client al server di area e le analisi saranno quindi più rapide. Questa impostazione, tuttavia, aumenterà anche il numero di richieste di memoria nel client.

![Numero di righe Apache HBase recuperate](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Non impostare il valore in modo tale che l'intervallo di tempo tra le chiamate al metodo next su uno scanner sia superiore al timeout dello scanner. La durata del timeout dello scanner è definita dalla proprietà `hbase.regionserver.lease.period`.

### <a name="optimize-write-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di scritture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di scritture.

#### <a name="maximum-region-file-size"></a>Dimensioni massime del file di area

HBase archivia i dati in un formato di file interno, denominato *HFile*. La proprietà `hbase.hregion.max.filesize` definisce le dimensioni di un singolo HFile per un'area.  Un'area viene divisa in due aree se la somma di tutti gli HFile di un'area è superiore al valore di questa impostazione.

![' Apache HBase HRegion max filesize '](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Maggiori sono le dimensioni del file di area, minore è il numero di suddivisioni. È possibile aumentare le dimensioni del file per determinare un valore che garantisca prestazioni in scrittura ottimali.

#### <a name="avoid-update-blocking"></a>Evitare il blocco degli aggiornamenti

* La proprietà `hbase.hregion.memstore.flush.size` definisce a quali dimensioni il memstore viene scaricato su disco. Le dimensioni predefinite sono pari a 128 MB.

* Il moltiplicatore di blocco Region HBase è `hbase.hregion.memstore.block.multiplier`definito da. Il valore predefinito è 4. Il valore massimo consentito è 8.

* HBase blocca gli aggiornamenti se il memstore è pari a (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Con i valori predefiniti delle dimensioni di flush e del moltiplicatore di blocco, gli aggiornamenti vengono bloccati quando le dimensioni del memstore sono pari a 128 * 4 = 512 MB. Per ridurre il conteggio dei blocchi degli aggiornamenti, aumentare il valore di `hbase.hregion.memstore.block.multiplier`.

![Moltiplicatore di blocco dell'area Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definire le dimensioni del memstore

Le dimensioni del memstore sono definite dai parametri `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit`. Se si impostano questi parametri sullo stesso valore, si riducono le pause tra un'operazione di scrittura e l'altra (aumentando anche la frequenza di flush) e migliorano le prestazioni in scrittura.

### <a name="set-memstore-local-allocation-buffer"></a>Impostare il buffer di allocazione locale del memstore

L'utilizzo del buffer di allocazione locale del memstore è determinato dalla proprietà `hbase.hregion.memstore.mslab.enabled`. Se abilitata (true), questa impostazione impedisce la frammentazione dell'heap durante un'operazione di scrittura intensiva. Il valore predefinito è true.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
