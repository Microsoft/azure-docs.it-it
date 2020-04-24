---
title: Usare Data Lake Storage Gen1 con Hadoop in Azure HDInsight
description: Informazioni su come eseguire query sui dati da Azure Data Lake Storage Gen1 e archiviare i risultati dell'analisi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251081"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Usare Data Lake Storage Gen1 con i cluster Azure HDInsight

> [!Note]
> Distribuire i nuovi cluster HDInsight usando [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) per ottenere prestazioni migliori e nuove funzionalità.

Per analizzare i dati nel cluster HDInsight, è possibile archiviare i dati in [archiviazione di Azure](../storage/common/storage-introduction.md), [Azure Data Lake storage generazione 1](../data-lake-store/data-lake-store-overview.md)o [Azure Data Lake storage generazione 2](../storage/blobs/data-lake-storage-introduction.md). Tutte le opzioni di archiviazione consentono l'eliminazione sicura dei cluster HDInsight utilizzati per i calcoli, senza perdita di dati utente.

Questo articolo illustra come funziona Data Lake Storage Gen1 con i cluster HDInsight. Per sapere come usare Archiviazione di Azure con i cluster HDInsight, vedere [Usare Archiviazione di Azure con cluster Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> L'accesso a Data Lake Storage Gen1 avviene sempre tramite un canale sicuro e quindi non è presente un nome di schema del file system `adls`. Viene usato sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilità per i cluster HDInsight

Apache Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.5 e versioni successive è possibile selezionare Archiviazione di Azure o Azure Data Lake Storage Gen1 come file system predefinito, con alcune eccezioni. Si noti che il cluster e l'account di archiviazione devono essere ospitati nella stessa area.

I cluster HDInsight possono usare Data Lake Storage Gen1 in due modi:

* Come risorsa di archiviazione predefinita
* Come risorsa di archiviazione aggiuntiva, con BLOB del servizio di archiviazione di Azure come risorsa predefinita.

Attualmente, solo alcuni dei tipi/versioni di cluster HDInsight supportano l'uso di Data Lake Storage Gen1 come account di archiviazione predefinito e aggiuntivo:

| Tipo di cluster HDInsight | Data Lake Storage Gen1 come risorsa di archiviazione predefinita | Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva| Note |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versione 4,0 | No | No |ADLS Gen1 non è supportato con HDInsight 4,0 |
| HDInsight versione 3.6 | Sì | Sì | Ad eccezione di HBase|
| HDInsight versione 3.5 | Sì | Sì | Ad eccezione di HBase|
| HDInsight versione 3.4 | No | Sì | |
| HDInsight versione 3.3 | No | No | |
| HDInsight versione 3.2 | No | Sì | |
| Storm | | |È possibile usare Data Lake Storage Gen1 per scrivere dati da una topologia Storm. È anche possibile usare Data Lake Storage per archiviare dati di riferimento che possono essere letti da una topologia Storm.|

> [!WARNING]  
> HDInsight HBase non è supportato con Azure Data Lake Storage Gen1

L'uso di Data Lake Storage Gen1 come account di archiviazione aggiuntivo non influisce sulle prestazioni o sulla possibilità di leggere o scrivere in archiviazione di Azure dal cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita

Quando si distribuisce HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione predefinita, i file correlati al cluster vengono archiviati in `adl://mydatalakestore/<cluster_root_path>/`, dove `<cluster_root_path>` è il nome di una cartella creata in Data Lake Storage. Specificando un percorso radice per ogni cluster, è possibile usare lo stesso account Data Lake Storage per più di un cluster. Pertanto, è possibile disporre di una configurazione in cui:

* Cluster1 può usare il percorso `adl://mydatalakestore/cluster1storage`
* Cluster2 può usare il percorso `adl://mydatalakestore/cluster2storage`

Si noti che entrambi i cluster usano lo stesso account di Data Lake Storage Gen1, **mydatalakestore**. Ogni cluster ha accesso al proprio file system radice in Data Lake Storage. Più nello specifico, l'esperienza di distribuzione del Portale di Azure richiede di utilizzare un nome di cartella come **/clusters/\<clustername>** per il percorso radice.

Per poter usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita, è necessario concedere all'entità servizio l'accesso ai percorsi seguenti:

* Radice dell'account Data Lake Storage Gen1.  ad esempio adl://mydatalakestore/.
* Cartella per tutte le cartelle del cluster,  ad esempio adl://mydatalakestore/clusters.
* Cartella per il cluster,  ad esempio adl://mydatalakestore/clusters/cluster1storage.

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, consultare la sezione Configurare l'accesso a Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Estrazione di un certificato da Azure Key Vault per l'uso nella creazione di cluster

Se si desidera configurare Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita per un nuovo cluster e il certificato per l'entità servizio viene archiviato in Azure Key Vault, è necessario eseguire alcuni passaggi aggiuntivi per convertire il certificato nel formato corretto. I frammenti di codice seguenti mostrano come eseguire la conversione.

Prima di tutto, scaricare il certificato da Key Vault ed estrarre il `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Convertire quindi il `SecretValueText` in un certificato.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

È quindi possibile usare il `$identityCertificate` per distribuire un nuovo cluster, come illustrato nel frammento di codice seguente:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Usare Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva

È possibile usare Data Lake Storage Gen1 anche come risorsa di archiviazione aggiuntiva per il cluster. In questi casi la risorsa di archiviazione predefinita del cluster può essere un BLOB del servizio di archiviazione di Azure o un account Data Lake Storage. Se si eseguono processi HDInsight sui dati archiviati in Data Lake Storage come risorsa di archiviazione aggiuntiva, è necessario usare il percorso completo dei file. Ad esempio:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Si noti che al momento non esiste alcun **cluster_root_path** nell'URL. Questo perché Data Lake Storage non è un archivio predefinito in questo caso, quindi è sufficiente fornire il percorso dei file.

Per poter usare Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, è necessario semplicemente concedere all'entità servizio l'accesso ai percorsi in cui sono archiviati i file.  Ad esempio:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, consultare la sezione Configurare l'accesso a Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Usare più account di Data Lake Storage

Le operazioni di aggiunta di un account Data Lake Storage come risorsa di archiviazione aggiuntiva e di aggiunta di più account Data Lake Storage vengono eseguite assegnando al cluster HDInsight l'autorizzazione sui dati in uno o più account Data Lake Storage. Consultare la sezione Configurare l'accesso a Data Lake Storage.

## <a name="configure-data-lake-storage-access"></a>Configurare l'accesso a Data Lake Storage

Per configurare l'accesso a Data Lake Storage dal cluster HDInsight, è necessario disporre di un'entità servizio di Azure Active Directory (Azure AD). Solo un amministratore di Azure AD può creare un'entità servizio. L'entità servizio deve essere creata con un certificato. Per altre informazioni, vedi [Avvio rapido: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) e [Creare un'entità servizio con certificato autofirmato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se si prevede di usare Azure Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva per il cluster HDInsight, è consigliabile aggiungere questa risorsa durante la creazione del cluster, come descritto in questo articolo. L'aggiunta di Azure Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva a un cluster HDInsight esistente non è supportata.

Per ulteriori informazioni sulle nozioni di base del modello di controllo di accesso per Data Lake Storage Gen1, vedere [controllo di accesso nella Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

Esistono diversi modi per accedere ai file in Data Lake Storage da un cluster HDInsight.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Uso del formato con percorso abbreviato**. Con questo approccio, si sostituisce il percorso fino alla radice del cluster con:

    ```
    adl:///<file path>
    ```

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono utilizzati tutti e tre gli schemi URI. Sostituire `DATALAKEACCOUNT` e `CLUSTERNAME` con i valori pertinenti.

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi HDFS

1. Creare un file semplice nell'archivio locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archivio cluster.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dalla risorsa di archiviazione locale all'archiviazione cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archivio cluster.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Creazione di una tabella hive

Per scopi illustrativi, vengono visualizzati tre percorsi di file. Per l'esecuzione effettiva, utilizzare solo una delle `LOCATION` voci.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identificare il percorso di archiviazione da Ambari

Per identificare il percorso completo dell'archivio predefinito configurato, passare a **HDFS** > **configs** e immettere `fs.defaultFS` nella casella Filter input.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Creare cluster HDInsight con accesso a Data Lake Storage Gen1

Usare i collegamenti seguenti per informazioni dettagliate su come creare cluster HDInsight con accesso a Data Lake Storage Gen1.

* [Uso del portale](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Uso di PowerShell con Data Lake Storage Gen1 come risorsa di archiviazione predefinita](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Uso di PowerShell con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Uso di modelli di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aggiornare il certificato di HDInsight per l'accesso a Data Lake Storage Gen1

Il codice di PowerShell di esempio seguente legge un certificato da un file locale o da Azure Key Vault e aggiorna il cluster HDInsight con il nuovo certificato per accedere ad Azure Data Lake Storage Gen1. Fornire il proprio nome del cluster HDInsight, nome del gruppo di risorse, ID di sottoscrizione, ID dell'app e il percorso locale al certificato. Scrivere la password quando richiesto.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare Azure Data Lake Storage Gen1, compatibile con Hadoop Distributed File System (HDFS), con HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedi:

* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Guida introduttiva: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Creare un cluster HDInsight per l'uso di Data Lake Storage Gen1 con Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Caricare i dati in HDInsight](hdinsight-upload-data.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Esercitazione: Estrarre, trasformare e caricare dati usando Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
