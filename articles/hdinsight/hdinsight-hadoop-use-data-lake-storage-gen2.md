---
title: Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: cd41c162d44320fc19af904118f202423e68e96a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195350"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight

Azure Data Lake Storage Gen2 è un servizio di archiviazione cloud dedicato a Big Data Analytics, basato sull'archiviazione BLOB di Azure. Data Lake Storage Gen2 combina le funzionalità di archiviazione BLOB e Azure Data Lake Storage Gen1 di Azure. Il servizio risultante offre funzionalità di Azure Data Lake Storage Gen1. Queste funzionalità includono: file system semantica, sicurezza a livello di directory e di file e adattabilità. Insieme alle funzionalità di archiviazione a più livelli, a disponibilità elevata e di ripristino di emergenza dell'archiviazione BLOB di Azure.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilità di Data Lake Storage Gen2

Data Lake Storage Gen2 è disponibile come opzione di archiviazione per quasi tutti i tipi di cluster HDInsight di Azure sia come predefinito che come account di archiviazione aggiuntivo. HBase, tuttavia, può avere un solo account Data Lake Storage Gen2.

Per un confronto completo delle opzioni di creazione del cluster con Data Lake Storage Gen2, vedere [confrontare le opzioni di archiviazione per l'uso con i cluster HDInsight di Azure](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Dopo aver selezionato Data Lake Storage Gen2 come **tipo di archiviazione primario**, non è possibile selezionare un account di data Lake storage Gen1 come risorsa di archiviazione aggiuntiva.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Creare un cluster con Data Lake Storage Gen2 tramite il portale di Azure

Per creare un cluster HDInsight che USA Data Lake Storage Gen2 per l'archiviazione, seguire questa procedura per configurare un account di Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente, se non è già disponibile.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **User assegnata** e fare clic su **identità gestita assegnata dall'utente**.
1. Fare clic su **Crea**.
1. Immettere un nome per l'identità gestita, selezionare la sottoscrizione, il gruppo di risorse e la località corretti.
1. Fare clic su **Crea**.

Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](hdinsight-managed-identities.md).

