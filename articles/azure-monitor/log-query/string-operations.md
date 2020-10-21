---
title: Usare le stringhe nelle query di log di Monitoraggio di Azure | Microsoft Docs
description: Descrive come modificare e confrontare le stringhe, eseguire ricerche al loro interno e svolgere numerose altre operazioni sulle stringhe nelle query di log di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/19/2020
ms.openlocfilehash: 7838f9f1febcab073633dbb4af011e99acbe22d3
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310283"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Usare le stringhe nelle query di log di Monitoraggio di Azure


> [!NOTE]
> Prima di completare questa esercitazione, è necessario completare l'introduzione a [monitoraggio di azure log Analytics](get-started-portal.md) e [iniziare a usare le query di log di monitoraggio di Azure](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Questo articolo descrive come modificare e confrontare le stringhe, eseguire ricerche al loro interno e svolgere numerose altre operazioni sulle stringhe.

Ogni carattere in una stringa ha un numero di indice, in base alla relativa posizione. Il primo carattere è in corrispondenza dell'indice 0, il carattere successivo è 1 e così via. Diverse funzioni di stringa usano i numeri di indice, come illustrato nelle sezioni seguenti. Molti degli esempi seguenti usano il comando **print** per illustrare la manipolazione della stringa senza usare un'origine dati specifica.


## <a name="strings-and-escaping-them"></a>Stringhe e caratteri di escape
I valori di stringa sono racchiusi tra virgolette singole o doppie. La barra rovesciata ( \\ ) viene usata per usare caratteri di escape per il carattere che lo segue, ad esempio \t per Tab, \n per la nuova riga e \" il carattere di virgolette.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Per evitare che "\\" venga considerato come carattere di escape, aggiungere "\@" come prefisso per la stringa:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Confronti tra stringhe

