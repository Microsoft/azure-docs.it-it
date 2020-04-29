---
title: Copiare i dati in Dynamics (Common Data Service)
description: Informazioni su come copiare dati da Microsoft Dynamics CRM o Microsoft Dynamics 365 (Common Data Service) in archivi dati di sink supportati o da archivi dati di origine supportati in Dynamics CRM o Dynamics 365 usando un'attività di copia in una pipeline di data factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417354"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiare i dati da e in Dynamics 365 (Common Data Service) o Dynamics CRM usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Microsoft Dynamics 365 o Microsoft Dynamics CRM. Si basa sull'articolo [Panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Dynamics 365 (Common Data Service) o Dynamics CRM in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a Dynamics 365 (Common Data Service) o Dynamics CRM. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Questo connettore Dynamics supporta Dynamics versione 7. x a 9. x sia in linea che in locale. Più in particolare,

- La versione 7. x è mappata a Dynamics CRM 2015
- La versione 8. x è mappata a Dynamics CRM 2016 e alla prima versione di Dynamics 365
- La versione 9. x è mappata alla versione più recente di Dynamics 365

Fare riferimento alla seguente tabella sui tipi di autenticazione supportati e sulle configurazioni per le rispettive versioni/prodotti Dynamics. IFD è l'acronimo di Internet Facing Deployment (distribuzione con connessione Internet).

| Versioni di Dynamics | Tipi di autenticazione | Esempi di servizi collegati |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 Online <br> Dynamics CRM Online | Entità servizio AAD <br> Office365 | [Dynamics Online, entità servizio AAD o autenticazione Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 locale con IFD <br> Dynamics CRM 2016 locale con IFD <br> Dynamics CRM 2015 locale con IFD | IFD | [Dynamics locale con IFD + autenticazione IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Per Dynamics 365 in particolare, sono supportati i tipi di applicazioni seguenti:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Altri tipi di applicazioni, ad esempio Finance and Operations, Talent e così via non sono supportati da questo connettore.

Questo connettore Dynamics si basa sugli strumenti di [Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Per copiare dati da **Dynamics 365 for Finance and Operations**, è possibile usare il [connettore di Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Dynamics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Dynamics sono supportate le proprietà seguenti.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **Dynamics**, **DynamicsCrm**o **CommonDataServiceForApps**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"Online"** per Dynamics online. | Sì |
| serviceUri | URL del servizio dell'istanza di Dynamics, ad esempio `https://adfdynamics.crm.dynamics.com`. | Sì |
| authenticationType | Il tipo di autenticazione per la connessione a un server Dynamics. I valori consentiti sono: **AADServicePrincipal** o **"Office365"**. | Sì |
| servicePrincipalId | Specificare l'ID client. dell'applicazione Azure Active Directory. | Sì quando si `AADServicePrincipal` usa l'autenticazione |
| servicePrincipalCredentialType | Specificare il tipo di credenziale da usare per l'autenticazione dell'entità servizio. I valori consentiti sono: **ServicePrincipalKey** o **ServicePrincipalCert**. | Sì quando si `AADServicePrincipal` usa l'autenticazione |
| servicePrincipalCredential | Specificare le credenziali dell'entità servizio. <br>Quando si `ServicePrincipalKey` USA come tipo di `servicePrincipalCredential` credenziale, può essere una stringa (ADF la crittografa durante la distribuzione del servizio collegato) o un riferimento a un segreto in AKV. <br>Quando si `ServicePrincipalCert` USA As Credential `servicePrincipalCredential` , deve essere un riferimento a un certificato in AKV. | Sì quando si `AADServicePrincipal` usa l'autenticazione | 
| nomeutente | Specificare il nome utente per la connessione a Dynamics. | Sì quando si `Office365` usa l'autenticazione |
| password | Specificare la password dell'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì quando si `Office365` usa l'autenticazione |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!NOTE]
>Connettore di Dynamics che permette di usare la proprietà facoltativa "organizationName" per identificare l'istanza di Dynamics CRM/365 Online. Durante l'esecuzione, viene consigliato di specificare invece la nuova proprietà "serviceUri" per ottenere prestazioni migliori per l'individuazione dell'istanza.

**Esempio: Dynamics online con l'autenticazione dell'entità servizio e della chiave di AAD**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Esempio: Dynamics online con l'entità servizio AAD e l'autenticazione del certificato**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Esempio: Dynamics Online usando l'autenticazione di Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM locale con IFD

*Proprietà aggiuntive rispetto a Dynamics online sono "hostName" e "port".*

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **Dynamics**, **DynamicsCrm**o **CommonDataServiceForApps**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"OnPremisesWithIfd"** per Dynamics locale con IFD.| Sì |
| hostName | Nome host del server Dynamics locale. | Sì |
| port | Porta del server Dynamics locale. | No, il valore predefinito è 443 |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | Sì |
| authenticationType | Tipo di autenticazione per la connessione al server Dynamics. Specificare **"Ifd"** per Dynamics locale con IFD. | Sì |
| nomeutente | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password dell'account utente specificato per il nome utente. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

**Esempio: Dynamics locale con IFD usando l'autenticazione IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere l'articolo [set di impostazioni](concepts-datasets-linked-services.md) . Questa sezione presenta un elenco delle proprietà supportate dal set di dati Dynamics.

Per copiare dati da e in Dynamics, sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **DynamicsEntity**, **DynamicsCrmEntity**o **CommonDataServiceForAppsEntity**. |Sì |
| entityName | Il nome logico dell'entità da recuperare. | No per l'origine (se nell'origine dell'attività è specificato "query"), Sì per il sink |

**Esempio:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dai tipi di origine e di sink Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics come tipo di origine

Per copiare dati da Dynamics, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **DynamicsSource**, **DynamicsCrmSource**o **CommonDataServiceForAppsSource**. | Sì |
| query | FetchXML è un linguaggio di query proprietario usato in Dynamics (online e locale). Vedere l'esempio seguente. Per altre informazioni, vedere [compilare query con un'FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | No (se nel set di dati è specificato "entityName") |

>[!NOTE]
>La colonna della chiave primaria verrà sempre copiata anche se la proiezione della colonna configurata nella query FetchXML non la contiene.

> [!IMPORTANT]
>- Quando si copiano dati da Dynamics, il mapping esplicito di colonne da Dynamics a sink è facoltativo ma altamente riordinato per assicurare il risultato di una copia deterministica.
>- Quando si importa lo schema nell'interfaccia utente di creazione, ADF deduce lo schema eseguendo il campionamento delle prime righe del risultato della query Dynamics per inizializzare l'elenco delle colonne di origine, nel qual caso le colonne senza valori nelle prime righe verranno omesse. Lo stesso comportamento si applica alle esecuzioni di copia se non esiste alcun mapping esplicito. È possibile esaminare e aggiungere altre colonne nel mapping, che verranno rispettate durante la copia del runtime.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Esempio di query FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics come tipo di sink

Per copiare i dati in Dynamics, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **DynamicsSink**, **DynamicsCrmSink**o **CommonDataServiceForAppsSink**. | Sì |
| writeBehavior | Comportamento dell'azione di scrittura dell'operazione.<br/>Il valore consentito è **"upsert"**. | Sì |
| alternateKeyName | Specificare il nome della chiave alternativa definito nell'entità per eseguire "upsert". | No |
| writeBatchSize | Conteggio delle righe di dati scritti da Dynamics in ogni batch. | No (il valore predefinito è 10) |
| ignoreNullValues | Indica se ignorare i valori null dai dati di input (tranne i campi chiave) durante un'operazione di scrittura.<br/>I valori consentiti sono **true** e **false**.<br>- **True**: i dati nell'oggetto di destinazione rimangono invariati quando si esegue un'operazione di upsert/aggiornamento. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.<br/>- **False**: i dati nell'oggetto di destinazione vengono aggiornati a NULL quando si esegue un'operazione di upsert/aggiornamento. Inserire un valore NULL quando si esegue un'operazione di inserimento. | No (il valore predefinito è false) |

>[!NOTE]
>Il valore predefinito del sink "**writeBatchSize**" e l'attività di copia "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" per il sink di Dynamics sono entrambi 10. Pertanto, vengono inviati simultaneamente 100 record a Dynamics.

Per Dynamics 365 online, è previsto un limite di [2 chiamate di batch simultanee per ogni organizzazione](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se tale limite viene superato, viene generato un errore "Server occupato" prima che venga eseguita la prima richiesta. Mantenendo "writeBatchSize" minore o uguale a 10 si evita la limitazione delle richieste di chiamate simultanee.

La combinazione ottimale di "**writeBatchSize**" e "**parallelCopies**" dipende dallo schema dell'entità, ad esempio il numero di colonne, le dimensioni delle righe, il numero di plug-in/flussi di lavoro/attività del flusso di lavoro collegati a tali chiamate e così via. L'impostazione predefinita di 10 writeBatchSize * 10 parallelCopies è la raccomandazione in base al servizio Dynamics, che funziona per la maggior parte delle entità Dynamics, ma potrebbe non essere ottimale. È possibile ottimizzare le prestazioni regolando la combinazione nelle impostazioni di attività di copia.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapping dei tipi di dati per Dynamics

Quando si copiano dati da Dynamics, vengono usati i mapping seguenti tra i tipi di dati di Dynamics e i tipi di dati provvisori di Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

Nella struttura del set di dati, configurare il tipo di dati corrispondente di Data Factory in base al tipo di dati di origine di Dynamics usando la tabella di mapping seguente.

| Tipo di dati di Dynamics | Tipo di dati provvisorio di Data Factory | Supportato come origine | Supportato come sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Stringa | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (associata a destinazione singola) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | Stringa | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Stringa | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> I tipi di dati di Dynamics AttributeType. CalendarRules, AttributeType. MultiSelectPicklist e AttributeType. party non sono supportati.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
