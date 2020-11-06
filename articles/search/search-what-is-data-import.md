---
title: Importazione e inserimento di dati negli indici di ricerca
titleSuffix: Azure Cognitive Search
description: Popolare e caricare i dati in un indice in ricerca cognitiva di Azure da origini dati esterne.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: b57d55e91918ba612ad42acd5e6059ae0dbd0090
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422451"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Panoramica dell'importazione dati-Azure ricerca cognitiva

In ricerca cognitiva di Azure le query vengono eseguite sul contenuto caricato e salvato in un [indice di ricerca](search-what-is-an-index.md). Questo articolo esamina i due approcci di base per il popolamento di un indice: effettuare il *push* dei dati nell'indice a livello di codice o puntare a un [indicizzatore ricerca cognitiva di Azure](search-indexer-overview.md) in un'origine dati supportata per eseguire il *pull* dei dati.

Con entrambi gli approcci, l'obiettivo è caricare i dati da un'origine dati esterna in un indice di ricerca cognitiva di Azure. Azure ricerca cognitiva consentirà di creare un indice vuoto, ma fino a quando non si effettua il push o il pull dei dati, non è possibile eseguire query su di essi.

> [!NOTE]
> Se l' [arricchimento ai](cognitive-search-concept-intro.md) è un requisito della soluzione, è necessario usare il modello pull (indicizzatori) per caricare un indice. L'elaborazione esterna è supportata solo tramite skillsets collegato a un indicizzatore.

## <a name="pushing-data-to-an-index"></a>Push dei dati in un indice

Il modello push, usato per inviare i dati a livello di codice ad Azure ricerca cognitiva, è l'approccio più flessibile. Prima di tutto non ha restrizioni per il tipo di origine dati. È possibile eseguire il push di un set di dati composto da documenti JSON in un indice di ricerca cognitiva di Azure, presupponendo che per ogni documento del set di dati siano presenti campi mappati ai campi definiti nello schema dell'indice In secondo luogo non ha restrizioni per la frequenza di esecuzione. È possibile eseguire il push delle modifiche a un indice ogni volta che si vuole. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario dinamici, il modello push è l'unica opzione disponibile.

Questo approccio è più flessibile rispetto al modello pull, perché si possono caricare i documenti individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo. Il modello push consente anche di caricare documenti in Azure ricerca cognitiva indipendentemente da dove si trovino i dati.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Come eseguire il push dei dati in un indice di ricerca cognitiva di Azure

Per caricare uno o più documenti in un indice, è possibile usare le API seguenti:

