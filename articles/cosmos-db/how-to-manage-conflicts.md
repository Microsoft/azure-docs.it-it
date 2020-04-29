---
title: Gestire i conflitti tra le aree in Azure Cosmos DB
description: Informazioni su come gestire i conflitti in Azure Cosmos DB mediante la creazione del criterio di risoluzione dei conflitti last-writer-WINS o Custom
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mjbrown
ms.openlocfilehash: 6d364f1a9974d6d638bb0f824e88ed3866644c15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247410"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gestire i criteri di risoluzione dei conflitti in Azure Cosmos DB

Con le scritture in più aree, se più client scrivono nello stesso elemento si potrebbero verificare conflitti. Se si verifica un conflitto, è possibile risolverlo usando diversi criteri di risoluzione. Questo articolo descrive come gestire i criteri di risoluzione dei conflitti.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Creare un criterio di risoluzione dei conflitti in cui prevale l'ultima scrittura

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti in cui prevale l'ultima scrittura. Il percorso predefinito del criterio per cui prevale l'ultima scrittura corrisponde al campo timestamp o alla proprietà `_ts`. Per l'API SQL può anche essere impostato su un percorso definito dall'utente con un tipo numerico. In un conflitto prevale il valore più alto. Se il percorso non è impostato o non è valido, per impostazione predefinita si usa `_ts`. I conflitti risolti con questo criterio non vengono visualizzati nel feed di conflitti. Questo criterio può essere usato da tutte le API.

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Creare un criterio personalizzato di risoluzione dei conflitti con una stored procedure

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti personalizzato con una stored procedure per risolvere il conflitto. Questi conflitti non vengono visualizzati nel feed dei conflitti, a meno che non vi sia un errore nella stored procedure. Dopo aver creato il criterio con il contenitore, è necessario creare la stored procedure. Di seguito è riportato un esempio di .NET SDK. Questo criterio è supportato solo dall'API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Esempio di stored procedure per la risoluzione personalizzata di conflitti

Le stored procedure per la risoluzione personalizzata di conflitti devono essere implementate usando la firma della funzione indicata di seguito. Anche se non è necessario, scegliendo per la funzione lo stesso nome usato per la registrazione della stored procedure con il contenitore si semplifica la denominazione. Ecco una descrizione dei parametri che è necessario implementare per questa stored procedure.

- **incomingItem**: l'elemento da inserire o aggiornare nel commit che genera i conflitti. Per le operazioni di eliminazione è Null.
- **existingItem**: elemento di cui è attualmente stato eseguito il commit. Questo valore è non Null in un aggiornamento e Null per un'operazione di inserimento o eliminazione.
- **Tombstone**: valore booleano che indica se incomingItem è in conflitto con un elemento eliminato in precedenza. Se True, existingItem è Null.
- **conflictingItems**: matrice della versione di cui è stato eseguito il commit di tutti gli elementi nel contenitore in conflitto con INCOMINGITEM su ID o qualsiasi altra proprietà di indice univoco.

> [!IMPORTANT]
> Come per qualsiasi stored procedure, una procedura personalizzata di risoluzione dei conflitti può accedere ai dati con la stessa chiave di partizione e può eseguire qualsiasi operazione di inserimento, aggiornamento o eliminazione per risolvere i conflitti.

Questo esempio di stored procedure risolve i conflitti selezionando il valore minimo dal percorso `/myCustomId`.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

## <a name="create-a-custom-conflict-resolution-policy"></a>Creare un criterio di risoluzione dei conflitti personalizzato

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti personalizzato. Questi conflitti vengono visualizzati nel feed dei conflitti.

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Leggere dal feed dei conflitti

Questi esempi illustrano come leggere dal feed dei conflitti di un contenitore. I conflitti vengono visualizzati nel feed dei conflitti solo se non sono stati risolti automaticamente oppure se si usa un criterio personalizzato di risoluzione.

### <a name="net-sdk-v2"></a><a id="read-from-conflict-feed-dotnet"></a>.NET SDK V2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a name="net-sdk-v3"></a><a id="read-from-conflict-feed-dotnet-v3"></a>.NET SDK V3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```

### <a name="java-async-sdk"></a><a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a name="java-sync-sdk"></a><a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Passaggi successivi

Apprendere i concetti di Azure Cosmos DB seguenti:

- [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
- [Come configurare la funzionalità multimaster nelle applicazioni](how-to-multi-master.md)
- [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md).
- [Partizionamento e distribuzione dei dati](partition-data.md)
- [Indicizzazione in Azure Cosmos DB](indexing-policies.md)
