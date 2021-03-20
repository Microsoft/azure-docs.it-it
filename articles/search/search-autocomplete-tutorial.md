---
title: Aggiungi completamento automatico a una casella di ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare le azioni di query in base al tipo di ricerca in Azure ricerca cognitiva creando suggerimenti e formulando richieste che completano una casella di ricerca con termini o frasi completati. È anche possibile restituire le corrispondenze suggerite.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 25c87971455ed3c5f59c92748794720d61e599e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96339609"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Aggiungere il completamento automatico e suggerimenti alle app client usando Azure ricerca cognitiva

Il tipo di ricerca è una tecnica comune per migliorare la produttività delle query avviate dall'utente. In Azure ricerca cognitiva questa esperienza è supportata tramite il *completamento automatico*, che completa un termine o una frase basata sull'input parziale (completando "micro" con "Microsoft"). Una seconda esperienza utente è costituita da *suggerimenti* o da un breve elenco di documenti corrispondenti (che restituiscono titoli di libro con un ID per potersi collegare a una pagina di dettaglio relativa a tale libro). Il completamento automatico e i suggerimenti vengono predicati in base a una corrispondenza nell'indice. Il servizio non offrirà query che restituiscono zero risultati.

Per implementare queste esperienze in Azure ricerca cognitiva, sarà necessario:

+ Definizione del *Suggerimento* incorporata nello schema dell'indice.
+ *Query* che specifica l'API di [completamento automatico](/rest/api/searchservice/autocomplete) o [suggerimenti](/rest/api/searchservice/suggestions) per la richiesta.
+ Un *controllo dell'interfaccia utente* per gestire le interazioni di tipo ricerca in una propria app client. A questo scopo, è consigliabile usare una libreria JavaScript esistente.

In ricerca cognitiva di Azure, le query con completamento automatico e i risultati suggeriti vengono recuperati dall'indice di ricerca, dai campi selezionati registrati con un suggerimento. Un suggerimento è parte dell'indice e specifica i campi che forniranno contenuto che completa una query, suggerisce un risultato oppure esegue entrambe le cause. Quando l'indice viene creato e caricato, viene creata internamente una struttura di dati del suggerimento per archiviare i prefissi utilizzati per la corrispondenza in query parziali. Per i suggerimenti, la scelta di campi appropriati che siano univoci o almeno non ripetitivi è essenziale per l'esperienza. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un suggerimento](index-add-suggesters.md).

Il resto di questo articolo è incentrato sulle query e sul codice client. Usa JavaScript e C# per illustrare i punti chiave. Gli esempi di API REST vengono usati per presentare brevemente ciascuna operazione. Per i collegamenti agli esempi di codice end-to-end, vedere [passaggi successivi](#next-steps).

## <a name="set-up-a-request"></a>Configurare una richiesta

Gli elementi di una richiesta includono una delle API di ricerca di tipo, una query parziale e un suggerimento. Lo script seguente illustra i componenti di una richiesta, usando l'API REST di AutoComplete come esempio.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

Il **suggesterName** fornisce i campi in grado di riconoscere il suggerimento usati per completare i termini o i suggerimenti. Per i suggerimenti in particolare, l'elenco dei campi deve essere composto da quelli che offrono scelte chiare tra i risultati corrispondenti. In un sito che vende giochi per computer, il campo potrebbe essere il titolo del gioco.

Il parametro **Search** fornisce la query parziale, in cui i caratteri vengono inseriti nella richiesta di query tramite il controllo di completamento automatico jQuery. Nell'esempio precedente, "Minecraf" è un'illustrazione statica di ciò che il controllo potrebbe avere passato.

Le API non impongono requisiti di lunghezza minima per la query parziale. può essere costituito da un minimo di un carattere. Tuttavia, il completamento automatico jQuery garantisce una lunghezza minima. Un minimo di due o tre caratteri è tipico.

Le corrispondenze si trovano all'inizio di un termine in qualsiasi punto della stringa di input. Dato "The Quick Brown Fox", sia il completamento automatico che i suggerimenti corrispondono a versioni parziali di "The", "Quick", "Brown" o "Fox", ma non a termini infissi parziali come "Erika" o "Ox". Ogni corrispondenza, inoltre, imposta l'ambito per le espansioni downstream. Una query parziale di "Quick BR" corrisponderà a "Quick Brown" o "Quick pane", ma "Brown" o "Bread" saranno uguali a meno che non sia preceduto da "Quick".