Operatore       |Descrizione                         |Distinzione maiuscole/minuscole|Esempio (restituisce `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Uguale a                              |Sì           |`"aBc" == "aBc"`
`!=`           |Diverso da                          |Sì           |`"abc" != "ABC"`
`=~`           |Uguale a                              |No            |`"abc" =~ "ABC"`
`!~`           |Diverso da                          |No            |`"aBc" !~ "xyz"`
`has`          |La stringa a destra corrisponde a un termine completo a sinistra |No|`"North America" has "america"`
`!has`         |La stringa a destra non corrisponde a un termine completo a sinistra       |No            |`"North America" !has "amer"` 
`has_cs`       |La stringa a destra corrisponde a un termine completo a sinistra |Sì|`"North America" has_cs "America"`
`!has_cs`      |La stringa a destra non corrisponde a un termine completo a sinistra       |Sì            |`"North America" !has_cs "amer"` 
`hasprefix`    |La stringa a destra corrisponde a un prefisso di un termine a sinistra         |No            |`"North America" hasprefix "ame"`
`!hasprefix`   |La stringa a destra non corrisponde a un prefisso di un termine a sinistra     |No            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |La stringa a destra corrisponde a un prefisso di un termine a sinistra         |Sì            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |La stringa a destra non corrisponde a un prefisso di un termine a sinistra     |Sì            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |La stringa a destra corrisponde a un suffisso di un termine a sinistra         |No            |`"North America" hassuffix "ica"`
`!hassuffix`   |La stringa a destra non corrisponde a un suffisso di un termine a sinistra     |No            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |La stringa a destra corrisponde a un suffisso di un termine a sinistra         |Sì            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |La stringa a destra non corrisponde a un suffisso di un termine a sinistra     |Sì            |`"North America" !hassuffix_cs "icA"`
`contains`     |La stringa a destra è presente come sottosequenza nella stringa a sinistra  |No            |`"FabriKam" contains "BRik"`
`!contains`    |La stringa a destra non è presente come sottosequenza nella stringa a sinistra           |No            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |La stringa a destra è presente come sottosequenza nella stringa a sinistra  |Sì           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |La stringa a destra non è presente come sottosequenza nella stringa a sinistra           |Sì           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |La stringa a destra è una sottosequenza iniziale della stringa a sinistra|No            |`"Fabrikam" startswith "fab"`
`!startswith`  |La stringa a destra non è una sottosequenza iniziale della stringa a sinistra|No        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |La stringa a destra è una sottosequenza iniziale della stringa a sinistra|Sì            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |La stringa a destra non è una sottosequenza iniziale della stringa a sinistra|Sì        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |La stringa a destra è una sottosequenza di chiusura della stringa a sinistra|No             |`"Fabrikam" endswith "Kam"`
`!endswith`    |La stringa a destra non è una sottosequenza di chiusura della stringa a sinistra|No         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |La stringa a destra è una sottosequenza di chiusura della stringa a sinistra|Sì             |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`    |La stringa a destra non è una sottosequenza di chiusura della stringa a sinistra|Sì         |`"Fabrikam" !endswith_cs "brik"`
`matches regex`|La stringa a sinistra contiene una corrispondenza per la stringa a destra        |Sì           |`"Fabrikam" matches regex "b.*k"`
`in`           |È uguale a uno degli elementi       |Sì           |`"abc" in ("123", "345", "abc")`
`!in`          |Non è uguale a nessuno degli elementi   |Sì           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Conta le occorrenze di una sottostringa in una stringa. Può trovare la corrispondenza con stringhe di testo normale o usare espressioni regolari. Le corrispondenze con stringhe di testo normale possono sovrapporsi, mentre quelle con espressioni regolari no.

### <a name="syntax"></a>Sintassi
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argomenti:
- `text`: stringa di input 
- `search`: stringa di testo normale o espressione regolare di cui trovare la corrispondenza nel testo.
- `kind` - _normale_  |  _Regex_ (impostazione predefinita: Normal).

### <a name="returns"></a>Restituisce

Il numero di volte in cui la stringa di ricerca può essere trovata nel contenitore. Le corrispondenze con stringhe di testo normale possono sovrapporsi, mentre quelle con espressioni regolari no.

### <a name="examples"></a>Esempi

#### <a name="plain-string-matches"></a>Corrispondenze con stringhe di testo normale

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Corrispondenze con espressioni regolari

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

Ottiene una corrispondenza per un'espressione regolare da una stringa specifica. Facoltativamente, converte anche la sottostringa estratta nel tipo specificato.

### <a name="syntax"></a>Sintassi

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argomenti

- `regex`: espressione regolare.
- `captureGroup`: costante intera positiva che indica il gruppo Capture da estrarre. 0 per la corrispondenza completa, 1 per il valore corrispondente alle prime '('parentesi')' nell'espressione regolare, 2 o più per le parentesi successive.
- `text`: stringa da cercare.
- `typeLiteral`: valore letterale di tipo facoltativo, ad esempio typeof(long). Se specificato, la sottostringa estratta viene convertita nel tipo.

### <a name="returns"></a>Restituisce
La sottostringa corrispondente nel gruppo Capture indicato da captureGroup, facoltativamente convertita in typeLiteral.
Se non ci sono corrispondenze o la conversione del tipo non riesce, restituisce null.

### <a name="examples"></a>Esempi

L'esempio seguente estrae l'ultimo ottetto di *ComputerIP* da un record di heartbeat:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

L'esempio seguente estrae l'ultimo ottetto, esegue il cast a un tipo *real* (numero) e calcola il valore IP successivo
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Nell'esempio seguente viene eseguita una ricerca nella stringa *Trace* per una definizione di "Duration". Viene eseguito il cast della corrispondenza a un tipo *real* e il valore viene moltiplicato per una costante di tempo (1 s) *eseguendo il cast di Duration a un tipo timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* restituisce true se l'argomento è una stringa vuota o null (vedere anche *isnull*).
- *isnotempty* restituisce true se l'argomento non è una stringa vuota o null (vedere anche *isnotnull*). alias: *notempty*.

### <a name="syntax"></a>Sintassi

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Esempi

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Suddivide un URL in parti (protocollo, host, porta e così via) e restituisce un oggetto dizionario contenente le parti come stringhe.

### <a name="syntax"></a>Sintassi

```
parseurl(urlstring)
```

### <a name="examples"></a>Esempi

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Il risultato sarà:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

Sostituire tutte le corrispondenze di espressioni regolari con un'altra stringa. 

### <a name="syntax"></a>Sintassi

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argomenti

- `regex`: espressione regolare in base a cui trovare una corrispondenza. Può contenere gruppi di acquisizione tra '('parentesi')'.
- `rewrite`: espressione regolare che sostituisce un'eventuale corrispondenza in base a un'espressione regolare. Usare \0 per fare riferimento all'intera corrispondenza, \1 per il primo gruppo Capture, \2 e così via per i gruppi Capture successivi.
- `input_text`: stringa di input in cui cercare.

### <a name="returns"></a>Restituisce
Il testo dopo la sostituzione di tutte le corrispondenze dell'espressione regolare con le valutazioni di rewrite. Le corrispondenze non si sovrappongono.

### <a name="examples"></a>Esempi

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Può avere i risultati seguenti:

Attività                                        |valore sostituito
------------------------------------------------|----------------------------------------------------------
4663 - È stato effettuato un tentativo di accedere a un oggetto  |Activity ID 4663: È stato effettuato un tentativo di accedere a un oggetto.


## <a name="split"></a>Split

Suddivide una determinata stringa in base a un delimitatore specificato e restituisce una matrice di sottostringhe risultanti.

### <a name="syntax"></a>Sintassi
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argomenti:

- `source`: stringa da suddividere in base al delimitatore specificato.
- `delimiter`: delimitatore che verrà usato per suddividere la stringa di origine.
- `requestedIndex`: indice in base zero facoltativo. Se specificato, la matrice di stringhe restituita conterrà solo tale elemento, se esistente.


### <a name="examples"></a>Esempi

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Concatena gli argomenti di stringa (supporta da 1 a 16 argomenti).

### <a name="syntax"></a>Sintassi
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Esempi
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Restituisce la lunghezza di una stringa.

### <a name="syntax"></a>Sintassi
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Esempi
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Estrarre una sottostringa da una determinata stringa di origine, a partire dall'indice specificato. Facoltativamente, è possibile specificare la lunghezza della sottostringa richiesta.

### <a name="syntax"></a>Sintassi
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argomenti:

- `source`: stringa di origine da cui verrà estratta la sottostringa.
- `startingIndex`: posizione del carattere iniziale in base zero della sottostringa richiesta.
- `length`: parametro facoltativo che può essere usato per specificare la lunghezza richiesta della sottostringa restituita.

### <a name="examples"></a>Esempi
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Converte una stringa specificata in lettere tutte minuscole o tutte maiuscole.

### <a name="syntax"></a>Sintassi
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Esempi
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Passaggi successivi
Continuare con le esercitazioni avanzate:
* [Funzioni di aggregazione](aggregations.md)
* [Aggregazioni avanzate](advanced-aggregations.md)
* [Grafici e diagrammi](charts.md)
* [Uso di JSON e strutture dei dati](json-data-structures.md)
* [Scrittura di query avanzate](advanced-query-writing.md)
* [Join - analisi incrociata](joins.md)
