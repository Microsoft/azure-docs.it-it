---
title: Arricchimento incrementale (anteprima)
titleSuffix: Azure Cognitive Search
description: Memorizzare nella cache il contenuto intermedio e le modifiche incrementali dalla pipeline di arricchimento di intelligenza artificiale in archiviazione di Azure per mantenere gli investimenti nei documenti elaborati esistenti. Questa funzionalità è attualmente in anteprima pubblica.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024144"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introduzione all'arricchimento e alla memorizzazione nella cache incrementali in Azure ricerca cognitiva

> [!IMPORTANT] 
> L'arricchimento incrementale è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

L'arricchimento incrementale aggiunge la memorizzazione nella cache e informazioni sullo stato a una pipeline di arricchimento, preservando l'investimento nell'output esistente, modificando solo i documenti interessati da particolari modifiche. Non solo questo consente di mantenere l'investimento monetario nell'elaborazione (in particolare, l'OCR e l'elaborazione di immagini), ma anche un sistema più efficiente. Quando le strutture e il contenuto vengono memorizzati nella cache, un indicizzatore può determinare quali competenze sono state modificate ed eseguire solo quelle che sono state modificate, oltre a eventuali competenze dipendenti a valle. 

## <a name="indexer-cache"></a>Cache dell'indicizzatore

L'arricchimento incrementale aggiunge una cache alla pipeline di arricchimento. L'indicizzatore memorizza nella cache i risultati di cracking del documento più gli output di ogni competenza per ogni documento. Quando un set di competenze viene aggiornato, vengono rieseguite solo le competenze modificate, o a valle. I risultati aggiornati vengono scritti nella cache e il documento viene aggiornato nell'indice di ricerca o nell'archivio delle informazioni.

Fisicamente, la cache è archiviata in un contenitore BLOB nell'account di archiviazione di Azure. La cache usa anche l'archiviazione tabelle per un record interno di elaborazione degli aggiornamenti. Tutti gli indici in un servizio di ricerca possono condividere lo stesso account di archiviazione per la cache dell'indicizzatore. A ogni indicizzatore viene assegnato un identificatore univoco e non modificabile della cache al contenitore utilizzato.

## <a name="cache-configuration"></a>Configurazione della cache

