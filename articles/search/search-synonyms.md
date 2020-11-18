---
title: Sinonimi per l'espansione di query su un indice di ricerca
titleSuffix: Azure Cognitive Search
description: Creare una mappa di sinonimi per espandere l'ambito di una query di ricerca in un indice ricerca cognitiva di Azure. L'ambito viene ampliato in modo da includere termini equivalenti forniti in un elenco.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a8f1fa07b94072d37cf83320b6c8956d3b412f12
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701117"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinonimi in Azure ricerca cognitiva

La funzionalità relativa ai sinonimi nei motori di ricerca associa termini equivalenti, che espandono in modo implicito l'ambito di una query, senza che l'utente debba fornire effettivamente il termine. Ad esempio, dato il termine "cane" e le associazioni sinonimiche "canino" e "cucciolo", tutti i documenti contenenti "cane", "canino" o "cucciolo" saranno inclusi nella query.

In Azure ricerca cognitiva l'espansione del sinonimo viene eseguita in fase di query. È possibile aggiungere mappe sinonimiche a un servizio senza compromettere le operazioni esistenti. La proprietà **synonymMaps** può essere aggiunta alla definizione di un campo senza dover ricompilare l'indice.

## <a name="create-synonyms"></a>Creare sinonimi

Non è disponibile il supporto del portale per la creazione di sinonimi, ma è possibile usare l'API REST o .NET SDK. Per iniziare a usare REST, si consiglia l' [inserimento o la Visual Studio Code](search-get-started-rest.md) e la formulazione delle richieste tramite questa API: [create sinonime Maps](/rest/api/searchservice/create-synonym-map). Per gli sviluppatori C#, è possibile iniziare a [aggiungere sinonimi in ricerca cognitiva di Azure con C#](search-synonyms-tutorial-sdk.md).

Facoltativamente, se si usano [chiavi gestite dal cliente](search-security-manage-encryption-keys.md) per la crittografia sul lato servizio, è possibile applicare tale protezione al contenuto della mappa di sinonimi.

## <a name="use-synonyms"></a>Usare sinonimi

In ricerca cognitiva di Azure, il supporto dei sinonimi è basato sulle mappe sinonimo definite e caricate nel servizio. Queste mappe costituiscono una risorsa indipendente (ad esempio indici o origini dati) e possono essere utilizzate da qualsiasi campo ricercabile in qualsiasi indice nel servizio di ricerca.

Le mappe sinonimiche e gli indici vengono mantenuti in modo indipendente. Dopo aver definito una mappa sinonimica e averla caricata nel servizio, è possibile abilitare la funzionalità relativa ai sinonimi per un campo tramite l'aggiunta della nuova proprietà **synonymMaps** nella definizione del campo. La creazione, l'aggiornamento e l'eliminazione di una mappa sinonimica è sempre un'operazione che riguarda l'intero documento, perciò non è possibile creare, aggiornare o eliminare parti della mappa sinonimica in modo incrementale. Se si aggiorna anche una singola voce è necessario ripetere il caricamento.

L'aggiunta di sinonimi in un'applicazione di ricerca è una procedura in due passaggi:

1.  Aggiungere una mappa sinonimica al servizio di ricerca tramite le API indicate di seguito.  

2.  Configurare un campo ricercabile per l'uso della mappa sinonimica nella definizione dell'indice.

È possibile creare più mappe sinonimiche per l'applicazione di ricerca (ad esempio in base alla lingua se l'applicazione supporta clienti multilingue). Attualmente un campo può usarne una sola. È possibile aggiornare la proprietà synonymMaps di un campo in qualsiasi momento.

### <a name="synonymmaps-resource-apis"></a>API di risorsa SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Aggiungere o aggiornare una mappa sinonimica nel servizio tramite POST o PUT.

Le mappe sinonimiche vengono caricate nel servizio tramite POST o PUT. Ogni regola deve essere delimitata dal carattere nuova riga ('\n'). È possibile definire fino a 5.000 regole per mapping sinonimo in un servizio gratuito e 20.000 regole per mappa in tutti gli altri SKU. Ogni regola può avere fino a 20 espansioni.

