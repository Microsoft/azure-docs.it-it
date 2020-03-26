---
title: Creare un archivio conoscenze (anteprima) con REST
titleSuffix: Azure Cognitive Search
description: Usare l'API REST e Postman per creare un archivio conoscenze di Ricerca cognitiva di Azure per salvare in modo permanente gli arricchimenti da una pipeline di arricchimento tramite intelligenza artificiale. Questa funzionalità è attualmente in anteprima pubblica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472316"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Creare un archivio conoscenze con REST e Postman

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

L'archivio conoscenze contiene l'output di una pipeline di arricchimento di Ricerca cognitiva di Azure per l'analisi successiva o l'ulteriore elaborazione downstream. Una pipeline arricchita tramite intelligenza artificiale accetta file di immagine o file di testo non strutturati, li indicizza tramite Ricerca cognitiva di Azure, applica gli arricchimenti di intelligenza artificiale di Servizi cognitivi (come l'analisi delle immagini e l'elaborazione del linguaggio naturale) e quindi salva i risultati in un archivio conoscenze in Archiviazione di Azure. È possibile usare strumenti come Power BI o Storage Explorer nel portale di Azure per esplorare l'archivio conoscenze.

In questo articolo si usa l'interfaccia dell'API REST per inserire, indicizzare e applicare gli arricchimenti di intelligenza artificiale a un set di recensioni di hotel. Le recensioni degli hotel vengono importate in Archiviazione BLOB di Azure. I risultati vengono salvati come archivio conoscenze in Archiviazione tabelle di Azure.

Dopo aver creato l'archivio conoscenze, è possibile imparare ad accedervi usando [Storage Explorer](knowledge-store-view-storage-explorer.md) o [Power BI](knowledge-store-connect-power-bi.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!TIP]
> Per questo articolo, è consigliata un'[app desktop Postman](https://www.getpostman.com/). Il [codice sorgente](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) per questo articolo include una raccolta Postman contenente tutte le richieste. 

## <a name="create-services-and-load-data"></a>Creare i servizi e caricare i dati

Questa guida di avvio rapido usa Ricerca cognitiva di Azure, Archiviazione BLOB di Azure e [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/) per l'intelligenza artificiale. 

Poiché il carico di lavoro è molto ridotto, Servizi cognitivi lavora dietro le quinte per offrire un'elaborazione gratuita per un massimo di 20 transazioni al giorno. Poiché il set di dati è molto ridotto, è possibile evitare di creare o collegare una risorsa di Servizi cognitivi.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Questi dati corrispondono ai dati delle recensioni di hotel salvati in un file CSV (generato da Kaggle.com) e contengono 19 commenti dei clienti su un unico hotel. 

1. [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) o [trovare un account esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) nella sottoscrizione corrente. Si userà l'archiviazione di Azure sia per il contenuto non elaborato da importare che per l'archivio conoscenze che rappresenta il risultato finale.

   Scegliere il tipo di account **Archiviazione V2 (utilizzo generico V2)** .

1. Aprire le pagine dei servizi BLOB e creare un contenitore denominato *hotel-reviews*.

1. Fare clic su **Carica**.

    ![Caricare i dati](media/knowledge-store-create-portal/upload-command-bar.png "Caricare le recensioni di hotel")

