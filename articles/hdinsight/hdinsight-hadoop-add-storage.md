---
title: Aggiungere altri account di archiviazione di Azure a HDInsightAdd additional Azure Storage accounts to HDInsight
description: Informazioni su come aggiungere altri account di Archiviazione di Azure a un cluster HDInsight esistente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206708"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Aggiungere altri account di archiviazione a HDInsight

Informazioni su come usare le azioni script per aggiungere altri account di Archiviazione di Azure a HDInsight.Learn how to use script actions to add additional Azure Storage *accounts* to HDInsight. I passaggi descritti in questo documento aggiungono un *account* di archiviazione a un cluster HDInsight esistente. Questo articolo si applica agli *account* di archiviazione (non all'account di archiviazione cluster predefinito) e non all'archiviazione aggiuntiva, ad esempio [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e Azure Data Lake Storage [Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

> [!IMPORTANT]  
> Le informazioni contenute in questo documento riguardano l'aggiunta di account di archiviazione aggiuntivi a un cluster dopo che è stato creato. Per informazioni sull'aggiunta di account di archiviazione durante la creazione del cluster, vedere [Configurare cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Hadoop in HDInsight.A Hadoop cluster on HDInsight. Vedere [Introduzione a HDInsight su Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chiave dell'account di archiviazione. Vedere Gestire le chiavi di [accesso dell'account di archiviazione.](../storage/common/storage-account-keys-manage.md)
* Se si usa PowerShell, è necessario il modulo A.A.If using PowerShell, you'll need the A A.A.If using PowerShell, you'll need the A A  Vedere [Panoramica di Azure PowerShell.See Overview of Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Funzionamento

Durante l'elaborazione, lo script esegue le azioni seguenti:

* Se la chiave dell'account di archiviazione esiste già nella configurazione del file core-site.xml per il cluster, lo script viene chiuso e non vengono eseguite altre azioni.

* Verifica che l'account di archiviazione esista e sia accessibile tramite la chiave.

* Crittografa la chiave mediante le credenziali del cluster,

* Aggiunge l'account di archiviazione al file core-site.xml.

* Arrestare e riavviare i servizi [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) e [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html). L'arresto e l'avvio di questi servizi consente di usare il nuovo account di archiviazione.

> [!WARNING]  
> L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="add-storage-account"></a>Aggiungere l’account di archiviazione

Utilizzare [l'azione script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) per applicare le modifiche con le considerazioni seguenti:Use Script Action to apply the changes with the following considerations:

|Proprietà | valore |
|---|---|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo/i di nodo|Head|
|Parametri|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opzionale)|

* `ACCOUNTNAME`è il nome dell'account di archiviazione da aggiungere al cluster HDInsight.
* `ACCOUNTKEY`è la chiave `ACCOUNTNAME`di accesso per .
* `-p` è facoltativo. Se specificato, la chiave non è crittografata e viene archiviata nel file core-site.xml come testo normale.

## <a name="verification"></a>Verifica

Quando si visualizza il cluster HDInsight nel portale di Azure, selezionare la voce __Account di archiviazione__ in __Proprietà__ non visualizza gli account di archiviazione aggiunti tramite questa azione di script. Azure PowerShell e l'interfaccia della riga di comando di Azure non visualizzano neanche l'account di archiviazione aggiuntivo. Le informazioni di archiviazione non vengono visualizzate perché lo `core-site.xml` script modifica solo la configurazione per il cluster. Queste informazioni non vengono usate durante il recupero delle informazioni del cluster usando le API di gestione di Azure.This information isn't used when retrieving the cluster information using Azure management APIs.

Per verificare l'archiviazione aggiuntiva, utilizzare uno dei metodi illustrati di seguito:

### <a name="powershell"></a>PowerShell

Lo script restituirà i nomi degli account di archiviazione associati al cluster specificato. Sostituire `CLUSTERNAME` con il nome effettivo del cluster e quindi eseguire lo script.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Passare a **HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Osservare i tasti `fs.azure.account.key`che iniziano con . Il nome dell'account farà parte della chiave, come illustrato in questa immagine di esempio:

   ![verifica tramite Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Rimuovere account di archiviazione

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Passare a **HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Rimuovere le seguenti chiavi:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Dopo aver rimosso queste chiavi e salvato la configurazione, è necessario riavviare Oozie, Yarn, MapReduce2, HDFS e Hive uno per uno.

## <a name="known-issues"></a>Problemi noti

### <a name="storage-firewall"></a>Firewall di archiviazione

Se si sceglie di proteggere l'account di archiviazione con le restrizioni **Firewall e reti virtuali** in Reti **selezionate**, assicurarsi di abilitare l'eccezione Consenti servizi **Microsoft attendibili...** in modo che HDInsight possa accedere all'account di archiviazione.

### <a name="unable-to-access-storage-after-changing-key"></a>Non è possibile accedere alla risorsa di archiviazione dopo la modifica della chiave

Se si modifica la chiave per un account di archiviazione, HDInsight non potrà più accedere all'account di archiviazione. HDInsight usa una copia memorizzata nella cache della chiave in core-site.xml per il cluster. Questa copia memorizzata nella cache deve essere aggiornata in modo che corrisponda alla nuova chiave.

La ripetizione dell'esecuzione dell'azione script __non__ aggiorna la chiave, perché lo script verifica se esiste già una voce per l'account di archiviazione. Se esiste già una voce, non apporta alcuna modifica.

Per risolvere questo problema:  
1. Rimuovere l'account di archiviazione.
1. Aggiungere l'account di archiviazione.

> [!IMPORTANT]  
> La rotazione della chiave di archiviazione per l'account di archiviazione primario collegato a un cluster non è supportata.

### <a name="poor-performance"></a>Prestazioni non ottimali

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che le prestazioni non siano ottimali. L'accesso ai dati in un'area diversa comporta l'invio di traffico di rete all'esterno del data center di Azure di un'area specifica e la trasmissione tramite Internet pubblico e ciò può introdurre latenza.

### <a name="additional-charges"></a>Costi aggiuntivi

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che la fatturazione di Azure includa addebiti di uscita aggiuntivi. Viene applicato un addebito di uscita quando i dati escono dal data center di un'area, anche se il traffico è destinato a un altro data center di Azure in un'area diversa.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come aggiungere altri account di archiviazione a un cluster HDInsight esistente. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md)