+ [Aggiungere, aggiornare o eliminare documenti (API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) o [classe IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Per un'introduzione a ogni metodologia, vedere [Guida introduttiva: creare un indice di azure ricerca cognitiva usando PowerShell](./search-get-started-powershell.md) o [la Guida introduttiva a C#: creare un indice di ricerca cognitiva di Azure con .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Azioni di indicizzazione: upload, merge, mergeOrUpload, Delete

È possibile controllare il tipo di azione di indicizzazione in base al documento, specificando se il documento deve essere caricato in modo completo, unito al contenuto del documento esistente o eliminato.

Nell'API REST inviare richieste HTTP POST con i corpi delle richieste JSON all'URL dell'endpoint dell'indice ricerca cognitiva di Azure. Ogni oggetto JSON nella matrice "value" contiene la chiave del documento e specifica se un'azione di indicizzazione aggiunge, aggiorna o Elimina il contenuto del documento. Per un esempio di codice, vedere [caricare documenti](search-get-started-dotnet.md#load-documents).

In .NET SDK, assemblare i dati in un `IndexBatch` oggetto. Un oggetto `IndexBatch` incapsula una raccolta di `IndexAction` oggetti, ognuno dei quali contiene un documento e una proprietà che indica ad Azure ricerca cognitiva l'azione da eseguire su tale documento. Per un esempio di codice, vedere la [Guida introduttiva a C#](search-get-started-dotnet.md).


| @search.action | Description | Campi necessari per ogni documento | Note |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |L'azione `upload` è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se esiste già. |chiave, oltre a tutti gli altri campi da definire |Quando si aggiorna o si sostituisce un documento esistente, qualsiasi campo non specificato nella richiesta avrà il campo impostato su `null`. Ciò si verifica anche quando il campo è stato precedentemente impostato su un valore diverso da null. |
| `merge` |Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice, l'unione non riuscirà. |chiave, oltre a tutti gli altri campi da definire |I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. In .NET SDK sono inclusi i campi di tipo `DataType.Collection(DataType.String)` . Nell'API REST sono inclusi i campi di tipo `Collection(Edm.String)` . Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue un'unione con valore `["economy", "pool"]` per `tags`, il valore finale del campo `tags` sarà `["economy", "pool"]` e non `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Questa azione si comporta come `merge` se nell'indice esiste già un documento con la chiave specificata. Se il documento non esiste, si comporta come `upload` con un nuovo documento. |chiave, oltre a tutti gli altri campi da definire |- |
| `delete` |Rimuove il documento specificato dall'indice. |solo campo chiave |Tutti i campi diversi dal campo chiave specificati verranno ignorati. Se si vuole rimuovere un singolo campo da un documento, usare invece `merge` e impostare il campo su Null in modo esplicito. |

### <a name="formulate-your-query"></a>Formulare la query

Esistono due modi per [eseguire una ricerca nell'indice usando l'API REST](/rest/api/searchservice/Search-Documents). Uno consiste nell'inviare una richiesta HTTP POST in cui i parametri di query vengono definiti in un oggetto JSON nel corpo della richiesta. L'altro consiste nell'inviare una richiesta HTTP GET in cui i parametri di query vengono definiti nell'URL della richiesta. POST ha [limiti più ridotti](/rest/api/searchservice/Search-Documents) per quanto riguarda le dimensioni dei parametri di query rispetto a GET. Per questo motivo, è consigliabile usare POST, a meno di non avere circostanze particolari in cui l'uso di GET potrebbe essere più conveniente.

Sia per POST che per GET è necessario fornire il *nome del servizio* , il *nome dell'indice* e una versione dell' *API* nell'URL della richiesta. 

Per GET i parametri di query devono essere forniti nella *stringa di query* alla fine dell'URL. Per il formato dell'URL, vedere di seguito:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

Il formato di POST è lo stesso, ma con `api-version` nei parametri della stringa di query.

## <a name="pulling-data-into-an-index"></a>Pull dei dati in un indice

Il modello pull effettua una ricerca per indicizzazione su un'origine dati supportata e carica automaticamente i dati nell'indice. In Azure ricerca cognitiva questa funzionalità viene implementata tramite gli *indicizzatori* , attualmente disponibili per queste piattaforme:

+ [Archiviazione BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Archiviazione tabelle](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Database SQL di Azure, SQL Istanza gestita e SQL Server in macchine virtuali di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Gli indicizzatori connettono un indice a un'origine dati, in genere una tabella, una vista o una struttura equivalente, e mappano i campi di origine ai campi equivalenti nell'indice. Durante l'esecuzione il set di righe viene automaticamente trasformato in JSON e caricato nell'indice specificato. Tutti gli indicizzatori supportano la pianificazione, in modo da consentire di specificare la frequenza dell'aggiornamento dei dati. La maggior parte degli indicizzatori fornisce il rilevamento delle modifiche se l'origine dati lo supporta. Tramite il rilevamento di modifiche ed eliminazioni nei documenti esistenti, oltre al riconoscimento di nuovi documenti, gli indicizzatori eliminano la necessità di gestire attivamente i dati nell'indice.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Come eseguire il pull dei dati in un indice di ricerca cognitiva di Azure

La funzionalità di indicizzatore viene esposta nel [portale di Azure](search-import-data-portal.md), nell'[API REST](/rest/api/searchservice/Indexer-operations) e in [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient).

Un vantaggio dell'uso del portale è che Azure ricerca cognitiva in genere può generare automaticamente uno schema di indice predefinito leggendo i metadati del set di dati di origine. È possibile modificare l'indice generato fino al termine dell'elaborazione dell'indice. In seguito, sono consentite solo le modifiche che non richiedono la reindicizzazione. Se le modifiche da apportare influiscono direttamente sullo schema, è necessario ricompilare l'indice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificare l'importazione dei dati con Esplora ricerche

Un modo rapido per eseguire un controllo preliminare sul caricamento del documento consiste nell'usare **Esplora ricerche** nel portale. Esplora ricerche consente di eseguire query su un indice senza che sia necessario scrivere codice. L'esperienza di ricerca è basata sulle impostazioni predefinite, ad esempio la [sintassi semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search) e il [parametro di query searchMode](/rest/api/searchservice/search-documents) predefinito. I risultati vengono restituiti in JSON, in modo che sia possibile esaminare l'intero documento.

> [!TIP]
> Numerosi [esempi di codice di ricerca cognitiva di Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) includono set di impostazioni disponibili o facilmente disponibili, che offrono un modo semplice per iniziare. Il portale offre anche un indicizzatore e un'origine dati di esempio, costituita da un piccolo set di dati immobiliari, denominato "realestate-us-sample". Quando si esegue l'indicizzatore preconfigurato nell'origine dati di esempio, viene creato un indice che viene caricato con i documenti su cui possono essere eseguite query in Esplora ricerche o tramite codice scritto dall'utente.

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Procedura dettagliata per il portale: creare, caricare ed eseguire query su un indice](search-get-started-portal.md)