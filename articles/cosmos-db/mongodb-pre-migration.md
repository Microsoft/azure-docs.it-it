---
title: Passaggi di pre-migrazione per la migrazione dei dati all'API di Azure Cosmos DB per MongoDB
description: Questo documento offre una panoramica dei prerequisiti per la migrazione dei dati da MongoDB a Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anfeldma
ms.openlocfilehash: cdc5dc9cee3520d9a3f22ff710dfa193e6ef4fed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553290"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di pre-migrazione per la migrazione dei dati da MongoDB all'API di Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Questa guida precedente alla migrazione di MongoDB è la prima di una serie sulla migrazione di MongoDB a Azure Cosmos DB l'API Mongo su larga scala. I clienti che gestiscono e distribuiscono MongoDB sull'infrastruttura autogestita possono voler ridurre e gestire il costo della propria data, eseguendo la migrazione a un servizio cloud gestito come Azure Cosmos DB con prezzi con pagamento in base al consumo e scalabilità elastica. L'obiettivo di questa serie è guidare il cliente attraverso il processo di migrazione:
>
> 1. [Pre-migrazione](mongodb-pre-migration.md) : è possibile inventariare i dati di MongoDB esistenti, pianificare la migrazione e scegliere gli strumenti di migrazione appropriati.
> 2. Esecuzione: eseguire la migrazione da MongoDB a Azure Cosmos DB usando le [esercitazioni]()fornite.
> 3. [Post-migrazione](mongodb-post-migration.md) : consente di aggiornare e ottimizzare le applicazioni esistenti per l'esecuzione in base alla nuova Azure Cosmos DB Data.
>

Un piano di pre-migrazione solido può avere un effetto sul ridimensionamento della tempestività e del successo della migrazione del team. Un'analogia efficace per la pre-migrazione è l'avvio di un nuovo progetto: è possibile iniziare definendo i requisiti, quindi delineare le attività necessarie e assegnare una priorità alle attività principali da affrontare per prime. In questo modo è possibile stimare la pianificazione del progetto, ma naturalmente i requisiti imprevisti possono verificarsi e complicare la pianificazione del progetto. Tornando alla migrazione: la creazione di un piano di esecuzione completo nella fase di pre-migrazione riduce al minimo la possibilità che vengano individuate attività di migrazione impreviste in ritardo nel processo, risparmiando tempo durante la migrazione e aiutandoci a garantire che gli obiettivi siano soddisfatti.

Prima di eseguire la migrazione dei dati da MongoDB (in locale o nel cloud) all'API di Azure Cosmos DB per MongoDB, è necessario:

