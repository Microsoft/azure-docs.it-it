---
title: Eseguire ricerche nel contenuto di archiviazione tabelle di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare i dati archiviati nell'archiviazione tabelle di Azure con un indicizzatore di Azure ricerca cognitiva.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 26be48e7968345863799191539bd668ea6d9a4a2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929568"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Come indicizzare le tabelle dall'archiviazione tabelle di Azure con Azure ricerca cognitiva

Questo articolo illustra come usare ricerca cognitiva di Azure per indicizzare i dati archiviati nell'archiviazione tabelle di Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurare l'indicizzazione in Archiviazione tabelle di Azure

È possibile configurare un indicizzatore dell'Archiviazione tabelle di Azure mediante queste risorse:

* [Azure portal](https://ms.portal.azure.com)
* [API REST](/rest/api/searchservice/Indexer-operations) di Azure ricerca cognitiva
* Azure ricerca cognitiva [.NET SDK](/dotnet/api/overview/azure/search)

In questo caso viene illustrato il flusso tramite l'API REST. 

### <a name="step-1-create-a-datasource"></a>Passaggio 1: Creare un'origine dati

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono ad Azure ricerca cognitiva di identificare in modo efficace le modifiche nei dati.

Per l'indicizzazione delle tabelle, l'origine dati deve possedere le proprietà seguenti:

- Il parametro **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
- **type** deve essere `azuretable`.
- Il parametro **credentials** contiene la stringa di connessione all'account di archiviazione. Per altre informazioni, vedere [Specificare le credenziali](#Credentials).
- **container** imposta il nome della tabella e una query facoltativa.
    - Specificare il nome della tabella usando il parametro `name`.
    - Specificare facoltativamente una query usando il parametro `query`. 

> [!IMPORTANT] 
> Se possibile, usare un filtro sul parametro PartitionKey per ottimizzare le prestazioni. Qualsiasi altra query comporterà un'analisi completa delle tabelle e un conseguente peggioramento delle prestazioni in caso di tabelle di grandi dimensioni. Vedere [Considerazioni sulle prestazioni](#Performance).


Per creare un'origine dati:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Metodi per specificare le credenziali ####

Per specificare le credenziali per la tabella, sono disponibili questi modi: 

- **Stringa di connessione dell'account di archiviazione con accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` è possibile ottenere la stringa di connessione dal portale di Azure passando al pannello **account di archiviazione**  >  **Settings**  >  **chiavi** impostazioni (per gli account di archiviazione classici) o **Impostazioni**  >  **chiavi di accesso** (per Azure Resource Manager account di archiviazione).
- **Stringa di connessione della firma di accesso condiviso dell'account di archiviazione**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` la firma di accesso condiviso deve avere le autorizzazioni di elenco e lettura per i contenitori (tabelle in questo caso) e gli oggetti (righe di tabella).
-  **Firma di accesso condiviso tabella**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` la firma di accesso condiviso deve avere le autorizzazioni di query (lettura) per la tabella.

Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali di firma di accesso condiviso scadono, l'indicizzatore ha esito negativo con un messaggio di errore simile a "Le credenziali specificate nella stringa di connessione non sono valide o sono scadute".  

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Per creare un indice:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passaggio 3: Creare un indicizzatore
Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati. 

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

L'indicizzatore verrà eseguito ogni due ore. (L'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di cinque minuti. La pianificazione è facoltativa; se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione dell'indicizzatore dati, vedere [Creare un indicizzatore](/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Gestire nomi campo diversi
I nomi campo nell'indice esistente sono talvolta diversi dai nomi proprietà nella tabella. È possibile usare i mapping dei campi per eseguire il mapping dei nomi di proprietà forniti dalla tabella ai nomi di campo nell'indice di ricerca. Per altre informazioni sui mapping dei campi, vedere [mapping dei campi dell'indicizzatore ricerca cognitiva di Azure colmare le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Gestire le chiavi del documento
In Azure ricerca cognitiva la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo `Edm.String`. Il campo chiave è necessario per ogni documento da aggiungere all'indice. È l'unico campo obbligatorio.

Poiché le righe della tabella hanno una chiave composta, Azure ricerca cognitiva genera un campo sintetico denominato `Key` che è una concatenazione di valori di chiave di partizione e chiave di riga. Se ad esempio il parametro PartitionKey di una riga è `PK1` e il parametro RowKey è `RK1`, il valore del campo `Key` è `PK1RK1`.

> [!NOTE]
> Il valore `Key` può contenere caratteri non validi nelle chiavi del documento, ad esempio i trattini. È possibile risolvere i problemi legati ai caratteri non validi usando la `base64Encode` [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction). In questo caso, ricordarsi di usare la codifica Base64 sicura per gli URL quando si passano le chiavi dei documenti nelle chiamate API, ad esempio in una ricerca.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni
Quando un indicizzatore di tabelle viene configurato per l'esecuzione in base a una pianificazione, vengono indicizzate nuovamente solo le righe nuove o aggiornate, come definito dal valore `Timestamp` di una riga. Non è necessario specificare una norma di rilevamento delle modifiche. L'indicizzazione incrementale viene abilitata automaticamente.

Per indicare che alcuni documenti specifici devono essere rimossi dall'indice, è possibile usare una strategia di eliminazione temporanea. Invece di eliminare una riga, aggiungere una proprietà che ne indica l'eliminazione e impostare norme di rilevamento dell'eliminazione temporanea nell'origine dati. Il tipo di norme seguente, ad esempio, indica che una riga viene eliminata se la proprietà `IsDeleted` della riga è impostata sul valore `"true"`:

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Per impostazione predefinita, Azure ricerca cognitiva usa il filtro di query seguente: `Timestamp >= HighWaterMarkValue` . Poiché le tabelle di Azure non dispongono di un indice secondario nel campo `Timestamp`, questo tipo di query richiede un'analisi completa delle tabelle e risulta pertanto lenta nell'analisi delle tabelle di grandi dimensioni.


Ecco due possibili approcci per migliorare le prestazioni dell'indicizzazione delle tabelle. Entrambi gli approcci si basano sull'utilizzo delle partizioni delle tabelle: 

- Se i dati possono essere partizionati naturalmente in diversi intervalli della partizione, creare un'origine dati e un indicizzatore corrispondente per ogni intervallo della partizione. Ogni indicizzatore deve a questo punto elaborare un solo intervallo specifico della partizione e ciò va a vantaggio delle prestazioni della query. Se i dati da indicizzare hanno un numero ridotto di partizioni fisse, è ancora meglio, perché ciascun indicizzatore analizza solo una partizione. Per creare ad esempio un'origine dati per l'elaborazione di un intervallo della partizione con le chiavi da `000` a `100`, usare una query simile alla seguente: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se i dati sono partizionati in base all'ora (ad esempio, si crea una nuova partizione ogni giorno o ogni settimana), considerare l'approccio seguente: 
    - Usare una query nel formato: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorare lo stato dell'indicizzatore usando l'[API Get Indexer Status](/rest/api/searchservice/get-indexer-status) (Ottenere lo stato dell'indicizzatore) e aggiornare periodicamente la condizione `<TimeStamp>` della query in base al valore limite massimo corretto più recente. 
    - Con questo approccio, se è necessario attivare una reindicizzazione completa, occorre reimpostare la query dell'origine dati oltre a reimpostare l'indicizzatore. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare Azure ricerca cognitiva
Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).