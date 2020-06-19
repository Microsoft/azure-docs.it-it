---
title: Copiare dati da Amazon Simple Storage Service (S3)
description: Informazioni su come copiare dati da Amazon Simple Storage Service (S3) in archivi dati di sink supportati usando Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 94de2f9043c8c86036331a2cce2d2720e8c9b423
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651004"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiare dati da Amazon Simple Storage Service usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versione corrente](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da Amazon Simple Storage Service (Amazon S3). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per informazioni sullo scenario di migrazione dei dati da Amazon S3 ad Archiviazione di Azure, vedere [Usare Azure Data Factory per eseguire la migrazione dei dati da Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Amazon S3 è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Attività Delete](delete-activity.md)

In particolare, il connettore Amazon S3 supporta la copia dei file così come sono e l'analisi dei file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md). È anche possibile scegliere di [mantenere i metadati dei file durante la copia](#preserve-metadata-during-copy). Il connettore usa [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) per autenticare le richieste a S3.

>[!TIP]
>È possibile usare il connettore Amazon S3 per copiare dati da **qualsiasi provider di archiviazione compatibile con S3**, ad esempio [Google Cloud Storage](connector-google-cloud-storage.md). Specificare l'URL del servizio corrispondente nella configurazione del servizio collegato.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per copiare i dati da Amazon S3, assicurarsi di avere le autorizzazioni indicate di seguito:

- **Per l'esecuzione di attività di copia**: `s3:GetObject` e `s3:GetObjectVersion` per operazioni di oggetti di Amazon S3.
- **Per la creazione di GUI di Data Factory**: `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` per le operazioni di bucket di Amazon S3 sono inoltre necessarie le autorizzazioni per operazioni come la connessione di test e l'esplorazione/navigazione dei percorsi dei file. Se non si vuole concedere queste autorizzazioni, ignorare la connessione di test nella pagina di creazione del servizio collegato e specificare il percorso direttamente nelle impostazioni del set di dati.

Per informazioni dettagliate sull'elenco completo delle autorizzazioni di Amazon S3 con tutti i dettagli in [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Specificare le autorizzazioni in un criterio).

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Amazon S3.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Amazon S3 sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AmazonS3**. | Sì |
| accessKeyId | ID della chiave di accesso segreta. |Sì |
| secretAccessKey | La stessa chiave di accesso segreta. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| serviceUrl | Specificare l'endpoint S3 personalizzato se si copiano dati da un provider di archiviazione compatibile con S3 diverso dal servizio ufficiale Amazon S3. Per copiare dati da Google Cloud Storage, ad esempio, specificare `https://storage.googleapis.com`. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!TIP]
>Specificare l'URL del servizio S3 personalizzato se si copiano dati da una risorsa di archiviazione compatibile con S3 diversa dal servizio ufficiale Amazon S3.

>[!NOTE]
>Questo connettore richiede le chiavi di accesso per l'account IAM per la copia di dati da Amazon S3. Le [credenziali di sicurezza temporanee](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) non sono supportate.
>

