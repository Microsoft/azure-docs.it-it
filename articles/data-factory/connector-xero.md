---
title: Copiare dati da Xero usando Azure Data Factory
description: Informazioni su come copiare dati da Xero in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: bdb2dc283287bf83410f1846aca11f233e93d01b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990849"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Copiare dati da Xero usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Xero. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Xero è supportato per le seguenti attività:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da Xero a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Xero supporta:

- L'[applicazione privata](https://developer.xero.com/documentation/getting-started/api-application-types) Xero, ma non l'applicazione pubblica.
- Tutte le tabelle di Xero (endpoint dell'API), ad eccezione di "Reports". 

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Xero.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Xero sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Xero** | Sì |
| host | Endpoint del server Xero (`api.xero.com`).  | Sì |
| consumerKey | Chiave utente associata all'applicazione Xero. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| privateKey | Chiave privata dal file con estensione pem generato per l'applicazione Xero privata; vedere [Create a public/private key pair](https://developer.xero.com/documentation/api-guides/create-publicprivate-key) (Creare una coppia di chiavi pubblica/privata). Si noti che **la generazione di file privatekey.pem di 512 bit** tramite `openssl genrsa -out privatekey.pem 512`; 1024 non è supportata. Includere tutto il testo dal file con estensione pem, incluse le terminazioni riga Unix (\n): vedere l'esempio seguente.<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No |
| useHostVerification | Specifica se il nome host è necessario nel certificato del server in modo che corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |

**Esempio:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Valore di chiave privata di esempio:**

Includere tutto il testo dal file con estensione pem, incluse le terminazioni riga Unix (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Xero.

Per copiare dati da Xero, impostare la proprietà type del set di dati su **XeroObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **XeroObject** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Xero.

### <a name="xero-as-source"></a>Xero come origine

Per copiare dati da Xero, impostare il tipo di origine nell'attività di copia su **XeroSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **XeroSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Contacts"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Notare quanto segue quando si specifica la query Xero:

- Le tabelle con elementi complessi verranno suddivise in più tabelle. Le transazioni bancarie, ad esempio, hanno una struttura di dati complessa "LineItems" e i dati delle transazioni vengono quindi mappati alla tabella `Bank_Transaction` e a `Bank_Transaction_Line_Items`, con `Bank_Transaction_ID` come chiave esterna per collegarli tra loro.

- I dati Xero sono disponibili tramite due schemi: `Minimal` (impostazione predefinita) e `Complete`. Lo schema Complete contiene tabelle di chiamate obbligatorie che richiedono dati aggiuntivi (ad esempio, la colonna ID) per poter eseguire la query desiderata.

Le tabelle seguenti contengono le stesse informazioni degli schemi Minimal e Complete. Per ridurre il numero di chiamate API, usare uno schema Minimal (impostazione predefinita).

- Bank_Transactions
- Contact_Groups 
- Contatti 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Fatture 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

Nelle tabelle seguenti è possibile eseguire query solo con lo schema Complete:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati dall'attività di copia, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
