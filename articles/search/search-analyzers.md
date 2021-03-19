---
title: Analizzatori per l'elaborazione linguistica e del testo
titleSuffix: Azure Cognitive Search
description: Assegnare degli analizzatori ai campi di testo di ricerca in un indice per sostituire lo standard Lucene predefinito con alternative specifiche per lingua, predefinite o personalizzate.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d40dd0b91f9dcfb7bf5b6e8f084f25ee4f90d780
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596553"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva

Un *analizzatore* è un componente della [ricerca full-text](search-lucene-query-architecture.md) responsabile dell'elaborazione di testo in stringhe di query e documenti indicizzati. L'elaborazione del testo (nota anche come analisi lessicale) è trasformativa, modificando una stringa di query tramite azioni come le seguenti:

+ Rimuovere parole non essenziali (parole non significative) e punteggiatura
+ Suddividere frasi e parole sillabate in parti componenti
+ Minuscole in lettere maiuscole
+ Ridurre le parole nei moduli radice primitivi per migliorare l'efficienza di archiviazione, in modo che sia possibile trovare corrispondenze indipendentemente dalla tensione

L'analisi si applica ai `Edm.String` campi contrassegnati come "ricercabili", che indica la ricerca full-text. 

Per i campi con questa configurazione, l'analisi si verifica durante l'indicizzazione quando vengono creati i token, quindi di nuovo durante l'esecuzione delle query quando le query vengono analizzate e il motore esegue l'analisi dei token corrispondenti. È più probabile che si verifichi una corrispondenza quando lo stesso analizzatore viene usato sia per l'indicizzazione che per le query, ma è possibile impostare l'analizzatore per ogni carico di lavoro in modo indipendente, a seconda dei requisiti.

I tipi di query che *non* sono una ricerca full-text, ad esempio filtri o ricerca fuzzy, non passano attraverso la fase di analisi sul lato query. Il parser Invia invece tali stringhe direttamente al motore di ricerca, usando il modello fornito come base per la corrispondenza. In genere, questi moduli di query richiedono token di stringa interi per eseguire il lavoro dei criteri di ricerca. Per garantire l'intero token di termini durante l'indicizzazione, potrebbero essere necessari [analizzatori personalizzati](index-add-custom-analyzers.md). Per altre informazioni su quando e perché i termini di query vengono analizzati, vedere [ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md).

Per ulteriori informazioni sull'analisi lessicale, ascoltare la seguente clip video per una breve spiegazione.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Analizzatore predefinito  

In Azure ricerca cognitiva query, un analizzatore viene richiamato automaticamente in tutti i campi stringa contrassegnati come ricercabili. 