1. Selezionare il file **HotelReviews-Free.csv** scaricato nel primo passaggio.

    ![Creazione del contenitore BLOB di Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Creazione del contenitore BLOB di Azure")

1. Questa risorsa è quasi terminata, ma prima di chiudere le pagine, fare clic sul collegamento nel riquadro di spostamento sinistro per aprire la pagina **Chiavi di accesso**. Ottenere una stringa di connessione per recuperare i dati dall'archiviazione BLOB. Una stringa di connessione è simile all'esempio seguente: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Sempre nel portale, passare a Ricerca cognitiva di Azure. [Creare un nuovo servizio](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Per questo esercizio è possibile usare un servizio gratuito.

## <a name="configure-postman"></a>Configurare Postman

Installare e configurare Postman.

### <a name="download-and-install-postman"></a>Scaricare e installare Postman

1. Scaricare il [codice sorgente della raccolta Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selezionare **File** > **Importa** per importare il codice sorgente in Postman.
1. Selezionare la scheda **Collections** (Raccolte), quindi selezionare il pulsante **...** (puntini di sospensione).
1. Selezionare **Modifica**. 
   
   ![App Postman che mostra le opzioni di spostamento](media/knowledge-store-create-rest/postman-edit-menu.png "Passare al menu Modifica in Postman")
1. Nella finestra di dialogo **Edit** (Modifica) selezionare la scheda **Variables** (Variabili). 

Nella scheda **Variables** (Variabili) è possibile aggiungere valori che Postman recupera ogni volta che rileva una variabile specifica racchiusa tra doppie parentesi graffe. Ad esempio, Postman sostituisce il simbolo `{{admin-key}}` con il valore corrente impostato per `admin-key`. Postman effettua questa sostituzione negli URL, nelle intestazioni, nel corpo della richiesta e così via. 

Per ottenere il valore per `admin-key`, passare al servizio Ricerca cognitiva di Azure e selezionare la scheda **Keys** (Chiavi). Sostituire `search-service-name` e `storage-account-name` con i valori scelti in [Creare i servizi](#create-services-and-load-data). Impostare `storage-connection-string` usando il valore presente nella scheda **Chiavi di accesso** dell'account di archiviazione. È possibile lasciare i valori predefiniti per le altre impostazioni.

![Scheda variabili app Postman](media/knowledge-store-create-rest/postman-variables-window.png "Finestra delle variabili di Postman")


| Variabile    | Informazioni sulla collocazione |
|-------------|-----------------|
| `admin-key` | Nella pagina **Chiavi** del servizio Ricerca cognitiva di Azure.  |
| `api-version` | Lasciare **2019-05-06-Preview**. |
| `datasource-name` | Lasciare **hotel-reviews-ds**. | 
| `indexer-name` | Lasciare **hotel-reviews-ixr**. | 
| `index-name` | Lasciare **hotel-reviews-ix**. | 
| `search-service-name` | Nome del servizio Ricerca cognitiva di Azure. L'URL è `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Lasciare **hotel-reviews-ss**. | 
| `storage-account-name` | nome dell'account di archiviazione. | 
| `storage-connection-string` | Nell'account di archiviazione, selezionare la scheda **Chiavi di accesso**, selezionare **key1** > **Stringa di connessione**. | 
| `storage-container-name` | Lasciare **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Esaminare la raccolta di richieste in Postman

Quando si crea un archivio conoscenze, è necessario inviare quattro richieste HTTP: 

- **Richiesta PUT per creare l'indice**: Questo indice contiene i dati usati e restituiti da Ricerca cognitiva di Azure.
- **Richiesta POST per creare l'origine dati**: Questa origine dati connette il comportamento di Ricerca cognitiva di Azure ai dati e all'account di archiviazione dell'archivio conoscenze. 
- **Richiesta PUT per creare il set di competenze**: Il set di competenze specifica gli arricchimenti applicati ai dati e alla struttura dell'archivio conoscenze.
- **Richiesta PUT per creare l'indicizzatore**: L'esecuzione dell'indicizzatore legge i dati, applica il set di competenze e archivia i risultati. È necessario eseguire questa richiesta per ultima.

Il [codice sorgente](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contiene una raccolta Postman con le quattro richieste. Per inviare le richieste, in Postman selezionare la scheda per la richiesta. Quindi, aggiungere le intestazioni di richiesta `api-key` e `Content-Type`. Impostare il valore di `api-key` su `{{admin-key}}`. Impostare il valore `Content-type` su `application/json`. 

![Screenshot che mostra l'interfaccia di Postman per le intestazioni](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> È necessario impostare le intestazioni `api-key` e `Content-type` in tutte le richieste. Se Postman riconosce una variabile, questa appare in arancione, come ad esempio `{{admin-key}}` nello screenshot. Se la variabile non è digitata correttamente, appare in rosso.
>

## <a name="create-an-azure-cognitive-search-index"></a>Creare un indice di Ricerca cognitiva di Azure

Creare un indice di Ricerca cognitiva di Azure per rappresentare i dati che si vogliono ricercare, filtrare e arricchire. Creare l'indice inviando una richiesta PUT a `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman sostituisce i simboli racchiusi tra doppie parentesi graffe, ad esempio `{{search-service-name}}`, `{{index-name}}` e `{{api-version}}`, con i valori specificati in [Configurare Postman](#configure-postman). Se si usa uno strumento diverso per immettere i comandi REST, è necessario sostituire queste variabili manualmente.

Impostare la struttura dell'indice di Ricerca cognitiva di Azure nel corpo della richiesta. In Postman, dopo aver impostato le intestazioni `api-key` e `Content-type`, passare al riquadro **Body** (Corpo) della richiesta. Verrà visualizzato il codice JSON seguente. In caso contrario, selezionare **Raw** > **JSON (application/json)** , quindi incollare il codice seguente come corpo:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Questa definizione di indice è una combinazione di dati che si vogliono presentare all'utente (nome dell'hotel, contenuto della recensione, data), metadati di ricerca e dati di arricchimento di intelligenza artificiale (Sentiment, Keyphrases e Language).

Selezionare **Send** (Invia) per inviare la richiesta PUT. Dovrebbe essere visualizzato lo stato `201 - Created`. Se si visualizza uno stato diverso, il riquadro **Body** (Corpo) contiene una risposta JSON con un messaggio di errore. 

## <a name="create-the-datasource"></a>Creare l'origine dati

A questo punto, connettere Ricerca cognitiva di Azure ai dati degli hotel archiviati nell'archivio BLOB. Per creare l'origine dati, inviare una richiesta POST a `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. È necessario impostare le intestazioni `api-key` e `Content-Type`, come illustrato in precedenza. 

In Postman passare alla richiesta **Create DataSource** (Crea origine dati), quindi al riquadro **Body**. Verrà visualizzato il codice seguente:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selezionare **Send** (Invia) per inviare la richiesta POST. 

## <a name="create-the-skillset"></a>Creare il set di competenze 

Il passaggio successivo consiste nello specificare il set di competenze, che specifica gli arricchimenti da applicare e l'archivio conoscenze in cui verranno archiviati i risultati. In Postman aprire la scheda **Create the Skillset** (Crea il set di competenze). Viene inviata una richiesta PUT a `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Impostare le intestazioni `api-key` e `Content-type` come è stato fatto in precedenza. 

Sono presenti due grandi oggetti di livello superiore: `skills` e `knowledgeStore`. Ogni oggetto all'interno dell'oggetto `skills` è un servizio di arricchimento. Ogni servizio di arricchimento include `inputs` e `outputs`. Per `LanguageDetectionSkill` l'output `targetName` è `Language`. Il valore di questo nodo viene usato come input dalla maggior parte delle altre competenze. L'origine è `document/Language`. La possibilità di usare l'output di un nodo come input di un altro è ancora più evidente in `ShaperSkill`, che specifica il tipo di flusso di dati nelle tabelle dell'archivio conoscenze.

L'oggetto `knowledge_store` si connette all'account di archiviazione tramite la variabile `{{storage-connection-string}}` di Postman. `knowledge_store` contiene un set di mapping tra il documento arricchito e le tabelle e le colonne nell'archivio conoscenze. 

Per generare il set di competenze, selezionare il pulsante **Send** (Invia) in Postman per inviare la richiesta PUT:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Creare l'indicizzatore

Il passaggio finale consiste nel creare l'indicizzatore, che legge effettivamente i dati e attiva il set di competenze. In Postman passare alla richiesta **Create Indexer** (Crea indicizzatore) ed esaminare il corpo. La definizione dell'indicizzatore fa riferimento a diverse altre risorse già create, ovvero l'origine dati, l'indice e il set di competenze. 

L'oggetto `parameters/configuration` controlla il modo in cui l'indicizzatore inserisce i dati. In questo caso i dati di input si trovano in un singolo documento con una riga di intestazione e valori separati da virgole. La chiave del documento è un identificatore univoco per il documento che, prima della codifica, corrisponde all'URL del documento di origine. Infine, i valori di output del set di competenze, come il codice della lingua, il sentiment e le frasi chiave, vengono mappati alle rispettive posizioni nel documento. Anche se per `Language` è presente un singolo valore, l'elemento `Sentiment` viene applicato a ogni elemento della matrice di `pages`. L'elemento `Keyphrases` è una matrice e viene anch'esso applicato a ogni elemento della matrice `pages`.

Dopo aver impostato le intestazioni `api-key` e `Content-type` e confermato che il corpo della richiesta è simile al codice sorgente che segue, premere **Send** (Invia) in Postman. Postman invia una richiesta PUT a `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Ricerca cognitiva di Azure crea ed esegue l'indicizzatore. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Eseguire l'indicizzatore 

Nel portale di Azure passare alla pagina **Panoramica** del servizio Ricerca cognitiva di Azure. Selezionare la scheda **Indicizzatori** e quindi selezionare **hotels-reviews-ixr**. Se l'indicizzatore non è già stato eseguito, selezionare **Esegui**. L'attività di indicizzazione potrebbe generare alcuni avvisi relativi al riconoscimento della lingua. I dati includono alcune recensioni scritte in lingue non ancora supportate dalle competenze cognitive. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver arricchito i dati con i servizi cognitivi e aver proiettato i risultati in un archivio conoscenze, è possibile usare Storage Explorer o Power BI per esplorare il set di dati arricchito.

Per informazioni su come esplorare questo archivio conoscenze usando Storage Explorer, vedere questa procedura dettagliata:

> [!div class="nextstepaction"]
> [Visualizzare con Storage Explorer](knowledge-store-view-storage-explorer.md)

Per informazioni su come connettere questo archivio conoscenze a Power BI, vedere questa procedura dettagliata:

> [!div class="nextstepaction"]
> [Stabilire la connessione con Power BI](knowledge-store-connect-power-bi.md)

Se si vuole ripetere questo esercizio o provare un'altra procedura dettagliata per gli arricchimenti di intelligenza artificiale, eliminare l'indicizzatore **hotel-reviews-idxr**. Eliminando l'indicizzatore, il contatore delle transazioni giornaliere gratuite viene azzerato.