Esempio:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Amazon S3 nelle impostazioni `location` nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà type in `location` nel set di dati deve essere impostata su **AmazonS3Location**. | Sì      |
| bucketName | Il nome del bucket S3.                                          | Sì      |
| folderPath | Percorso della cartella nel bucket specificato. Se si intende usare un carattere jolly per filtrare le cartelle, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file nel bucket e nel percorso cartella specificati. Se si intende usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificarla nelle impostazioni dell'origine dell'attività. | No       |
| version | La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. Se non è specificato, verrà recuperata la versione più recente. |No |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per Amazon S3 nelle impostazioni `storeSettings` nell'origine di copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La proprietà type in `storeSettings` deve essere impostata su **AmazonS3ReadSettings**. | Sì                                                         |
| ***Individuare i file da copiare:*** |  |  |
| OPZIONE 1: percorso statico<br> | Copia dal percorso del bucket o della cartella/file specificato nel set di dati. Per copiare tutti i file da un bucket/una cartella, specificare anche `wildcardFileName` come `*`. |  |
| OPZIONE 2: prefisso S3<br>- prefix | Prefisso per il nome della chiave S3 nel bucket specificato configurato nel set di dati per filtrare i file S3 di origine. Vengono selezionate le chiavi S3 il cui nome inizia con `bucket_in_dataset/this_prefix`. Usa il filtro lato servizio S3, che offre prestazioni migliori rispetto al filtro con caratteri jolly. | No |
| OPZIONE 3: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly nel bucket specificato configurato nel set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 3: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly nel bucket e nel percorso folderPath/wildcardFolderPath specificati per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 3: un elenco di file<br>- fileListPath | Indica di copiare un determinato set di file. Puntare a un file di testo che include un elenco di file da copiare, un file per riga, che rappresenta il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [Esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive:*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono **true** (predefinito) e **false**.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| modifiedDatetimeStart    | Filtro di file basato sull'attributo: Ultima modifica. <br>I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                                          |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi alla risorsa di archiviazione. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No                                                          |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| bucket | Key | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in grassetto sono stati recuperati)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

Questa sezione descrive il comportamento risultante dall'uso del percorso di elenco file nell'origine dell'attività di copia.

Si supponga di disporre della struttura di cartelle di origine seguente e di voler copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Bucket: `bucket`<br>- Percorso cartella: `FolderA`<br><br>**Nell'origine dell'attività Copy:**<br>- Percorso elenco file: `bucket/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco di file fa riferimento a un file di testo nello stesso archivio dati che include un elenco di file da copiare, un file per riga con il percorso relativo del percorso configurato nel set di dati. |

## <a name="preserve-metadata-during-copy"></a>Mantenere i metadati durante la copia

Quando si copiano i file da Amazon S3 ad Azure Data Lake Storage Gen2/BLOB di Azure, è possibile scegliere di mantenere i metadati del file insieme ai dati. Altre informazioni in [Mantenere i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Proprietà dell'attività Delete

Per altre informazioni sulle proprietà, vedere [Attività Delete](delete-activity.md)

## <a name="legacy-models"></a>Modalità legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati così come sono per la compatibilità con le versioni precedenti. In futuro, è consigliabile usare il nuovo modello citato nelle sezioni precedenti, tenendo presente che l'interfaccia utente di creazione di Azure Data Factory è passata alla generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **AmazonS3Object** |Sì |
| bucketName | Il nome del bucket S3. Il filtro con caratteri jolly non è supportato. |Sì per l'attività Copy/Lookup, no per l'attività GetMetadata |
| Key | Il **nome o il filtro con caratteri jolly** della chiave dell'oggetto S3 nel bucket specificato. Si applica solo se la proprietà "prefix" non è specificata. <br/><br/>Il filtro con caratteri jolly è supportato sia per la parte della cartella che per la parte del nome file. I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Esempio 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). Usare `^` per applicare una sequenza di escape se il nome effettivo del file o della cartella include caratteri jolly o tale carattere escape. |No |
| prefix | Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo se la proprietà "key" non è specificata. |No |
| version | La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. Se non è specificato, verrà recuperata la versione più recente. |No |
| modifiedDatetimeStart | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro di file basato sull'attributo: Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati sono interessate dall'abilitazione di questa impostazione quando si intende applicare un filtro a grandi quantità di file. <br/><br/> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs-legacy.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono: **Optimal** (Ottimale) e **Fastest** (Più veloce). |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare **bucketName** per il bucket e **prefix** per la parte della cartella.<br>Per copiare un singolo file con un nome specifico, specificare **bucketName** per il bucket e **key** per la parte della cartella più il nome file.<br>Per copiare un subset di file in una cartella, specificare **bucketName** per il bucket e **key** per la parte della cartella più il filtro con caratteri jolly.

**Esempio: uso del prefisso**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Esempio: uso della chiave e della versione (facoltativo)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modello di origine dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **FileSystemSource** |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che se recursive è impostata su true e il sink è un archivio basato su file, la cartella o la sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono: **true** (predefinito), **false** | No |
| maxConcurrentConnections | Numero di connessioni simultanee per connettersi all'archivio dati. Valore da specificare solo quando si vuole limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
