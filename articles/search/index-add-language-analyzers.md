---
title: Aggiungere analizzatori di lingua ai campi stringa
titleSuffix: Azure Cognitive Search
description: Analisi del testo lessicale multilingue per query e indici non in lingua inglese in ricerca cognitiva di Azure.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283147"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Aggiungere analizzatori di lingua ai campi stringa in un indice di ricerca cognitiva di Azure

L'*analizzatore del linguaggio* è un tipo specifico di [analizzatore di testo](search-analyzers.md) che esegue l'analisi lessicale usando le regole linguistiche della lingua di destinazione. Ogni campo che consente la ricerca ha una proprietà **analyzer**. Se l'indice contiene stringhe tradotte, ad esempio campi separati per un testo in lingua inglese e cinese, è possibile specificare gli analizzatori del linguaggio in ogni campo per accedere alle funzionalità linguistiche avanzate di tali analizzatori.  

Azure ricerca cognitiva supporta gli analizzatori 35 supportati da Lucene e 50 analizzatori supportati dalla tecnologia di elaborazione del linguaggio naturale Microsoft usata in Office e Bing.

## <a name="comparing-analyzers"></a>Confronto tra analizzatori

Alcuni sviluppatori potrebbero preferire la soluzione open source di Lucene, più semplice e familiare. Gli analizzatori del linguaggio Lucene sono più veloci, ma gli analizzatori Microsoft dispongono di funzionalità avanzate, ad esempio lemmatizzazione, scomposizione delle parole (in lingue come tedesco, danese, olandese, svedese, norvegese, estone, finlandese, ungherese, slovacco) e riconoscimento di entità (URL, messaggi di posta elettronica, date, numeri). Si consiglia di confrontare, se possibile, gli analizzatori Microsoft e Lucene per scegliere la soluzione più adatta al proprio caso. 

L'indicizzazione con gli analizzatori Microsoft è in media da due a tre volte più lenta rispetto agli analizzatori Lucene corrispondenti, a seconda della lingua. Le prestazioni della ricerca non devono essere influenzate in modo significativo per le query di dimensioni medie. 

### <a name="english-analyzers"></a>Analizzatori per la lingua inglese

L'analizzatore predefinito è Lucene standard, che funziona bene per la lingua inglese, ma probabilmente non altrettanto quanto l'analizzatore per la lingua inglese Lucene o l'analizzatore per la lingua inglese di Microsoft. 
 
+ L'analizzatore per la lingua inglese Lucene estende l'analizzatore standard. Rimuove il genitivo sassone (la 's finale) dalle parole, applica lo stemming in base all'algoritmo Porter Stemming e rimuove le parole non significative per la lingua inglese.  

+ L'analizzatore per la lingua inglese Microsoft esegue la lemmatizzazione anziché lo stemming. Ciò significa che è in grado di gestire in modo molto migliore i form di Word flessivi e irregolari, con risultati di ricerca più rilevanti 

## <a name="configuring-analyzers"></a>Configurazione degli analizzatori

Gli analizzatori di lingua vengono usati così come sono. Per ogni campo nella definizione dell'indice, è possibile impostare la proprietà **analyzer** su un nome di analizzatore che specifica la lingua e lo stack linguistico (Microsoft o Lucene). Lo stesso analizzatore verrà applicato per l'indicizzazione e la ricerca di tale campo. Ad esempio, nello stesso indice possono essere presenti campi separati per le descrizioni di hotel in lingua inglese, francese e spagnola.

> [!NOTE]
> Non è possibile utilizzare un analizzatore del linguaggio diverso in fase di indicizzazione rispetto al tempo di query per un campo. Questa funzionalità è riservata agli [analizzatori personalizzati](index-add-custom-analyzers.md). Per questo motivo, se si tenta di impostare le proprietà **searchAnalyzer** o **indexAnalyzer** sul nome di un analizzatore del linguaggio, l'API REST restituirà una risposta di errore. In alternativa, è necessario utilizzare la proprietà **Analyzer** .

Usare il parametro di query **searchFields** per indicare il campo specifico della lingua da ricercare nelle query. È possibile esaminare gli esempi di query che includono la proprietà Analyzer nei [documenti di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Per altre informazioni sulle proprietà degli indici, vedere [creare un indice &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Per altre informazioni sull'analisi in Azure ricerca cognitiva, vedere [analizzatori in azure ricerca cognitiva](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Elenco di analizzatori di lingua 
 Di seguito è riportato l'elenco delle lingue supportate con i nomi degli analizzatori Lucene e Microsoft.  

|Linguaggio|Nome analizzatore Microsoft|Nome analizzatore Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabo|ar.microsoft|ar.lucene|  
|Armeno||hy.lucene|  
|Bengalese|bn.microsoft||  
|Basco||eu.lucene|  
|Bulgaro|bg.microsoft|bg.lucene|  
|Catalano|ca.microsoft|ca.lucene|  
|Cinese semplificato|zh-Hans.microsoft|zh-Hans.lucene|  
|Cinese tradizionale|zh-Hant.microsoft|zh-Hant.lucene|  
|Croato|hr.microsoft||  
|Ceco|cs.microsoft|cs.lucene|  
|Danese|da.microsoft|da.lucene|  
|Olandese|nl.microsoft|nl.lucene|  
|Inglese|en.microsoft|en.lucene|  
|Estone|et.microsoft||  
|Finlandese|fi.microsoft|fi.lucene|  
|Francese|fr.microsoft|fr.lucene|  
|Galiziano||gl.lucene|  
|Tedesco|de.microsoft|de.lucene|  
|Greco|el.microsoft|el.lucene|  
|Gujarati|gu.microsoft||  
|Ebraico|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Ungherese|hu.microsoft|hu.lucene|  
|Islandese|is.microsoft||  
|Indonesiano (Bahasa)|id.microsoft|id.lucene|  
|Irlandese||ga.lucene|  
|Italiano|it.microsoft|it.lucene|  
|Giapponese|ja.microsoft|ja.lucene|  
|Kannada|kN. Microsoft||  
|Coreano|ko.Microsoft|ko.lucene|  
|Lettone|lv.microsoft|lv.lucene|  
|Lituano|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malese (alfabeto latino)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norvegese|nb.microsoft|no.lucene|  
|Persiano||fa.lucene|  
|Polacco|pl.microsoft|pl.lucene|  
|Portoghese (Brasile)|pt-Br.microsoft|pt-Br.lucene|  
|Portoghese (Portogallo)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Rumeno|ro.microsoft|ro.lucene|  
|Russo|ru.microsoft|ru.lucene|  
|Serbo (alfabeto cirillico)|sr-cyrillic.microsoft||  
|Serbo (alfabeto latino)|sr-latin.microsoft||  
|Slovacco|sk.microsoft||  
|Sloveno|sl.microsoft||  
|Spagnolo|es.microsoft|es.lucene|  
|Svedese|sv.microsoft|sv.lucene|  
|Tamil|ta.microsoft||  
|Telugu|te.microsoft||  
|Thai|th.microsoft|th.lucene|  
|Turco|tr.microsoft|tr.lucene|  
|Ucraino|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamita|vi.microsoft||  

 Tutti gli analizzatori con nomi contenenti la parola **Lucene** sono basati sugli [analizzatori del linguaggio Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Vedere anche  

+ [Creare l'indice &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

