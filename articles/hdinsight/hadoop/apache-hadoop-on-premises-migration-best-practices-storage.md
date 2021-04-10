---
title: 'Archiviazione: eseguire la migrazione di Apache Hadoop locali ad Azure HDInsight'
description: Informazioni sulle procedure consigliate di archiviazione per la migrazione dei cluster Hadoop locali in Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715412"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Eseguire la migrazione di cluster di Apache Hadoop locali ad Azure HDInsight

Questo articolo contiene indicazioni per l'archiviazione dei dati nei sistemi Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Scegliere il sistema di archiviazione corretto per i cluster di HDInsight

La struttura di directory Apache Hadoop file system locale (HDFS) può essere ricreata nell'archivio BLOB di Azure o Azure Data Lake Storage. È quindi possibile eliminare in modo sicuro i cluster HDInsight usati per i calcoli senza perdere i dati degli utenti. Entrambi i servizi possono essere usati sia come file system predefinito che come file system per un cluster HDInsight. Il cluster HDInsight e l'account di archiviazione devono trovarsi nella stessa area del cluster.

### <a name="azure-storage"></a>Archiviazione di Azure

I cluster HDInsight possono usare il contenitore BLOB in Archiviazione di Azure come file system predefinito o come file system aggiuntivo. L'account di archiviazione di livello Standard è supportato per l'uso con i cluster HDInsight. Il livello Premier non è supportato. Il contenitore BLOB predefinito archivia informazioni specifiche del cluster come i log e la cronologia processo. La condivisione di un contenitore BLOB come file system predefinito per più cluster non è supportata.

Gli account di archiviazione definiti nel processo di creazione e le rispettive chiavi vengono archiviati in `%HADOOP_HOME%/conf/core-site.xml` nei nodi del cluster. Tali account sono accessibili anche nella sezione "Custom core site" nella configurazione HDFS dell'interfaccia utente di Ambari. La chiave dell'account di archiviazione viene crittografata per impostazione predefinita e uno script di decrittografia personalizzato viene usato per decrittografare le chiavi prima di essere passate al daemon Hadoop. I processi, inclusi Hive, MapReduce, lo streaming Hadoop e Pig, includono una descrizione degli account di archiviazione e dei metadati associati.

Archiviazione di Azure può essere replicata geograficamente. Sebbene la replica geografica offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Per accedere ai dati memorizzati in Archiviazione di Azure, è possibile usare i formati seguenti.

|Formato di accesso ai dati |Descrizione |
|---|---|
|`wasb:///`|Accesso allo spazio di archiviazione predefinito usando la comunicazione non crittografata.|
|`wasbs:///`|Accesso allo spazio di archiviazione predefinito usando la comunicazione crittografata.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Usato durante la comunicazione con un account di archiviazione non predefinito. |

[Obiettivi di scalabilità per gli account di archiviazione standard](../../storage/common/scalability-targets-standard-account.md) elenca i limiti correnti per gli account di archiviazione di Azure. Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, l'applicazione può essere creata per usare più account di archiviazione e quindi partizionare gli oggetti dati tra tali account.

[Analisi archiviazione di Azure](../../storage/common/storage-analytics.md) fornisce le metriche per tutti i servizi di archiviazione e portale di Azure può essere configurato per raccogliere le metriche da visualizzare tramite i grafici. È possibile creare avvisi per inviare una notifica quando sono state raggiunte le soglie per le metriche delle risorse di archiviazione.

Archiviazione di Azure offre l' [eliminazione temporanea per gli oggetti BLOB](../../storage/blobs/soft-delete-blob-overview.md) che consentono di ripristinare i dati quando vengono accidentalmente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione.

