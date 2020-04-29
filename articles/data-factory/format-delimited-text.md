---
title: Formato testo delimitato in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato testo delimitato in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417099"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato testo delimitato in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file di testo delimitati o scrivere i dati in un formato di testo delimitato**. 

Il formato testo delimitato è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere l'articolo [set di impostazioni](concepts-datasets-linked-services.md) . Questa sezione presenta un elenco delle proprietà supportate dal set di dati di testo delimitato.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **DelimitedText**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location`.  | Sì      |
| columnDelimiter  | Carattere o caratteri utilizzati per separare le colonne in un file. <br>Il valore predefinito è **virgola `,` **. Quando il delimitatore di colonna è definito come una stringa vuota che indica nessun delimitatore, l'intera riga viene considerata come una singola colonna.<br>Attualmente, il delimitatore di colonna come stringa vuota o multicarattere è supportato solo per il mapping del flusso di dati ma non per l'attività di copia.  | No       |
| rowDelimiter     | Singolo carattere o "\r\n" usato per separare le righe in un file. <br>Il valore predefinito è uno dei seguenti valori **in lettura: ["\r\n", ",", "\n"]** e **"\n" o "\r\n" in scrittura** mediante mapping del flusso di dati e dell'attività di copia rispettivamente. <br>Quando il delimitatore di riga è impostato su nessun delimitatore (stringa vuota), il delimitatore di colonna deve essere impostato come nessun delimitatore (stringa vuota), ovvero trattare l'intero contenuto come valore singolo.<br>Attualmente, il delimitatore di riga come stringa vuota è supportato solo per il mapping del flusso di dati, ma non per l'attività di copia. | No       |
| quoteChar        | Carattere singolo per citare i valori di colonna se contiene un delimitatore di colonna. <br>Il valore predefinito è **virgolette** `"`doppie. <br>Per il mapping del flusso `quoteChar` di dati, non può essere una stringa vuota. <br>Per l'attività di copia `quoteChar` , quando viene definito come una stringa vuota, significa che non sono presenti virgolette e il valore della colonna non `escapeChar` è racchiuso tra virgolette e viene usato per eseguire l'escape del delimitatore di colonna e di se stesso. | No       |
| escapeChar       | Carattere singolo per l'escape delle virgolette all'interno di un valore racchiuso tra virgolette.<br>Il valore predefinito è **la `\`barra rovesciata **. <br>Per il mapping del flusso `escapeChar` di dati, non può essere una stringa vuota. <br/>Per l' `quoteChar` attività di copia `escapeChar` , quando viene definito come stringa vuota, è necessario impostare anche come stringa vuota, nel qual caso assicurarsi che tutti i valori di colonna non contengano delimitatori. | No       |
| firstRowAsHeader | Specifica se trattare o rendere la prima riga come riga di intestazione con i nomi delle colonne.<br>I valori consentiti sono **true** e **false** (impostazione predefinita). | No       |
| nullValue        | Specifica la rappresentazione di stringa del valore null. <br>Il valore predefinito è una **stringa vuota**. | No       |
| encodingName     | Tipo di codifica utilizzato per leggere/scrivere file di test. <br>I valori consentiti sono i seguenti: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Nota il flusso di dati del mapping non supporta la codifica UTF-7. | No       |
| compressionCodec | Codec di compressione utilizzato per leggere/scrivere file di testo. <br>I valori consentiti sono **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **Snapper**o **LZ4**. Il valore predefinito non è compresso. <br>**Nota** attualmente l'attività di copia non supporta "blocco" & "LZ4" e il flusso di dati di mapping non supporta "ZipDeflate". <br>**Nota** quando si usa l'attività di copia per decomprimere i file ZipDeflate e scrivere nell'archivio dati sink basato su file, i file verranno estratti nella `<path specified in dataset>/<folder named as source zip file>/`cartella:. | No       |
| compressionLevel | Rapporto di compressione. <br>I valori consentiti sono **ottimali** o più **veloci**.<br>- Più **veloce:** L'operazione di compressione deve essere completata il più rapidamente possibile, anche se il file risultante non è compresso in modo ottimale.<br>- **Ottimale**: l'operazione di compressione deve essere compressa in modo ottimale, anche se il completamento dell'operazione richiede più tempo. Per maggiori informazioni, vedere l'argomento relativo al [livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Di seguito è riportato un esempio di set di dati di testo delimitato nell'archivio BLOB di Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di testo delimitati.

### <a name="delimited-text-as-source"></a>Testo delimitato come origine 

Nella sezione *** \*origine\* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSource**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di lettura testo delimitate** . | No       |
| storeSettings  | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate `storeSettings`in. | No       |

**Impostazioni di lettura testo delimitato** supportate `formatSettings`in:

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextReadSettings**. | Sì      |
| skipLineCount | Indica il numero di righe **non vuote** da ignorare durante la lettura di dati da file di input. <br>Se vengono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e quindi le informazioni dell'intestazione vengono lette dal file di input. | No       |

### <a name="delimited-text-as-sink"></a>Testo delimitato come sink

Nella sezione *** \*sink\* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà       | Descrizione                                                  | Obbligatoria |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **DelimitedTextSink**. | Sì      |
| formatSettings | Gruppo di proprietà. Vedere la tabella **delle impostazioni di scrittura testo delimitate** . |          |
| storeSettings  | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate `storeSettings`in.  | No       |

**Impostazioni di scrittura testo delimitate** supportate `formatSettings`in:

| Proprietà      | Descrizione                                                  | Obbligatoria                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Il tipo di formatSettings deve essere impostato su **DelimitedTextWriteSettings**. | Sì                                                   |
| fileExtension | Estensione di file utilizzata per assegnare un nome ai file di output `.csv`, `.txt`ad esempio. Deve essere specificato quando l' `fileName` oggetto non è specificato nel set di dati DelimitedText di output. Quando il nome del file viene configurato nel set di dati di output, verrà usato come nome del file di sink e l'impostazione dell'estensione di file verrà ignorata.  | Sì quando il nome file non è specificato nel set di dati di output |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Per informazioni dettagliate, vedere [trasformazione origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nel flusso di dati del mapping.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