È necessario impostare la `cache` proprietà nell'indicizzatore per iniziare a trarre vantaggio dall'arricchimento incrementale. Nell'esempio seguente viene illustrato un indicizzatore con la memorizzazione nella cache abilitata. Nelle sezioni seguenti vengono descritte alcune parti specifiche di questa configurazione. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione dell'arricchimento incrementale](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Se si imposta questa proprietà su un indicizzatore esistente, sarà necessario reimpostare e rieseguire l'indicizzatore, che comporterà l'elaborazione di tutti i documenti nell'origine dati. Questo passaggio è necessario per eliminare tutti i documenti arricchiti dalle versioni precedenti del livello di competenze. 

## <a name="cache-management"></a>Gestione della cache

Il ciclo di vita della cache è gestito dall'indicizzatore. Se la `cache` proprietà nell'indicizzatore è impostata su null o la stringa di connessione viene modificata, la cache esistente viene eliminata alla successiva esecuzione dell'indicizzatore. Il ciclo di vita della cache è anche legato al ciclo di vita dell'indicizzatore. Se viene eliminato un indicizzatore, viene eliminata anche la cache associata.

Mentre l'arricchimento incrementale è progettato per rilevare e rispondere alle modifiche senza alcun intervento da parte dell'utente, è possibile utilizzare parametri per eseguire l'override dei comportamenti predefiniti:

+ Assegnare priorità ai nuovi documenti
+ Ignorare i controlli delle competenze
+ Ignora controlli origine dati
+ Valutazione Force Skills

### <a name="prioritize-new-documents"></a>Assegnare priorità ai nuovi documenti

Impostare la `enableReprocessing` proprietà per controllare l'elaborazione dei documenti in ingresso già rappresentati nella cache. Quando `true` (impostazione predefinita), i documenti già presenti nella cache vengono rielaborati quando si esegue nuovamente l'indicizzatore, presumendo che l'aggiornamento delle competenze influisca sul documento. 

Quando `false`, i documenti esistenti non vengono rielaborati, classificando in modo efficace il contenuto nuovo in ingresso rispetto al contenuto esistente. È consigliabile impostare `enableReprocessing` `false` su su base temporanea. Per garantire la coerenza tra il corpus `enableReprocessing` , dovrebbe `true` essere la maggior parte del tempo, assicurandosi che tutti i documenti, sia nuovi che esistenti, siano validi per la definizione di competenze corrente.

### <a name="bypass-skillset-evaluation"></a>Ignora valutazione competenze

La modifica di un skillt e la rielaborazione di tali competenze sono in genere di mano. Tuttavia, alcune modifiche apportate a un insieme di competenze non possono causare la rielaborazione, ad esempio la distribuzione di un'abilità personalizzata in un nuovo percorso o con una nuova chiave di accesso. Probabilmente si tratta di modifiche periferiche che non hanno alcun effetto reale sulla sostanza del. 

Se si è certi che una modifica al set di competenze è effettivamente superficiale, è necessario eseguire l'override della valutazione di `disableCacheReprocessingChangeDetection` Skills `true`impostando il parametro su:

1. Chiamare Update Skills e modificare la definizione di Skills.
1. Aggiungere il `disableCacheReprocessingChangeDetection=true` parametro nella richiesta.
1. Inviare la modifica.

L'impostazione di questo parametro garantisce che vengano sottoposte a commit solo gli aggiornamenti alla definizione del set di competenze e che la modifica non venga valutata per gli effetti sul Corpus esistente.

Nell'esempio seguente viene illustrata una richiesta Update Skills con il parametro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Ignorare i controlli di convalida dell'origine dati

La maggior parte delle modifiche apportate a una definizione dell'origine dati invalida la cache. Tuttavia, per gli scenari in cui si sa che una modifica non deve invalidare la cache, ad esempio la modifica di una stringa di connessione o la rotazione della chiave nell'`ignoreResetRequirement` account di archiviazione, aggiungere il parametro nell'aggiornamento dell'origine dati. Se si imposta questo `true` parametro su, il commit verrà eseguito senza attivare una condizione di reimpostazione che comporterebbe la ricompilazione e il popolamento di tutti gli oggetti da zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Valutazione Force Skills

Lo scopo della cache è quello di evitare un'elaborazione non necessaria, ma si supponga di apportare una modifica a una competenza non rilevata dall'indicizzatore (ad esempio, modificando qualcosa nel codice esterno, ad esempio un'abilità personalizzata).

In questo caso, è possibile usare le [competenze di reimpostazione](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) per forzare la rielaborazione di una determinata competenza, incluse le competenze downstream che hanno una dipendenza dall'output di tale capacità. Questa API accetta una richiesta POST con un elenco di competenze che devono essere invalidate e contrassegnate per la rielaborazione. Dopo aver reimpostato le competenze, eseguire l'indicizzatore per richiamare la pipeline.

## <a name="change-detection"></a>Rilevamento delle modifiche

Una volta abilitata una cache, l'indicizzatore valuta le modifiche nella composizione della pipeline per determinare il contenuto che può essere riutilizzato e che necessita di rielaborazione. Questa sezione enumera le modifiche che invalidano la cache in modo non corretto, seguite da modifiche che attivano l'elaborazione incrementale. 

### <a name="changes-that-invalidate-the-cache"></a>Modifiche che invalidano la cache

Una modifica invalidante è una modifica che fa sì che l'intera cache non sia più valida. Un esempio di modifica invalidante è quello in cui l'origine dati viene aggiornata. Ecco l'elenco completo delle modifiche che, se apportate, invaliderebbero la cache:

* Modifica del tipo di origine dati
* Modifica del contenitore dell'origine dati
* Credenziali dell'origine dati
* Criteri di rilevamento delle modifiche all'origine dati
* Criteri di rilevamento delle eliminazioni di origini dati
* Mapping dei campi dell'indicizzatore
* Parametri dell'indicizzatore
    * Modalità di analisi
    * Estensioni escluse
    * Estensioni indicizzate
    * Solo metadati di archiviazione dell'indice per i documenti di dimensioni eccessive
    * Intestazioni di testo delimitate
    * Delimitatore di testo delimitato
    * Radice documento
    * Azione per immagini (modifiche alla modalità di estrazione delle immagini)

### <a name="changes-that-trigger-incremental-processing"></a>Modifiche che attivano l'elaborazione incrementale

L'elaborazione incrementale consente di valutare la definizione delle competenze e di determinare le competenze da rieseguire, aggiornando selettivamente le parti interessate dell'albero del documento. Di seguito è riportato l'elenco completo delle modifiche risultante dall'arricchimento incrementale:

* Competenza nel set di competenze di tipo diverso Tipo OData della competenza aggiornato
* Parametri specifici delle competenze aggiornati, ad esempio l'URL, i valori predefiniti o altri parametri
* Modifiche agli output delle competenze, la competenza restituisce output aggiuntivi o diversi
* Aggiornamenti delle competenze che producono una cronologia diversa, il concatenamento delle competenze è cambiato, ad esempio gli input delle competenze
* Invalidamento di qualsiasi competenza a monte, se una competenza che fornisce un input a questa competenza viene aggiornata
* Aggiornamenti alla posizione di proiezione dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche alle proiezioni dell'archivio conoscenze, risultanti nella ripetizione della proiezione dei documenti
* Modifiche ai mapping dei campi di output in un indicizzatore risultanti nella ripetizione della proiezione dei documenti nell'indice

## <a name="api-reference"></a>API (riferimento)

La versione `2019-05-06-Preview` dell'API REST fornisce un'arricchimento incrementale tramite proprietà aggiuntive su indicizzatori, skillsets e origini dati. Oltre alla documentazione di riferimento, vedere [configurare la memorizzazione nella cache per l'arricchimento incrementale](search-howto-incremental-index.md) per informazioni dettagliate su come chiamare le API.

+ [Crea indicizzatore (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Aggiorna indicizzatore (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Update Skills (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (nuovo parametro URI nella richiesta)

+ [Reimpostazione competenze (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indicizzatori di database (SQL di Azure, Cosmos DB). Alcuni indicizzatori recuperano i dati tramite query. Per le query che recuperano dati, l' [aggiornamento dell'origine dati](https://docs.microsoft.com/rest/api/searchservice/update-data-source) supporta un nuovo parametro in una richiesta **ignoreResetRequirement**, che `true` deve essere impostata su quando l'azione di aggiornamento non deve invalidare la cache. 

  Usare **ignoreResetRequirement** sporadicamente poiché potrebbe causare incoerenza indesiderata nei dati che non verranno rilevati facilmente.

## <a name="next-steps"></a>Passaggi successivi

L'arricchimento incrementale è una funzionalità potente che estende il rilevamento delle modifiche per l'arricchimento di skillsets e intelligenza artificiale. L'arricchimento AIncremental consente il riutilizzo del contenuto elaborato esistente durante l'iterazione della progettazione di competenze.

Come passaggio successivo, abilitare la memorizzazione nella cache su un indicizzatore esistente o aggiungere una cache quando si definisce un nuovo indicizzatore.

> [!div class="nextstepaction"]
> [Configurare la memorizzazione nella cache per l'arricchimento incrementale](search-howto-incremental-index.md)