È possibile creare [snapshot di BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Uno snapshot è una versione di sola lettura di un BLOB e viene acquisito in un determinato momento per eseguire il backup di un BLOB. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

> [!Note]
> Per le versioni precedenti di distribuzioni di Hadoop locali che non dispongono del certificato "wasbs", è necessario che il certificato venga importato nell'archivio di attendibilità di Java.

I metodi seguenti consentono di importare i certificati nell'archivio di attendibilità di Java.

Scaricare il certificato TLS/SSL del BLOB di Azure in un file

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importare il file precedente nell'archivio di attendibilità Java su tutti i nodi

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Verificare che il certificato aggiunto sia presente nell'archivio di attendibilità

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Per altre informazioni, vedere gli articoli seguenti:

- [Usare archiviazione di Azure con i cluster HDInsight di Azure](../hdinsight-hadoop-use-blob-storage.md)
- [Obiettivi di scalabilità per gli account di archiviazione standard](../../storage/common/scalability-targets-standard-account.md)
- [Obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../../storage/blobs/scalability-targets.md)
- [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../../storage/blobs/storage-performance-checklist.md)
- [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorare un account di archiviazione nel portale di Azure](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementa il modello di controllo degli accessi in stile HDFS e POSIX. Offre un'integrazione di prima classe con Azure AD per il controllo di accesso con granularità fine. Non sono previsti limiti alle dimensioni dei dati che è possibile archiviare né alla capacità di eseguire analisi con architettura altamente parallela.

Per altre informazioni, vedere gli articoli seguenti:

- [Creare cluster HDInsight con Data Lake Storage Gen1 usando il portale di Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usare Data Lake Storage Gen1 con i cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 è l'offerta di archiviazione più recente. Unifica le funzionalità principali dalla prima generazione di Azure Data Lake Storage Gen1 con un endpoint file system compatibile con Hadoop direttamente integrato nell'archiviazione BLOB di Azure. Questo miglioramento combina i vantaggi in termini di scalabilità e di costi dell'archiviazione di oggetti con l'affidabilità e le prestazioni associate in genere a file system locali.

Azure Data Lake Storage generazione 2 è basata sull' [archiviazione BLOB di Azure](../../storage/blobs/storage-blobs-introduction.md) e consente di interfacciarsi con i dati usando i paradigmi di archiviazione di file System e di oggetti. Le funzionalità di [Azure Data Lake storage Gen1](../../data-lake-store/index.yml), ad esempio la semantica di file System, la sicurezza a livello di file e la scalabilità, sono combinate con l'archiviazione a più livelli, le funzionalità di disponibilità elevata/ripristino di emergenza e un ecosistema di SDK/strumenti di grandi dimensioni dall' [archiviazione BLOB di Azure](../../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 offre non solo tutte le qualità dell'archiviazione di oggetti, ma anche i vantaggi di un'interfaccia di file system ottimizzata per i carichi di lavoro di analisi.

Una funzionalità fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](../../storage/blobs/data-lake-storage-namespace.md) al servizio di archiviazione BLOB, che consente di organizzare gli oggetti e i file in una gerarchia di directory per l'accesso ai dati ad alte prestazioni. La struttura gerarchica consente che operazioni quali la ridenominazione o l'eliminazione di una directory siano singole operazioni atomiche sui metadati della directory anziché operazioni di enumerazione ed elaborazione di tutti gli oggetti che condividono il prefisso del nome della directory.

In passato, le analisi basate sul cloud imponevano il raggiungimento di un compromesso tra prestazioni, gestione e sicurezza. Le funzionalità principale di Azure Data Lake Storage (ADLS) Gen2 sono le seguenti:

- **Accesso compatibile con Hadoop**: Azure Data Lake storage Gen2 consente di gestire i dati e di accedervi esattamente come si farebbe con un [Hadoop Distributed file System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Il nuovo [driver ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) è disponibile all'interno di tutti gli ambienti Apache Hadoop inclusi in [Azure HDInsight](../index.yml). Tale driver consente di accedere ai dati archiviati in Data Lake Storage Gen2.

- **Un superset di autorizzazioni POSIX**: il modello di protezione per Data Lake Gen2 supporta completamente l'elenco di controllo di accesso e le autorizzazioni POSIX oltre ad una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite gli strumenti di amministrazione o i framework come Hive e Spark.

- **Economicamente conveniente**: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Con la transizione dei dati attraverso il ciclo di vita completo, le tariffe di fatturazione cambiano per ridurre i costi tramite funzionalità predefinite, ad esempio il [ciclo di vita dell'archiviazione BLOB di Azure](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Funziona con strumenti di archiviazione BLOB, framework e app**: Data Lake Storage Gen2 continua a funzionare con l'ampia gamma di strumenti, framework e app attualmente esistenti per l'archiviazione BLOB di Azure.

- **Driver ottimizzato**: il driver del file System BLOB di Azure (ABFS) è [ottimizzato in modo specifico](../../storage/blobs/data-lake-storage-abfs-driver.md) per Big Data Analytics. Le API REST corrispondenti vengono rilevate tramite l'endpoint dfs, ovvero dfs.core.windows.net.

Per accedere ai dati memorizzati in ADLS Gen2, è possibile usare i formati seguenti.
- `abfs:///`: Consente di accedere all'istanza predefinita di Data Lake Storage per il cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Usato durante la comunicazione con un'istanza di Data Lake Storage non predefinita.

Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione ad Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Driver ABFS (Azure Blob File System): un driver di Archiviazione di Azure dedicato per Hadoop](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Proteggere le chiavi di Archiviazione di Azure nella configurazione di cluster Hadoop locali

Le chiavi di archiviazione di Azure che vengono aggiunte ai file di configurazione di Hadoop stabiliscono la connettività tra HDFS locali e l'archiviazione BLOB di Azure. Tali chiavi possono essere protette tramite la crittografia con il framework di provider di credenziali di Hadoop. Dopo essere state crittografate, le chiavi possono essere archiviate ed è possibile accedervi in modo sicuro.

**Per eseguire il provisioning di credenziali:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Per aggiungere il percorso del provider precedente al file core-site.xml oppure alla configurazione Ambari nella sezione Custom core-site:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> La proprietà del percorso del provider può anche essere aggiunta anche alla riga di comando distcp anziché archiviare la chiave a livello di cluster nel file core-site.xml, come indicato di seguito:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Limitare l'accesso ai dati di archiviazione di Azure con SAS

Per impostazione predefinito, HDInsight ha accesso completo ai dati negli account di Archiviazione di Azure associati al cluster. Le firme di accesso condiviso per il contenitore BLOB possono essere usate per limitare l'accesso ai dati, ad esempio per concedere agli utenti l'accesso in sola lettura.

### <a name="using-the-sas-token-created-with-python"></a>Uso del token di firma di accesso condiviso creato con Python

1. Aprire il file [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e modificare i valori seguenti:

    |Proprietà token|Descrizione|
    |---|---|
    |policy_name|nome da usare per i criteri archiviati da creare.|
    |storage_account_name|nome dell'account di archiviazione.|
    |storage_account_key|Chiave per l'account di archiviazione.|
    |storage_container_name|contenitore nell'account di archiviazione a cui si vuole limitare l'accesso.|
    |example_file_path|percorso di un file caricato nel contenitore.|

2. Al file SASToken.py sono associate le autorizzazioni `ContainerPermissions.READ + ContainerPermissions.LIST` e possono essere modificate in base al caso d'uso.

3. Eseguire lo script come indicato di seguito: `python SASToken.py`

4. Al termine dello script viene visualizzato un token di firma di accesso condiviso simile al testo seguente: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Per limitare l'accesso a un contenitore con firma di accesso condiviso, aggiungere una voce personalizzata alla configurazione core-site per il cluster nella proprietà Ambari HDFS Configs Advanced Custom core-site Add.

6. Usare i valori seguenti per i campi **Key** e **Value**:

    **Chiave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **valore**: chiave SAS restituita dall'applicazione Python del passaggio 4 precedente.

7. Fare clic sul pulsate **Add** per salvare la chiave e il valore, quindi fare clic sul pulsante **Save** per salvare le modifiche alla configurazione. Quando richiesto, aggiungere una descrizione della modifica, ad esempio "aggiunta di accesso alle risorse di archiviazione per le firme di accesso condiviso", e quindi fare clic su **Save** (Salva).

8. Nell'interfaccia utente Web di Ambari selezionare HDFS dall'elenco a sinistra e quindi selezionare **Restart All Affected** (Reimposta tutti gli interessati) dall'elenco a discesa Service Actions (Azioni servizio) a destra. Quando viene chiesto, selezionare **Confirm restart all** (Conferma riavvio di tutti).

9. Ripetere il processo per MapReduce2 e YARN.

Esistono tre aspetti importanti da ricordare sull'uso dei token SAS in Azure:

1. Quando si creano i token di firma di accesso condiviso con autorizzazioni di "READ + LIST", gli utenti che hanno accesso al contenitore BLOB con questo token non sono in grado di scrivere né di eliminare dati. Gli utenti che hanno accesso al contenitore BLOB con questo token di firma di accesso condiviso e provano a eseguire un'operazione di scrittura o eliminazione, ricevono un messaggio analogo a `"This request is not authorized to perform this operation"`.

2. Quando vengono generati i token di firma di accesso condiviso con le autorizzazioni `READ + LIST + WRITE` (solo per limitare `DELETE` solo), comandi come `hadoop fs -put` scrivono prima in un file `\_COPYING\_` e quindi provano a rinominare il file. Questa operazione HDFS esegue il mapping in un'operazione `copy+delete` per WASB. Poiché l' `DELETE` autorizzazione non è stata specificata, il "put" ha esito negativo. L'operazione `\_COPYING\_` è una funzionalità Hadoop pensata per offrire controllo della concorrenza. Attualmente non è possibile limitare solo l'operazione "DELETE" senza influire sulle operazioni di "scrittura".

3. Sfortunatamente, il provider di credenziali Hadoop e il provider di chiavi di decrittografia (ShellDecryptionKeyProvider) attualmente non funzionano con i token di firma di accesso condiviso e pertanto non possono attualmente essere protetti dalla visibilità.

Per altre informazioni, vedere [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati in HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Usare la crittografia e la replica dei dati

Tutti i dati scritti in Archiviazione di Azure vengono crittografati automaticamente con la [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md). I dati nell'account di archiviazione di Azure vengono sempre replicati per la disponibilità elevata. Quando si crea un account di archiviazione, è possibile scegliere una delle opzioni di replica seguenti:

- [Archiviazione con ridondanza locale (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Archiviazione con ridondanza della zona (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Archiviazione con ridondanza geografica (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Archiviazione con ridondanza geografica e accesso in lettura](../../storage/common/storage-redundancy.md)

Archiviazione di Azure fornisce l'archiviazione con ridondanza locale (con ridondanza locale), ma è anche necessario copiare i dati critici in un altro account di archiviazione di Azure in un'altra area con una frequenza allineata alle esigenze del piano di ripristino di emergenza. Esistono diversi metodi per copiare i dati, tra cui [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)o [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). È anche consigliabile applicare i criteri di accesso per l'account di archiviazione di Azure per evitare l'eliminazione accidentale.

Per altre informazioni, vedere gli articoli seguenti:

- [Replica di Archiviazione di Azure](../../storage/common/storage-redundancy.md)
- [Linee guida di emergenza per Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Aggiungere altri account di archiviazione di Azure al cluster

Durante il processo di creazione di HDInsight, viene scelto un account di archiviazione di Azure, Azure Data Lake Storage Gen1 o Azure Data Lake Storage Gen2 come file system predefinito. Oltre all'account di archiviazione predefinito, durante il processo di creazione o dopo la creazione di un cluster è possibile aggiungere altri account di archiviazione della stessa sottoscrizione di Azure o di sottoscrizioni di Azure diverse.

Altri account di archiviazione possono essere aggiunti in uno in modi seguenti:
- Usare Ambari HDFS Config Advanced Custom core-site Aggiungere il nome dell'account di archiviazione e la chiave Riavviare i servizi
- Usare l'[azione script](../hdinsight-hadoop-add-storage.md) passando il nome e la chiave dell'account di archiviazione

> [!Note]
> Nei casi d'uso validi, i limiti di archiviazione di Azure possono essere aumentati tramite una richiesta effettuata al [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passaggi successivi

Leggere il prossimo articolo di questa serie: [procedure consigliate per la migrazione dei dati per la migrazione da sito locale a Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).