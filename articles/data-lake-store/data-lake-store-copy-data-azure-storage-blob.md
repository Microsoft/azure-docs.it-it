---
title: Copiare i dati da BLOB di archiviazione di Azure a Data Lake Storage Gen1
description: Usare lo strumento AdlCopy per copiare i dati da BLOB di Archiviazione di Azure ad Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980090"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiare i dati da BLOB di Archiviazione di Azure ad Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 fornisce uno strumento da riga di comando, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), per copiare i dati dalle origini seguenti:

* Da BLOB di archiviazione di Azure in Data Lake Storage Gen1. Non è possibile usare AdlCopy per copiare i dati da Data Lake Storage Gen1 nei BLOB di archiviazione di Azure.
* Tra due account Data Lake Storage Gen1.

È anche possibile usare lo strumento AdlCopy in due modi diversi:

* **Autonomo**, in cui lo strumento usa le risorse di Data Lake Storage Gen1 per eseguire l'attività.
* **Tramite un account di Analisi Data Lake**, in cui le unità assegnate all'account di Analisi Data Lake vengono usate per eseguire l'operazione di copia. È consigliabile scegliere questa opzione se si intende eseguire le attività di copia in modo prevedibile.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Contenitore **BLOB di archiviazione di Azure** con alcuni dati.
* **Un account Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Introduzione a Azure Data Lake storage Gen1](data-lake-store-get-started-portal.md)
* **Data Lake Analytics account (facoltativo)** : per istruzioni su come creare un account data Lake Analytics, vedere [Introduzione a Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .
* **Lo strumento AdlCopy**. Installare lo [strumento AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintassi dello strumento AdlCopy

Usare la sintassi seguente per usare lo strumento AdlCopy

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

I parametri nella sintassi sono descritti di seguito:

| Opzione | Descrizione |
| --- | --- |
| Source (Sorgente) |Specifica il percorso dei dati di origine nel BLOB di Archiviazione di Azure. L'origine può essere un contenitore BLOB, un BLOB o un altro account Data Lake Storage Gen1. |
| Dest |Specifica la destinazione di Data Lake Storage Gen1 in cui eseguire la copia. |
| SourceKey |Specifica la chiave di accesso alle risorse di archiviazione per l'origine BLOB di Archiviazione di Azure. La chiave è necessaria solo se l'origine è un contenitore BLOB o un BLOB. |
| Account |**Facoltativo**. Scegliere questa opzione se si vuole usare l'account di Analisi Azure Data Lake per eseguire il processo di copia. Se si usa l'opzione /Account nella sintassi ma non si specifica un account di Analisi Data Lake, AdlCopy usa un account predefinito per eseguire il processo. Inoltre, se si sceglie questa opzione, è necessario aggiungere l'origine (BLOB di Archiviazione di Azure) e la destinazione (Azure Data Lake Storage Gen1) come origini dati per l'account di Data Lake Analytics. |
| Unità |Specifica il numero di unità di Analisi Data Lake che verranno usate per il processo di copia. Questa opzione è obbligatoria se si usa l'opzione **/Account** per specificare l'account di Analisi Data Lake. |
| Modello |Specifica un modello regex che indica quali BLOB o file copiare. AdlCopy usa la corrispondenza tra maiuscole e minuscole. Il criterio predefinito quando non viene specificato alcun modello consiste nel copiare tutti gli elementi. Non è consentito specificare più criteri file. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Usare AdlCopy (come file autonomo) per copiare i dati da un BLOB di Archiviazione di Azure

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
1. Eseguire il comando seguente per copiare un BLOB specifico dal contenitore di origine a una cartella di Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Ad esempio:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >La sintassi precedente specifica il file da copiare in una cartella dell'account Data Lake Storage Gen1. Se il nome della cartella specificato non esiste, lo strumento AdlCopy crea una cartella.

    Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Data Lake Storage Gen1. L'output visualizzato sarà simile al seguente:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. È anche possibile copiare tutti i BLOB da un contenitore all'account di Data Lake Storage Gen1 tramite il comando seguente:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Ad esempio:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si esegue una copia da un account di Archiviazione BLOB di Azure, è possibile che alcune operazioni siano limitate nell'ambito dell'archiviazione BLOB. In questo caso le prestazioni del processo di copia diminuiranno. Per altre informazioni sui limiti di Archiviazione BLOB di Azure, vedere i limiti di Archiviazione di Azure in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Usare AdlCopy (come file autonomo) per copiare i dati da un altro account di Data Lake Storage Gen1

È anche possibile usare AdlCopy per copiare dati tra due account Data Lake Storage Gen1.

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
1. Eseguire il comando seguente per copiare un file specifico da un account Data Lake Storage Gen1 a un altro.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Ad esempio:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > La sintassi precedente specifica il file da copiare in una cartella dell'account Data Lake Storage Gen1 di destinazione. Se il nome della cartella specificato non esiste, lo strumento AdlCopy crea una cartella.
   >
   >

    Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Data Lake Storage Gen1. L'output visualizzato sarà simile al seguente:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. Il comando seguente consente di copiare tutti i file da una cartella specifica dell'account Data Lake Storage Gen1 di origine in una cartella dell'account Data Lake Storage Gen1 di destinazione.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si usa AdlCopy come strumento autonomo, la copia viene eseguita su risorse condivise gestite da Azure. Le prestazioni che è possibile ottenere in questo ambiente dipendono dal carico del sistema e dalle risorse disponibili. Questa modalità è più adatta ai trasferimenti di piccole dimensioni eseguiti ad hoc. Quando si usa AdlCopy come strumento autonomo, non è necessario impostare alcun parametro.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Usare AdlCopy (con un account Data Lake Analytics) per copiare i dati

È anche possibile usare l'account di Data Lake Analytics per eseguire il processo AdlCopy per copiare dati da BLOB di Archiviazione di Azure a Data Lake Storage Gen1. In genere si sceglie questa opzione quando i dati da spostare sono nell'ordine di gigabyte e terabyte e si vuole una velocità effettiva delle prestazioni migliore e prevedibile.

Per usare l'account Data Lake Analytics con AdlCopy per eseguire la copia da un BLOB di Archiviazione di Azure, l'origine (BLOB di Archiviazione di Azure) deve essere aggiunta come origine dati per l'account Data Lake Analytics. Per istruzioni sull'aggiunta di origini dati aggiuntive all'account di Data Lake Analytics, vedere [Gestire Analisi Data Lake tramite il portale di Azure](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se si esegue la copia da un account Azure Data Lake Storage Gen1 come origine tramite un account Data Lake Analytics, non è necessario associare l'account Data Lake Storage Gen1 all'account Data Lake Analytics. È necessario associare l'archivio di origine con l'account Data Lake Analytics solo quando l'origine è un account di Archiviazione di Azure.
>
>

Eseguire il comando seguente per copiare da un BLOB di Archiviazione di Azure a un account Data Lake Storage Gen1 tramite un account Data Lake Analytics:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Ad esempio:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Analogamente, eseguire il comando seguente per copiare tutti i file da una cartella specifica dell'account Data Lake Storage Gen1 di origine in una cartella dell'account Data Lake Storage Gen1 di destinazione tramite l'account di Data Lake Analytics:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si copiano dati nell'ordine di terabyte, l'uso di AdlCopy con il proprio account di Azure Data Lake Analytics consente di ottenere prestazioni migliori e più prevedibili. Il parametro da impostare è il numero di unità di Azure Data Lake Analytics da usare per il processo di copia. L'aumento del numero di unità si traduce in un aumento delle prestazioni del processo di copia. Per ogni file da copiare, è possibile usare al massimo un'unità. Se si specificano più unità rispetto al numero di file da copiare, le prestazioni non aumentano.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Usare AdlCopy per copiare i dati usando la corrispondenza dei modelli

Questa sezione descrive come usare AdlCopy per copiare i dati da un'origine (nell'esempio di seguito un BLOB di Archiviazione di Azure) a un account Data Lake Storage Gen1 di destinazione tramite la corrispondenza dei modelli. Ad esempio, è possibile usare la procedura seguente per copiare tutti i file con estensione csv dal BLOB di origine alla destinazione.

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
1. Eseguire il comando seguente per copiare tutti i file con estensione csv da un BLOB specifico del contenitore di origine a una cartella di Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Ad esempio:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Fatturazione

* Se si usa lo strumento AdlCopy in modalità autonoma, verranno fatturati i costi di uscita per lo spostamento dei dati, se l'account di origine di Archiviazione di Azure non si trova nella stessa area dell'account Data Lake Storage Gen1.
* Se si usa lo strumento AdlCopy con l'account di Analisi Data Lake, verranno applicati i [Prezzi di Analisi Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) standard.

## <a name="considerations-for-using-adlcopy"></a>Considerazioni sull'uso di AdlCopy

* AdlCopy (per la versione 1.0.5) supporta la copia dei dati da origini che collettivamente contengono migliaia di file e cartelle. Tuttavia, se si verificano problemi durante la copia di un set di dati di grandi dimensioni, è possibile distribuire i file o le cartelle in sottocartelle diverse e utilizzare invece il percorso delle sottocartelle come origine.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerazioni sulle prestazioni per l'uso di AdlCopy

AdlCopy supporta la copia dei dati che contengono migliaia di file e cartelle. Tuttavia, se si verificano problemi durante la copia di un set di dati di grandi dimensioni, è possibile distribuire i file o le cartelle in sottocartelle più piccole. AdlCopy è stato creato per copie ad hoc. Se si tenta di copiare dati su base periodica, è consigliabile usare [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) che consente gestire completamente le operazioni di copia.

## <a name="release-notes"></a>Note sulla versione

* 1.0.13 - Se si copiano dati nello stesso account Azure Data Lake Storage Gen1 tramite più comandi adlcopy, non è più necessario immettere di nuovo le credenziali per ogni esecuzione. Adlcopy memorizza ora nella cache queste informazioni tra le diverse esecuzioni.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
