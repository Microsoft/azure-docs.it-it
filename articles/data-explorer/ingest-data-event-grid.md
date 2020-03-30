---
title: Inserire BLOB di Azure in Esplora dati di Azure
description: In questo articolo viene illustrato come inviare i dati dell'account di archiviazione ad Azure Data Explorer usando una sottoscrizione di Griglia di eventi.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370441"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Inserire BLOB in Esplora dati di Azure tramite la sottoscrizione delle notifiche di Griglia di eventi

> [!div class="op_single_selector"]
> * [Portale](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-event-grid-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e scalabile per dati di log e di telemetria. Consente l'inserimento continuo (caricamento di dati) dai BLOB scritti nei contenitori di BLOB. 

In questo articolo viene illustrato come impostare una sottoscrizione di [Griglia di eventi](/azure/event-grid/overview) di Azure e instradare gli eventi ad Azure Data Explorer tramite un hub eventi. Per iniziare, è necessario avere un account di archiviazione con una sottoscrizione di Griglia di eventi che invia notifiche a Hub eventi di Azure. Si creerà quindi una connessione dati a Griglia di eventi e si esaminerà il flusso di dati attraverso l'intero sistema.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Creare un [account Azure gratuito.](https://azure.microsoft.com/free/)
* [Un cluster e](create-cluster-database-portal.md)un database .
* [Un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Creare una sottoscrizione di Griglia di eventi nell'account di archiviazione

1. Nel portale di Azure trovare l'account di archiviazione.
1. Selezionare**Sottoscrizione eventi**. **Events** > 

    ![Collegamento all'applicazione di query](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Nella scheda **Di base** della finestra **Crea sottoscrizione di eventi** specificare i valori seguenti:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Nome | *test-grid-connection* | Il nome della griglia di eventi da creare.|
    | Schema di eventi | *Schema della griglia di eventi* | Lo schema da usare per la griglia di eventi. |
    | Tipo di argomento | *Account di archiviazione* | Il tipo di argomento della griglia di eventi. |
    | Risorsa argomento | *gridteststorage* | nome dell'account di archiviazione. |
    | Esegui la sottoscrizione di tutti i tipi di eventi | *Chiaro* | Non si ricevono notifiche per tutti gli eventi. |
    | Tipi di evento definiti | *BLOB creato* | Gli eventi specifici per cui ricevere notifiche. |
    | Tipo di endpoint | *Hub eventi* | Il tipo di endpoint a cui vengono inviati gli eventi. |
    | Endpoint | *test-hub* | Hub eventi creato. |
    | | |

1. Selezionare la scheda **Filtri** se si desidera tenere traccia dei file da un contenitore specifico. Impostare i filtri per le notifiche come segue:
    * Il campo **L'oggetto inizia con** è il prefisso *letterale* del contenitore di BLOB. Poiché il criterio applicato è *startswith*, può interessare più contenitori. I caratteri jolly non sono consentiti.
     È *necessario* impostarlo come segue: *`/blobServices/default/containers/`*[prefisso contenitore]
    * Il campo **L'oggetto termina con** è il suffisso di tipo *letterale* del BLOB. I caratteri jolly non sono consentiti.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Creare una tabella di destinazione in Esplora dati di Azure

Creare una tabella in Esplora dati di Azure a cui verranno inviati i dati da Hub eventi. Creare la tabella nel cluster e nel database preparati nei prerequisiti.

1. Nel portale di Azure, in corrispondenza del cluster selezionare **Query**.

    ![Collegamento all'applicazione di query](media/ingest-data-event-grid/query-explorer-link.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per creare la tabella (TestTable) che riceverà i dati inseriti.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Esecuzione della creazione di query](media/ingest-data-event-grid/run-create-table.png)

1. Copiare il comando seguente nella finestra e selezionare **Esegui** per eseguire il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati della tabella (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Creare una connessione dati di Griglia di eventi in Esplora dati di Azure

A questo punto connettersi a Griglia di eventi da Esplora dati di Azure, in modo che i dati provenienti nel contenitore BLOB vengano trasmessi alla tabella di test. 

1. Selezionare **Notifiche** sulla barra degli strumenti per verificare che la distribuzione dell'hub eventi abbia avuto esito positivo.

1. Nel cluster creato selezionare **Database** > **TestDatabase**.

    ![Selezionare il database di test](media/ingest-data-event-grid/select-test-database.png)

1. Selezionare **Inserimento dati** > **Aggiungi connessione dati**.

    ![Inserimento di dati](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selezionare il tipo di connessione: **Archiviazione BLOB**.

1. Compilare il modulo con le informazioni seguenti e quindi selezionare **Crea**.

    ![Connessione all'hub eventi](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Data connection name (Nome connessione dati) | *test-hub-connection* | Il nome della connessione da creare in Esplora dati di Azure.|
    | Sottoscrizione dell'account di archiviazione | ID sottoscrizione | L'ID sottoscrizione in cui risiede l'account di archiviazione.|
    | Account di archiviazione | *gridteststorage* | Il nome dell'account di archiviazione creato in precedenza.|
    | Griglia di eventi | *test-grid-connection* | Il nome della griglia di eventi creata. |
    | Nome dell'hub eventi | *test-hub* | L'hub eventi creato. Questo campo viene compilato automaticamente quando si seleziona una griglia di eventi. |
    | Gruppo di consumer | *test-group* | Il gruppo di consumer definito nell'hub eventi creato. |
    | | |

    Tabella di destinazione:

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Tabella | *TestTable* | Tabella creata in **TestDatabase**. |
    | Formato dati | *Json* | I formati supportati sono Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW e TXT. Opzioni di compressione supportate: zip e cap |
    | Mapping di colonne | *TestMapping* | Mapping creato in **TestDatabase** che esegue il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati di **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generare i dati di esempio

Dopo aver connesso Esplora dati di Azure e l'account di archiviazione, è possibile creare i dati di esempio e caricarli nella risorsa di archiviazione BLOB.

Verrà usato un piccolo script della shell che invia alcuni semplici comandi dell'interfaccia della riga di comando di Azure per interagire con le risorse di Archiviazione di Azure. Questo script crea un nuovo contenitore nell'account di archiviazione, carica un file esistente (come BLOB) in tale contenitore e quindi elenca gli oggetti BLOB nel contenitore. È possibile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per eseguire lo script direttamente nel portale.

Salvare i dati in un file e caricarlo con questo script:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Per ottenere le migliori prestazioni di inserimento, è necessario comunicare le dimensioni *non compresse* dei BLOB compressi inviati per l'inserimento. Poiché le notifiche di Griglia di eventi contengono solo dettagli di base, le informazioni sulle dimensioni devono essere comunicate in modo esplicito. Le informazioni sulle dimensioni non compresse possono essere fornite impostando la `rawSizeBytes` proprietà sui metadati BLOB con la dimensione dei dati non *compressi* in byte.

### <a name="ingestion-properties"></a>Proprietà di inserimento

È possibile specificare le [proprietà di inserimento](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) dell'inserimento BLOB tramite i metadati del BLOB.

È possibile impostare queste proprietà:These properties can be set:

|**Proprietà** | **Descrizione proprietà**|
|---|---|
| `rawSizeBytes` | Dimensione dei dati non elaborati (non compressi). Per Avro/ORC/Parquet, questa è la dimensione prima dell'applicazione della compressione specifica del formato.|
| `kustoTable` |  Nome della tabella di destinazione esistente. Sostituisce `Table` il set `Data Connection` nel pannello. |
| `kustoDataFormat` |  Formato dei dati. Sostituisce `Data format` il set `Data Connection` nel pannello. |
| `kustoIngestionMappingReference` |  Nome del mapping di inserimento esistente da utilizzare. Sostituisce `Column mapping` il set `Data Connection` nel pannello.|
| `kustoIgnoreFirstRecord` | Se impostato `true`su , Kusto ignora la prima riga del BLOB. Utilizzare in formato tabulare i dati (CSV, TSV o simili) per ignorare le intestazioni. |
| `kustoExtentTags` | Stringa che rappresenta i [tag](/azure/kusto/management/extents-overview#extent-tagging) che verranno associati all'estensione risultante. |
| `kustoCreationTime` |  Esegue [l'override $IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) per il BLOB, formattato come stringa ISO 8601. Utilizzare per il riempimento. |

> [!NOTE]
> Azure Data Explorer non eliminerà i BLOB dopo l'inserimento.
> Conservare i blob per un tempo o cinque giorni.
> Usare il ciclo di [vita dell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) di Azure per gestire l'eliminazione dei BLOB. 

## <a name="review-the-data-flow"></a>Esaminare il flusso di dati

> [!NOTE]
> Esplora dati di Azure prevede un criterio di aggregazione (invio in batch) per l'inserimento di dati in modo da ottimizzare il processo di inserimento.
Per impostazione predefinita, il criterio viene impostato su 5 minuti.
Se necessario, sarà possibile modificare il criterio in un secondo momento. In questo articolo ci si può aspettare una latenza di pochi minuti.

1. Nella griglia di eventi del portale di Azure si noterà un picco di attività durante l'esecuzione dell'app.

    ![Grafico della griglia di eventi](media/ingest-data-event-grid/event-grid-graph.png)

1. Per verificare il numero di messaggi arrivati al database fino a questo momento, eseguire la query seguente nel database di test.

    ```kusto
    TestTable
    | count
    ```

1. Per visualizzare il contenuto dei messaggi, eseguire la query seguente nel database di test.

    ```kusto
    TestTable
    ```

    Il set di risultati dovrebbe essere simile al seguente.

    ![Set di risultati dei messaggi](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare nuovamente la griglia di eventi, eliminare **test-hub-rg**, per evitare l'addebito di costi.

1. Nel portale di Azure selezionare **Gruppi di risorse** all'estrema sinistra e quindi selezionare il gruppo di risorse creato.  

    Se il menu a sinistra è compresso, selezionare ![pulsante Espandi](media/ingest-data-event-grid/expand.png) per espanderlo.

   ![Selezionare il gruppo di risorse da eliminare](media/ingest-data-event-grid/delete-resources-select.png)

1. In **test-resource-group** selezionare **Elimina gruppo di risorse**.

1. Nella nuova finestra immettere il nome del gruppo di risorse da eliminare (*test-hub-rg*) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Query data in Azure Data Explorer](web-query-data.md)
