---
title: Filtrare in base ai risultati della ricerca
titleSuffix: Azure Cognitive Search
description: Filtrare in base all'identità di sicurezza utente, al linguaggio, alla posizione geografica o ai valori numerici per ridurre i risultati della ricerca nelle query in Ricerca cognitiva di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure.Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191008"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search 

Un *filtro* fornisce i criteri per la selezione dei documenti usati in una query di Ricerca cognitiva di Azure.A filter provides criteria for selecting documents used in an Azure Cognitive Search query. Se non si usano i filtri, in una ricerca vengono inclusi tutti i documenti nell'indice. Un filtro definisce l'ambito di una query di ricerca restringendolo a un subset dei documenti. Un filtro può ad esempio limitare la ricerca full-text ai soli prodotti con un marchio o un colore specifico, con un prezzo superiore a una determinata soglia.

Alcune esperienze di ricerca impongono requisiti di filtro come parte dell'implementazione, ma è possibile usare i filtri ogni volta che si vuole limitare una ricerca usando criteri *basati su valore* (ad esempio per limitare l'ambito della ricerca al tipo di prodotto "libri" per la categoria "saggistica" con editore "Simon & Schuster").

Se invece l'obiettivo è una ricerca mirata in *strutture* di dati specifiche (ad esempio se si vuole limitare l'ambito della ricerca al campo delle recensioni dei clienti), sono disponibili metodi alternativi, descritti di seguito.

## <a name="when-to-use-a-filter"></a>Quando usare un filtro

I filtri sono fondamentali per diverse esperienze di ricerca, ad esempio la ricerca di elementi nelle vicinanze, l'esplorazione in base a facet e i filtri di sicurezza, che mostrano solo i documenti che un utente è autorizzato a visualizzare. Se si implementa una di queste esperienze, è necessario un filtro. Le coordinate di georilevazione, la categoria di facet selezionata dall'utente o l'ID di sicurezza del richiedente vengono forniti dal filtro associato alla query di ricerca.

Ecco alcuni scenari di esempio:

1. Usare un filtro per sezionare l'indice in base ai valori di dati nell'indice. Se si ha uno schema con città, tipo di abitazione e attrattive, è possibile creare un filtro per selezionare in modo esplicito i documenti che soddisfano i criteri (Seattle, condomini, lungomare). 

   La ricerca full-text con gli stessi input produce spesso risultati simili, ma un filtro è più preciso, perché richiede una corrispondenza esatta delle condizioni di filtro nel contenuto nell'indice. 

2. Usare un filtro se l'esperienza di ricerca prevede un requisito di filtro:

   * L'[esplorazione in base a facet](search-faceted-navigation.md) usa un filtro per passare la categoria di facet selezionata dall'utente.
   * La ricerca geografica usa un filtro per passare le coordinate della posizione corrente alle app che eseguono ricerche di elementi nelle vicinanze. 
   * I filtri di sicurezza passano gli identificatori di sicurezza come criteri di filtro, in cui una corrispondenza nell'indice funge da proxy per i diritti di accesso al documento.

3. Usare un filtro per applicare criteri di ricerca in un campo numerico. 

   I campi numerici possono essere recuperati nel documento e visualizzati nei risultati della ricerca, ma non sono disponibili per la ricerca (soggetti a ricerca full-text) individualmente. Se sono necessari criteri di selezione basati su dati numerici, usare un filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Metodi alternativi per ridurre l'ambito

I filtri non sono l'unica opzione disponibile per limitare i risultati della ricerca. Le alternative seguenti potrebbero essere più appropriate, a seconda dell'obiettivo:

 + Il parametro di query `searchFields` delimita la ricerca a campi specifici. Se, ad esempio, l'indice fornisce campi separati per le descrizioni in inglese e in spagnolo, è possibile usare searchFields per definire i campi da usare per la ricerca full-text. 

+ Il parametro `$select` viene usato per specificare i campi da includere in un set di risultati, per limitare in modo efficace la risposta prima di inviarla all'applicazione chiamante. Questo parametro non perfeziona la query o riduce la raccolta di documenti, ma se l'obiettivo è una risposta più piccola, questo parametro è un'opzione da considerare. 

