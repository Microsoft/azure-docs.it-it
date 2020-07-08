---
title: Usare la preferenza di lettura con l'API Azure Cosmos DB per MongoDB
description: Informazioni su come usare le preferenze di lettura di MongoDB con l'API di Azure Cosmos DB per MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.openlocfilehash: afdbd1c44170344be6edee8b8b2ee38c9853f81c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263076"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Come distribuire a livello globale le letture tramite l'API di Azure Cosmos DB per MongoDB

Questo articolo illustra come distribuire a livello globale le operazioni di lettura usando le impostazioni relative alle [preferenze di lettura di MongoDB](https://docs.mongodb.com/manual/core/read-preference/) con l'API di Azure Cosmos DB per MongoDB.

## <a name="prerequisites"></a>Prerequisiti 
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Fare riferimento a questo articolo [Avvio rapido](tutorial-global-distribution-mongodb.md) per istruzioni su come usare il portale di Azure per configurare un account Cosmos con distribuzione globale e quindi connettersi.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una directory di lavoro.  

Eseguire i comandi seguenti per clonare il repository di esempio. In base alla piattaforma rilevante, usare uno dei repository di esempio seguenti:

1. [Applicazione di esempio .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Applicazione di esempio NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Applicazione di esempio Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Applicazione di esempio Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Applicazione di esempio SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Eseguire l'applicazione

In base alla piattaforma in uso, installare i pacchetti necessari e avviare l'applicazione. Per installare le dipendenze, seguire le istruzioni nel file README incluso nel repository dell'applicazione di esempio. Ad esempio, nell'applicazione di esempio NodeJS eseguire i comandi seguenti per installare i pacchetti necessari e avviare l'applicazione.

```bash
cd mean
npm install
node index.js
```
L'applicazione prova a connettersi a un'origine MongoDB e l'operazione ha esito negativo perché la stringa di connessione non è valida. Seguire i passaggi nel file README per aggiornare la stringa di connessione `url`. Inoltre, aggiornare `readFromRegion` a un'area di lettura nell'account Cosmos. Le istruzioni seguenti sono tratte dall'esempio NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Dopo aver completato questi passaggi, l'applicazione di esempio viene eseguita e produce l'output seguente:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Leggere usando le modalità relative alle preferenze di lettura

Il protocollo MongoDB offre le modalità seguenti relative alle preferenze di lettura, per l'uso da parte dei client:

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

Vedere la documentazione sul [preferenze di lettura di MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) per informazioni dettagliate sul comportamento delle singole modalità delle preferenze di lettura. In Cosmos DB, la modalità PRIMARY corrisponde all'area WRITE e la modalità SECONDARY corrisponde all'area READ.

In base agli scenari comuni, è consigliabile usare le impostazioni seguenti:

1. Se sono necessarie **letture a bassa latenza**, usare la modalità **NEAREST**. Questa impostazione indirizza le operazioni di lettura all'area disponibile più vicina. Si noti che se l'area più vicina è l'area di scrittura, queste operazioni vengono indirizzate a tale area.
2. Se è richiesta la **disponibilità elevata e la distribuzione geografica delle letture** (la latenza non è un vincolo), usare la modalità preferenza di lettura preferita **primaria** o **secondaria** preferita. Questa impostazione indirizza le operazioni di lettura rispettivamente a un'area di scrittura o di lettura disponibile. Se l'area non è disponibile, le richieste vengono indirizzate all'area disponibile successiva in base al comportamento della preferenza di lettura.

Il frammento di codice seguente tratto dall'applicazione di esempio mostra come configurare le preferenze di lettura NEAREST in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Analogamente, il frammento di codice seguente mostra come configurare le preferenze di lettura SECONDARY_PREFERRED in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Le preferenze di lettura possono essere impostate anche passando `readPreference` come parametro nelle opzioni URI della stringa di connessione:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Fare riferimento al repository dell'applicazione di esempio corrispondente per altre piattaforme, ad esempio [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Leggere usando i tag

Oltre alle modalità relative alle preferenze di lettura, il protocollo MongoDB consente l'uso di tag per indirizzare le operazioni di lettura. Nell'API di Cosmos DB per MongoDB, il tag `region` è incluso per impostazione predefinita come parte della risposta `isMaster`:

```json
"tags": {
         "region": "West US"
      }
```

Di conseguenza, MongoClient può usare il tag `region` insieme al nome dell'area per indirizzare le operazioni di lettura ad aree specifiche. Per gli account Cosmos, è possibile trovare i nomi delle aree nel portale di Azure a sinistra in **Impostazioni -> Replica i dati a livello globale**. Questa impostazione è utile per ottenere l'**isolamento di lettura**, nei casi in cui l'applicazione client vuole indirizzare le operazioni di lettura solo a una determinata area. Questa impostazione è ideale per scenari non di produzione/di analisi, che vengono eseguiti in background e non sono servizi di produzione critici.

Il frammento di codice seguente tratto dall'applicazione di esempio mostra come configurare le preferenze di lettura mediante i tag in NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Fare riferimento al repository dell'applicazione di esempio corrispondente per altre piattaforme, ad esempio [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

In questo articolo si è appreso come distribuire a livello globale le operazioni di lettura usando le preferenze di lettura con l'API di Azure Cosmos DB per MongoDB.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questo articolo nel portale di Azure eseguendo questi passaggi:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)
* [Configurare un database distribuito a livello globale usando l'API di Azure Cosmos DB per MongoDB](tutorial-global-distribution-mongodb.md)
* [Sviluppare in locale con l'emulatore di Azure Cosmos DB](local-emulator.md)
