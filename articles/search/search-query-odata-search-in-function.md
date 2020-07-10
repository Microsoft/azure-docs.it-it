---
title: Guida di riferimento alle funzioni OData search.in
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso della funzione search.in in Azure ricerca cognitiva query.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 1748a334c024401d845145947ecd55519f61e5e3
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206930"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>`search.in`Funzione OData in Azure ricerca cognitiva

Uno scenario comune nelle [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) consiste nel controllare se un singolo campo in ogni documento è uguale a uno dei molti valori possibili. Ad esempio, questo è il modo in cui alcune applicazioni implementano il [taglio di sicurezza](search-security-trimming-for-azure-search.md) , verificando un campo contenente uno o più ID entità rispetto a un elenco di ID entità che rappresentano l'utente che ha emesso la query. Un modo per scrivere una query come questa consiste nell'usare gli [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) operatori e:

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

Tuttavia, esiste un modo più breve per scriverlo, usando la `search.in` funzione:

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> Oltre a essere più brevi e facili da leggere, `search.in` l'utilizzo di offre anche vantaggi in merito alle [prestazioni](#bkmk_performance) ed evita determinate [limitazioni di dimensioni dei filtri](search-query-odata-filter.md#bkmk_limits) quando sono presenti centinaia o addirittura migliaia di valori da includere nel filtro. Per questo motivo, è consigliabile usare `search.in` anziché una disgiunzione più complessa di espressioni di uguaglianza.

> [!NOTE]
> La versione 4,01 dello standard OData ha introdotto di recente l' [ `in` operatore](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), che ha un comportamento simile `search.in` a quello della funzione in Azure ricerca cognitiva. Tuttavia, ricerca cognitiva di Azure non supporta questo operatore, quindi è necessario usare la `search.in` funzione.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica della `search.in` funzione:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

La `search.in` funzione verifica se una determinata variabile di intervallo o campo stringa è uguale a uno di un determinato elenco di valori. L'uguaglianza tra la variabile e ogni valore nell'elenco viene determinata con distinzione tra maiuscole e minuscole, come per l' `eq` operatore. Un'espressione come `search.in(myfield, 'a, b, c')` equivale quindi a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, tranne per il fatto che `search.in` garantisce prestazioni di gran lunga migliori.

Sono disponibili due overload della `search.in` funzione:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Descrizione |
| --- | --- | --- |
| `variable` | `Edm.String` | Riferimento a un campo stringa (o una variabile di intervallo su un campo di raccolta di stringhe nel caso in cui `search.in` venga utilizzato all'interno di un' `any` `all` espressione or). |
| `valueList` | `Edm.String` | Stringa contenente un elenco delimitato di valori da confrontare con il `variable` parametro. Se il `delimiters` parametro non è specificato, i delimitatori predefiniti sono spazio e virgola. |
| `delimiters` | `Edm.String` | Stringa in cui ogni carattere viene considerato come separatore durante l'analisi del `valueList` parametro. Il valore predefinito di questo parametro è `' ,'` che indica che tutti i valori con spazi e/o virgole tra di essi verranno separati. Se è necessario usare separatori diversi da spazi e virgole perché i valori includono tali caratteri, è possibile specificare delimitatori alternativi, ad esempio `'|'` in questo parametro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestazioni di`search.in`

Se si usa `search.in`, è previsto un tempo di risposta inferiore al secondo quando il secondo parametro contiene un elenco di centinaia o migliaia di valori. Non esiste alcun limite esplicito per il numero di elementi che è possibile passare a `search.in` , sebbene sia ancora limitato dalla dimensione massima della richiesta. Tuttavia, la latenza aumenterà man mano che aumenta il numero di valori.

## <a name="examples"></a>Esempi

Trovare tutti gli alberghi con nome uguale a' Sea View Motel ' o ' Budget Hotel '. Le frasi contengono spazi, ovvero un delimitatore predefinito. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Trovare tutti gli alberghi con nome uguale a' Sea View Motel ' o ' Budget Hotel ' separati da' |'):

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Trovare tutti gli hotel con le chat room con tag "WiFi" o "tub":

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

Trovare una corrispondenza sulle frasi all'interno di una raccolta, ad esempio "rack di asciugamani riscaldati" o "asciugacapelli incluso" nei tag.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Trovare tutti gli alberghi senza il tag ' Motel ' o ' Cabin ':

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche nei documenti &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
