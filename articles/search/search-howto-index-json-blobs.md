---
title: Eseguire ricerche su BLOB JSON
titleSuffix: Azure Cognitive Search
description: Eseguire l'indicizzazione di BLOB JSON di Azure per il contenuto di testo usando l'indicizzatore Azure ricerca cognitiva BLOB. Gli indicizzatori automatizzano l'inserimento di dati per le origini dati selezionate, ad esempio l'archiviazione BLOB di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533962"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Come indicizzare i BLOB JSON usando un indicizzatore BLOB in Azure ricerca cognitiva

Questo articolo illustra come configurare un [indicizzatore](search-indexer-overview.md) di BLOB di Azure ricerca cognitiva per estrarre il contenuto strutturato dai documenti JSON nell'archivio BLOB di Azure e renderlo ricercabile in Azure ricerca cognitiva. Questo flusso di lavoro crea un indice di ricerca cognitiva di Azure e lo carica con il testo esistente estratto dai BLOB JSON. 

Per indicizzare il contenuto JSON è possibile usare il [portale](#json-indexer-portal), le [API REST](#json-indexer-rest) o [.NET SDK](#json-indexer-dotnet). Il comune a tutti gli approcci è che i documenti JSON si trovano in un contenitore BLOB in un account di archiviazione di Azure. Per istruzioni sul push di documenti JSON da altre piattaforme non Azure, vedere l'articolo relativo [all'importazione di dati in Azure ricerca cognitiva](search-what-is-data-import.md).

I BLOB JSON in archiviazione BLOB di Azure sono in genere un singolo documento JSON (modalità di `json`analisi) o una raccolta di entità JSON. Per le raccolte, il BLOB può avere una **matrice** di elementi JSON ben formati (la modalità di analisi `jsonArray`è). I BLOB possono anche essere composti da più entità JSON singole separate da una nuova riga (la modalità `jsonLines`di analisi è). Il parametro **parsingMode** nella richiesta determina le strutture di output.

> [!NOTE]
> Per altre informazioni sull'indicizzazione di più documenti di ricerca da un singolo BLOB, vedere [indicizzazione uno-a-molti](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

Il metodo più semplice di indicizzazione dei documenti JSON consiste nell'usare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Analizzando i metadati nel contenitore BLOB di Azure, la procedura guidata [**Importa dati**](search-import-data-portal.md) può creare un indice predefinito, eseguire il mapping dei campi di origine ai campi di indice di destinazione e caricare l'indice con una sola operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

È consigliabile usare la stessa area o la stessa località per Azure ricerca cognitiva e archiviazione di Azure per una latenza più bassa ed evitare costi di larghezza di banda.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

[Accedere al portale di Azure](https://portal.azure.com/) e [creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) per contenere i dati. Il livello di accesso pubblico può essere impostato su uno dei valori validi.

Per recuperare i dati nella procedura guidata **Importa dati** , sono necessari il nome dell'account di archiviazione, il nome del contenitore e una chiave di accesso.

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

Nella pagina Panoramica del servizio di ricerca è possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **Nuova origine dati** l'origine deve essere **Archivio BLOB di Azure**, con le specifiche seguenti:

+ **Dati da estrarre** deve essere impostato su *Contenuto e metadati*. La selezione di questa opzione consente alla procedura guidata di dedurre uno schema di indice e di eseguire il mapping dei campi per l'importazione.
   
+ La **modalità di analisi** deve essere impostata su *JSON*, *matrici JSON* o *righe JSON*. 

  *JSON* articola ogni BLOB come singolo documento di ricerca, che viene visualizzato come elemento indipendente nei risultati della ricerca. 

  La *matrice JSON* è per i BLOB che contengono dati JSON ben formati, il JSON ben formato corrisponde a una matrice di oggetti o ha una proprietà che è una matrice di oggetti e si vuole che ogni elemento venga articolato come documento di ricerca autonomo e indipendente. Se in presenza di BLOB complessi non si seleziona *Matrice JSON*, l'intero BLOB viene inserito come un solo documento.

  Le *righe JSON* sono per i BLOB composti da più entità JSON separate da una nuova riga, in cui si vuole che ogni entità venga articolata come documento di ricerca indipendente autonomo. Se i BLOB sono complessi e non si sceglie la modalità di analisi delle *righe JSON* , l'intero BLOB viene inserito come singolo documento.
   
+ In **Contenitore di archiviazione** occorre specificare l'account di archiviazione e il contenitore oppure una stringa di connessione che si risolve nel contenitore. È possibile ottenere le stringhe di connessione nella pagina del portale del servizio BLOB.

   ![Definizione dell'origine dati BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-ignorare la pagina "arricchisci contenuto" della procedura guidata

L'aggiunta di competenze cognitive (o arricchimento) non è un requisito di importazione. A meno che non sia necessario aggiungere l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, è consigliabile ignorare questo passaggio.

Per ignorare il passaggio, fare clic sui pulsanti blu nella parte inferiore della pagina per "Avanti" e "Ignora".

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un elenco di campi in base ai metadati e al campionamento dei dati di origine. 

È possibile selezionare in blocco gli attributi facendo clic sulla casella di controllo nella parte superiore di una colonna attributo. Scegliere **recuperabile** e **ricercabile** per ogni campo che deve essere restituito a un'app client e soggetto all'elaborazione della ricerca full-text. Si noterà che gli Integer non sono full-text o ricerca fuzzy (i numeri vengono valutati Verbatim e spesso sono utili nei filtri).

Per ulteriori informazioni, vedere la descrizione degli [attributi degli indici](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e degli [analizzatori della lingua](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione di indice BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio ricerca cognitiva di Azure. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Se non si ha familiarità con gli indicizzatori, un *indicizzatore* è una risorsa in Azure ricerca cognitiva che esegue la ricerca per indicizzazione di un'origine dati esterna per il contenuto ricercabile. L'output della procedura guidata **Importa dati** è un indicizzatore che esegue la ricerca per indicizzazione dell'origine dati JSON, estrae contenuto ricercabile e lo importa in un indice in ricerca cognitiva di Azure.

   ![Definizione di indicizzatore BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Fare clic su **OK** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. 

Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

> [!NOTE]
> Se non vengono visualizzati i dati previsti, potrebbe essere necessario impostare più attributi in altri campi. Eliminare l'indice e l'indicizzatore appena creati, quindi eseguire di nuovo la procedura guidata modificando le selezioni per gli attributi di indice nel passaggio 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

È possibile usare l'API REST per indicizzare i BLOB JSON, seguendo un flusso di lavoro in tre parti comune a tutti gli indicizzatori in Azure ricerca cognitiva: creare un'origine dati, creare un indice, creare un indicizzatore. L'estrazione dei dati dall'archiviazione BLOB si verifica quando si invia la richiesta create Indexer. Al termine di questa richiesta, sarà presente un indice Queryable. 

È possibile esaminare il [codice di esempio REST](#rest-example) alla fine di questa sezione in cui viene illustrato come creare tutti e tre gli oggetti. Questa sezione contiene anche informazioni dettagliate sulle [modalità di analisi JSON](#parsing-modes), [BLOB singoli](#parsing-single-blobs), [matrici JSON](#parsing-arrays)e [matrici annidate](#nested-json-arrays).

Per l'indicizzazione JSON basata sul codice, usare il [post](search-get-started-postman.md) e l'API REST per creare questi oggetti:

+ [Indice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

L'ordine delle operazioni richiede la creazione e la chiamata di oggetti in questo ordine. A differenza del flusso di lavoro del portale, un approccio del codice richiede un indice disponibile per accettare i documenti JSON inviati tramite la richiesta **create Indexer** .

I BLOB JSON nell'archivio BLOB di Azure sono in genere un singolo documento JSON o una "matrice" JSON. L'indicizzatore BLOB in Azure ricerca cognitiva può analizzare una costruzione, a seconda di come si imposta il parametro **parsingMode** nella richiesta.

| Documento JSON | parsingMode | Descrizione | Disponibilità |
|--------------|-------------|--------------|--------------|
| Un solo documento per BLOB | `json` | Analizza i BLOB JSON come un singolo blocco di testo. Ogni BLOB JSON diventa un singolo documento di ricerca cognitiva di Azure. | Disponibile a livello generale nell'API [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e in [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Più documenti per BLOB | `jsonArray` | Analizza una matrice JSON nel BLOB, in cui ogni elemento della matrice diventa un documento di ricerca cognitiva di Azure separato.  | Disponibile a livello generale nell'API [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e in [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Più documenti per BLOB | `jsonLines` | Analizza un BLOB che contiene più entità JSON (una "matrice") separate da una nuova riga, in cui ogni entità diventa un documento di ricerca cognitiva di Azure separato. | Disponibile a livello generale nell'API [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e in [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1-assemblare gli input per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e la chiave amministratore per Azure ricerca cognitiva (nell'intestazione POST) e il nome e la chiave dell'account di archiviazione per l'archiviazione BLOB. È possibile usare il [post](search-get-started-postman.md) per inviare richieste HTTP ad Azure ricerca cognitiva.

Copiare i quattro valori seguenti nel blocco note in modo che sia possibile incollarli in una richiesta:

+ Nome del servizio ricerca cognitiva di Azure
+ Chiave amministratore di Azure ricerca cognitiva
+ Nome dell'account di archiviazione di Azure
+ Chiave dell'account di archiviazione di Azure

È possibile trovare questi valori nel portale:

1. Nelle pagine del portale per ricerca cognitiva di Azure copiare l'URL del servizio di ricerca dalla pagina panoramica.

2. Nel riquadro di spostamento a sinistra fare clic su **chiavi** e quindi copiare la chiave primaria o secondaria (sono equivalenti).

3. Passare alle pagine del portale per l'account di archiviazione. Nel riquadro di spostamento a sinistra, in **Impostazioni**, fare clic su **chiavi di accesso**. In questa pagina vengono forniti il nome e la chiave dell'account. Copiare il nome dell'account di archiviazione e una delle chiavi nel blocco note.

### <a name="2---create-a-data-source"></a>2-creare un'origine dati

Questo passaggio fornisce le informazioni di connessione all'origine dati utilizzate dall'indicizzatore. L'origine dati è un oggetto denominato in Azure ricerca cognitiva che rende permanente le informazioni di connessione. Il tipo di origine dati `azureblob`,, determina quali comportamenti di estrazione dei dati vengono richiamati dall'indicizzatore. 

Sostituire i valori validi per il nome del servizio, la chiave amministratore, l'account di archiviazione e i segnaposto della chiave dell'account.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3: creare un indice di ricerca di destinazione 

Gli indicizzatori sono associati allo schema di un indice. Se si usa l'API, anziché il portale, preparare un indice in anticipo in modo poterlo specificare per l'operazione dell'indicizzatore.

L'indice archivia contenuto ricercabile in ricerca cognitiva di Azure. Per creare un indice, fornire uno schema che specifichi i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza di ricerca. Se si crea un indice con gli stessi nomi di campo e gli stessi tipi di dati dell'origine, l'indicizzatore stabilirà la corrispondenza tra i campi di origine e i campi di destinazione, evitando all'utente di dover eseguire il mapping esplicito dei campi.

L'esempio seguente illustra una richiesta [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index). L'indice avrà un campo `content` ricercabile in cui archiviare il testo estratto dai BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4-configurare ed eseguire l'indicizzatore

Come per un indice e un'origine dati, l'indicizzatore è anche un oggetto denominato che è possibile creare e riutilizzare in un servizio ricerca cognitiva di Azure. Una richiesta completamente specificata per la creazione di un indicizzatore potrebbe avere un aspetto simile al seguente:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

La configurazione dell'indicizzatore è nel corpo della richiesta. Richiede un'origine dati e un indice di destinazione vuoto già esistente in Azure ricerca cognitiva. 

La pianificazione e i parametri sono facoltativi. Se vengono omessi, l'indicizzatore viene eseguito immediatamente, `json` usando come modalità di analisi.

Questo particolare indicizzatore non include i mapping dei campi. All'interno della definizione dell'indicizzatore è possibile escludere i **mapping dei campi** se le proprietà del documento JSON di origine corrispondono ai campi dell'indice di ricerca di destinazione. 


### <a name="rest-example"></a>Esempio REST

Questa sezione è un riepilogo di tutte le richieste usate per la creazione di oggetti. Per una discussione sulle parti del componente, vedere le sezioni precedenti di questo articolo.

### <a name="data-source-request"></a>Richiesta origine dati

Tutti gli indicizzatori richiedono un oggetto origine dati che fornisca informazioni di connessione ai dati esistenti. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Richiesta di indice

Tutti gli indicizzatori richiedono un indice di destinazione che riceve i dati. Il corpo della richiesta definisce lo schema dell'indice, costituito da campi, con attributi per supportare i comportamenti desiderati in un indice ricercabile. Questo indice deve essere vuoto quando si esegue l'indicizzatore. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Richiesta dell'indicizzatore

Questa richiesta Mostra un indicizzatore completamente specificato. Sono inclusi i mapping dei campi, che sono stati omessi negli esempi precedenti. Tenere presente che "Schedule", "Parameters" e "fieldMappings" sono facoltativi a condizione che esista un valore predefinito. Se si omette "Schedule", l'indicizzatore viene eseguito immediatamente. Se si omette "parsingMode", l'indice userà il valore predefinito "JSON".

La creazione dell'indicizzatore in Azure ricerca cognitiva attiva l'importazione dati. Viene eseguito immediatamente e successivamente in base a una pianificazione, se ne è stato specificato uno.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usare .NET SDK

.NET SDK ha una parità completa con l'API REST. È consigliabile rivedere la sezione relativa dall'API REST per apprenderne i concetti, il flusso di lavoro e i requisiti. Consultare quindi la seguente documentazione di riferimento sull'API .NET per implementare un indicizzatore JSON nel codice gestito.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modalità di analisi

I BLOB JSON possono assumere più forme. Il parametro **parsingMode** nell'indicizzatore JSON determina il modo in cui il contenuto BLOB JSON viene analizzato e strutturato in un indice ricerca cognitiva di Azure:

| parsingMode | Descrizione |
|-------------|-------------|
| `json`  | Indicizzare ogni BLOB come un singolo documento. Questa è la modalità predefinita. |
| `jsonArray` | Scegliere questa modalità se i BLOB sono costituiti da matrici JSON ed è necessario che ogni elemento della matrice diventi un documento separato in Azure ricerca cognitiva. |
|`jsonLines` | Scegliere questa modalità se i BLOB sono costituiti da più entità JSON, separate da una nuova riga, ed è necessario che ogni entità diventi un documento separato in Azure ricerca cognitiva. |

Un documento può essere considerato come un singolo elemento nei risultati della ricerca. Se si vuole che ogni elemento nella matrice venga visualizzato nei risultati della ricerca come elemento indipendente, usare l' `jsonArray` opzione o `jsonLines` nel modo appropriato.

Se si vuole, all'interno della definizione dell'indicizzatore è possibile usare i [mapping dei campi](search-indexer-field-mappings.md) per scegliere quali proprietà del documento JSON di origine vengono usate per popolare l'indice di ricerca di destinazione. Per `jsonArray` la modalità di analisi, se la matrice esiste come proprietà di livello inferiore, è possibile impostare una radice del documento che indichi la posizione della matrice all'interno del BLOB.

> [!IMPORTANT]
> Quando si usa `json`la `jsonArray` modalità `jsonLines` di analisi o, Azure ricerca cognitiva presuppone che tutti i BLOB nell'origine dati contengano JSON. Se è necessario supportare una combinazione di BLOB di tipo JSON e non JSON nella stessa origine dati, comunicare questa esigenza sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analizzare singoli BLOB JSON

Per impostazione predefinita, l' [indicizzatore BLOB di Azure ricerca cognitiva](search-howto-indexing-azure-blob-storage.md) analizza i BLOB JSON come singolo blocco di testo. È spesso possibile che si voglia preservare la struttura dei documenti JSON. Si supponga, ad esempio, che in Archiviazione BLOB di Azure sia presente il documento JSON seguente:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

L'indicizzatore BLOB analizza il documento JSON in un singolo documento di ricerca cognitiva di Azure. Per caricare un indice, l'indicizzatore stabilisce una corrispondenza tra i campi "text", "datePublished" e "tags" dell'origine e i campi di indice di destinazione tipizzati con lo stesso nome.

Come già indicato, i mapping dei campi non sono obbligatori. Dato un indice con i campi "text", "datePublished" e "tag", l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza che sia definito un mapping di campi nella richiesta.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizza matrici JSON

In alternativa, è possibile usare l'opzione di matrice JSON. Questa opzione è utile quando i BLOB contengono una *matrice di oggetti JSON ben formati*e si vuole che ogni elemento diventi un documento di Azure ricerca cognitiva separato. Dato il BLOB JSON seguente, ad esempio, è possibile popolare l'indice di Azure ricerca cognitiva con tre documenti distinti, ognuno con i campi "ID" e "Text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Per una matrice JSON, la definizione di indicizzatore dovrebbe avere un aspetto simile all'esempio seguente. Si noti che il parametro parsingMode specifica il parser `jsonArray`. Specificare il parser corretto e avere l'input di dati appropriato sono gli unici due requisiti specifici della matrice per l'indicizzazione dei BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Anche in questo caso il mapping dei campi può essere omesso. Dato un indice con i campi "id" e "text" con nome identico, l'indicizzatore di BLOB è in grado di dedurre il mapping corretto senza un elenco di mapping dei campi esplicito.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analizza matrici annidate
Per le matrici JSON con elementi annidati, è possibile specificare `documentRoot` un per indicare una struttura a più livelli. Ad esempio, se i BLOB sono simili a questo:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Usare questa configurazione per indicizzare la matrice contenuta nella proprietà `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analizza i BLOB separati da una nuova riga

Se il BLOB contiene più entità JSON separate da una nuova riga e si vuole che ogni elemento diventi un documento di Azure ricerca cognitiva separato, è possibile scegliere l'opzione per le righe JSON. Ad esempio, dato il BLOB seguente (in cui sono presenti tre entità JSON diverse), è possibile popolare l'indice di ricerca cognitiva di Azure con tre documenti distinti, ognuno con i campi "ID" e "Text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Per le righe JSON, la definizione dell'indicizzatore dovrebbe essere simile all'esempio seguente. Si noti che il parametro parsingMode specifica il parser `jsonLines`. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Anche in questo caso, si noti che è possibile omettere i mapping dei `jsonArray` campi, analogamente alla modalità di analisi.

## <a name="add-field-mappings"></a>Aggiungere i mapping dei campi

Quando i campi di origine e di destinazione non sono perfettamente allineati, è possibile definire una sezione di mapping dei campi nel corpo della richiesta per esplicitare le associazioni tra i campi.

Attualmente, Azure ricerca cognitiva non può indicizzare direttamente i documenti JSON arbitrari perché supporta solo i tipi di dati primitivi, le matrici di stringhe e i punti GeoJSON. È tuttavia possibile usare i **mapping dei campi** per selezionare parti del documento JSON e "portarle" nei campi di livello superiore del documento di ricerca. Per informazioni sulle nozioni di base sui mapping dei campi, vedere [mapping dei campi in Azure ricerca cognitiva Indexers](search-indexer-field-mappings.md).

Tornando al documento JSON di esempio:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Si supponga di avere un indice di ricerca con i campi seguenti: `text` di tipo `Edm.String`, `date` di tipo `Edm.DateTimeOffset` e `tags` di tipo `Collection(Edm.String)`. Si noti la discrepanza tra "datePublished" nell'origine e il campo `date` nell'indice. Per eseguire il mapping del file JSON per ottenere la forma desiderata, usare i mapping dei campi seguenti:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

I nomi dei campi di origine nei mapping vengono specificati mediante la notazione di tipo [Puntatore JSON](https://tools.ietf.org/html/rfc6901) . Iniziare con una barra per fare riferimento alla radice del documento JSON, quindi passare alla proprietà desiderata, a un livello di annidamento arbitrario, usando un percorso separato da barre.

È anche possibile fare riferimento a singoli elementi della matrice usando un indice a base zero. Ad esempio, per selezionare il primo elemento della matrice "tags" dall'esempio precedente, usare un mapping dei campi analogo al seguente:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se un nome del campo di origine in un percorso di mapping dei campi fa riferimento a una proprietà inesistente nel file JSON, il mapping verrà ignorato senza errori. Ciò consente di supportare documenti con schemi diversi, ovvero un caso di utilizzo comune. Poiché non è disponibile alcuna convalida, sarà necessario evitare accuratamente gli errori di ortografia nella specifica dei mapping dei campi.
>
>

## <a name="see-also"></a>Vedi anche

+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Indicizzazione dell'archiviazione BLOB di Azure con Azure ricerca cognitiva](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore BLOB di Azure ricerca cognitiva](search-howto-index-csv-blobs.md)
+ [Esercitazione: eseguire la ricerca di dati semistrutturati dall'archivio BLOB di Azure](search-semi-structured-data.md)
