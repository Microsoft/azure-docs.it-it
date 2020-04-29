---
title: Copiare e trasformare i dati nel database SQL di Azure
description: Informazioni su come copiare dati da e verso il database SQL di Azure e trasformare i dati nel database SQL di Azure usando Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: db55e685fb50c89eb850e1b9ee9dcf13d20fb614
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417534"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiare e trasformare i dati nel database SQL di Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-azure-sql-connector.md)
> * [Versione corrente](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e nel database SQL di Azure e usare il flusso di dati per trasformare i dati nel database SQL di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore del database SQL di Azure è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con tabella della [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività di copia, questo connettore del database SQL di Azure supporta le funzioni seguenti:

- Copia dei dati usando l'autenticazione SQL e l'autenticazione del token dell'applicazione Azure Active Directory (Azure AD) con un'entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero dei dati tramite una query SQL o un stored procedure.
- Come sink, l'aggiunta di dati a una tabella di destinazione o la chiamata di un stored procedure con la logica personalizzata durante la copia.

>[!NOTE]
>Il [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) del database SQL di Azure non è attualmente supportato da questo connettore. Per aggirare il contenuto, è possibile usare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC SQL Server tramite un runtime di integrazione self-hosted. Attenersi a [queste linee guida per il](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) download del driver ODBC e le configurazioni della stringa di connessione.

> [!IMPORTANT]
> Se si copiano i dati usando Azure Data Factory Integration Runtime, configurare un [firewall di azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) in modo che i servizi di Azure possano accedere al server.
> Se si copiano i dati usando un runtime di integrazione self-hosted, configurare il firewall di Azure SQL Server per consentire l'intervallo di indirizzi IP appropriato. Questo intervallo include l'IP del computer usato per connettersi al database SQL di Azure.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Azure Data Factory entità specifiche di un connettore del database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato al database SQL di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureSqlDatabase**. | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database SQL di Azure per la proprietà **ConnectionString** . <br/>È anche possibile inserire una password o una chiave dell'entità servizio in Azure Key Vault. Se si tratta dell'autenticazione SQL, estrarre `password` la configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere l'esempio JSON che segue la tabella e [archivia le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo posizionando il puntatore del mouse nell'angolo in alto a destra della portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. Se l'archivio dati si trova in una rete privata, è possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Autenticazione del token dell'applicazione Azure AD: entità servizio](#service-principal-authentication)
- [Autenticazione token dell'applicazione Azure AD: identità gestite per le risorse di Azure](#managed-identity)

>[!TIP]
>Se viene raggiunto un errore con il codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio simile a "il limite della sessione per il database è XXX ed è stato raggiunto `Pooling=false` ", aggiungere alla stringa di connessione e riprovare.

### <a name="sql-authentication"></a>Autenticazione SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Esempio di servizio collegato tramite l'autenticazione SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Password in Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
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

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione token dell'applicazione di Azure AD basata sull'entità servizio, seguire questa procedura:

1. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Effettuare il [provisioning di un amministratore Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) per il SQL Server di Azure nel portale di Azure, se non è già stato fatto. L'amministratore di Azure AD deve essere un utente o un gruppo di Azure AD, ma non può essere un'entità servizio. Questo passaggio viene eseguito in modo che, nel passaggio successivo, sia possibile usare un'identità di Azure AD per creare un utente di database indipendente per l'entità servizio.

3. [Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) per l'entità servizio. Connettersi al database da o a cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Concedere all'entità servizio le autorizzazioni necessarie, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'autenticazione del database SQL di Azure. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Per utilizzare l'autenticazione di identità gestita, attenersi alla seguente procedura.

1. Effettuare il [provisioning di un amministratore Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) per il SQL Server di Azure nel portale di Azure, se non è già stato fatto. L'amministratore Azure AD può essere un utente Azure AD o un gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore dispone di accesso completo al database.

2. [Creare utenti di database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) per l'identità gestita del Azure Data Factory. Connettersi al database da o a cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Concedere le autorizzazioni necessarie per la Data Factory identità gestita come si fa normalmente per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.

**Esempio**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [DataSets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Per il set di dati del database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del set di dati deve essere impostata su **AzureSqlTable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella o della vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella o della vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per il nuovo carico di `schema` lavoro `table`, utilizzare e. | No per l'origine, Sì per il sink |

#### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database SQL di Azure.

### <a name="azure-sql-database-as-the-source"></a>Database SQL di Azure come origine

Per copiare dati dal database SQL di Azure, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** dell'origine dell'attività di copia deve essere impostata su **AzureSqlSource**. Il tipo "sqlSource" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| sqlReaderQuery | Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri stored procedure. | No |
| isolationLevel | Specifica il comportamento di blocco delle transazioni per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **snapshot**. Per ulteriori informazioni, fare riferimento a [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | No |

**Punti da notare:**

- Se viene specificato **sqlReaderQuery** per **AzureSqlSource**, l'attività di copia esegue questa query nell'origine del database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica **sqlReaderQuery** o **sqlReaderStoredProcedureName**, le colonne definite nella sezione "Structure" del set di dati JSON vengono usate per creare una query. La query `select column1, column2 from mytable` viene eseguita nel database SQL di Azure. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

#### <a name="sql-query-example"></a>Esempio di query SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Esempio di stored procedure

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Database SQL di Azure come sink

> [!TIP]
> Scopri di più sui comportamenti di scrittura supportati, sulle configurazioni e sulle procedure consigliate, dalla [procedura consigliata per il caricamento dei dati nel database SQL di Azure](#best-practice-for-loading-data-into-azure-sql-database).

Per copiare i dati nel database SQL di Azure, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **Type** del sink dell'attività di copia deve essere impostata su **AzureSqlSink**. Il tipo "sqlsink" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| writeBatchSize | Numero di righe da inserire nella tabella SQL *per batch*.<br/> Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga. | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/> Il valore consentito è **TimeSpan**. Un esempio è "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database SQL di Azure. Viene richiamata solo una volta per ogni esecuzione della copia. Usare questa proprietà per pulire i dati precaricati. | No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno nulla a che fare con i dati di origine, ad esempio delete `preCopyScript` o TRUNCATE, usare la proprietà. | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nell'stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nel stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| tableOption | Specifica se creare automaticamente la tabella di sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica stored procedure o la copia temporanea è configurata nell'attività di copia. I valori consentiti sono: `none` ( `autoCreate`impostazione predefinita),. |No |
| disableMetricsCollection | Data Factory raccoglie le metriche, ad esempio il database SQL di Azure DTU per l'ottimizzazione delle prestazioni di copia e le raccomandazioni. Se si è interessati a questo comportamento, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |

**Esempio 1: accodare i dati**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Esempio 2: richiamare un stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Procedura consigliata per il caricamento dei dati nel database SQL di Azure

Quando si copiano dati nel database SQL di Azure, potrebbe essere necessario un comportamento di scrittura diverso:

- [Accoda](#append-data): i dati di origine hanno solo i nuovi record.
- [Upsert](#upsert-data): i dati di origine sono inserimenti e aggiornamenti.
- [Overwrite](#overwrite-the-entire-table): si desidera ricaricare ogni volta un'intera tabella delle dimensioni.
- [Scrivi con logica personalizzata](#write-data-with-custom-logic): ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Vedere le rispettive sezioni sulla configurazione di in Azure Data Factory e procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'accodamento dei dati è il comportamento predefinito del connettore di sink del database SQL di Azure. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente nella tabella. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, usare l'approccio seguente per eseguire una Upsert: 

- Utilizzare innanzitutto una [tabella temporanea con ambito database](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) per eseguire il caricamento bulk di tutti i record utilizzando l'attività di copia. Poiché le operazioni sulle tabelle temporanee con ambito database non vengono registrate, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività stored procedure in Azure Data Factory per applicare un'istruzione [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o Insert/Update. Utilizzare la tabella temp come origine per eseguire tutti gli aggiornamenti o gli inserimenti come una singola transazione. In questo modo, il numero di round trip e di operazioni di log è ridotto. Alla fine dell'attività stored procedure, è possibile troncare la tabella temporanea in modo che sia pronta per il ciclo Upsert successivo.

In Azure Data Factory, ad esempio, è possibile creare una pipeline con un' **attività di copia** concatenata a un' **attività di stored procedure**. Il primo copia i dati dall'archivio di origine in una tabella temporanea del database SQL di Azure, ad esempio **# #UpsertTempTable**, come nome della tabella nel set di dati. Il secondo richiama quindi un stored procedure per unire i dati di origine dalla tabella temporanea alla tabella di destinazione e pulire la tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire un stored procedure con la logica di MERGE, come nell'esempio seguente, a cui fa riferimento l'attività stored procedure precedente. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **profileid**, **stato**e **categoria**. Eseguire Upsert in base alla colonna **profileid** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opzione 2:** È anche possibile scegliere di [richiamare un stored procedure all'interno dell'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio esegue ogni batch, come regolato dalla `writeBatchSize` proprietà, nella tabella di origine anziché utilizzare BULK INSERT come approccio predefinito nell'attività di copia.

### <a name="overwrite-the-entire-table"></a>Sovrascrivi l'intera tabella

È possibile configurare la proprietà **preCopyScript** nel sink dell'attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Esegue quindi la copia per inserire i dati. Per sovrascrivere l'intera tabella con i dati più recenti, ad esempio, specificare uno script per eliminare prima tutti i record prima di eseguire il caricamento bulk dei nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzata

I passaggi per scrivere i dati con la logica personalizzata sono simili a quelli descritti nella sezione relativa ai [dati di Upsert](#upsert-data) . Quando è necessario applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità elevata è possibile eseguire una delle due operazioni seguenti:

- Caricare in una tabella temporanea con ambito database e quindi richiamare un stored procedure. 
- Richiama un stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati nel database SQL di Azure, è anche possibile configurare e richiamare un stored procedure specificato dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La funzionalità stored procedure sfrutta i [parametri con valori di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'ulteriore elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserirli in più di una tabella.

L'esempio seguente illustra come usare una stored procedure per eseguire un'operazione di upsert in una tabella del database SQL di Azure. Si supponga che i dati di input e la tabella di **Marketing** sink includano tre colonne: **profileid**, **state**e **Category**. Eseguire Upsert in base alla colonna **profileid** e applicarla solo a una categoria specifica denominata "producta".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire il stored procedure con lo stesso nome di **sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure corrisponde a **TableName** definito nel set di dati.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definire la sezione del **sink SQL** nell'attività di copia come indicato di seguito:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle tabelle del database SQL di Azure. Per ulteriori informazioni, vedere trasformazione [origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) in mapping di flussi di dati.

### <a name="source-transformation"></a>Trasformazione origine

Le impostazioni specifiche del database SQL di Azure sono disponibili nella scheda **Opzioni di origine** della trasformazione origine. 

**Input:** Consente di specificare se puntare l'origine a una tabella (equivalente ```Select * from <table-name>```a) o immettere una query SQL personalizzata.

**Query**: se si seleziona query nel campo di input, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Le clausole **Order by** non sono supportate in questo punto, ma è possibile impostare un'istruzione SELECT from completa. È anche possibile usare funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile utilizzare nel flusso di dati. L'utilizzo di query è anche un ottimo modo per ridurre le righe per il test o per le ricerche. 

* Esempio SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Dimensioni batch**: immettere le dimensioni del batch per suddividere i dati di grandi dimensioni in letture.

**Livello di isolamento**: il valore predefinito per le origini SQL nel flusso di dati di mapping è READ UNCOMMITTED. È possibile modificare il livello di isolamento in uno dei seguenti valori:
* Read Committed
* Read Uncommitted
* Repeatable Read
* Serializable
* Nessuno (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Sink-trasformazione

Le impostazioni specifiche del database SQL di Azure sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Metodo di aggiornamento:** Determina quali operazioni sono consentite nella destinazione del database. Per impostazione predefinita, vengono consentiti solo gli inserimenti. Per aggiornare, Upsert o eliminare righe, è necessaria una trasformazione alter-Row per contrassegnare le righe per tali azioni. Per gli aggiornamenti, Upsert ed eliminazioni, è necessario impostare una colonna chiave o colonne per determinare la riga da modificare.

![Colonne chiave](media/data-flow/keycolumn.png "Colonne chiave")

Il nome della colonna scelto come chiave verrà usato da ADF come parte del successivo aggiornamento, Upsert, DELETE. Pertanto, è necessario selezionare una colonna esistente nel mapping del sink. Se non si desidera scrivere il valore in questa colonna chiave, fare clic su "Ignora scrittura colonne chiave".

**Azione tabella:** Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.
* None: nessuna azione verrà eseguita nella tabella.
* Ricrea: la tabella viene eliminata e ricreata. Obbligatorio se si crea una nuova tabella in modo dinamico.
* TRUNCATE: tutte le righe della tabella di destinazione vengono rimosse.

**Dimensioni batch**: controlla il numero di righe scritte in ogni bucket. Dimensioni di batch più grandi migliorano la compressione e l'ottimizzazione della memoria, ma rischiano le eccezioni di memoria durante la memorizzazione nella cache dei dati.

**Script pre e post SQL**: immettere gli script SQL a più righe che verrà eseguito prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink

![script pre e post-elaborazione SQL](media/data-flow/prepost1.png "Script di elaborazione SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapping dei tipi di dati per il database SQL di Azure

Quando i dati vengono copiati da o nel database SQL di Azure, i mapping seguenti vengono usati dai tipi di dati del database SQL di Azure per Azure Data Factory tipi di dati provvisori. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati del database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se i dati hanno una precisione maggiore di 28, provare a eseguire la conversione in una stringa nella query SQL.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
