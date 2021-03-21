---
title: Come modellare tipi di dati complessi
titleSuffix: Azure Cognitive Search
description: Le strutture di dati annidati o gerarchici possono essere modellate in un indice di ricerca cognitiva di Azure usando i tipi di dati ComplexType e Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/27/2020
ms.openlocfilehash: b0b2dd9904682121c83b22b9029097e7ee57fb11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96303752"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Come modellare tipi di dati complessi in Azure ricerca cognitiva

I set di impostazioni esterni usati per popolare un indice di ricerca cognitiva di Azure possono essere disponibili in molte forme. A volte includono sottostrutture gerarchiche o nidificate. Esempi possono includere più indirizzi per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un singolo libro e così via. Nei termini di modellazione, è possibile visualizzare queste strutture denominate tipi di dati *complessi*, *composti* *, composti* o *aggregati* . Il termine utilizzo di Azure ricerca cognitiva per questo concetto è di **tipo complesso**. In ricerca cognitiva di Azure, i tipi complessi vengono modellati usando **campi complessi**. Un campo complesso è un campo che contiene elementi figlio (campi secondari) che possono essere di qualsiasi tipo di dati, inclusi altri tipi complessi. Funziona in modo analogo ai tipi di dati strutturati in un linguaggio di programmazione.

I campi complessi rappresentano un singolo oggetto nel documento o una matrice di oggetti, a seconda del tipo di dati. I campi di tipo `Edm.ComplexType` rappresentano singoli oggetti, mentre i campi di tipo `Collection(Edm.ComplexType)` rappresentano matrici di oggetti.

Azure ricerca cognitiva supporta in modo nativo i tipi e le raccolte complesse. Questi tipi consentono di modellare praticamente qualsiasi struttura JSON in un indice di ricerca cognitiva di Azure. Nelle versioni precedenti delle API di Azure ricerca cognitiva, è possibile importare solo set di righe bidimensionali. Nella versione più recente, l'indice può ora corrispondere più strettamente ai dati di origine. In altre parole, se i dati di origine hanno tipi complessi, l'indice può avere anche tipi complessi.

Per iniziare, si consiglia il [set di dati degli Alberghi](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), che è possibile caricare nella procedura guidata **importa dati** nel portale di Azure. La procedura guidata rileva i tipi complessi nell'origine e suggerisce uno schema dell'indice basato sulle strutture rilevate.