### <a name="apis-for-search-as-you-type"></a>API per la ricerca di tipo

Seguire questi collegamenti per le pagine di riferimento REST e .NET SDK:

+ [API REST suggerimenti](/rest/api/searchservice/suggestions) 
+ [API REST di completamento automatico](/rest/api/searchservice/autocomplete) 
+ [Metodo SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Metodo AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Struttura di una risposta

Le risposte per il completamento automatico e i suggerimenti sono quelle che è possibile prevedere per il modello: il [completamento automatico](/rest/api/searchservice/autocomplete#response) restituisce un elenco di termini, i [suggerimenti](/rest/api/searchservice/suggestions#response) restituiscono termini più un ID documento in modo che sia possibile recuperare il documento (usare l'API di [ricerca del documento](/rest/api/searchservice/lookup-document) per recuperare il documento specifico per una pagina di dettaglio).

Le risposte vengono modellate in base ai parametri della richiesta. Per il completamento automatico, impostare [**autocompleteMode**](/rest/api/searchservice/autocomplete#autocomplete-modes) per determinare se il completamento del testo viene eseguito in uno o due termini. Per i suggerimenti, il campo scelto determina il contenuto della risposta.

Per i suggerimenti, è necessario affinare ulteriormente la risposta per evitare i duplicati o i risultati non correlati. Per controllare i risultati, includere più parametri nella richiesta. I parametri seguenti si applicano sia a completamento automatico che a suggerimenti, ma sono forse più necessari per i suggerimenti, soprattutto quando un suggerimento include più campi.

| Parametro | Utilizzo |
|-----------|-------|
| **$select** | Se si dispone di più **sourceFields** in un suggerimento, utilizzare **$SELECT** per scegliere il campo che contribuisce ai valori ( `$select=GameTitle` ). |
| **searchFields** | Vincolare la query a campi specifici. |
| **$filter** | Applicare i criteri di corrispondenza nel set di risultati ( `$filter=Category eq 'ActionAdventure'` ). |
| **$top** | Limitare i risultati a un numero specifico ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Aggiungi codice interazione utente

Il riempimento automatico di un termine di query o l'eliminazione di un elenco di collegamenti corrispondenti richiede il codice di interazione dell'utente, in genere JavaScript, che può utilizzare richieste provenienti da origini esterne, ad esempio query di completamento automatico o suggerimenti su un indice cognitivo di ricerca di Azure.

Sebbene sia possibile scrivere questo codice in modo nativo, è molto più semplice usare le funzioni della libreria JavaScript esistente. In questo articolo vengono illustrati due, uno per i suggerimenti e un altro per il completamento automatico. 

+ Nell'esempio di suggerimento viene usato il [widget di completamento automatico (interfaccia utente di jQuery)](https://jqueryui.com/autocomplete/) . È possibile creare una casella di ricerca e quindi farvi riferimento in una funzione JavaScript che usa il widget completamento automatico. Le proprietà del widget impostano l'origine (funzione di completamento automatico o suggerimenti), la lunghezza minima dei caratteri di input prima che venga eseguita l'azione e il posizionamento.

+ Il [plug-in di completamento automatico XDSoft](https://xdsoft.net/jqplugins/autocomplete/) viene usato come esempio di completamento automatico.

Queste librerie vengono usate per compilare la casella di ricerca che supporta i suggerimenti e il completamento automatico. Gli input raccolti nella casella di ricerca sono abbinati ai suggerimenti e alle azioni di completamento automatico.

## <a name="suggestions"></a>Suggerimenti

In questa sezione viene illustrata un'implementazione dei risultati suggeriti, a partire dalla definizione della casella di ricerca. Viene inoltre illustrato come e script che richiama la prima libreria di completamento automatico JavaScript a cui si fa riferimento in questo articolo.

### <a name="create-a-search-box"></a>Creare una casella di ricerca

Supponendo che la [libreria di completamento automatico dell'interfaccia utente jQuery](https://jqueryui.com/autocomplete/) e un progetto MVC in C#, è possibile definire la casella di ricerca usando JavaScript nel file **index. cshtml** . La libreria aggiunge l'interazione di ricerca in base al tipo alla casella di ricerca effettuando chiamate asincrone al controller MVC per recuperare i suggerimenti.

In **index. cshtml** nella cartella \Views\Home, una riga per creare una casella di ricerca può essere la seguente:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Questo esempio contiene una casella di testo di input semplice con una classe per definire lo stile, un ID a cui JavaScript farà riferimento e un testo segnaposto.  

All'interno dello stesso file, incorporare JavaScript che fa riferimento alla casella di ricerca. La funzione seguente chiama l'API suggest, che richiede i documenti corrispondenti suggeriti in base a input con termini parziali:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source`Indica alla funzione di completamento automatico dell'interfaccia utente di jQuery dove ottenere l'elenco di elementi da visualizzare nella casella di ricerca. Poiché questo progetto è un progetto MVC, chiama la funzione **suggest** in **HomeController. cs** che contiene la logica per la restituzione di suggerimenti per la query. Questa funzione passa anche alcuni parametri per controllare evidenziazioni, corrispondenze fuzzy e termini. L'API JavaScript di completamento automatico aggiunge il parametro per i termini.

Il `minLength: 3` garantisce che le raccomandazioni vengano visualizzate solo quando nella casella di ricerca sono presenti almeno tre caratteri.

### <a name="enable-fuzzy-matching"></a>Abilita corrispondenza fuzzy

La ricerca fuzzy consente di ottenere risultati in base alle corrispondenze simili, anche se l'utente commette errori di digitazione di una parola nella casella di ricerca. La distanza di modifica è 1, il che significa che può essere presente una discrepanza massima di un carattere tra l'input dell'utente e una corrispondenza. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Abilita evidenziazione

L'evidenziazione applica lo stile del carattere ai caratteri nel risultato che corrispondono all'input. Ad esempio, se l'input parziale è "micro", il risultato verrebbe visualizzato come **micro** soft, ambito **micro** e così via. L'evidenziazione si basa sui parametri HighlightPreTag e HighlightPostTag, definiti inline con la funzione di suggerimento.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Suggerisci funzione

Se si usa C# e un'applicazione MVC, il file **HomeController. cs** nella directory Controllers è il punto in cui è possibile creare una classe per i risultati suggeriti. In .NET una funzione suggest è basata sul [Metodo SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Per altre informazioni su .NET SDK, vedere [come usare ricerca cognitiva di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

Il `InitSearch` metodo crea un client di indice http autenticato per il servizio ricerca cognitiva di Azure. Le proprietà della classe [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) determinano quali campi vengono ricercati e restituiti nei risultati, il numero di corrispondenze e se viene utilizzata la corrispondenza fuzzy. 

Per il completamento automatico, la corrispondenza fuzzy è limitata a una distanza di modifica (un carattere omesso o inserito in modo non consentito). Si noti che la corrispondenza fuzzy nelle query di completamento automatico può talvolta produrre risultati imprevisti a seconda delle dimensioni dell'indice e del partizionamento. Per altre informazioni, vedere [concetti relativi a partizionamento e partizionamento orizzontale](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

La funzione SuggestAsync accetta due parametri che determinano se vengono restituite le evidenziazioni dei riscontri o se viene utilizzata la corrispondenza fuzzy oltre all'input del termine di ricerca. Nei risultati suggeriti è possibile includere fino a otto corrispondenze. Il metodo crea un [oggetto SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions), che viene quindi passato all'API suggest. Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

## <a name="autocomplete"></a>Completamento automatico

Fino ad ora, il codice UX di ricerca è stato centrato sui suggerimenti. Il blocco di codice successivo Mostra il completamento automatico, usando la funzione di completamento automatico dell'interfaccia utente di XDSoft jQuery, passando una richiesta di Azure ricerca cognitiva completamento automatico. Come per i suggerimenti, in un'applicazione C# il codice che supporta l'interazione con l'utente passa a **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funzione di completamento automatico

Il completamento automatico è basato sul [Metodo AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Come per i suggerimenti, questo blocco di codice viene inserito nel file **HomeController. cs** .

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

La funzione Autocomplete accetta l'input del termine di ricerca. Il metodo crea un [oggetto AutoCompleteParameters](/rest/api/searchservice/autocomplete). Il risultato viene quindi convertito in formato JSON in modo da essere visualizzato nel client.

## <a name="next-steps"></a>Passaggi successivi

Segui questi collegamenti per istruzioni end-to-end o codice che dimostrano entrambe le esperienze di ricerca in base al tipo. Entrambi gli esempi di codice includono implementazioni ibride di suggerimenti e completamento automatico insieme.

+ [Esercitazione: creare la prima app in C# (lezione 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Esempio di codice C#: Azure-Search-DotNet-Samples/create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)