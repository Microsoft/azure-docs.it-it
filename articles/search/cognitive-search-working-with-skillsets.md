---
title: Concetti e flusso di lavoro di competenze
titleSuffix: Azure Cognitive Search
description: Skillsets è la posizione in cui si crea una pipeline di arricchimento per l'intelligenza artificiale in Azure ricerca cognitiva. Informazioni sui concetti e i dettagli importanti sulla composizione di competenze.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191034"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Concetti e composizione di competenze in Azure ricerca cognitiva

Questo articolo è per gli sviluppatori che necessitano di una conoscenza più approfondita del funzionamento della pipeline di arricchimento e presuppone che l'utente abbia una conoscenza concettuale del processo di arricchimento di intelligenza artificiale. Se si ha familiarità con questo concetto, iniziare con:
+ [Arricchimento di intelligenza artificiale in Azure ricerca cognitiva](cognitive-search-concept-intro.md)
+ [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Specificare il livello di competenze
Un insieme di competenze è una risorsa riutilizzabile in Azure ricerca cognitiva che specifica una raccolta di competenze cognitive usate per l'analisi, la trasformazione e l'arricchimento di contenuto di testo o immagine durante l'indicizzazione. La creazione di un oggetto di competenze consente di aggiungere arricchimenti di testo e immagini nella fase di inserimento dei dati, estraendo e creando nuove informazioni e strutture da contenuto non elaborato.

Un skillt dispone di tre proprietà:

+   ```skills```, una raccolta non ordinata di competenze per le quali la piattaforma determina la sequenza di esecuzione in base agli input necessari per ogni Skill
+   ```cognitiveServices```, la chiave di servizi cognitivi necessaria per la fatturazione delle competenze cognitive richiamate
+   ```knowledgeStore```, l'account di archiviazione in cui verranno proiettati i documenti arricchiti



Skillsets vengono creati in JSON. È possibile compilare skillsets complessi con cicli e [diramazioni](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) usando il [linguaggio delle espressioni](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Il linguaggio delle espressioni usa la notazione del percorso del [puntatore JSON](https://tools.ietf.org/html/rfc6901) con alcune modifiche per identificare i nodi nell'albero di arricchimento. Un ```"/"``` oggetto attraversa un livello inferiore nell'albero e ```"*"``` funge da operatore for-each nel contesto. Questi concetti sono descritti meglio con un esempio. Per illustrare alcuni dei concetti e delle funzionalità, verranno illustrate le procedure per l' [esempio delle recensioni degli hotel](knowledge-store-connect-powerbi.md) . Per visualizzare le competenze dopo aver seguito il flusso di lavoro di importazione dei dati, è necessario usare un client dell'API REST per [ottenere l'](https://docs.microsoft.com/rest/api/searchservice/get-skillset)insieme di competenze.

### <a name="enrichment-tree"></a>Albero di arricchimento

Per conoscere il modo in cui un insieme di competenze arricchisce progressivamente il documento, iniziamo con l'aspetto del documento prima di qualsiasi arricchimento. L'output di cracking del documento dipende dall'origine dati e dalla modalità di analisi specifica selezionata. Questo è anche lo stato del documento per il quale i [mapping dei campi](search-indexer-field-mappings.md) possono essere originati da durante l'aggiunta di dati all'indice di ricerca.
![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Archivio conoscenze nel diagramma della pipeline")

Quando un documento si trova nella pipeline di arricchimento, viene rappresentato come albero di contenuto e arricchimenti associati. Viene creata un'istanza di questo albero come output di cracking del documento. Il formato dell'albero di arricchimento consente alla pipeline di arricchimento di alleghi i metadati ai tipi di dati ancora primitivi, non è un oggetto JSON valido, ma può essere proiettato in un formato JSON valido. La tabella seguente illustra lo stato di un documento che entra nella pipeline di arricchimento:

|Modalità Source\Parsing dati|Impostazione predefinita|JSON, righe JSON & CSV|
|---|---|---|
|Archiviazione BLOB|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

 Quando le competenze vengono eseguite, aggiungono nuovi nodi all'albero di arricchimento. Questi nuovi nodi possono quindi essere utilizzati come input per le competenze downstream, proiettando nell'archivio informazioni o eseguendo il mapping a campi di indice. Gli arricchimenti non sono modificabili: una volta creati, i nodi non possono essere modificati. Poiché il skillsets è più complesso, l'albero di arricchimento, ma non tutti i nodi dell'albero di arricchimento, deve renderlo nell'indice o nell'archivio informazioni. 

È possibile salvare in modo selettivo solo un subset degli arricchimenti nell'indice o nell'archivio informazioni.
Per la parte restante di questo documento si presuppone che si stia lavorando all' [esempio di recensioni degli Alberghi](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), ma gli stessi concetti si applicano all'arricchimento dei documenti da tutte le altre origini dati.

### <a name="context"></a>Context
Ogni competenza richiede un contesto. Un contesto determina:
+   Il numero di volte in cui viene eseguita l'abilità, in base ai nodi selezionati. Per i valori di contesto della raccolta di tipi ```/*``` , l'aggiunta di un oggetto alla fine comporterà la richiamata della capacità una volta per ogni istanza nella raccolta. 
+   Dove nell'albero di arricchimento vengono aggiunti gli output delle competenze. Gli output vengono sempre aggiunti all'albero come elementi figlio del nodo di contesto. 
+   Forma degli input. Per le raccolte a più livelli, l'impostazione del contesto sulla raccolta padre influirà sulla forma dell'input per la competenza. Ad esempio, se si dispone di un albero di arricchimento con un elenco di paesi, ciascuno arricchito con un elenco di stati contenente un elenco di zipcodes.

|Context|Input|Forma dell'input|Chiamata di competenze|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Elenco di tutti ZipCodes nel paese |Una volta per paese |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Elenco di ZipCodes nello stato | Una volta per ogni combinazione di paese e stato|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` Viene utilizzato solo in input e [proiezioni](knowledge-store-projection-overview.md)di competenze. Viene utilizzato per costruire oggetti annidati a più livelli. Potrebbe essere necessario creare un nuovo oggetto per passarlo come input a una competenza o a un progetto nell'archivio informazioni. Poiché i nodi di arricchimento non possono essere un oggetto JSON valido nell'albero di arricchimento e fare riferimento a un nodo nell'albero restituisce solo lo stato del nodo al momento della creazione, l'uso degli arricchimenti per le proiezioni o gli input delle competenze richiede la creazione di un oggetto JSON ben formato. `sourceContext` Consente di creare un oggetto di tipo gerarchico e anonimo, che richiederebbe più competenze se si utilizzasse solo il contesto. L' `sourceContext` uso di è illustrato nella sezione successiva. Esaminare l'output delle competenze che ha generato un arricchimento per determinare se si tratta di un oggetto JSON valido e non di un tipo primitivo.

### <a name="projections"></a>Proiezioni

La proiezione è il processo di selezione dei nodi dall'albero di arricchimento da salvare nell'archivio informazioni. Le proiezioni sono forme personalizzate del documento (contenuto e arricchimenti) che possono essere restituite come proiezioni di tabelle o oggetti. Per ulteriori informazioni sull'utilizzo delle proiezioni, vedere [utilizzo delle proiezioni](knowledge-store-projection-overview.md).

![Opzioni di mapping dei campi](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opzioni di mapping dei campi per la pipeline di arricchimento")

Il diagramma precedente descrive il selettore che si utilizza in base alla posizione della pipeline di arricchimento.

## <a name="generate-enriched-data"></a>Genera dati arricchiti 

A questo punto, è possibile seguire l' [esercitazione](knowledge-store-connect-powerbi.md) per creare il proprio Skills o [visualizzare](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) le competenze. Verrà esaminato:

* evoluzione dell'albero di arricchimento con l'esecuzione di ogni competenza 
* funzionamento del contesto e degli input per determinare il numero di esecuzioni di un'abilità 
* Qual è la forma dell'input basata sul contesto. 

Poiché si sta usando la modalità di analisi del testo delimitata per l'indicizzatore, un documento all'interno del processo di arricchimento rappresenta una singola riga all'interno del file CSV.

### <a name="skill-1-split-skill"></a>#1 Skill: suddivisione skill 

![albero di arricchimento dopo la screpolatura del documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Struttura ad albero di arricchimento dopo il cracking del documento e prima dell'esecuzione delle competenze")

Con il contesto di competenza ```"/document/reviews_text"```di, questa skill viene eseguita una sola `reviews_text`volta per il. L'output delle competenze è un elenco in `reviews_text` cui l'oggetto è suddiviso in segmenti di 5000 caratteri. L'output dell'abilità Split è denominato `pages` e aggiunto all'albero di arricchimento. La `targetName` funzionalità consente di rinominare un output di competenze prima di essere aggiunto all'albero di arricchimento.

L'albero di arricchimento dispone ora di un nuovo nodo inserito nel contesto della competenza. Questo nodo è disponibile per qualsiasi mapping dei campi di competenze, proiezione o output.


Il nodo radice per tutti gli arricchimenti è `"/document"`. Quando si utilizzano gli indicizzatori BLOB, `"/document"` il nodo avrà nodi figlio di `"/document/content"` e `"/document/normalized_images"`. Quando si usano i dati CSV, come in questo esempio, i nomi delle colonne vengono mappati ai nodi `"/document"`sotto. Per accedere a uno qualsiasi degli arricchimenti aggiunti a un nodo da un'abilità, è necessario il percorso completo per l'arricchimento. Se ad esempio si vuole usare il testo del ```pages``` nodo come input per un'altra abilità, sarà necessario specificarlo come. ```"/document/reviews_text/pages/*"```
 
 ![struttura ad albero di arricchimento dopo #1 skill](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Albero di arricchimento dopo l'esecuzione di skill #1")

### <a name="skill-2-language-detection"></a>Rilevamento di competenze #2 lingua
 Sebbene la competenza del rilevamento della lingua sia la terza competenza (skill #3) definita in competenze, è la capacità successiva da eseguire. Poiché non è bloccata richiedendo gli input, verrà eseguita in parallelo con la competenza precedente. Analogamente all'abilità Split che lo precede, viene richiamata anche una volta per ogni documento. L'albero di arricchimento dispone ora di un nuovo nodo per la lingua.
 ![struttura ad albero di arricchimento dopo #2 Skill](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Albero di arricchimento dopo l'esecuzione di Skill #2")
 
 ### <a name="skill-3-key-phrases-skill"></a>Skill #3: abilità di frasi chiave 

Dato che il contesto ```/document/reviews_text/pages/*``` delle frasi chiave verrà richiamato una volta per ogni elemento della `pages` raccolta. L'output dell'abilità sarà un nodo sotto l'elemento della pagina associato. 

 A questo punto dovrebbe essere possibile esaminare le altre competenze del insieme di competenze e visualizzare il modo in cui l'albero degli arricchimenti continuerà a crescere con l'esecuzione di ogni competenza. Alcune competenze, ad esempio la capacità di Unione e la capacità dell'shaper, creano anche nuovi nodi, ma usano solo i dati provenienti da nodi esistenti e non creano nuovi arricchimenti di rete.

![struttura ad albero di arricchimento dopo tutte le competenze](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Struttura ad albero di arricchimento dopo tutte le competenze")

I colori dei connettori nell'albero sopra indicati indicano che gli arricchimenti sono stati creati con competenze diverse e che i nodi devono essere risolti singolarmente e non faranno parte dell'oggetto restituito quando si seleziona il nodo padre.

## <a name="save-enrichments-in-a-knowledge-store"></a>Salvare gli arricchimenti in un archivio informazioni 

Skillsets definisce inoltre un archivio informazioni in cui è possibile proiettare i documenti arricchiti come tabelle o oggetti. Per salvare i dati arricchiti nell'archivio informazioni, è necessario definire un set di proiezioni per il documento arricchito. Per ulteriori informazioni sull'archivio informazioni, vedere [Cenni preliminari sull'archivio](knowledge-store-concept-intro.md) informazioni

### <a name="slicing-projections"></a>Proiezioni di sezionamento

Quando si definisce un gruppo di proiezione della tabella, è possibile suddividere in più tabelle correlate un singolo nodo nell'albero di arricchimento. Se si aggiunge una tabella con un percorso di origine figlio di una proiezione di tabella esistente, il nodo figlio risultante non sarà un figlio della proiezione della tabella esistente, ma verrà proiettato nella nuova tabella correlata. Questa tecnica di sezionamento consente di definire un singolo nodo in un'abilità di shaper che può essere l'origine di tutte le proiezioni di tabella. 

### <a name="shaping-projections"></a>Modellazione delle proiezioni

Esistono due modi per definire una proiezione. Per creare un nuovo nodo che rappresenta il nodo radice per tutti gli arricchimenti che si sta proiettando, è possibile usare un'abilità di shaper. Quindi, nelle proiezioni, si fa riferimento solo all'output della capacità dell'shaper. È anche possibile definire in linea una proiezione all'interno della definizione di proiezione.

L'approccio shaper è più dettagliato rispetto al data shaping, ma garantisce che tutte le mutazioni dell'albero di arricchimento siano contenute all'interno delle competenze e che l'output sia un oggetto che può essere riutilizzato. La formazione in linea consente di creare la forma necessaria, ma è un oggetto anonimo ed è disponibile solo per la proiezione per la quale è definita. Gli approcci possono essere utilizzati insieme o separatamente. Le competenze create nel flusso di lavoro del portale contengono entrambe. Usa un'abilità shaper per le proiezioni di tabella, ma usa anche il data shaping per proiettare la tabella delle frasi chiave.

Per estendere l'esempio, è possibile scegliere di rimuovere il data shaping in linea e di usare un'abilità shaper per creare un nuovo nodo per le frasi chiave. Per creare una forma proiettata in tre tabelle, vale a dire `hotelReviewsDocument` `hotelReviewsPages`,, e `hotelReviewsKeyPhrases`, le due opzioni vengono descritte nelle sezioni riportate di seguito.


#### <a name="shaper-skill-and-projection"></a>Capacità e proiezione del shaper 

> [!Note]
> Alcune colonne della tabella Document sono state rimosse da questo esempio per brevità.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Con il `tableprojection` nodo definito nella `outputs` sezione precedente, è ora possibile usare la funzionalità di sezionamento per proiettare parti del `tableprojection` nodo in tabelle diverse:

> [!Note]
> Si tratta solo di un frammento della proiezione nella configurazione dell'archivio informazioni.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Proiezioni di data shaping in linea

L'approccio di data shaping in linea non richiede una capacità di formatore perché tutte le forme necessarie per le proiezioni vengono create nel momento in cui sono necessarie. Per proiettare gli stessi dati dell'esempio precedente, l'opzione di proiezione inline avrà un aspetto simile al seguente:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Un'osservazione da entrambi gli approcci è il modo in `"Keyphrases"` cui i valori di vengono `"sourceContext"`proiettati usando. Il `"Keyphrases"` nodo che contiene una raccolta di stringhe è a sua volta un elemento figlio del testo della pagina. Tuttavia, poiché le proiezioni richiedono un oggetto JSON e la pagina è un primitivo (stringa) `"sourceContext"` , viene usato per eseguire il wrapping della frase chiave in un oggetto con una proprietà denominata. Questa tecnica consente di proiettare anche le primitive in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, è possibile creare il primo insieme di competenze con competenze cognitive.

> [!div class="nextstepaction"]
> [Creare il primo sistema di competenze](cognitive-search-defining-skillset.md).
