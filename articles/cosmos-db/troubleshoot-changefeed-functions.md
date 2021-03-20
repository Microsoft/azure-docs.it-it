---
title: Risolvere i problemi relativi all'uso del trigger funzioni di Azure per Cosmos DB
description: Problemi comuni, soluzioni alternative e procedure di diagnostica, quando si usa il trigger di funzioni di Azure per Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825627"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticare e risolvere i problemi quando si usa il trigger di funzioni di Azure per Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive i problemi comuni, le soluzioni alternative e i passaggi di diagnostica, quando si usa il [trigger di funzioni di Azure per Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependencies

Il trigger e le associazioni di funzioni di Azure per Cosmos DB dipendono dai pacchetti di estensione nel runtime di funzioni di Azure di base. Mantieni sempre aggiornati i pacchetti, in quanto potrebbero includere correzioni e nuove funzionalità che potrebbero risolvere eventuali problemi potenziali che possono verificarsi:

* Per funzioni di Azure V2, vedere [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Per funzioni di Azure V1, vedere [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Questo articolo si riferisce sempre a funzioni di Azure V2 ogni volta che viene indicato il runtime, a meno che non venga specificato in modo esplicito.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Utilizzare il Azure Cosmos DB SDK in modo indipendente

La funzionalità principale del pacchetto di estensione consiste nel fornire supporto per il trigger e le associazioni di funzioni di Azure per Cosmos DB. Include anche [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), utile se si desidera interagire con Azure Cosmos DB a livello di codice senza utilizzare il trigger e le associazioni.

Se si vuole usare Azure Cosmos DB SDK, assicurarsi di non aggiungere al progetto un altro riferimento al pacchetto NuGet. Al contrario, **lasciare che il riferimento all'SDK venga risolto tramite il pacchetto di estensione delle funzioni di Azure**. Utilizzare il Azure Cosmos DB SDK separatamente dal trigger e dalle associazioni

Inoltre, se si crea manualmente una propria istanza del [client Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), è necessario seguire il modello per avere una sola istanza del client [usando un approccio basato su modello singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Questo processo eviterà i potenziali problemi di socket nelle operazioni.

## <a name="common-scenarios-and-workarounds"></a>Scenari comuni e soluzioni alternative

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>La funzione di Azure non riesce e la raccolta dei messaggi di errore non esiste

La funzione di Azure ha esito negativo con il messaggio di errore "la raccolta di origine ' Collection-Name ' (nel database ' database-name ') o la raccolta di lease ' Collection2-Name ' (nel database ' Database2-Name ') non esiste. Entrambe le raccolte devono esistere prima che il listener venga avviato. Per creare automaticamente la raccolta di lease, impostare "CreateLeaseCollectionIfNotExists" su "true" "

Ciò significa che uno o entrambi i contenitori di Azure Cosmos necessari per il funzionamento del trigger non esistono o non sono raggiungibili dalla funzione di Azure. **L'errore indica che il database e il contenitore di Azure Cosmos sono il trigger che cerca** in base alla configurazione.

1. Verificare l' `ConnectionStringSetting` attributo e che **faccia riferimento a un'impostazione esistente nel app per le funzioni di Azure**. Il valore di questo attributo non deve essere la stringa di connessione, ma il nome dell'impostazione di configurazione.
2. Verificare che `databaseName` e `collectionName` esistano nell'account Azure Cosmos. Se si usa la sostituzione automatica dei valori (usando i `%settingName%` modelli), assicurarsi che il nome dell'impostazione esista nel app per le funzioni di Azure.
3. Se non si specifica un oggetto `LeaseCollectionName/leaseCollectionName` , il valore predefinito è "leases". Verificare che tale contenitore esista. Facoltativamente, è possibile impostare l' `CreateLeaseCollectionIfNotExists` attributo nel trigger su `true` per crearlo automaticamente.
4. Verificare la [configurazione del firewall dell'account Azure Cosmos](how-to-configure-firewall.md) per verificare che non stia bloccando la funzione di Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Non è possibile avviare la funzione di Azure con la "raccolta della velocità effettiva condivisa deve avere una chiave di partizione"

Le versioni precedenti dell'estensione Azure Cosmos DB non supportano l'utilizzo di un contenitore lease creato in un [database di velocità effettiva condivisa](./set-throughput.md#set-throughput-on-a-database). Per risolvere questo problema, aggiornare l'estensione [Microsoft. Azure. Webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) per ottenere la versione più recente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>La funzione di Azure non viene avviata con "è necessario specificare PartitionKey per questa operazione".

Questo errore indica che si sta attualmente utilizzando una raccolta di lease partizionata con una [dipendenza di estensione](#dependencies)obsoleta. Eseguire l'aggiornamento alla versione più recente disponibile. Se è attualmente in esecuzione in funzioni di Azure V1, sarà necessario eseguire l'aggiornamento ad Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>La funzione di Azure non inizia con "la raccolta di lease, se partizionata, deve avere una chiave di partizione uguale a ID".

Questo errore indica che il contenitore dei lease correnti è partizionato, ma il percorso della chiave di partizione non lo è `/id` . Per risolvere questo problema, è necessario ricreare il contenitore leases con `/id` come chiave di partizione.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Viene visualizzato un valore che non può essere null. Nome parametro: o "nei log di funzioni di Azure quando si tenta di eseguire il trigger

Questo problema viene visualizzato se si usa il portale di Azure e si tenta di selezionare il pulsante **Esegui** sullo schermo quando si esamina una funzione di Azure che usa il trigger. Per il trigger non è necessario selezionare Esegui per avviare, che verrà avviato automaticamente quando viene distribuita la funzione di Azure. Se si vuole controllare il flusso di log della funzione di Azure nella portale di Azure, è sufficiente passare al contenitore monitorato e inserire alcuni nuovi elementi. verrà visualizzato automaticamente il trigger in esecuzione.

### <a name="my-changes-take-too-long-to-be-received"></a>La ricezione delle modifiche richiede troppo tempo

Questo scenario può avere più cause e tutti devono essere controllati:

1. La funzione di Azure è distribuita nella stessa area dell'account Azure Cosmos? Per una latenza di rete ottimale, sia la funzione di Azure che l'account Azure Cosmos devono trovarsi nella stessa area di Azure.
2. Le modifiche apportate nel contenitore Azure Cosmos sono continue o sporadiche?
Se sono sporadiche, potrebbe esserci un ritardo tra l'archiviazione delle modifiche e la loro scelta da parte della funzione di Azure. Questo avviene perché internamente, quando controlla la presenza di modifiche nel contenitore Azure Cosmos e non ne trova nessuna in sospeso, il trigger rimane in sospensione per un periodo di tempo configurabile (per impostazione predefinita 5 secondi) prima di controllare la presenza di nuove modifiche (per evitare un consumo di UR elevato). È possibile configurare questo tempo di sospensione tramite l'impostazione `FeedPollDelay/feedPollDelay` nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del trigger (si prevede un valore in millisecondi).
3. Il contenitore Azure Cosmos potrebbe avere una [frequenza limitata](./request-units.md).
4. È possibile usare l' `PreferredLocations` attributo nel trigger per specificare un elenco delimitato da virgole di aree di Azure per definire un ordine di connessione preferito personalizzato.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Alcune modifiche vengono ripetute nel trigger

Il concetto di "modifica" è un'operazione su un documento. Gli scenari più comuni in cui vengono ricevuti gli eventi per lo stesso documento sono:
* L'account usa la coerenza finale. Quando si usa il feed di modifiche in un livello di coerenza finale, è possibile che si verifichino eventi duplicati tra le operazioni di lettura di feed di modifiche successivi (l'ultimo evento di un'operazione di lettura viene visualizzato come il primo dell'operazione successiva).
* È in corso l'aggiornamento del documento. Il feed delle modifiche può contenere più operazioni per gli stessi documenti, se il documento riceve aggiornamenti, può selezionare più eventi (uno per ogni aggiornamento). Un modo semplice per distinguere tra le diverse operazioni per lo stesso documento consiste nel tenere traccia della `_lsn` [proprietà per ogni modifica](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Se non corrispondono, si tratta di modifiche diverse nello stesso documento.
* Se si identificano i documenti solo da `id` , tenere presente che l'identificatore univoco per un documento è `id` e la relativa chiave di partizione (possono essere presenti due documenti con la stessa chiave di `id` partizione diversa).

### <a name="some-changes-are-missing-in-my-trigger"></a>Alcune modifiche non sono presenti nel trigger

Se si ritiene che alcune delle modifiche apportate al contenitore Azure Cosmos non vengano prelevate dalla funzione di Azure o che alcune modifiche non siano presenti nella destinazione durante la copia, seguire questa procedura.

Quando la funzione di Azure riceve le modifiche, le elabora spesso e può facoltativamente inviare il risultato a un'altra destinazione. Quando si esaminano le modifiche mancanti, assicurarsi di **misurare le modifiche ricevute nel punto di** inserimento (all'avvio della funzione di Azure) e non nella destinazione.

Se nella destinazione mancano alcune modifiche, ciò potrebbe indicare che si è verificato un errore durante l'esecuzione della funzione di Azure dopo la ricezione delle modifiche.

In questo scenario, il modo migliore consiste nell'aggiungere `try/catch` blocchi nel codice e all'interno dei cicli che potrebbero elaborare le modifiche, per rilevare eventuali errori per un subset specifico di elementi e gestirli di conseguenza (inviarli a un'altra risorsa di archiviazione per un'ulteriore analisi o nuovi tentativi).

> [!NOTE]
> Per impostazione predefinita, il trigger di Funzioni di Azure per Cosmos DB non cerca di ripetere un batch di modifiche se si è verificata un'eccezione non gestita durante l'esecuzione del codice. Ciò significa che il motivo per cui le modifiche non arrivano alla destinazione è dovuto al fatto che l'elaborazione non è riuscita.

Se la destinazione è un altro contenitore Cosmos ed è in corso l'esecuzione di operazioni Upsert per copiare gli elementi, **verificare che la definizione della chiave di partizione sia nel contenitore Monitored che in quello di destinazione sia la stessa**. Le operazioni Upsert potrebbero salvare più elementi di origine come uno nella destinazione a causa di questa differenza di configurazione.

Se si rileva che alcune modifiche non sono state ricevute dal trigger, lo scenario più comune è che è **in esecuzione un'altra funzione di Azure**. Potrebbe trattarsi di un'altra funzione di Azure distribuita in Azure o di una funzione di Azure in esecuzione in locale nel computer di uno sviluppatore che ha **esattamente la stessa configurazione** (gli stessi contenitori monitorati e di lease) e che questa funzione di Azure sta rubando un subset delle modifiche che si prevede vengano elaborate dalla funzione di Azure.

Inoltre, è possibile convalidare lo scenario, se si conosce il numero di istanze di app per le funzioni di Azure in esecuzione. Se si esamina il contenitore dei lease e si conta il numero di elementi di lease in, i valori distinti della `Owner` Proprietà in essi contenuti devono essere uguali al numero di istanze del app per le funzioni. Se sono presenti più proprietari delle istanze note di Azure app per le funzioni, significa che questi proprietari aggiuntivi sono quelli che "rubano" le modifiche.

Un modo semplice per aggirare questa situazione consiste nell'applicare un `LeaseCollectionPrefix/leaseCollectionPrefix` alla funzione con un valore nuovo o diverso oppure, in alternativa, eseguire il test con un nuovo contenitore lease.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>È necessario riavviare e rielaborare tutti gli elementi nel contenitore dall'inizio 
Per rielaborare tutti gli elementi di un contenitore dall'inizio:
1. Arrestare la funzione di Azure se è attualmente in esecuzione. 
1. Eliminare i documenti nella raccolta di lease (oppure eliminare e ricreare la raccolta di lease in modo che sia vuota)
1. Impostare l'attributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger nella funzione su true. 
1. Riavviare la funzione di Azure. Verranno ora lette ed elaborate tutte le modifiche dall'inizio. 

Se si imposta [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) su true, la funzione di Azure inizierà a leggere le modifiche dall'inizio della cronologia della raccolta anziché dall'ora corrente. Questa operazione funziona solo quando non sono presenti lease già creati, ovvero documenti nella raccolta Leases. L'impostazione di questa proprietà su true quando sono già stati creati lease non ha alcun effetto; in questo scenario, quando una funzione viene arrestata e riavviata, inizierà a leggere dall'ultimo checkpoint, come definito nella raccolta Leases. Per rielaborare dall'inizio, seguire i passaggi precedenti 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>L'associazione può essere eseguita solo con IReadOnlyList \<Document> o JArray

Questo errore si verifica se il progetto di funzioni di Azure o qualsiasi progetto a cui si fa riferimento contiene un riferimento a NuGet manuale a Azure Cosmos DB SDK con una versione diversa da quella fornita dall' [estensione Cosmos DB di funzioni di Azure](./troubleshoot-changefeed-functions.md#dependencies).

Per ovviare a questa situazione, rimuovere il riferimento NuGet manuale che è stato aggiunto e consentire al Azure Cosmos DB riferimento SDK di risolversi tramite il pacchetto di estensioni Cosmos DB di funzioni di Azure.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Modifica dell'intervallo di polling della funzione di Azure per le modifiche di rilevamento

Come spiegato in precedenza, [la ricezione delle modifiche richiede troppo tempo](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received). la funzione di Azure verrà sospesa per un periodo di tempo configurabile (per impostazione predefinita, 5 secondi) prima di verificare la presenza di nuove modifiche (per evitare un consumo di ur elevato). È possibile configurare questo tempo di sospensione tramite l'impostazione `FeedPollDelay/feedPollDelay` nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del trigger (si prevede un valore in millisecondi).

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio per le funzioni di Azure](../azure-functions/functions-monitoring.md)
* [Risoluzione dei problemi di Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
