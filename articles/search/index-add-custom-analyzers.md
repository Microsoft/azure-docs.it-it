---
title: Aggiungere analizzatori personalizzati ai campi stringa
titleSuffix: Azure Cognitive Search
description: Configurare tokenizer di testo e filtri per i caratteri per eseguire analisi del testo sulle stringhe durante l'indicizzazione e le query.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604441"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Aggiungere analizzatori personalizzati a campi stringa in un indice di ricerca cognitiva di Azure

Un *analizzatore personalizzato* è una combinazione di Tokenizer, uno o più filtri di token e uno o più filtri di caratteri definiti nell'indice di ricerca, quindi fanno riferimento a definizioni di campo che richiedono l'analisi personalizzata. Il tokenizer è responsabile della suddivisione del testo in token, i filtri di token invece sono responsabili della modifica dei token emessi dal tokenizer. I filtri di caratteri preparano il testo di input prima che venga elaborato da Tokenizer. 

Un analizzatore personalizzato consente di controllare il processo di conversione del testo in token indicizzabili e ricercabili, consentendo di scegliere i tipi di analisi o filtro da richiamare e l'ordine in cui si verificano. Se si vuole usare un analizzatore predefinito con opzioni personalizzate, ad esempio la modifica di maxTokenLength in standard, è necessario creare un analizzatore personalizzato, con un nome definito dall'utente, per impostare tali opzioni.

Le situazioni in cui gli analizzatori personalizzati possono essere utili includono:

- Usare i filtri di tipo carattere per rimuovere il markup HTML prima che gli input di testo vengano suddivisi in token oppure sostituire determinati caratteri o simboli.

- Ricerca fonetica. Aggiungere un filtro fonetico per abilitare la ricerca basata sul suono e sulla compitazione di una parola.  

- Disabilitare l'analisi lessicale. Usare l'analizzatore Keyword per creare campi ricercabili che non vengono analizzati.  

- Ricerca rapida di prefissi/suffissi. Aggiungere il filtro di token di n-grammi di Edge per indicizzare i prefissi delle parole per poter abilitare la corrispondenza rapida dei prefissi. Combinarlo con il filtro di token Reverse per eseguire la corrispondenza dei suffissi.  

- Tokenizzazione personalizzata. Usare, ad esempio, il tokenizer Whitespace per suddividere le frasi in token usando lo spazio vuoto come delimitatore  

- ASCII folding. Aggiungere il filtro ASCII Folding Standard per normalizzare i segni diacritici come ö o ê nei termini di ricerca.  

Per creare un analizzatore personalizzato, specificarlo nella sezione "analizzatori" di un indice in fase di progettazione e quindi farvi riferimento nei campi ricercabili, Edm. String usando la proprietà "Analyzer" o la coppia "indexAnalyzer" e "searchAnalyzer".

> [!NOTE]  
> Gli analizzatori personalizzati creati non vengono esposti nel portale di Azure. L'unico modo per aggiungere un analizzatore personalizzato consiste nel codice che definisce un indice. 

## <a name="create-a-custom-analyzer"></a>Creare un analizzatore personalizzato

Una definizione dell'analizzatore include un nome, un tipo, uno o più filtri per i caratteri, un massimo di un tokenizer e uno o più filtri token per l'elaborazione post-token. I filtri di tipo carattere vengono applicati prima della suddivisione in token. I filtri dei token e i filtri di caratteri vengono applicati da sinistra a destra.

- I nomi in un analizzatore personalizzato devono essere univoci e non possono essere uguali a quelli predefiniti per gli analizzatori, tokenizer, i filtri di token o i filtri di caratteri. Può contenere solo lettere, numeri, spazi, trattini o caratteri di sottolineatura, deve iniziare e terminare con caratteri alfanumerici e non può contenere più di 128 caratteri. 

- Il tipo deve essere #Microsoft. Azure. search. CustomAnalyzer.