Per altre informazioni su questi parametri, vedere [Search Documents > Request > Query parameters](/rest/api/searchservice/search-documents#query-parameters) (Ricerca di documenti > Richiesta > Parametri di query).


## <a name="how-filters-are-executed"></a>Modalità di esecuzione dei filtri

In fase di query, un parser di filtri accetta i criteri come input, converte l'espressione in espressioni booleane atomiche rappresentate come albero e quindi valuta l'albero dei filtri sui campi filtrabili in un indice.

Il filtro si verifica insieme alla ricerca, qualificando i documenti da includere nell'elaborazione a valle per il recupero dei documenti e il punteggio di pertinenza. Se associato a una stringa di ricerca, il filtro riduce in modo efficace il set di richiamo dell'operazione di ricerca successiva. Se usato da solo (ad esempio, quando la stringa di query è vuota con `search=*`), il criterio di filtro è l'unico input. 

## <a name="defining-filters"></a>Definizione di filtri
I filtri sono espressioni OData, articolate usando un [sottoinsieme della sintassi OData V4 supportata in Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)di Azure. 

È possibile specificare un filtro per ogni operazione di **ricerca,** ma il filtro stesso può includere più campi, più criteri e, se si utilizza una funzione **ismatch,** più espressioni di ricerca full-text. In un'espressione di filtro composta da più parti, è possibile specificare i predicati in qualsiasi ordine (soggetto alle regole di precedenza degli operatori). Il riposizionamento dei predicati in una sequenza specifica non offre un miglioramento delle prestazioni rilevante.

Uno dei limiti di un'espressione di filtro è il limite di dimensione massima della richiesta. L'intera richiesta, compreso il filtro, può avere dimensioni massime di 16 MB per POST o 8 KB per GET. Esiste inoltre un limite al numero di clausole nell'espressione di filtro. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.

Gli esempi seguenti rappresentano definizioni di filtro prototipo in diverse API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrare i modelli di utilizzo

Negli esempi seguenti vengono illustrati diversi modelli di utilizzo per gli scenari di filtro. Per altre idee, vedere [OData expression syntax > Examples](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) (Sintassi delle espressioni OData -> Esempi).

+ Elemento **$filter** autonomo, senza una stringa di query, utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Si noti che la stringa di ricerca è solo un asterisco, che significa "corrispondenza di tutti i documenti".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Combinazione di stringa di query e **$filter**, dove il filtro crea il subset e la stringa di query fornisce gli input dei termini per la ricerca full-text nel subset filtrato. L'aggiunta di termini (a piedi diteatri a distanza) introduce punteggi di ricerca nei risultati, dove i documenti che meglio corrispondono ai termini sono classificati più in alto. L'utilizzo di un filtro con una stringa di query è il modello di utilizzo più comune.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Query composte, separate da "or", ciascuna con i propri criteri di filtro (ad esempio, 'beagle' in 'cane' or 'siamese' in 'gatto'). Le espressioni `or` combinate con vengono valutate singolarmente, con l'unione di documenti corrispondenti a ogni espressione inviata nella risposta. Questo modello di utilizzo `search.ismatchscoring` viene ottenuto tramite la funzione. È inoltre possibile utilizzare la `search.ismatch`versione senza punteggio, .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  È anche possibile combinare la ricerca `search.ismatchscoring` full-text tramite con filtri utilizzando `and` instead di `or`, ma questo è funzionalmente equivalente all'utilizzo dei `search` parametri e `$filter` in una richiesta di ricerca. Ad esempio, le due query seguenti producono lo stesso risultato:For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Leggere gli articoli seguenti per indicazioni complete sui casi d'uso specifici:

+ [Filtri facet](search-filters-facets.md)
+ [Filtri per linguaggio](search-filters-language.md)
+ [Limitazione per motivi di sicurezza](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisiti dei campi per il filtro

Nell'API REST, filtrabile è *attivato* per impostazione predefinita per i campi semplici. I campi filtrabili causano un aumento delle dimensioni dell'indice. Assicurarsi di impostare `"filterable": false` per i campi che non si prevede di usare in un filtro. Per altre informazioni sulle impostazioni delle definizioni di campo, vedere l'articolo relativo all'operazione [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

In .NET SDK la proprietà filterable è *disattivata* per impostazione predefinita. È possibile rendere un campo filtrabile impostando la [proprietà IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) dell'oggetto [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) corrispondente su `true`. È inoltre possibile eseguire questa operazione in modo dichiarativo utilizzando [l'attributo IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Nell'esempio seguente, l'attributo `BaseRate` viene impostato sulla proprietà di una classe modello che esegue il mapping alla definizione dell'indice.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Rendere filtrabile un campo esistente

Non è possibile modificare i campi esistenti per renderli filtrabili. È invece necessario aggiungere un nuovo campo o ricompilare l'indice. Per altre informazioni sulla ricostruzione di un indice o sul ripopolamento dei campi, vedere Come ricostruire un indice di Ricerca cognitiva di Azure.For more information about rebuilding an index or repopopopulating fields, [see How to rebuild an Azure Cognitive Search index.](search-howto-reindex.md)

## <a name="text-filter-fundamentals"></a>Concetti fondamentali sui filtri di testo

I filtri di testo confrontano i campi stringa con le stringhe letterali fornite nel filtro. A differenza della ricerca full-text, non esiste un'analisi lessicale o un'interruzione di parola per i filtri di testo, pertanto i confronti sono solo per corrispondenze esatte. Si supponga, ad *f* esempio, che un `$filter=f eq 'Sunny'` campo f `$filter=f eq 'sunny day'` contenga "giorno di sole", non corrisponda, ma lo farà. 

Per le stringhe di testo viene fatta distinzione tra maiuscole e minuscole. Non c'è minuscola di parole maiuscole: `$filter=f eq 'Sunny day'` non troverà "giorno di sole".

### <a name="approaches-for-filtering-on-text"></a>Approcci per filtrare il testo

| Approccio | Descrizione | Utilizzo |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funzione che confronta un campo con un elenco delimitato di stringhe. | Consigliato per [i filtri](search-security-trimming-for-azure-search.md) di sicurezza e per tutti i filtri in cui è necessario associare molti valori di testo non elaborato a un campo stringa. La funzione **search.in** è progettata per la velocità ed è `eq` `or`molto più veloce rispetto al confronto esplicito del campo con ogni stringa utilizzando e . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funzione che consente di combinare le operazioni di ricerca full-text con operazioni di filtro esclusivamente booleane nella stessa espressione filtro. | Utilizzare **search.ismatch** (o il relativo equivalente del punteggio, **search.ismatchscoring**) quando si desidera più combinazioni di filtri di ricerca in una richiesta. Può essere usata anche con un filtro *contains* per applicare un filtro su una stringa parziale all'interno di una stringa più grande. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Espressione definita dall'utente composta da campi, operatori e valori. | Utilizzare questa opzione quando si desidera trovare corrispondenze esatte tra un campo stringa e un valore stringa. |

## <a name="numeric-filter-fundamentals"></a>Concetti fondamentali sui filtri numerici

I campi numerici non sono di tipo `searchable` nel contesto della ricerca full-text. Solo le stringhe sono soggette a ricerca full-text. Se, ad esempio, si immette 99,99 come termine di ricerca, non si otterranno gli articoli con prezzo $99,99. Verranno invece visualizzati gli articoli con il numero 99 nei campi stringa del documento. Se quindi si hanno dati numerici, il presupposto è che verranno usati per i filtri, inclusi intervalli, facet, gruppi e così via. 

I documenti che contengono campi numerici (prezzo, dimensioni, SKU, ID) forniscono tali valori nei risultati della ricerca se il campo è contrassegnato come `retrievable`. Il concetto è che la ricerca full-text non è applicabile ai tipi di campi numerici.

## <a name="next-steps"></a>Passaggi successivi

Provare a usare **Esplora ricerche** nel portale per inviare query con parametri **$filter**. L'[indice real-estate-sample](search-get-started-portal.md) fornisce risultati interessanti per le query di filtro seguenti quando le si incolla nella barra di ricerca:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Per altri esempi, vedere [OData Filter Expression Syntax > Examples](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) (Sintassi delle espressioni di filtro OData -> Esempi).

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
