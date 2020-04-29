---
title: Copiare e trasformare i dati nell'archivio BLOB di Azure
description: Informazioni su come copiare dati da e verso l'archiviazione BLOB e trasformare i dati nell'archiviazione BLOB usando Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415475"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copiare e trasformare i dati nell'archiviazione BLOB di Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-azure-blob-connector.md)
> * [Versione corrente](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in archiviazione BLOB di Azure e usare il flusso di dati per trasformare i dati nell'archiviazione BLOB di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

>[!TIP]
>Per informazioni sullo scenario di migrazione a data Lake o data warehouse, vedere [usare Azure Data Factory per eseguire la migrazione dei dati dal data Lake o data warehouse ad Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore BLOB di Azure è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
- [Elimina attività](delete-activity.md)

Per l'attività di copia, questo connettore di archiviazione BLOB supporta:

- La copia di BLOB in e da account di archiviazione di Azure per utilizzo generico e servizi di Archiviazione BLOB ad accesso frequente o sporadico. 
- La copia di BLOB tramite autenticazione con chiave dell'account, firma di accesso condiviso del servizio, entità servizio o identità gestite per le risorse di Azure.
- La copia di BLOB da BLOB in blocchi, di aggiunta o di pagine e la copia di dati solo in BLOB in blocchi.
- La copia di BLOB così come sono o l'analisi o generazione di BLOB con i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).
- [Mantieni i metadati del file durante la copia](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Se si Abilita l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** nelle impostazioni del firewall di archiviazione di Azure e si vuole usare il runtime di integrazione di Azure per connettersi all'archiviazione BLOB, è necessario usare [l'autenticazione dell'identità gestita](#managed-identity).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per Archiviazione BLOB.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il connettore BLOB di Azure supporta i seguenti tipi di autenticazione. Vedere la sezione corrispondente per le informazioni dettagliate:

- [Autenticazione basata sulla chiave dell'account](#account-key-authentication)
- [Autenticazione con firma di accesso condiviso](#shared-access-signature-authentication)
- [Autenticazione dell'entità servizio](#service-principal-authentication)
- [Identità gestite per l'autenticazione delle risorse di Azure](#managed-identity)

>[!NOTE]
>Quando si usa la polibase per caricare i dati in SQL Data Warehouse, se l'archiviazione BLOB di origine o di gestione temporanea è configurata con l'endpoint di rete virtuale, è necessario usare l'autenticazione di identità gestita come richiesto da polibase e usare Integration Runtime indipendenti con la versione 3,18 o successiva. Vedere la sezione [autenticazione di identità gestita](#managed-identity) con altri prerequisiti di configurazione.

>[!NOTE]
>Le attività HDInsights e Azure Machine Learning supportano solo l'autenticazione con chiave dell'account di archiviazione BLOB di Azure.

### <a name="account-key-authentication"></a>Autenticazione basata sulla chiave dell'account

Per usare l'autenticazione basata sulla chiave dell'account di archiviazione, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà tipo deve essere impostata su **AzureBlobStorage** (consigliato) o **AzureStorage** (vedere le note sottostanti). |Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'archiviazione per la proprietà connectionString. <br/> È anche possibile inserire la chiave dell'account in Azure Key Vault e rimuovere la configurazione di `accountKey` dalla stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'endpoint di servizio BLOB secondario non è supportato quando si usa l'autenticazione della chiave dell'account. È possibile utilizzare altri tipi di autenticazione.

>[!NOTE]
>L'uso di un servizio collegato di tipo "AzureStorage", è ancora supportato così com'è, tuttavia in futuro verrà consigliato di usare il nuovo tipo di servizio collegato "AzureBlobStorage".

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticazione con firma di accesso condiviso

Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile usarla per concedere a un client autorizzazioni limitate per gli oggetti nell'account di archiviazione per un periodo di tempo specificato. Non è necessario condividere le chiavi di accesso degli account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma di accesso condiviso al costruttore o al metodo appropriato. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory supporta attualmente sia le **firme di accesso condiviso del servizio** che le **firme di accesso condiviso dell'account**. Per altre informazioni sulle firme di accesso condiviso, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md).
>- Nella configurazione successiva dei set di dati il percorso della cartella corrisponde al percorso assoluto a partire dal livello del contenitore. È necessario configurare un set di dati allineato con il percorso nell'URI SAS.

Per usare l'autenticazione basata sulla firma di accesso condiviso, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà tipo deve essere impostata su **AzureBlobStorage** (consigliato) o **AzureStorage** (vedere le note sottostanti). |Sì |
| sasUri | Specificare l'URI della firma di accesso condiviso per le risorse di archiviazione come BLOB o contenitore. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire il token SAS in Azure Key Vault per sfruttare la rotazione automatica e rimuovere la parte del token. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'uso di un servizio collegato di tipo "AzureStorage", è ancora supportato così com'è, tuttavia in futuro verrà consigliato di usare il nuovo tipo di servizio collegato "AzureBlobStorage".

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la chiave dell'account in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Quando si crea un URI di firma di accesso condiviso, tenere presente quanto segue:

- Impostare le autorizzazioni appropriate di lettura o scrittura per gli oggetti in base al modo in cui il servizio collegato (lettura, scrittura, lettura/scrittura) viene usato nella data factory.
- Impostare **Ora di scadenza** in modo appropriato. Assicurarsi che l'accesso agli oggetti di archiviazione non scada nel periodo attivo della pipeline.
- L'URI deve essere creato nel contenitore o nel BLOB corretto, in base alle esigenze. Un URI di firma di accesso condiviso per un BLOB consente a Data Factory di accedere a tale BLOB specifico. Un URI di firma di accesso condiviso per un contenitore di risorse di archiviazione BLOB consente a Data Factory di passare da un BLOB all'altro nel contenitore. Per consentire l'accesso a più o meno oggetti in un secondo momento o aggiornare l'URI di firma di accesso condiviso, ricordarsi di aggiornare il servizio collegato con il nuovo URI.

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per l'autenticazione con entità servizio di archiviazione di Azure in generale, vedere [Autenticare l'accesso ad Archiviazione di Azure con Azure Active Directory](../storage/common/storage-auth-aad.md).

Per usare l'autenticazione basata su entità servizio, eseguire la procedura seguente:

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo le indicazioni fornite in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'entità servizio un'autorizzazione appropriata in Archiviazione BLOB di Azure. Per informazioni dettagliate sui ruoli, vedere [Gestire i diritti di accesso ai dati di archiviazione di Azure con il controllo degli accessi in base al ruolo](../storage/common/storage-auth-aad-rbac.md).

    - **Come origine**, in Controllo degli accessi in base al ruolo, concedere almeno il ruolo **Lettore dei dati dei BLOB di archiviazione**.
    - **Come sink**, in Controllo di accesso (IAM), concedere almeno il ruolo **Collaboratore dei dati dei BLOB di archiviazione**.

Per un servizio collegato ad Archiviazione BLOB di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureBlobStorage**. |Sì |
| serviceEndpoint | Specificare l'endpoint del servizio di Archiviazione BLOB di Azure con il criterio `https://<accountName>.blob.core.windows.net/`. |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!NOTE]
>L'autenticazione tramite entità servizio è supportata solo dal servizio collegato di tipo "AzureBlobStorage", ma non dal servizio collegato precedente di tipo "AzureStorage".

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identità gestite per l'autenticazione delle risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare direttamente questa identità gestita per l'autenticazione dell'archiviazione BLOB in modo analogo all'uso della propria entità servizio. Consente alla factory designata di accedere e copiare i dati da/nella risorsa di archiviazione BLOB.

Per informazioni generali sull'autenticazione per l'archiviazione di Azure, vedere [Autenticare l'accesso all'archiviazione di Azure tramite Azure Active Directory](../storage/common/storage-auth-aad.md). Per usare l'autenticazione di identità gestite per le risorse di Azure, seguire questa procedura:

1. [Recuperare data factory informazioni sull'identità gestita](data-factory-service-identity.md#retrieve-managed-identity) copiando il valore dell' **ID oggetto identità gestito** generato insieme alla Factory.

2. Concedere l'autorizzazione appropriata per l'identità gestita in Archiviazione BLOB di Azure. Per informazioni dettagliate sui ruoli, vedere [Gestire i diritti di accesso ai dati di archiviazione di Azure con il controllo degli accessi in base al ruolo](../storage/common/storage-auth-aad-rbac.md).

    - **Come origine**, in Controllo degli accessi in base al ruolo, concedere almeno il ruolo **Lettore dei dati dei BLOB di archiviazione**.
    - **Come sink**, in Controllo di accesso (IAM), concedere almeno il ruolo **Collaboratore dei dati dei BLOB di archiviazione**.

>[!IMPORTANT]
>Se si usa la funzione polibase per caricare i dati da un BLOB (come origine o come gestione temporanea) in SQL Data Warehouse, quando si usa l'autenticazione di identità gestita per BLOB, assicurarsi di seguire anche i passaggi 1 e 2 in [questa guida](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) a 1) registrare il server di database sql con Azure Active Directory (Azure ad) e 2) assegnare il ruolo di collaboratore dati BLOB di archiviazione al server di database il resto viene gestito da Data Factory. Se l'archiviazione BLOB è configurata con un endpoint di rete virtuale di Azure, per usare la polibase per il caricamento dei dati, è necessario usare l'autenticazione di identità gestita come richiesto da polibase.

Per un servizio collegato ad Archiviazione BLOB di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureBlobStorage**. |Sì |
| serviceEndpoint | Specificare l'endpoint del servizio di Archiviazione BLOB di Azure con il criterio `https://<accountName>.blob.core.windows.net/`. |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

> [!NOTE]
> L'autenticazione di identità gestite per le risorse di Azure è supportata solo dal servizio collegato di tipo "AzureBlobStorage", ma non dal servizio collegato precedente di tipo "AzureStorage". 

**Esempio:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere l'articolo [set di impostazioni](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per il BLOB di `location` Azure in impostazioni nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà Type del percorso nel set di dati deve essere impostata su **AzureBlobStorageLocation**. | Sì      |
| contenitore  | Contenitore BLOB.                                          | Sì      |
| folderPath | Percorso della cartella nel contenitore specificato. Se si vuole usare il carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file nel contenitore specificato + folderPath. Se si vuole usare il carattere jolly per filtrare i file, ignorare questa impostazione e specificare nelle impostazioni dell'origine dell'attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Archiviazione BLOB di Azure.

### <a name="blob-storage-as-a-source-type"></a>Archiviazione BLOB come tipo di origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per il BLOB di `storeSettings` Azure in impostazioni in origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureBlobStorageReadSettings**. | Sì                                           |
| ricorsiva                | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. I valori consentiti sono **true** (impostazione predefinita) e **false**. | No                                            |
| prefix                   | Prefisso per il nome del BLOB nel contenitore specificato configurato nel set di dati per filtrare i BLOB di origine. I BLOB il cui nome inizia con questo prefisso sono selezionati. <br>Si applica solo `wildcardFolderPath` quando `wildcardFileName` le proprietà e non sono specificate. | No                                                          |
| wildcardFolderPath       | Il percorso della cartella con caratteri jolly nel contenitore specificato configurato nel set di dati per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br>Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | No                                            |
| wildcardFileName         | Nome file con caratteri jolly nel contenitore specificato + folderPath/wildcardFolderPath per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape.  Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). | Sì se `fileName` non è specificato nel DataSet |
| modifiedDatetimeStart    | Filtro file basato sull'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br> Le proprietà possono essere NULL, a indicare che al set di dati non viene applicato alcun filtro di attributo di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime. | No                                            |
| modifiedDatetimeEnd      | Come sopra.                                               | No                                            |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |

> [!NOTE]
> Per il formato di testo parquet/delimitato, l'origine dell'attività di copia di tipo **BlobSource** citata nella sezione successiva è ancora supportata così com'è per la compatibilità con le versioni precedenti. Si consiglia di usare questo nuovo modello in futuro e l'interfaccia utente di creazione di ADF ha cambiato la generazione di questi nuovi tipi.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Archiviazione BLOB come tipo di sink

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per il BLOB di `storeSettings` Azure in impostazioni in sink di copia basato sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La proprietà Type in `storeSettings` deve essere impostata su **AzureBlobStorageWriteSettings**. | Sì      |
| copyBehavior             | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>-PreserveHierarchy (impostazione predefinita)</b>: conserva la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>-MergeFiles</b>: unisce tutti i file della cartella di origine in un unico file. Se viene specificato il nome del file o del BLOB , il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No       |
| blockSizeInMB | Specificare le dimensioni del blocco in MB usate per scrivere i dati nei BLOB in blocchi. Altre informazioni [sui BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Il valore consentito è **compreso tra 4 e 100 MB**. <br/>Per impostazione predefinita, ADF determina automaticamente le dimensioni del blocco in base al tipo e ai dati dell'archivio di origine. Per la copia non binaria nel BLOB, le dimensioni predefinite del blocco sono pari a 100 MB, in modo da contenere al massimo 4,95 TB di dati. Potrebbe non essere ottimale quando i dati non sono di grandi dimensioni, soprattutto quando si usano Integration Runtime indipendenti con una rete scarsa, causando il timeout dell'operazione o un problema di prestazioni. È possibile specificare in modo esplicito le dimensioni del blocco, assicurandosi che blockSizeInMB * 50000 sia sufficientemente grande per archiviare i dati. in caso contrario, l'esecuzione dell'attività di copia non riuscirà. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No       |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

Questa sezione descrive il comportamento risultante del percorso cartella e del nome del file con i filtri con caratteri jolly.

| folderPath | fileName | ricorsiva | Struttura della cartella di origine e risultato del filtro (vengono recuperati i file in **grassetto** )|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vuoto, usare valore predefinito) | false | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vuoto, usare valore predefinito) | true | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contenitore<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alcuni esempi dei valori recursive e copyBehavior

In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Struttura della cartella di origine | Destinazione risultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File5 |
| true |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles | Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La Cartella1 di destinazione viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome di file generato automaticamente. Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

## <a name="preserve-metadata-during-copy"></a>Mantieni metadati durante la copia

Quando si copiano i file da Amazon S3/BLOB di Azure/Azure Data Lake Storage Gen2 al BLOB di Azure Data Lake Storage Gen2/Azure, è possibile scegliere di mantenere i metadati del file insieme ai dati. Per altre informazioni, vedere [preservare i metadati](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere file dall'archiviazione BLOB di Azure in formato JSON, avro, testo delimitato o parquet. Per ulteriori informazioni, vedere trasformazione [origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nella funzionalità flusso di dati mapping.

### <a name="source-transformation"></a>Trasformazione origine

Nella trasformazione origine è possibile leggere da un contenitore, una cartella o un singolo file nell'archivio BLOB di Azure. La scheda **Opzioni di origine** consente di gestire il modo in cui i file vengono letti. 

![Opzioni di origine](media/data-flow/sourceOptions1.png "Opzioni di origine")

**Percorso con caratteri jolly:** L'utilizzo di un modello con caratteri jolly indicherà ad ADF di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più modelli di corrispondenza con caratteri jolly con il segno + visualizzato quando si passa il mouse sul modello con caratteri jolly esistente.

Dal contenitore di origine, scegliere una serie di file che corrispondono a un modello. Nel set di dati è possibile specificare solo il contenitore. Il percorso del carattere jolly deve quindi includere anche il percorso della cartella nella cartella radice.

Esempi di caratteri jolly:

* ```*```Rappresenta qualsiasi set di caratteri
* ```**```Rappresenta l'annidamento delle directory ricorsive
* ```?```Sostituisce un carattere
* ```[]```Trova la corrispondenza di uno o più caratteri tra parentesi quadre

* ```/data/sales/**/*.csv```Ottiene tutti i file CSV in/data/Sales
* ```/data/sales/20??/**/```Ottiene tutti i file nel ventesimo secolo
* ```/data/sales/*/*/*.csv```Ottiene i file CSV a due livelli sotto/data/Sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Ottiene tutti i file CSV in 2004 nel dicembre a partire da X o Y preceduto da un numero a due cifre

**Percorso radice partizione:** Se nell'origine file sono presenti cartelle partizionate con un ```key=value``` formato (ad esempio, Year = 2019), è possibile assegnare il livello principale dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

Per prima cosa, impostare un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate e i file foglia che si desidera leggere.

![Impostazioni del file di origine della partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Utilizzare l'impostazione percorso radice partizione per definire il livello superiore della struttura di cartelle. Quando si Visualizza il contenuto dei dati tramite un'anteprima dei dati, si noterà che ADF aggiungerà le partizioni risolte presenti in ogni livello di cartella.

![Percorso radice partizione](media/data-flow/partfile1.png "Anteprima percorso radice partizione")

**Elenco di file:** Si tratta di un set di file. Creare un file di testo che includa un elenco di file di percorso relativi da elaborare. Puntare a questo file di testo.

**Colonna in cui archiviare il nome del file:** Archiviare il nome del file di origine in una colonna nei dati. Immettere qui il nome di una nuova colonna per archiviare la stringa del nome file.

**Dopo il completamento:** Scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare prima di tutto "Sposta" per operazione su file. Quindi, impostare la directory "from". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "from" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file originati in/data/Sales vengono spostati in/backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione di una pipeline) che utilizza l'attività Esegui flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite nella modalità di debug del flusso di dati.

**Filtra per Ultima modifica:** È possibile filtrare i file elaborati specificando un intervallo di date di data e ora dell'Ultima modifica. Tutte le ore di data sono in formato UTC. 

### <a name="sink-properties"></a>Proprietà sink

Nella trasformazione sink è possibile scrivere in un contenitore o in una cartella nell'archiviazione BLOB di Azure. la scheda **Impostazioni** consente di gestire il modo in cui i file vengono scritti.

![opzioni sink](media/data-flow/file-sink-settings.png "opzioni sink")

**Cancellare la cartella:** Determina se la cartella di destinazione viene cancellata prima della scrittura dei dati.

**Opzione nome file:** Determina la modalità di denominazione dei file di destinazione nella cartella di destinazione. Le opzioni del nome file sono:
   * **Impostazione predefinita**: consente a Spark di assegnare un nome ai file in base alle impostazioni predefinite della parte.
   * **Modello**: immettere un modello che enumera i file di output per partizione. Ad esempio, **Loans [n]. csv** creerà loans1. csv, loans2. csv e così via.
   * **Per partizione**: immettere un nome di file per partizione.
   * **Come dati in column**: impostare il file di output sul valore di una colonna. Il percorso è relativo al contenitore del set di dati e non alla cartella di destinazione. Se nel set di dati è presente un percorso di cartella, ne verrà eseguito l'override.
   * **Output in un singolo file**: combina i file di output partizionati in un singolo file denominato. Il percorso è relativo alla cartella del set di dati. Tenere presente che l'operazione di merge di te potrebbe avere esito negativo in base alla dimensione del nodo. Questa opzione non è consigliata per i set di impostazioni di grandi dimensioni.

**Tutte le virgolette:** Determina se racchiudere tutti i valori tra virgolette

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Elimina proprietà attività

Per informazioni dettagliate sulle proprietà, controllare l' [attività di eliminazione](delete-activity.md)

## <a name="legacy-models"></a>Modelli legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati così come sono per la compatibilità con le versioni precedenti. Si consiglia di usare il nuovo modello menzionato nelle sezioni precedenti in futuro e l'interfaccia utente di creazione di ADF ha cambiato la generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello DataSet legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **AzureBlob**. |Sì |
| folderPath | Percorso del contenitore e della cartella nell'archivio BLOB. <br/><br/>Il filtro con caratteri jolly è supportato per il percorso escluso il nome del contenitore. I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero caratteri o a un carattere singolo). Usare `^` come carattere di escape se il nome effettivo della cartella include caratteri jolly o questo carattere di escape. <br/><br/>Esempi: myblobcontainer/myblobfolder/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì per l'attività Copy/Lookup, no per l'attività GetMetadata |
| fileName | **Filtro con nome o carattere jolly** per i BLOB nell'elemento "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i BLOB nella cartella. <br/><br/>Per un filtro, i caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per il carattere escape se il nome effettivo del file include caratteri jolly o escape.<br/><br/>Quando fileName non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, l'attività di copia genera automaticamente il nome del BLOB con il modello seguente: "*Data. [ GUID ID esecuzione attività]. [GUID se FlattenHierarchy]. [format se configurato]. [compressione se configurata]*", ad esempio" Data. 0a405f8a-93ff-4C6F-B3BE-f69616f1df7a. txt. gz "; Se si copia da un'origine tabulare usando il nome della tabella anziché la query, il modello del nome è "*[nome tabella]. [ formato]. [compressione se configurata]*", ad esempio" MyTable. csv ". |No |
| modifiedDatetimeStart | Filtro file basato sull'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da grandi quantità di file. <br/><br/> Le proprietà possono essere NULL che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| modifiedDatetimeEnd | Filtro file basato sull'attributo: data Ultima modifica. I file vengono selezionati se l'ora dell'ultima modifica è inclusa nell'intervallo di tempo tra `modifiedDatetimeStart` e `modifiedDatetimeEnd`. L'ora viene applicata con il fuso orario UTC e il formato "2018-12-01T05:00:00Z". <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera eseguire il filtro file da grandi quantità di file. <br/><br/> Le proprietà possono essere NULL che significa che al set di dati non verrà applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore datetime ma `modifiedDatetimeEnd` è NULL, vengono selezionati i file il cui ultimo attributo modificato è maggiore o uguale al valore datetime.  Quando `modifiedDatetimeEnd` ha un valore datetime ma `modifiedDatetimeStart` è NULL vengono selezionati i file il cui ultimo attributo modificato è minore del valore datetime.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare o generare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni formato [testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e [formato parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

>[!TIP]
>Per copiare tutti i BLOB in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo BLOB con un determinato nome, specificare **folderPath** con il percorso della cartella e **fileName** con il nome del file.<br>Per copiare un subset di BLOB in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly. 

**Esempio:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Modello di origine dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **BlobSource**. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che quando la proprietà recursive è impostata su true e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink.<br/>I valori consentiti sono **true** (impostazione predefinita) e **false**. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Modello di sink dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **BlobSink**. |Sì |
| copyBehavior | Definisce il comportamento di copia quando l'origine è costituita da file di un archivio dati basato su file.<br/><br/>I valori consentiti sono i seguenti:<br/><b>-PreserveHierarchy (impostazione predefinita)</b>: conserva la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><b>- FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. I nomi dei file di destinazione vengono generati automaticamente. <br/><b>-MergeFiles</b>: unisce tutti i file della cartella di origine in un unico file. Se viene specificato il nome del file o del BLOB , il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome di file generato automaticamente. | No |
| maxConcurrentConnections | Numero di connessioni simultanee per la connessione all'archivio di archiviazione. Specificare solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
