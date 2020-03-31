---
title: Filtrare in base alla lingua in un indice di ricerca
titleSuffix: Azure Cognitive Search
description: Criteri di filtro per supportare la ricerca in più lingue, l'ambito dell'esecuzione delle query a campi specifici del linguaggio.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112056"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Come filtrare in base alla lingua in Ricerca cognitiva di AzureHow to filter by language in Azure Cognitive Search 

Il requisito principale in un'applicazione di ricerca multilingue è la possibilità di eseguire la ricerca e recuperare i risultati nella lingua dell'utente. In Ricerca cognitiva di Azure un modo per soddisfare i requisiti linguistici di un'app multilingue consiste nel creare una serie di campi dedicati all'archiviazione di stringhe in una lingua specifica e quindi vincolare la ricerca full-text solo a tali campi in fase di query.

I parametri di query nella richiesta vengono usati sia per definire l'ambito dell'operazione di ricerca sia per ridurre quindi i risultati di tutti i campi che non forniscono contenuto compatibile con l'esperienza di ricerca che si vuole offrire.

| Parametri | Scopo |
|-----------|--------------|
| **searchFields** | Limita la ricerca full-text all'elenco dei campi denominati. |
| **$select** | Riduce la risposta in modo da includere solo i campi specificati. Per impostazione predefinita, vengono restituiti tutti i campi recuperabili. Il parametro **$select** permette di scegliere quali restituire. |

Il successo di questa tecnica si basa sull'integrità del contenuto dei campi. Ricerca cognitiva di Azure non traduce stringhe né esegue il rilevamento della lingua. È responsabilità dell'utente assicurarsi che i campi contengano le stringhe previste.

## <a name="define-fields-for-content-in-different-languages"></a>Definire i campi per il contenuto in lingue diverse

In Ricerca cognitiva di Azure le query hanno come destinazione un singolo indice. Gli sviluppatori che vogliono fornire stringhe specifiche della lingua in un'unica esperienza di ricerca in genere definiscono campi dedicati per l'archiviazione dei valori: un campo per le stringhe in inglese, uno per il francese e così via. 

Negli esempi, incluso l'[esempio sui dati immobiliari](search-get-started-portal.md) mostrato di seguito, possono essere state osservate definizioni di campo simili allo screenshot seguente. Notare come questo esempio mostri le assegnazioni dell'analizzatore di lingua per i campi nell'indice. I campi che contengono stringhe hanno prestazioni migliori nella ricerca full-text se associati a un analizzatore progettato per gestire le regole linguistiche della lingua di destinazione.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Per esempi di codice che mostrano definizioni di campo con analizzatori di lingua, vedere [Definire un indice (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) e [Definire un indice (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Creare e caricare un indice

Un passaggio intermedio necessario (e probabilmente ovvio) consiste nel [compilare e popolare l'indice](https://docs.microsoft.com/azure/search/search-create-index-dotnet) prima di formulare una query. Questo passaggio viene citato qui per motivi di completezza. Un modo per determinare se l'indice è disponibile consiste nel controllare l'elenco degli indici nel [portale](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Limitare la query e ridurre i risultati

I parametri nella query vengono usati per limitare la ricerca ai campi specifici e quindi ridurre i risultati di tutti i campi non utili per lo scenario. Considerando un caso in cui l'obiettivo è limitare la ricerca ai campi che contengono stringhe in francese, è necessario usare **searchFields** perché la destinazione della query siano i campi che contengono stringhe in tale lingua. 

Per impostazione predefinita, una ricerca restituisce tutti i campi contrassegnati come recuperabili. Di conseguenza, si potrebbe voler escludere i campi che non sono conformi all'esperienza di ricerca specifica della lingua che si vuole fornire. In particolare, se si limita la ricerca a un campo con stringhe in francese, probabilmente si vorrà escludere i campi contenenti stringhe in inglese dai risultati. L'uso del parametro di query **$select** consente di controllare i campi restituiti all'applicazione chiamante.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Anche se l'argomento $filter non è presente nella query, questo caso d'uso è fortemente correlato ai concetti di filtro e di conseguenza viene presentato come scenario di applicazione di filtri.

## <a name="see-also"></a>Vedere anche

+ [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
+ [Analizzatori di linguaggi](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)