Le mappe sinonimiche devono essere nel formato Apache Solr descritto di seguito. Se si ha un dizionario di sinonimi in un formato diverso e si desidera usarlo direttamente, segnalarlo mediante [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

È possibile creare una nuova mappa sinonimica usando HTTP POST, come nell'esempio seguente:

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

In alternativa è possibile usare PUT e specificare il nome della mappa sinonimica nell'URI. Se la mappa sinonimica non esiste, verrà creata.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Formato dei sinonimi Apache Solr

Il formato Solr supporta il mapping sinonimico equivalente ed esplicito. Le regole di mapping sono conformi alla specifica del filtro di sinonimi open source di Apache Solr, descritta in questo documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Di seguito è riportata una regola di esempio per sinonimi equivalenti.

```
USA, United States, United States of America
```

Con la regola precedente, la query di ricerca "USA" si espanderà in "USA" OR "Stati Uniti" OR "Stati Uniti d'America".

Il mapping esplicito è indicato da una freccia "=>". Quando specificato, una sequenza di termini di una query di ricerca che corrisponde al lato sinistro di "=>" verrà sostituita con le alternative sul lato destro. Data la regola seguente, le query di ricerca "Washington", "Wash." o "WA" saranno riscritte tutte come "WA". Il mapping esplicito si applica solo nella direzione specificata e, in questo caso, non riscrivere la query "WA" come "Washington".

```
Washington, Wash., WA => WA
```

Se è necessario definire sinonimi che contengono virgole, è possibile usare una barra rovesciata come carattere di escape, come nell'esempio seguente:

```
WA\, USA, WA, Washington
```

Poiché la barra rovesciata è a sua volta un carattere speciale in altri linguaggi come JSON e C#, probabilmente sarà necessario eseguire un doppio escape. Ad esempio, il codice JSON inviato all'API REST per la mappa di sinonimi precedente avrà un aspetto simile al seguente:

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>Elencare le mappe sinonimiche del proprio servizio.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>Aggiungere una mappa sinonimica al proprio servizio.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>Eliminare una mappa sinonimica dal proprio servizio.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configurare un campo ricercabile per l'uso della mappa sinonimica nella definizione dell'indice.

La nuova proprietà di campo **synonymMaps** consente di specificare una mappa sinonimica da usare per un campo ricercabile. Le mappe sinonimiche sono risorse a livello di servizio e possono essere referenziate da qualsiasi campo di un indice del servizio.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }
```

È possibile specificare **synonymMaps** per i campi ricercabili di tipo "Edm. String" o "Collection".

> [!NOTE]
> È possibile avere solo una mappa sinonimica per campo. Se si vuole usare più mappe sinonimiche, segnalarlo tramite [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impatto dei sinonimi sulle altre funzionalità di ricerca

La funzionalità relativa ai sinonimi riscrive la query originale con sinonimi usando l'operatore OR. Per questo motivo, l'evidenziazione dei risultati e i profili di punteggio trattano il termine originale e i sinonimi come equivalenti.

La funzionalità relativa ai sinonimi si applica alle query di ricerca e non ai filtri o ai facet. Analogamente, i suggerimenti sono basati solo sul termine originale; le corrispondenze sinonimiche non compaiono nella risposta.

Le espansioni dei sinonimi non si applicano ai termini di ricerca con caratteri jolly; i termini con prefisso, fuzzy e regex non vengono espansi.

Se è necessario eseguire un'unica query che comprenda espansioni sinonimiche, caratteri jolly, espressioni regolari o ricerche fuzzy, è possibile combinare le relative istruzioni con la sintassi OR. Ad esempio, per combinare sinonimi e caratteri jolly in una sintassi di query semplice, il termine è `<query> | <query>*`.

Se si ha un indice esistente in un ambiente di sviluppo (non di produzione), provare con un piccolo dizionario per vedere come l'aggiunta di sinonimi cambia l'esperienza di ricerca, compreso l'impatto sui profili di punteggio, l'evidenziazione dei risultati e i suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una mappa di sinonimi](/rest/api/searchservice/create-synonym-map)