> [!Note]
> Il supporto per i tipi complessi è diventato disponibile a livello generale a partire da `api-version=2019-05-06` . 
>
> Se la soluzione di ricerca è basata sulle soluzioni alternative precedenti dei set di impostazioni flat in una raccolta, è necessario modificare l'indice per includere i tipi complessi come supportato nella versione più recente dell'API. Per altre informazioni sull'aggiornamento delle versioni dell'API, vedere [eseguire l'aggiornamento alla versione più recente dell'API REST](search-api-migration.md) o [eseguire l'aggiornamento alla versione più recente di .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Esempio di struttura complessa

Il documento JSON seguente è costituito da campi semplici e campi complessi. I campi complessi, ad esempio `Address` e `Rooms` , presentano sottocampi. `Address` dispone di un singolo set di valori per quei sottocampi, poiché si tratta di un singolo oggetto nel documento. Al contrario, `Rooms` dispone di più set di valori per i relativi sottocampi, uno per ogni oggetto nella raccolta.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"]
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

## <a name="indexing-complex-types"></a>Indicizzazione di tipi complessi

Durante l'indicizzazione, è possibile avere un massimo di 3000 elementi in tutte le raccolte complesse in un singolo documento. Un elemento di una raccolta complessa è un membro di tale raccolta, quindi, nel caso di Room (l'unica raccolta complessa nell'esempio di Hotel), ogni stanza è un elemento. Nell'esempio precedente, se il "Motel del punto segreto" avesse 500 stanze, il documento dell'hotel avrebbe 500 elementi della stanza. Per le raccolte complesse annidate, anche ogni elemento annidato viene conteggiato, oltre all'elemento esterno (padre).

Questo limite si applica solo alle raccolte complesse e non ai tipi complessi (ad esempio, Address) o alle raccolte di stringhe (ad esempio, tag).

## <a name="creating-complex-fields"></a>Creazione di campi complessi

Come per qualsiasi definizione di indice, è possibile usare il portale, l' [API REST](/rest/api/searchservice/create-index)o [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindex) per creare uno schema che includa tipi complessi. 

Nell'esempio seguente viene illustrato uno schema di indice JSON con semplici campi, raccolte e tipi complessi. Si noti che all'interno di un tipo complesso ogni sottocampo dispone di un tipo e può avere attributi, così come i campi di primo livello. Lo schema corrisponde ai dati di esempio precedenti. `Address` è un campo complesso che non è una raccolta (un hotel ha un indirizzo). `Rooms` è un campo di raccolta complesso (un hotel ha molte chat).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aggiornamento di campi complessi

Tutte le [regole di reindicizzazione](search-howto-reindex.md) applicabili ai campi in generale sono comunque valide per i campi complessi. Riaffermando alcune delle regole principali, l'aggiunta di un campo a un tipo complesso non richiede la ricompilazione dell'indice, ma la maggior parte delle modifiche.

### <a name="structural-updates-to-the-definition"></a>Aggiornamenti strutturali alla definizione

È possibile aggiungere nuovi campi secondari a un campo complesso in qualsiasi momento senza la necessità di una ricompilazione dell'indice. Ad esempio, l'aggiunta di "Cap" a `Address` o "comforts" a `Rooms` è consentita, proprio come l'aggiunta di un campo di primo livello a un indice. I documenti esistenti hanno un valore null per i nuovi campi finché i campi non vengono popolati in modo esplicito tramite l'aggiornamento dei dati.

Si noti che all'interno di un tipo complesso ogni sottocampo dispone di un tipo e può avere attributi, così come i campi di primo livello

### <a name="data-updates"></a>Aggiornamenti dei dati

L'aggiornamento dei documenti esistenti in un indice con l' `upload` azione funziona allo stesso modo per i campi complessi e semplici: tutti i campi vengono sostituiti. Tuttavia, `merge` o `mergeOrUpload` se applicato a un documento esistente, non funziona allo stesso modo in tutti i campi. In particolare, `merge` non supporta l'Unione di elementi all'interno di una raccolta. Questa limitazione esiste per le raccolte di tipi primitivi e raccolte complesse. Per aggiornare una raccolta, è necessario recuperare il valore completo della raccolta, apportare modifiche e quindi includere la nuova raccolta nella richiesta dell'API index.

## <a name="searching-complex-fields"></a>Ricerca di campi complessi

Le espressioni di ricerca in formato libero funzionano come previsto con i tipi complessi. Se un campo o un sottocampo ricercabile in qualsiasi punto di un documento corrisponde, il documento stesso è una corrispondenza.

Le query sono più sfumate quando si hanno più termini e operatori e alcuni termini hanno nomi di campo specificati, come possibile con la [sintassi Lucene](query-lucene-syntax.md). Questa query, ad esempio, tenta di trovare la corrispondenza con due termini, "Portland" e "OR", rispetto a due campi secondari del campo Address:

> `search=Address/City:Portland AND Address/State:OR`

Query come questa non sono *correlate* per la ricerca full-text, a differenza dei filtri. Nei filtri, le query sui sottocampi di una raccolta complessa sono correlate usando variabili di intervallo in [ `any` o `all` ](search-query-odata-collection-operators.md). La query Lucene riportata sopra restituisce documenti che contengono sia "Portland, Maine" che "Portland, Oregon", insieme ad altre città in Oregon. Questa situazione si verifica perché ogni clausola si applica a tutti i valori del relativo campo nell'intero documento, quindi non esiste alcun concetto di "documento secondario corrente". Per altre informazioni, vedere informazioni sui [filtri di raccolta OData in Azure ricerca cognitiva](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selezione di campi complessi

Il `$select` parametro viene usato per scegliere i campi che vengono restituiti nei risultati della ricerca. Per usare questo parametro per selezionare sottocampi specifici di un campo complesso, includere il campo padre e il campo secondario separati da una barra ( `/` ).

> `$select=HotelName, Address/City, Rooms/BaseRate`

I campi devono essere contrassegnati come recuperabili nell'indice se desiderati nei risultati della ricerca. In un'istruzione è possibile utilizzare solo i campi contrassegnati come recuperabili `$select` .

## <a name="filter-facet-and-sort-complex-fields"></a>Campi di filtro, facet e di ordinamento complessi

La stessa [sintassi del percorso OData](query-odata-filter-orderby-syntax.md) usata per il filtro e le ricerche in campo può essere usata anche per il facet, l'ordinamento e la selezione dei campi in una richiesta di ricerca. Per i tipi complessi, vengono applicate le regole che determinano quali sottocampi possono essere contrassegnati come ordinabili o con facet. Per ulteriori informazioni su queste regole, vedere il [riferimento all'API create index](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Sottocampi facet

Qualsiasi campo secondario può essere contrassegnato come facet a meno che non sia di tipo `Edm.GeographyPoint` o `Collection(Edm.GeographyPoint)` .

Il conteggio dei documenti restituiti nei risultati dei facet viene calcolato per il documento padre (un hotel), non per i documenti secondari in una raccolta complessa (chat). Si supponga, ad esempio, che un hotel disponga di 20 camere di tipo "Suite". Dato questo parametro facet `facet=Rooms/Type` , il numero di facet sarà uno per l'hotel, non 20 per le chat room.

### <a name="sorting-complex-fields"></a>Ordinamento di campi complessi

Le operazioni di ordinamento si applicano ai documenti (Alberghi) e non ai documenti secondari (chat). Quando si dispone di una raccolta di tipi complessi, ad esempio chat room, è importante tenere presente che non è possibile eseguire l'ordinamento in chat. In realtà, non è possibile eseguire l'ordinamento in una raccolta.

Le operazioni di ordinamento funzionano quando i campi hanno un solo valore per documento, se il campo è un campo semplice o un campo secondario in un tipo complesso. Ad esempio, può `Address/City` essere ordinabile perché esiste un solo indirizzo per ogni albergo, quindi Ordina gli `$orderby=Address/City` Hotel per città.

### <a name="filtering-on-complex-fields"></a>Applicazione di filtri a campi complessi

È possibile fare riferimento ai campi secondari di un campo complesso in un'espressione di filtro. È sufficiente usare la stessa [sintassi del percorso OData](query-odata-filter-orderby-syntax.md) usata per il facet, l'ordinamento e la selezione dei campi. Ad esempio, il filtro seguente restituirà tutti gli hotel in Canada:

> `$filter=Address/Country eq 'Canada'`

Per filtrare in un campo di raccolta complesso, è possibile usare un' **espressione lambda** con gli [ `any` `all` operatori e](search-query-odata-collection-operators.md). In tal caso, la **variabile di intervallo** dell'espressione lambda è un oggetto con campi secondari. È possibile fare riferimento a questi campi secondari con la sintassi del percorso OData standard. Ad esempio, il filtro seguente restituirà tutti gli alberghi con almeno una stanza Deluxe e tutte le camere non fumanti:

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

Come per i campi semplici di primo livello, i campi secondari semplici dei campi complessi possono essere inclusi solo nei filtri se l'attributo **filtrabile** è impostato su `true` nella definizione dell'indice. Per altre informazioni, vedere le informazioni di [riferimento sull'API create index](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passaggi successivi

Provare il [set di dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) nella procedura guidata **Importa dati** . Per accedere ai dati, è necessario disporre delle informazioni di connessione Cosmos DB fornite nel file Leggimi.

Con queste informazioni a disposizione, il primo passaggio della procedura guidata consiste nel creare una nuova origine dati Azure Cosmos DB. Più avanti nella procedura guidata, quando si arriva alla pagina di indice di destinazione, verrà visualizzato un indice con tipi complessi. Creare e caricare questo indice, quindi eseguire query per comprendere la nuova struttura.

> [!div class="nextstepaction"]
> [Guida introduttiva: creazione guidata portale per importazione, indicizzazione e query](search-get-started-portal.md)