1. [Leggere le considerazioni principali sull'uso dell'API di Azure Cosmos DB per MongoDB](#considerations)
2. [Scegliere un'opzione per eseguire la migrazione dei dati](#options)
3. [Stimare la velocità effettiva necessaria per i carichi di lavoro](#estimate-throughput)
4. [Scegliere una chiave di partizione ottimale per i dati](#partitioning)
5. [Conoscere i criteri di indicizzazione che è possibile impostare sui dati](#indexing)

Se si soddisfano già i suddetti pre-requisiti per la migrazione, è possibile [migrare i dati di MongoDB all'API di Azure Cosmos DB per MongoDB usando il servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db.md). Se, inoltre, non è stato creato un account, è possibile consultare uno degli [argomenti di avvio rapido](create-mongodb-dotnet.md) che illustrano la procedura per la creazione di un account.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerazioni sull'uso dell'API di Azure Cosmos DB per MongoDB

Di seguito sono illustrate alcune caratteristiche specifiche dell'API di Azure Cosmos DB per MongoDB:

- **Modello di capacità**: la capacità del database in Azure Cosmos DB si basa su un modello basato sulla velocità effettiva. Questo modello è basato sulle [unità richiesta al secondo](request-units.md), ovvero le unità che rappresentano il numero di operazioni di database che possono essere eseguite su una raccolta al secondo. Questa capacità può essere allocata a [livello di database o di raccolta](set-throughput.md) ed è possibile effettuarne il provisioning in un modello di allocazione o tramite la [velocità effettiva di provisioning a scalabilità automatica](provision-throughput-autoscale.md).

- **Unità richiesta**: per ogni operazione di database è associato un costo di unità richiesta in Azure Cosmos DB. Quando viene applicato, il costo viene sottratto dal livello delle unità richiesta disponibili in un determinato secondo. Se per una richiesta sono necessarie più UR rispetto alle UR/s attualmente allocate, è possibile risolvere il problema scegliendo tra due opzioni: aumentare la quantità di UR o attendere che abbia inizio il secondo successivo per ripetere l'operazione.

- **Capacità elastica**: la capacità di una raccolta o un database può cambiare in qualsiasi momento. Il database, in questo modo, può adattarsi in modo elastico ai requisiti di velocità effettiva del carico di lavoro.

- **Partizionamento orizzontale automatico**: Azure Cosmos DB offre un sistema di partizionamento automatico che richiede solo una partizione (o una chiave di partizione). Il [meccanismo di partizionamento automatico](partitioning-overview.md) viene condiviso tra tutte le API di Azure Cosmos DB e consente la continuità dei dati e la scalabilità della velocità effettiva tramite distribuzione orizzontale.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opzioni di migrazione per l'API di Azure Cosmos DB per MongoDB

Il [servizio Migrazione del database di Azure per l'API di Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) offre un meccanismo che semplifica la migrazione dei dati mettendo a disposizione una piattaforma di hosting completamente gestita, opzioni di monitoraggio della migrazione e gestione automatica delle limitazioni. Di seguito è riportato l'elenco completo delle opzioni:

|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Online|[Servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Usa la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull; Adatto per set di dati di grandi dimensioni e capace di replicare modifiche in tempo reale <br/>&bull; Compatibile con altre origini MongoDB|
|Offline|[Servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Usa la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull; Adatto per set di dati di grandi dimensioni e capace di replicare modifiche in tempo reale <br/>&bull; Compatibile con altre origini MongoDB|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Facile da configurare e in grado di supportare più origini <br/>&bull; Usa la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull; Adatto per set di dati di grandi dimensioni. <br/>&bull; La mancanza di checkpoint significa che qualsiasi problema durante il processo di migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull; La mancanza di una coda di messaggi non recapitabili significa che alcuni file non corretti potrebbero arrestare l'intero processo di migrazione <br/>&bull; Richiede codice personalizzato per aumentare la velocità effettiva di lettura per alcune origini dati|
|Offline|[ Strumenti Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Facile da configurare e integrare <br/>&bull; Richiede una gestione personalizzata per le limitazioni|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Stimare la velocità effettiva necessaria per i carichi di lavoro

In Azure Cosmos DB, il provisioning della velocità effettiva viene eseguito in anticipo e misurato in unità richiesta (UR) al secondo. Diversamente dalle macchine virtuali o dai server locali, le UR sono facilmente scalabili in qualsiasi momento. È possibile modificare immediatamente il numero di UR sottoposte a provisioning. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](request-units.md).

Per determinare la quantità di unità richiesta in base alla configurazione dell'account del database, alla quantità di dati, alle dimensioni dei documenti e al numero richiesto di letture/scritture al secondo, è possibile usare lo [strumento di calcolo della capacità di Cosmos DB](https://cosmos.azure.com/capacitycalculator/).

Di seguito sono elencati i fattori chiave che influiscono sul numero di UR richiesto:
- **Dimensioni del documento**: con l'aumentare delle dimensioni di un elemento/documento, aumenta anche il numero di UR usate per leggere o scrivere l'elemento/documento.

- **Numero di proprietà del documento**: il numero di UR usate per creare o aggiornare un documento è correlato al numero, alla complessità e alla lunghezza delle relative proprietà. È possibile ridurre l'utilizzo di unità richiesta per operazioni di scrittura [limitando il numero di proprietà indicizzate](mongodb-indexing.md).

- **Modelli di query**: la complessità di una query influisce sulla quantità di unità richiesta usate da una query. 

Il modo migliore per determinare il costo delle query è quello di usare i dati di esempio in Azure Cosmos DB [ed eseguire query di esempio dalla shell di MongoDB](connect-mongodb-account.md) usando il comando `getLastRequestStastistics` per ottenere l'addebito della richiesta, che restituirà il numero di UR usate:

`db.runCommand({getLastRequestStatistics: 1})`

Con questo comando verrà restituito un documento JSON simile al seguente:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

È anche possibile usare le [impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) per conoscere la frequenza e i modelli delle query eseguite in Azure Cosmos DB. I risultati dei log di diagnostica possono essere inviati a un account di archiviazione, un'istanza di EventHub o [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Scegliere la chiave di partizione
Il partizionamento, o meglio il partizionamento orizzontale, è un punto essenziale di cui tenere conto prima della migrazione dei dati. Azure Cosmos DB usa il partizionamento completamente gestito per aumentare la capacità di un database in modo da soddisfare i requisiti di archiviazione e di velocità effettiva. Per questa funzionalità non è necessario l'hosting o la configurazione di server di routing.   

In modo analogo, la funzionalità di partizionamento aggiunge automaticamente capacità e ri-bilancia i dati di conseguenza. Per informazioni dettagliate e raccomandazioni sulla scelta della chiave di partizione corretta per i dati, vedere l'articolo [Scelta di una chiave di partizione](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indicizzare i dati

L'API Azure Cosmos DB per MongoDB Server versione 3,6 e successive indicizza automaticamente solo il `_id` campo. Questo campo non può essere eliminato. Viene automaticamente applicata l'univocità del `_id` campo per ogni chiave di partizione. Per indicizzare campi aggiuntivi, applicare i [comandi di gestione degli indici MongoDB](mongodb-indexing.md). Questo criterio di indicizzazione predefinito differisce dall'API di Azure Cosmos DB SQL, che indicizza tutti i campi per impostazione predefinita.

Le funzionalità di indicizzazione fornite da Azure Cosmos DB includono l'aggiunta di indici composti, indici univoci e indici TTL (time-to-Live). Viene eseguito il mapping dell'interfaccia di gestione degli indici al comando `createIndex()`. Per altre informazioni, vedere l'articolo sull' [indicizzazione nell'API Azure Cosmos DB per MongoDB](mongodb-indexing.md).

Il [servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db.md) esegue automaticamente la migrazione delle raccolte di MongoDB con indici univoci. È tuttavia necessario che gli indici univoci vengano creati prima della migrazione. Azure Cosmos DB non supporta la creazione di indici univoci quando sono già presenti dati nelle raccolte. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione dei dati di MongoDB a Cosmos DB usando il servizio Migrazione del database.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