- "charFilters" può essere uno o più filtri da [filtri di caratteri](#CharFilter), elaborati prima della suddivisione in token, nell'ordine specificato. Alcuni filtri di caratteri includono opzioni, che possono essere impostate in una sezione "charFilter. I filtri di tipo carattere sono facoltativi.

- "Tokenizer" è esattamente un [Tokenizer](#tokenizers). È necessario specificare un valore. Se è necessario più di un tokenizer, è possibile creare più analizzatori personalizzati e assegnarli a ogni singolo campo nello schema dell'indice.

- "tokenFilters" può essere costituito da uno o più filtri dai [filtri dei token](#TokenFilters), elaborati dopo la suddivisione in token, nell'ordine specificato. Per i filtri token che includono opzioni, aggiungere una sezione "tokenFilter" per specificare la configurazione. I filtri dei token sono facoltativi.

Gli analizzatori non devono produrre token di lunghezza superiore a 300 caratteri o l'indicizzazione avrà esito negativo. Per tagliare un token lungo o per escluderli, usare rispettivamente **TruncateTokenFilter** e **LengthTokenFilter** . Per informazioni di riferimento, vedere [**filtri dei token**](#TokenFilters) .

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

All'interno di una definizione di indice è possibile inserire questa sezione in un punto qualsiasi del corpo di una richiesta di creazione dell'indice, ma in genere viene inserita alla fine:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

La definizione di un analizzatore è una parte dell'indice più grande. Le definizioni per filtri di caratteri, tokenizer e filtri di token vengono aggiunte all'indice solo se si stanno impostando opzioni personalizzate. Per usare un filtro o un tokenizer esistente così com'è, specificarlo per nome nella definizione dell'analizzatore. Per altre informazioni, vedere [create index (REST)](/rest/api/searchservice/create-index). Per altri esempi, vedere [aggiungere analizzatori in Azure ricerca cognitiva](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Testare gli analizzatori personalizzati

È possibile usare l' [analizzatore di test (REST)](/rest/api/searchservice/test-analyzer) per vedere come un analizzatore suddivide il testo in token.

**Richiesta**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Risposta**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Aggiornare gli analizzatori personalizzati

Una volta definito un analizzatore, un tokenizer, un filtro token o un filtro caratteri, non è possibile modificarlo. È possibile aggiungerne di nuovi a un indice esistente solo se il flag `allowIndexDowntime` è impostato su true nella richiesta di aggiornamento dell'indice:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Questa operazione porta l'indice offline almeno per alcuni secondi, causando la mancata riuscita dell'indicizzazione e delle richieste di query. Le prestazioni e la disponibilità alla scrittura dell'indice possono risultare inferiori per vari minuti dopo che l'indice è stato aggiornato o più a lungo per gli indici molto grandi, ma si tratta di effetti temporanei che alla fine si risolvono da soli.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Analizzatori incorporati

Se si vuole usare un analizzatore predefinito con opzioni personalizzate, la creazione di un analizzatore personalizzato è il meccanismo che consente di specificare tali opzioni. Al contrario, per usare un analizzatore incorporato così com'è, è sufficiente fare riferimento al [nome](search-analyzers.md#how-to-specify-analyzers) nella definizione del campo.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrizione e opzioni**|  
|-----------------|-------------------------------|---------------------------|  
|[parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| Il tipo si applica solo quando sono disponibili le opzioni |Considera l'intero contenuto di un campo come token singolo. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Separa in modo flessibile il testo in termini tramite un modello di espressione regolare. </br></br>**Opzioni** </br></br>lowercase (tipo: booleano): determina se i termini sono in minuscolo. Il valore predefinito è true. </br></br>[pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tipo: stringa): modello di espressione regolare a cui devono corrispondere i separatori di token. Il valore predefinito è `\W+` , che corrisponde a caratteri non alfanumerici. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: stringa): flag di espressione regolare. Il valore predefinito è una stringa vuota. Valori consentiti: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>stopwords (tipo: matrice di stringhe): elenco di parole non significative. Il valore predefinito è un elenco vuoto.|  
|[semplice](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|Il tipo si applica solo quando sono disponibili le opzioni |Divide il testo in corrispondenza di elementi diversi da lettere e li converte in lettere minuscole. |  
|[standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(denominato anche standard.lucene)|StandardAnalyzer|Analizzatore Lucene Standard, costituito dal tokenizer, dal filtro di lettere minuscole e dal filtro di parole non significative standard. </br></br>**Opzioni** </br></br>maxTokenLength (tipo: int): lunghezza massima del token. Il valore predefinito è 255. I token con lunghezza superiore a quella massima vengono suddivisi. Lunghezza massima del token che può essere usata è di 300 caratteri. </br></br>stopwords (tipo: matrice di stringhe): elenco di parole non significative. Il valore predefinito è un elenco vuoto.|  
|standardasciifolding.lucene|Il tipo si applica solo quando sono disponibili le opzioni |Analizzatore standard con filtro ASCII Folding Standard. |  
|[stop](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Suddivide il testo in corrispondenza di elementi diversi da lettere e applica i filtri di token di lettere minuscole e di parole non significative. </br></br>**Opzioni** </br></br>stopwords (tipo: matrice di stringhe): elenco di parole non significative. Il valore predefinito è un elenco per la lingua inglese. |  
|[Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|Il tipo si applica solo quando sono disponibili le opzioni |Analizzatore che usa il tokenizer whitespace. I token che contengono più di 255 caratteri vengono suddivisi.|  

 <sup>1</sup> I tipi di analizzatori presentano sempre il prefisso "#Microsoft.Azure.Search" nel codice, ad esempio "PatternAnalyzer" verrà di fatto specificato come "#Microsoft.Azure.Search.PatternAnalyzer". Per brevità, il prefisso è stato rimosso, ma è obbligatorio usarlo nel codice.

analyzer_type viene fornito solo per gli analizzatori che possono essere personalizzati. Se non esistono opzioni, come avviene con l'analizzatore keyword, non è associato nessun tipo #Microsoft.Azure.Search.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Filtri di caratteri

Nella tabella seguente i filtri di caratteri che vengono implementati usando Apache Lucene sono collegati alla documentazione dell'API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrizione e opzioni**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Filtro di caratteri che cerca di rimuovere i costrutti HTML.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Filtro di caratteri che applica i mapping definiti con l'opzione mappings. La corrispondenza è greedy. I criteri di ricerca più lunghi in un determinato punto hanno la precedenza. È consentita la sostituzione con una stringa vuota.  </br></br>**Opzioni**  </br></br> mappings (tipo: matrice di stringhe): elenco di mapping nel formato seguente: "a=>b" (tutte le occorrenze del carattere "a" vengono sostituite con il carattere "b"). Obbligatorio.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Filtro di caratteri che sostituisce i caratteri nella stringa di input. Usa un'espressione regolare per identificare le sequenze di caratteri da conservare e un criterio di sostituzione per identificare i caratteri da sostituire. Ad esempio, testo di input = "aa  bb aa bb", criterio="(aa)\\\s+(bb)" sostituzione="$1#$2", risultato = "aa#bb aa#bb".  </br></br>**Opzioni**  </br></br>pattern (tipo: stringa): obbligatorio.  </br></br>replacement (tipo: stringa): obbligatorio.|  

 <sup>1</sup> I tipi di filtri di caratteri presentano sempre il prefisso "#Microsoft.Azure.Search" nel codice, ad esempio "MappingCharFilter" verrà di fatto specificato come "#Microsoft.Azure.Search.MappingCharFilter". Il prefisso è stato rimosso per ridurre la larghezza della tabella, ma si ricordi di includerlo nel codice. Si noti che char_filter_type è disponibile solo per i filtri che possono essere personalizzati. Se non esistono opzioni, come avviene con l'analizzatore html_strip, non è associato nessun tipo #Microsoft.Azure.Search.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizer

Un tokenizer divide un testo continuo in una sequenza di token, ad esempio suddividendo una frase in parole. Nella tabella seguente i tokenizer che vengono implementati usando Apache Lucene sono collegati alla documentazione dell'API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrizione e opzioni**|  
|------------------|-------------------------------|---------------------------|  
|[classico](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizer basato sulla grammatica adatto per l'elaborazione della maggior parte dei documenti in lingue Europae.  </br></br>**Opzioni**  </br></br>maxTokenLength (tipo: int): lunghezza massima del token. Valore predefinito: 255, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Suddivide in token l'input da un token perimetrale in n-grammi di determinate dimensioni.  </br></br> **Opzioni**  </br></br>minGram (tipo: int)-valore predefinito: 1, massimo: 300.  </br></br>maxGram (tipo: int)-valore predefinito: 2, massimo: 300. Deve essere maggiore di minGram.  </br></br>tokenChars (tipo: matrice di stringhe): classi di caratteri da conservare nei token. Valori consentiti: </br>"letter", "digit", "whitespace", "punctuation", "symbol". Il valore predefinito è una matrice vuota, che mantiene tutti i caratteri. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Genera l'intero input come token singolo.  </br></br>**Opzioni**  </br></br>maxTokenLength (tipo: int): lunghezza massima del token. Valore predefinito: 256, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi.|  
|[lettera](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Divide il testo in corrispondenza di elementi diversi da lettere. I token che contengono più di 255 caratteri vengono suddivisi.|  
|[minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Divide il testo in corrispondenza di elementi diversi da lettere e li converte in lettere minuscole. I token che contengono più di 255 caratteri vengono suddivisi.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide il testo usando le regole specifiche della lingua.  </br></br>**Opzioni**  </br></br>maxTokenLength (tipo: int): lunghezza massima del token, valore predefinito: 255, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi. I token con più di 300 caratteri vengono prima di tutto divisi in token di lunghezza pari a 300 caratteri e ognuno di tali token viene quindi diviso in base al set maxTokenLength.  </br></br>isSearchTokenizer (tipo: booleano): impostarlo su true se usato come tokenizer di ricerca, impostarlo su false se usato come tokenizer di indicizzazione. </br></br>language (tipo: stringa): lingua da usare, valore predefinito "english". I valori consentiti includono: </br>"bangla", "bulgarian", "catalan", "chineseSimplified",  "chineseTraditional", "croatian", "czech", "danish", "dutch", "english",  "french", "german", "greek", "gujarati", "hindi", "icelandic", "indonesian", "italian", "japanese", "kannada", "korean", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovenian", "spanish", "swedish", "tamil", "telugu", "thai", "ukrainian", "urdu", "vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide il testo usando le regole specifiche della lingua e riduce le parole alle forme di base </br></br>**Opzioni** </br></br>maxTokenLength (tipo: int): lunghezza massima del token, valore predefinito: 255, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi. I token con più di 300 caratteri vengono prima di tutto divisi in token di lunghezza pari a 300 caratteri e ognuno di tali token viene quindi diviso in base al set maxTokenLength. </br></br> isSearchTokenizer (tipo: booleano): impostarlo su true se usato come tokenizer di ricerca, impostarlo su false se usato come tokenizer di indicizzazione. </br></br>language (tipo: stringa): lingua da usare, valore predefinito "english". I valori consentiti includono: </br>"arabic", "bangla", "bulgarian", "catalan", "croatian", "czech", "danish", "dutch", "english", "estonian", "finnish", "french", "german", "greek", "gujarati", "hebrew", "hindi", "hungarian", "icelandic", "indonesian", "italian", "kannada", "latvian", "lithuanian", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovak", "slovenian", "spanish", "swedish", "tamil", "telugu", "turkish", "ukrainian", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Suddivide in token l'input in n-grammi di determinate dimensioni. </br></br>**Opzioni** </br></br>minGram (tipo: int)-valore predefinito: 1, massimo: 300. </br></br>maxGram (tipo: int)-valore predefinito: 2, massimo: 300. Deve essere maggiore di minGram. </br></br>tokenChars (tipo: matrice di stringhe): classi di caratteri da conservare nei token. Valori consentiti: "letter", "digit", "whitespace", "punctuation", "symbol". Il valore predefinito è una matrice vuota, che mantiene tutti i caratteri. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer per le gerarchie di tipo percorso. **Opzioni** </br></br>delimiter (tipo: stringa): valore predefinito: '/. </br></br>replacement (tipo: stringa): se impostato, sostituisce il carattere di delimitazione. Il valore predefinito corrisponde al valore del delimitatore. </br></br>maxTokenLength (tipo: int): lunghezza massima del token. Valore predefinito: 300, massimo: 300. I percorsi più lunghi rispetto a maxTokenLength vengono ignorati. </br></br>reverse (tipo: booleano): se true, genera il token in ordine inverso. Valore predefinito: false. </br></br>skip (tipo: booleano): token iniziali da ignorare. Il valore predefinito è 0.|  
|[pattern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Il tokenizer usa i criteri di ricerca regex per costruire token distinti. </br></br>**Opzioni** </br></br> [pattern](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (Type: String): criterio di espressione regolare per la corrispondenza di separatori di token. Il valore predefinito è `\W+` , che corrisponde a caratteri non alfanumerici. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: stringa): flag di espressione regolare. Il valore predefinito è una stringa vuota. Valori consentiti: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>group (tipo: int): gruppo da estrarre nei token. Il valore predefinito è -1 (suddivisione).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Suddivide il testo in base alle [regole di segmentazione del testo Unicode](https://unicode.org/reports/tr29/). </br></br>**Opzioni** </br></br>maxTokenLength (tipo: int): lunghezza massima del token. Valore predefinito: 255, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Suddivide in token gli URL e gli indirizzi di posta elettronica come un unico token. </br></br>**Opzioni** </br></br> maxTokenLength (tipo: int): lunghezza massima del token. Valore predefinito: 255, massimo: 300. I token con lunghezza superiore a quella massima vengono suddivisi.|  
|[Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|Il tipo si applica solo quando sono disponibili le opzioni |Divide il testo in corrispondenza degli spazi vuoti. I token che contengono più di 255 caratteri vengono suddivisi.|  

 <sup>1</sup> I tipi di tokenizer presentano sempre il prefisso "#Microsoft.Azure.Search" nel codice, ad esempio "ClassicTokenizer" verrà di fatto specificato come "#Microsoft.Azure.Search.ClassicTokenizer". Il prefisso è stato rimosso per ridurre la larghezza della tabella, ma si ricordi di includerlo nel codice. Si noti che tokenizer_type viene fornito solo per Tokenizer che possono essere personalizzati. Se non esistono opzioni, come avviene con il tokenizer letter, non è associato nessun tipo #Microsoft.Azure.Search.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Filtri di token

Un filtro di token viene usato per filtrare o modificare i token generati da un tokenizer. È possibile, ad esempio, specificare un filtro lowercase che converte tutti i caratteri in lettere minuscole. In un analizzatore personalizzato possono essere presenti più filtri di token. I filtri di token vengono eseguiti nello stesso ordine in cui sono elencati. 

Nella tabella seguente i filtri di token che vengono implementati usando Apache Lucene sono collegati alla documentazione dell'API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrizione e opzioni**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Filtro di token che applica il normalizzatore per la lingua araba per normalizzare l'ortografia.|  
|[apostrophe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Rimuove tutti i caratteri dopo un apostrofo (incluso l'apostrofo stesso). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converte i caratteri Unicode alfabetici, numerici e simbolici non compresi nei primi 127 caratteri ASCII (blocco Unicode "Latino di base") negli equivalenti ASCII, se esistenti.<br /><br /> **Opzioni**<br /><br /> preserveOriginal (tipo: booleano): se true, il token originale viene conservato. Il valore predefinito è false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Forma digrammi di termini CJK generati da StandardTokenizer.<br /><br /> **Opzioni**<br /><br /> ignoreScripts (tipo: matrice di stringhe): script da ignorare. I valori consentiti includono: "han", "hiragana", "katakana", "hangul". Il valore predefinito è un elenco vuoto.<br /><br /> outputUnigrams (tipo: booleano): impostarlo su true per visualizzare sempre sia gli unigrammi che i digrammi. Il valore predefinito è false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza le differenze di larghezza per le lingue CJK. Riduce le varianti ASCII a larghezza intera a quelle equivalenti del latino di base e le varianti Katakana a metà larghezza a quelle kana equivalenti. |  
|[classico](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Rimuove i possessivi dell'inglese e i punti dagli acronimi. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Costruisce digrammi per i termini frequenti durante l'indicizzazione. Anche i termini singoli continuano a essere indicizzati, con i digrammi sovrapposti.<br /><br /> **Opzioni**<br /><br /> commonWords (tipo: matrice di stringhe): set di parole comuni. Il valore predefinito è un elenco vuoto. Obbligatorio.<br /><br /> ignoreCase (tipo: booleano): se true, la corrispondenza non fa distinzione tra maiuscole e minuscole. Il valore predefinito è false.<br /><br /> queryMode (tipo: booleano): genera i digrammi, quindi rimuove le parole comuni e i termini singoli seguiti da una parola comune. Il valore predefinito è false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Scompone le parole composte presenti in molte lingue germaniche.<br /><br /> **Opzioni**<br /><br /> wordList (tipo: matrice di stringhe): elenco di parole con cui eseguire il confronto. Il valore predefinito è un elenco vuoto. Obbligatorio.<br /><br /> minWordSize (tipo: int): vengono elaborate solo parole di lunghezza superiore a quella specificata. Il valore predefinito è 5.<br /><br /> minSubwordSize (tipo: int): vengono visualizzate solo sottoparole di lunghezza superiore a quella specificata. Il valore predefinito è 2.<br /><br /> maxSubwordSize (tipo: int): vengono visualizzate solo sottoparole di lunghezza inferiore a quella specificata. Il valore predefinito è 15.<br /><br /> onlyLongestMatch (tipo: booleano): aggiunge all'output solo la sottoparola con corrispondenza più lunga. Il valore predefinito è false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Genera n-grammi delle dimensioni specificate a partire dall'inizio o dalla fine di un token di input.<br /><br /> **Opzioni**<br /><br /> minGram (tipo: int)-valore predefinito: 1, massimo: 300.<br /><br /> maxGram (tipo: int)-valore predefinito: 2, massimo 300. Deve essere maggiore di minGram.<br /><br /> side (tipo: stringa): specifica da quale lato dell'input deve essere generato l'n-gramma. Valori consentiti: "front", "back" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Rimuove le elisioni. Ad esempio, "l'avion" (l'aereo) viene convertito in "avion" (aereo).<br /><br /> **Opzioni**<br /><br /> articles (tipo: matrice di stringhe): set di articoli da rimuovere. Il valore predefinito è un elenco vuoto. Se non è impostato alcun elenco di articoli, per impostazione predefinita vengono rimossi tutti gli articoli del francese.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza i caratteri del tedesco in base all'euristica dell'[algoritmo snowball German2](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza il testo in hindi per rimuovere alcune differenze nelle varianti di ortografia. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizza la rappresentazione Unicode del testo nelle lingue indiane.
|[mantenere](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Filtro di token che mantiene solo i token con testo contenuto nell'elenco di parole specificato.<br /><br /> **Opzioni**<br /><br /> keepWords (tipo: matrice di stringhe): elenco di parole da mantenere. Il valore predefinito è un elenco vuoto. Obbligatorio.<br /><br /> keepWordsCase (tipo: booleano): se true, applica prima il minuscolo a tutte le parole. Il valore predefinito è false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Contrassegna i termini come parole chiave.<br /><br /> **Opzioni**<br /><br /> keywords (tipo: matrice di stringhe): elenco di parole da contrassegnare come parole chiave. Il valore predefinito è un elenco vuoto. Obbligatorio.<br /><br /> ignoreCase (tipo: booleano): se true, applica prima il minuscolo a tutte le parole. Il valore predefinito è false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Genera due volte ogni token in ingresso, una volta come parola chiave e una come parola non chiave. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Filtro kstem con prestazioni elevate per l'inglese. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Rimuove le parole troppo lunghe o troppo corte.<br /><br /> **Opzioni**<br /><br /> min (tipo: int): numero minimo. Valore predefinito: 0, massimo: 300.<br /><br /> max (tipo: int): numero massimo. Valore predefinito: 300, massimo: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita il numero di token durante l'indicizzazione.<br /><br /> **Opzioni**<br /><br /> maxTokenCount (tipo: int): numero massimo di token da generare. Il valore predefinito è 1.<br /><br /> consumeAllTokens (tipo: booleano): specifica se devono essere utilizzati tutti i token dell'input anche se viene raggiunto il valore maxTokenCount. Il valore predefinito è false.|  
|[minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza il testo del token applicando il minuscolo. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genera n-grammi delle dimensioni specificate.<br /><br /> **Opzioni**<br /><br /> minGram (tipo: int)-valore predefinito: 1, massimo: 300.<br /><br /> maxGram (tipo: int)-valore predefinito: 2, massimo 300. Deve essere maggiore di minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Usa espressioni regolari Java per generare più token, uno per ogni gruppo Capture in uno o più criteri.<br /><br /> **Opzioni**<br /><br /> patterns (tipo: matrice di stringhe): elenco di criteri da confrontare con ogni token. Obbligatorio.<br /><br /> preserveOriginal (tipo: booleano): impostarlo su true per restituire il token originale anche se uno dei criteri corrisponde. Valore predefinito: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Filtro di token che applica un criterio a ogni token nel flusso, sostituendo le occorrenze della corrispondenza con la stringa di sostituzione specificata.<br /><br /> **Opzioni**<br /><br /> pattern (tipo: stringa): obbligatorio.<br /><br /> replacement (tipo: stringa): obbligatorio.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni |Applica la normalizzazione per il persiano. |  
|[phonetic](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Crea i token per le corrispondenze fonetiche.<br /><br /> **Opzioni**<br /><br /> encoder (tipo: stringa): codificatore fonetico da usare. I valori consentiti includono: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Valore predefinito: "metaphone". Il valore predefinito è metaphone.<br /><br /> Per altre informazioni, vedere [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html).<br /><br /> replace (tipo: booleano): true se i token codificati devono sostituire i token originali, false se devono essere aggiunti come sinonimi. Il valore predefinito è true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Trasforma il flusso di token in base all'[algoritmo di stemming Porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[inverso](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Inverte la stringa del token. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza l'uso dei caratteri scandinavi intercambiabili. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Riduce i caratteri scandinavi åÅäæÄÆ->a e öÖøØ->o. Discrimina anche l'uso delle vocali doppie aa, ae, ao, oe e oo, lasciando solo la prima. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Crea combinazioni di token come token singolo.<br /><br /> **Opzioni**<br /><br /> maxShingleSize (tipo: int): il valore predefinito è 2.<br /><br /> minShingleSize (tipo: int): il valore predefinito è 2.<br /><br /> outputUnigrams (tipo: booleano): se true, il flusso di output contiene i token di input (unigrammi), oltre agli n-grammi. Il valore predefinito è true.<br /><br /> outputUnigramsIfNoShingles (tipo: booleano): se true, esegue l'override del comportamento outputUnigrams==false quando non sono disponibili n-grammi. Il valore predefinito è false.<br /><br /> tokenSeparator (tipo: stringa): stringa da usare quando si uniscono token adiacenti per formare un n-gramma. Il valore predefinito è " ".<br /><br /> filterToken (tipo: stringa): stringa da inserire per ogni posizione in cui non sono presenti token. Il valore predefinito è "_".|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtro di token Snowball.<br /><br /> **Opzioni**<br /><br /> language (tipo: stringa): i valori consentiti includono: "armenian", "basque", "catalan", "danish", "dutch", "english", "finnish", "french", "german", "german2", "hungarian", "italian", "kp", "lovins", "norwegian", "porter", "portuguese", "romanian", "russian", "spanish", "swedish", "turkish"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizza la rappresentazione Unicode del testo in sorani.<br /><br /> **Opzioni**<br /><br /> Nessuna.|  
|stemmer|StemmerTokenFilter|Filtro di stemming specifico della lingua.<br /><br /> **Opzioni**<br /><br /> language (tipo: stringa): i valori consentiti includono: <br /> -   [Arabo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [Armeno](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [Basco](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [brasiliano](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"bulgaro"<br />-   [Catalano](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [Ceca](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [Danish](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [olandese](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [Inglese](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [finlandese](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [francese](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"galiziano"<br />- "minimalGalician"<br />-   [tedesco](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [Greco](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"Hindi"<br />-   [Hungarian](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [Indonesiano](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [irlandese](https://snowballstem.org/otherapps/oregan/)<br />-   [Italiano](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [Latvian](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [Norwegian](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [Portoghese](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [Romanian](https://snowballstem.org/otherapps/romanian/)<br />-   [russo](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [Spagnolo](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [Swedish](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [turco](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|I termini di stemming del dizionario vengono contrassegnati come parole chiave, impedendo lo stemming verso il basso della catena. Da inserire prima dei filtri di stemming.<br /><br /> **Opzioni**<br /><br /> rules (tipo: matrice di stringhe): regole di stemming nel formato "word => stem", ad esempio "ran => run". Il valore predefinito è un elenco vuoto.  Obbligatorio.|  
|[parole non significative](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Rimuove le parole non significative da un flusso di token. Per impostazione predefinita, il filtro usa un elenco di parole non significative predefinite per la lingua inglese.<br /><br /> **Opzioni**<br /><br /> stopwords (tipo: matrice di stringhe): elenco di parole non significative. Non può essere specificato se viene specificato stopwordsList.<br /><br /> stopwordsList (tipo: stringa): elenco predefinito di parole non significative. Non può essere specificato se viene specificato stopwords. I valori consentiti includono: "arabic", "armenian", "basque", "brazilian", "bulgarian", "catalan", "czech", "danish", "dutch", "english", "finnish", "french", "galician", "german", "greek", "hindi", "hungarian", "indonesian", "irish", "italian", "latvian", "norwegian", "persian", "portuguese", "romanian", "russian", "sorani", "spanish", "swedish", "thai", "turkish". Valore predefinito: "english". Non può essere specificato se viene specificato stopwords. <br /><br /> ignoreCase (tipo: booleano): se true, viene applicato prima il minuscolo a tutte le parole. Il valore predefinito è false.<br /><br /> removeTrailing (tipo: booleano): se true, ignora l'ultimo termine di ricerca se è una parola non significativa. Il valore predefinito è true.
|[sinonimo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Trova i sinonimi corrispondenti costituiti da una o più parole in un flusso di token.<br /><br /> **Opzioni**<br /><br /> synonyms (tipo: matrice di stringhe): obbligatorio. Elenco di sinonimi in uno dei due formati seguenti:<br /><br /> -incredible, unbelievable, fabulous => amazing: tutti i termini a sinistra del simbolo => vengono sostituiti con tutti i termini a destra del simbolo.<br /><br /> -incredible, unbelievable, fabulous, amazing: elenco di parole equivalenti separate da virgole. Impostare l'opzione expand per modificare la modalità di interpretazione di questo elenco.<br /><br /> ignoreCase (tipo: booleano): riduce le lettere maiuscole dell'input per la corrispondenza. Il valore predefinito è false.<br /><br /> expand (tipo: booleano): se true, tutte le parole nell'elenco di sinonimi (se la notazione => non viene usata) eseguono il mapping reciproco. <br />L'elenco seguente: incredible, unbelievable, fabulous, amazing equivale a: incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing<br /><br />- Se false, l'elenco seguente: incredible, unbelievable, fabulous, amazing equivale a: incredible, unbelievable, fabulous, amazing => incredible.|  
|[Trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Elimina lo spazio vuoto iniziale e finale dai token. |  
|[truncate](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Tronca i termini a una lunghezza specifica.<br /><br /> **Opzioni**<br /><br /> Lunghezza (tipo: int)-valore predefinito: 300, massimo: 300. Obbligatorio.|  
|[unique](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtra i token con lo stesso testo del token precedente.<br /><br /> **Opzioni**<br /><br /> onlyOnSamePosition (tipo: booleano): se impostato, rimuove i duplicati solo nella stessa posizione. Il valore predefinito è true.|  
|[maiuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|Il tipo si applica solo quando sono disponibili le opzioni  |Normalizza il testo del token applicando il maiuscolo. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Suddivide le parole in sottoparole ed esegue trasformazioni facoltative sui gruppi di sottoparole.<br /><br /> **Opzioni**<br /><br /> generateWordParts (tipo: booleano): consente la generazione delle parti delle parole, ad esempio "AzureSearch" diventa "Azure" "Search". Il valore predefinito è true.<br /><br /> generateNumberParts (tipo: booleano): consente la generazione delle sottoparole numeriche. Il valore predefinito è true.<br /><br /> catenateWords (tipo: booleano): consente la concatenazione del numero massimo di porzioni di una parola, ad esempio "Azure-Search" diventa "AzureSearch". Il valore predefinito è false.<br /><br /> catenateNumbers (tipo: booleano): consente la concatenazione del numero massimo di porzioni di un numero, ad esempio "1-2" diventa "12". Il valore predefinito è false.<br /><br /> catenateAll (tipo: booleano): consente la concatenazione di tutte le sottoparole, ad esempio "Azure-Search-1" diventa "AzureSearch1". Il valore predefinito è false.<br /><br /> splitOnCaseChange (tipo: booleano): se true, divide le parole in corrispondenza di una lettera maiuscola, ad esempio "AzureSearch" diventa "Azure" "Search". Il valore predefinito è true.<br /><br /> preserveOriginal: le parole originali vengono mantenute e aggiunte all'elenco di sottoparole. Il valore predefinito è false.<br /><br /> splitOnNumerics (tipo: booleano): se true, divide in corrispondenza dei numeri, ad esempio "Azure1Search" diventa "Azure" "1" "Search". Il valore predefinito è true.<br /><br /> stemEnglishPossessive (tipo: booleano): l'elemento "'s" finale viene rimosso per ogni sottoparola. Il valore predefinito è true.<br /><br /> protectedWords (tipo: matrice di stringhe): token di cui evitare la delimitazione. Il valore predefinito è un elenco vuoto.|  

 <sup>1</sup> I tipi di filtri di token presentano sempre il prefisso "#Microsoft.Azure.Search" nel codice, ad esempio "ArabicNormalizationTokenFilter" verrà di fatto specificato come "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Il prefisso è stato rimosso per ridurre la larghezza della tabella, ma si ricordi di includerlo nel codice.  

## <a name="see-also"></a>Vedi anche

- [API REST di Azure ricerca cognitiva](/rest/api/searchservice/)
- [Analizzatori in Azure ricerca cognitiva (esempi)](search-analyzers.md#examples)
- [Creare un indice (REST)](/rest/api/searchservice/create-index)