Per impostazione predefinita, Azure ricerca cognitiva USA [Apache Lucene standard Analyzer (standard Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), che suddivide il testo in elementi che seguono le regole ["segmentazione del testo Unicode"](https://unicode.org/reports/tr29/) . L'analizzatore standard, inoltre, converte tutti i caratteri nel rispettivo formato minuscolo. I documenti indicizzati e i termini di ricerca vengono sottoposti ad analisi durante l'indicizzazione e l'elaborazione delle query.  

È possibile eseguire l'override del valore predefinito nei singoli campi. Gli analizzatori alternativi possono essere un [analizzatore del linguaggio](index-add-language-analyzers.md) per l'elaborazione linguistica, un [analizzatore personalizzato](index-add-custom-analyzers.md)o un analizzatore incorporato dall' [elenco degli analizzatori disponibili](index-add-custom-analyzers.md#built-in-analyzers).

## <a name="types-of-analyzers"></a>Tipi di analizzatori

Nell'elenco seguente vengono descritti gli analizzatori disponibili in Azure ricerca cognitiva.

| Category | Descrizione |
|----------|-------------|
| [Analizzatore Lucene standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Valore predefinito. Non è necessaria alcun specifica o configurazione. Questo analizzatore di utilizzo generico è adatto per molti linguaggi e scenari.|
| Analizzatori incorporati | Utilizzato come-e a cui viene fatto riferimento in base al nome. Esistono due tipi: lingua e indipendente dal linguaggio. </br></br>Gli [analizzatori specializzati (indipendenti dal linguaggio)](index-add-custom-analyzers.md#built-in-analyzers) vengono usati quando gli input di testo richiedono un'elaborazione minima o specializzata. Esempi di analizzatori in questa categoria includono **Asciifolding**, **keyword**, **pattern**, **Simple**, **Stop**, **spazi**. </br></br>Gli [analizzatori del linguaggio](index-add-language-analyzers.md) vengono usati quando è necessario un supporto linguistico avanzato per i singoli linguaggi. Azure ricerca cognitiva supporta gli analizzatori del linguaggio 35 Lucene e 50 gli analizzatori di elaborazione del linguaggio naturale Microsoft. |
|[Analizzatori personalizzati](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Si riferisce a una configurazione definita dall'utente di una combinazione di elementi esistenti, costituiti da un tokenizer (obbligatorio) e da filtri facoltativi (char o token).|

Alcuni analizzatori incorporati, ad esempio **pattern** o **Stop**, supportano un set limitato di opzioni di configurazione. Per impostare queste opzioni, creare un analizzatore personalizzato, costituito dall'analizzatore incorporato e una delle opzioni alternative documentate negli [analizzatori predefiniti](index-add-custom-analyzers.md#built-in-analyzers). Come per qualsiasi configurazione personalizzata, attribuire un nome alla nuova configurazione, ad esempio *myPatternAnalyzer*, per distinguerla dall'analizzatore Pattern Lucene.

## <a name="how-to-specify-analyzers"></a>Come specificare gli analizzatori

L'impostazione di un analizzatore è facoltativa. Come regola generale, provare a usare prima l'analizzatore Lucene standard predefinito per vedere come viene eseguito. Se le query non riescono a restituire i risultati previsti, il trasferimento a un analizzatore diverso è spesso la soluzione corretta.

1. Quando si crea una definizione di campo nell' [Indice](/rest/api/searchservice/create-index), impostare la proprietà "Analyzer" su uno dei seguenti elementi: un [analizzatore incorporato](index-add-custom-analyzers.md#built-in-analyzers) , ad esempio una **parola chiave**, un [analizzatore del linguaggio](index-add-language-analyzers.md) come `en.microsoft` o un analizzatore personalizzato (definito nello stesso schema dell'indice).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Se si usa un [analizzatore del linguaggio](index-add-language-analyzers.md), è necessario usare la proprietà "Analyzer" per specificarla. Le proprietà "searchAnalyzer" e "indexAnalyzer" non si applicano agli analizzatori di linguaggio.

1. In alternativa, impostare "indexAnalyzer" e "searchAnalyzer" per variare l'analizzatore per ogni carico di lavoro. Queste proprietà vengono impostate insieme e sostituiscono la proprietà "Analyzer", che deve essere null. È possibile utilizzare diversi analizzatori per l'indicizzazione e le query se una di queste attività richiede una trasformazione specifica non necessaria per l'altra.

   ```json
     "fields": [
    {
      "name": "ProductGroup",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "standard"
    },
   ```

1. Solo per gli analizzatori personalizzati, creare una voce nella sezione **[analizzatori]** dell'indice e quindi assegnare l'analizzatore personalizzato alla definizione di campo per uno dei due passaggi precedenti. Per altre informazioni, vedere [Creare un indice](/rest/api/searchservice/create-index) e [Aggiungere analizzatori personalizzati](index-add-custom-analyzers.md).

## <a name="when-to-add-analyzers"></a>Quando aggiungere analizzatori

Il momento migliore per aggiungere e assegnare gli analizzatori è durante lo sviluppo attivo, quando eliminare e ricreare gli indici è routine.

Poiché gli analizzatori vengono usati per tokenize i termini, è necessario assegnare un analizzatore quando viene creato il campo. Infatti, l'assegnazione di un analizzatore o indexAnalyzer a un campo che è già stato creato fisicamente non è consentita (anche se è possibile modificare la proprietà searchAnalyzer in qualsiasi momento senza alcun effetto sull'indice).

Per modificare l'analizzatore di un campo esistente, è necessario [ricompilare completamente l'indice](search-howto-reindex.md) (non è possibile ricompilare singoli campi). Per gli indici in produzione, è possibile rinviare una ricompilazione creando un nuovo campo con la nuova assegnazione dell'analizzatore e iniziare a usarlo al posto di quello precedente. Usare [Aggiornare un indice](/rest/api/searchservice/update-index) per incorporare il nuovo campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) per compilarlo. In un secondo momento, nell'ambito della manutenzione pianificata dell'indice, sarà possibile pulire l'indice per rimuovere i campi obsoleti.

Per aggiungere un nuovo campo a un indice esistente, chiamare [Update index](/rest/api/searchservice/update-index) per aggiungere il campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) per popolarlo.

Per aggiungere un analizzatore personalizzato a un indice esistente, passare il flag "allowIndexDowntime" nell' [indice di aggiornamento](/rest/api/searchservice/update-index) se si desidera evitare questo errore:

*"L'aggiornamento dell'indice non è consentito perché potrebbe causare tempi di inattività. Per aggiungere nuovi analizzatori, tokenizer, filtri di token o filtri di tipo carattere a un indice esistente, impostare il parametro di query ' allowIndexDowntime ' su' true ' nella richiesta di aggiornamento dell'indice. Si noti che questa operazione inserirà l'indice in modalità offline per almeno alcuni secondi, causando un errore di indicizzazione e richieste di query. Le prestazioni e la disponibilità di scrittura dell'indice possono essere compromesse per alcuni minuti dopo l'aggiornamento dell'indice o più a lungo per gli indici di grandi dimensioni.*

## <a name="recommendations-for-working-with-analyzers"></a>Consigli per l'utilizzo degli analizzatori

Questa sezione offre consigli su come usare gli analizzatori.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Usare un unico analizzatore in lettura-scrittura, a meno che non si disponga di requisiti specifici

Ricerca cognitiva di Azure consente di specificare analizzatori diversi per l'indicizzazione e la ricerca tramite proprietà aggiuntive dei campi indexAnalyzer e searchAnalyzer. Se non è specificato, l'analizzatore impostato con la proprietà analizzatore viene usato per l'indicizzazione e la ricerca. Se l'analizzatore non è specificato, viene usato l'analizzatore Lucene standard predefinito.

Una regola generale consiste nell'usare lo stesso analizzatore sia per l'indicizzazione che per l'esecuzione di query, salvo diversamente indicato da requisiti specifici. Assicurarsi di eseguire test approfonditi. Quando l'elaborazione del testo è diversa in fase di indicizzazione e ricerca, si corre il rischio che i termini di query e i termini indicizzati non corrispondano quando le configurazioni degli analizzatori di ricerca e indicizzazione non sono allineate.

### <a name="test-during-active-development"></a>Test durante lo sviluppo attivo

L'override dell'analizzatore standard richiede una ricompilazione dell'indice. Se possibile, stabilire quali analizzatori usare durante la fase di sviluppo attivo, prima di passare un indice nell'ambiente di produzione.

### <a name="inspect-tokenized-terms"></a>Controllare i termini in formato token

Se la ricerca non restituisce i risultati previsti, è molto probabile che esistano discrepanze a livello di token tra i termini di input nella query e i termini in formato token nell'indice. Se il token non corrispondono, non vengono individuate corrispondenze. Per esaminare l'output del tokenizer, è consigliabile usare l'[API di analisi](/rest/api/searchservice/test-analyzer) come strumento di indagine. La risposta è costituita da token, generati da un analizzatore specifico.

<a name="examples"></a>

## <a name="rest-examples"></a>Esempi REST

Gli esempi seguenti mostrano le definizioni degli analizzatori per alcuni scenari chiave.

+ [Esempio di analizzatore personalizzato](#Custom-analyzer-example)
+ [Esempio dell'assegnazione degli analizzatori a un campo](#Per-field-analyzer-assignment-example)
+ [Combinazioni di analizzatori per indicizzazione e ricerca](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Esempio di analizzatore del linguaggio](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Esempio di analizzatore personalizzato

Questo esempio illustra una definizione di analizzatore con opzioni personalizzate. Le opzioni personalizzate per filtri char, tokenizer e filtri token vengono specificate separatamente come costrutti denominati a cui si fa riferimento nella definizione dell'analizzatore. Gli elementi predefiniti vengono usati as-is, facendo riferimento a essi semplicemente in base al nome.

Passaggi di questo esempio:

+ Gli analizzatori sono una proprietà della classe di campo per un campo ricercabile.

+ Un analizzatore personalizzato fa parte della definizione di un indice. Esso può essere parzialmente personalizzato (ad esempio, tramite la personalizzazione di una singola opzione in un filtro) o personalizzato in più posizioni.

+ In questo caso, l'analizzatore personalizzato è "my_analyzer", che a sua volta usa il tokenizer standard personalizzato "my_standard_tokenizer" e due filtri token: un filtro asciifolding minuscolo e uno personalizzato "my_asciifolding".

+ Definisce inoltre due filtri di caratteri personalizzati: "map_dash" e "remove_whitespace". Il primo sostituisce tutti i trattini con caratteri di sottolineatura, mentre il secondo rimuove tutti gli spazi. Nelle regole di mapping gli spazi devono essere codificati con UTF-8. I filtri di caratteri vengono applicati prima della tokenizzazione e influiranno sui token risultanti (le interruzioni standard dei tokenizer sui trattini e gli spazi, ma non sui caratteri di sottolineatura).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Esempio di assegnazione dell'analizzatore in base al campo

L'analizzatore Standard è l'impostazione predefinita. Si supponga di volere sostituire l'elemento predefinito con un analizzatore predefinito diverso, ad esempio l'analizzatore Pattern. Se non si impostano delle opzioni personalizzate, occorre specificarlo in base al nome nella definizione del campo.

L'elemento "analizzatore" sostituisce l'analizzatore Standard campo per campo. Non vi è alcun override globale. In questo esempio, `text1` usa l'analizzatore Pattern, mentre `text2`, che non specifica un analizzatore, usa l'elemento predefinito.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Combinazioni di analizzatori per le operazioni di indicizzazione e ricerca

Le API includono attributi di indice aggiuntivi per specificare analizzatori diversi per l'indicizzazione e la ricerca. Gli attributi searchAnalyzer e indexAnalyzer devono essere specificati come coppia, sostituendo il singolo attributo analizzatore.


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Esempio di analizzatore del linguaggio

I campi contenenti stringhe in diverse lingue possono utsare un analizzatore del linguaggio, mentre altri campi mantengono il valore predefinito (o usano altri analizzatori predefiniti o personalizzati). Se si usa un analizzatore del linguaggio, questo deve essere usato per le operazioni di indicizzazione e ricerca. I campi che usano un analizzatore del linguaggio non possono disporre di analizzatori diversi per indicizzazione e ricerca.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>Esempi di C#

Se si usano gli esempi di codice di .NET SDK, è possibile aggiungere questi esempi per usare o configurare gli analizzatori.

+ [Assegnare un analizzatore incorporato](#Assign-a-language-analyzer)
+ [Configurare un analizzatore](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Assegnare un analizzatore del linguaggio

Qualsiasi analizzatore usato così com'è, senza configurazione, viene specificato in una definizione di campo. Non è necessario creare una voce nella sezione **[analizzatori]** dell'indice. 

Gli analizzatori di lingua vengono usati così come sono. Per usarli, chiamare [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer), specificando il tipo [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) che fornisce un analizzatore di testo supportato in ricerca cognitiva di Azure.

Gli analizzatori personalizzati sono specificati in modo analogo nella definizione del campo, ma per questo lavoro è necessario specificare l'analizzatore nella definizione dell'indice, come descritto nella sezione successiva.

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definire un analizzatore personalizzato

Quando è necessaria la personalizzazione o la configurazione, aggiungere un costrutto dell'analizzatore a un indice. Una volta definita, è possibile aggiungerla alla definizione del campo, come illustrato nell'esempio precedente.

Creare un oggetto [CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) . Un analizzatore personalizzato è una combinazione definita dall'utente di un tokenizer noto, di zero o più filtri di token e di zero o più nomi di filtro di caratteri:

+ [CustomAnalyzer. Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer.TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer.CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

Nell'esempio seguente viene creato un analizzatore personalizzato denominato "URL-Analyze" che usa il [uax_url_email Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer) e il [filtro dei token minuscoli](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase).

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

Per altri esempi, vedere [CustomAnalyzerTests. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

## <a name="next-steps"></a>Passaggi successivi

Una descrizione dettagliata dell'esecuzione di query è disponibile nella [ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md). L'articolo usa esempi per spiegare i comportamenti che potrebbero sembrare poco intuitivi sulla superficie.

Per ulteriori informazioni sugli analizzatori, vedere gli articoli seguenti:

+ [Analizzatori di linguaggi](index-add-language-analyzers.md)
+ [Analizzatori personalizzati](index-add-custom-analyzers.md)
+ [Creare un indice di ricerca](search-what-is-an-index.md)
+ [Creare un indice multilingue](search-language-support.md)