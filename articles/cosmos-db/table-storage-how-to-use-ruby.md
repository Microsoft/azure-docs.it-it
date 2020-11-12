---
title: Usare l'API Tabella di Azure Cosmos DB e l'archiviazione tabelle di Azure con Ruby
description: Archiviare dati strutturati nel cloud usando l'archiviazione tabelle di Azure o l'API Tabelle di Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 2d0c8433fff58854cb77a4e806058eae1937e71b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101120"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Come usare l'archiviazione tabelle di Azure o l'API Tabelle di Azure Cosmos DB con Ruby
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Questo articolo illustra come creare tabelle, archiviare i dati ed eseguire operazioni CRUD sui dati. Scegliere se usare il servizio Tabella di Azure o l'API Tabella di Azure Cosmos DB. Gli esempi descritti in questo articolo sono scritti in Ruby e usano la [libreria client delle tabelle di Archiviazione di Azure per Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Gli scenari trattati includono: creare una tabella, eliminare una tabella, inserire entità ed eseguire query sulle entità dalla tabella.

## <a name="create-an-azure-service-account"></a>Creare un account del servizio di Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Creare un account di archiviazione di Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Creare un account di Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-azure-storage-or-azure-cosmos-db"></a>Aggiungere l'accesso ad Archiviazione di Azure o ad Azure Cosmos DB

Per usare Archiviazione di Azure o Azure Cosmos DB, è necessario scaricare e usare il pacchetto di Azure per Ruby, che comprende un set di pratiche librerie che comunicano con i servizi REST per le tabelle.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Digitare **gem install azure-storage-table** nella finestra di comando per installare la gemma e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

```ruby
require "azure/storage/table"
```

## <a name="add-your-connection-string"></a>Aggiungere la stringa di connessione

È possibile connettersi all'account di archiviazione di Azure o all'account dell'API Tabella di Azure Cosmos DB. Ottenere la stringa di connessione in base al tipo di account in uso.

### <a name="add-an-azure-storage-connection"></a>Aggiungere una connessione ad Archiviazione di Azure

I modulo di Archiviazione di Azure legge le variabili di ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni relative all'account prima di usare **Azure::Storage::Table::TableService** con il codice seguente:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Per ottenere questi valori da un account di archiviazione classico o di Resource Manager nel portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si vuole usare.
3. Nel pannello Impostazioni a destra fare clic su **Chiavi di accesso**.
4. Nel pannello Chiavi di accesso visualizzato notare la chiave di accesso 1 e la chiave di accesso 2. È possibile usare una di queste indifferentemente.
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti.

### <a name="add-an-azure-cosmos-db-connection"></a>Aggiungere una connessione ad Azure Cosmos DB

Per connettersi ad Azure Cosmos DB, copiare la stringa di connessione primaria dal portale di Azure e creare un oggetto **Client** usando la stringa di connessione copiata. È possibile passare l'oggetto **Client** quando si crea un oggetto **TableService** :

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Creare una tabella

L'oggetto **Azure::Storage::Table::TableService** consente di usare tabelle ed entità. Per creare una tabella, usare il metodo **create_table()** . Nell'esempio seguente viene creata una tabella o stampato l'eventuale errore.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto hash che definisca le proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Archiviazione Azure utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. RowKey **è l'ID univoco dell'entità all'interno della partizione cui appartiene.**

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

* **update_entity():** aggiorna un'entità esistente sostituendola.
* **merge_entity():** aggiorna un'entità esistente unendovi nuovi valori delle proprietà.
* **insert_or_merge_entity():** aggiorna un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova:
* **insert_or_replace_entity():** aggiorna un'entità esistente unendovi nuovi valori delle proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

L'esempio seguente illustra l'aggiornamento di un'entità mediante **update_entity()** :

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Con **update_entity()** e **merge_entity()** , se l'entità che si sta aggiornando non esiste, l'operazione di aggiornamento ha esito negativo. Se pertanto si vuole archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario usare **insert_or_replace_entity()** o **insert_or_merge_entity()** .

## <a name="work-with-groups-of-entities"></a>Usare i gruppi di entità

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, è necessario prima creare un oggetto **Batch** e quindi usare il metodo **execute_batch()** su **TableService**. Nell'esempio seguente viene dimostrato l'invio di due entità con RowKey 2 e 3 in un batch: Si noti che funziona solo per le entità con lo stesso oggetto PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Eseguire una query su un'entità

Per eseguire una query su un'entità in una tabella, usare il metodo **get_entity()** passando il nome della tabella e i parametri **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Eseguire query su un set di entità

Per eseguire query su un set di entità in una tabella, creare un oggetto hash di query e usare il metodo **query_entities()** . Nell'esempio seguente viene dimostrato l'invio di tutte le entità con lo stesso oggetto **PartitionKey** :

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se il set di risultati è troppo grande per essere restituito da un'unica query, viene restituito un token di continuazione per recuperare le pagine successive.


## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, soprattutto per le entità di grandi dimensioni. Utilizzare la clausola select e passare i nomi delle proprietà da inoltrare al client.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Eliminare un'entità

Per eliminare un'entità, usare il metodo **delete_entity()** . Passare il nome della tabella che contiene l'entità e i parametri PartitionKey e RowKey dell'entità.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Eliminare una tabella

Per eliminare una tabella, usare il metodo **delete_table()** e passare il nome della tabella che si vuole eliminare.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Passaggi successivi

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Centro per sviluppatori Ruby](https://azure.microsoft.com/develop/ruby/)
* [Libreria client delle tabelle di Archiviazione di Microsoft Azure per Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table)