![Creare un'identità gestita assegnata dall'utente](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Creare un account di Data Lake Storage Gen2

Creare un account di archiviazione di Azure Data Lake Storage Gen2.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **storage** e fare clic su **account di archiviazione**.
1. Fare clic su **Crea**.
1. Nella schermata **Crea account di archiviazione** :
    1. Selezionare la sottoscrizione e il gruppo di risorse corretti.
    1. Immettere un nome per l'account Data Lake Storage Gen2.
    1. Fare clic sulla scheda **Avanzate** .
    1. Fare clic su **abilitato** accanto a **spazio dei nomi gerarchico** in **Data Lake storage Gen2**.
    1. Fare clic su **Rivedi e crea**.
    1. Fare clic su **Crea**

Per altre informazioni sulle altre opzioni durante la creazione dell'account di archiviazione, vedere [Guida introduttiva: creare un account di archiviazione Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurare le autorizzazioni per l'identità gestita nell'account Data Lake Storage Gen2

Assegnare l'identità gestita al ruolo di **proprietario dei dati del BLOB di archiviazione** nell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare l'account di archiviazione e quindi selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selezionare il pulsante **+ Aggiungi assegnazione ruolo** per aggiungere un nuovo ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo **proprietario dati BLOB di archiviazione** . Selezionare quindi la sottoscrizione a cui sono associati l'identità gestita e l'account di archiviazione. Individuare l'identità gestita assegnata dall'utente creata in precedenza. Infine, selezionare l'identità gestita, che verrà elencata in **membri selezionati**.

    ![Screenshot che mostra come assegnare un ruolo di controllo degli accessi in base al ruolo](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selezionare **Salva**. L'identità assegnata dall'utente selezionata è ora elencata sotto il ruolo selezionato.
1. Al termine della configurazione iniziale, è possibile creare un cluster tramite il portale. Il cluster deve trovarsi nella stessa area di Azure dell'account di archiviazione. Nella scheda **archiviazione** del menu di creazione del cluster selezionare le opzioni seguenti:

    * Per **tipo di archiviazione primario**selezionare **Azure Data Lake storage Gen2**.
    * In **account di archiviazione primario**cercare e selezionare l'account di archiviazione data Lake storage Gen2 appena creato.

    * In **Identity (identità**) selezionare l'identità gestita assegnata dall'utente appena creata.

        ![Impostazioni di archiviazione per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Per aggiungere un account di Data Lake Storage Gen2 secondario, a livello di account di archiviazione, è sufficiente assegnare l'identità gestita creata in precedenza al nuovo account di archiviazione Data Lake Storage Gen2 che si desidera aggiungere. Si noti che l'aggiunta di un account di Data Lake Storage Gen2 secondario tramite il pannello "account di archiviazione aggiuntivi" in HDInsight non è supportata.
    > * È possibile abilitare RA-GRS o RA-ZRS nell'account di archiviazione di Azure usato da HDInsight. Tuttavia, la creazione di un cluster con l'endpoint secondario RA-GRS o RA-ZRS non è supportata.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Creare un cluster con Data Lake Storage Gen2 tramite l'interfaccia della riga di comando di Azure

È possibile [scaricare un file modello di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e [scaricare un file di parametri di esempio](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Prima di usare il modello e il frammento di codice dell'interfaccia della riga di comando di Azure, sostituire i segnaposto seguenti con i valori corretti:

| Segnaposto | Descrizione |
|---|---|
| `<SUBSCRIPTION_ID>` | ID della sottoscrizione di Azure |
| `<RESOURCEGROUPNAME>` | Il gruppo di risorse in cui si vuole creare il nuovo cluster e l'account di archiviazione. |
| `<MANAGEDIDENTITYNAME>` | Nome dell'identità gestita a cui verranno concesse le autorizzazioni per l'account Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nuovo account di Azure Data Lake Storage Gen2 che verrà creato. |
| `<CLUSTERNAME>` | Il nome del cluster HDInsight. |
| `<PASSWORD>` | Password scelta per l'accesso al cluster tramite SSH e il dashboard di Ambari. |

Il frammento di codice seguente esegue i passaggi iniziali seguenti:

1. Accede all'account Azure.
1. Imposta la sottoscrizione attiva in cui verranno eseguite le operazioni di creazione.
1. Consente di creare un nuovo gruppo di risorse per le nuove attività di distribuzione.
1. Crea un'identità gestita assegnata dall'utente.
1. Aggiunge un'estensione all'interfaccia della riga di comando di Azure per usare le funzionalità per Data Lake Storage Gen2.
1. Crea un nuovo account Data Lake Storage Gen2 usando il `--hierarchical-namespace true` flag.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Accedere quindi al portale. Aggiungere la nuova identità gestita assegnata dall'utente al ruolo di **collaboratore dati BLOB di archiviazione** nell'account di archiviazione. Questo passaggio è descritto nel passaggio 3 in [uso del portale di Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Dopo aver assegnato il ruolo per l'identità gestita assegnata dall'utente, distribuire il modello usando il frammento di codice seguente.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Creare un cluster con Data Lake Storage Gen2 tramite Azure PowerShell

L'uso di PowerShell per creare un cluster HDInsight con Azure Data Lake Storage Gen2 attualmente non è supportato.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controllo di accesso per Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Quali tipi di autorizzazioni sono supportati da Data Lake Storage Gen2?

Data Lake Storage Gen2 usa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo (RBAC) e gli elenchi di controllo di accesso (ACL) di tipo POSIX. Data Lake Storage Gen1 supporta gli elenchi di controllo di accesso solo per il controllo dell'accesso ai dati.

RBAC usa le assegnazioni di ruolo per applicare in modo efficace set di autorizzazioni a utenti, gruppi ed entità servizio per le risorse di Azure. Tali risorse di Azure sono in genere vincolate alle risorse di primo livello (ad esempio gli account di archiviazione di Azure). Per archiviazione di Azure e anche Data Lake Storage Gen2, questo meccanismo è stato esteso alla risorsa file system.

 Per altre informazioni sulle autorizzazioni per i file con RBAC, vedere [controllo degli accessi in base al ruolo di Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Per altre informazioni sulle autorizzazioni per i file con ACL, vedere [elenchi di controllo di accesso su file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Ricerca per categorie controllare l'accesso ai dati in Data Lake Storage Gen2?

La capacità del cluster HDInsight di accedere ai file in Data Lake Storage Gen2 viene controllata tramite identità gestite. Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure. Con le identità gestite non è necessario registrare le entità servizio in Azure AD. Oppure mantenere le credenziali, ad esempio i certificati.

I servizi di Azure hanno due tipi di identità gestite, ovvero assegnati dal sistema e assegnati dall'utente. HDInsight Usa identità gestite assegnate dall'utente per accedere Data Lake Storage Gen2. Un `user-assigned managed identity` viene creato come una risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure.

Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. Per altre informazioni sulle identità gestite, vedere [come funzionano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Ricerca per categorie impostare le autorizzazioni per consentire agli utenti Azure AD di eseguire query sui dati in Data Lake Storage Gen2 usando hive o altri servizi?

Per impostare le autorizzazioni per consentire agli utenti di eseguire query sui dati, utilizzare Azure AD gruppi di sicurezza come entità assegnata negli ACL. Non assegnare direttamente le autorizzazioni di accesso ai file a singoli utenti o entità servizio. Con Azure AD gruppi di sicurezza per controllare il flusso di autorizzazioni, è possibile aggiungere e rimuovere utenti o entità servizio senza riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. È sufficiente aggiungere o rimuovere gli utenti dal gruppo di sicurezza di Azure AD appropriato. Gli ACL non vengono ereditati, pertanto per riapplicare gli ACL è necessario aggiornare l'ACL in ogni file e sottodirectory.

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

È possibile accedere ai file in Data Lake Storage Gen2 da un cluster HDInsight in diversi modi.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Uso del formato con percorso abbreviato**. Con questo approccio, si sostituisce il percorso fino alla radice del cluster con:

    ```
    abfs:///<file.path>/
    ```

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Esempi di accesso ai dati

Gli esempi sono basati su una [connessione SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo head del cluster. Negli esempi vengono utilizzati tutti e tre gli schemi URI. Sostituire `CONTAINERNAME` e `STORAGEACCOUNT` con i valori rilevanti

#### <a name="a-few-hdfs-commands"></a>Alcuni comandi HDFS

1. Creare un file nella risorsa di archiviazione locale.

    ```bash
    touch testFile.txt
    ```

1. Creare directory nell'archivio cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiare i dati dalla risorsa di archiviazione locale all'archiviazione cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Elencare il contenuto della directory nell'archivio cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Passaggi successivi

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integrazione di Azure HDInsight con Data Lake Storage Gen2 (anteprima) - Aggiornamento di ACL e sicurezza)
* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Esercitazione: Estrarre, trasformare e caricare dati usando Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
