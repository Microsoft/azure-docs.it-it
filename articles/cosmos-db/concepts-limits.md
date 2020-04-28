---
title: Quote del servizio Azure Cosmos DB
description: Azure Cosmos DB le quote del servizio e i limiti predefiniti per diversi tipi di risorse.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6dcda58652abe51d360748222263f30f337a2251
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182736"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quote del servizio Azure Cosmos DB

Questo articolo fornisce una panoramica delle quote predefinite offerte a risorse diverse in Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Archiviazione e velocità effettiva

Dopo aver creato un account Azure Cosmos nella sottoscrizione, è possibile gestire i dati nell'account [creando database, contenitori ed elementi](databases-containers-items.md). È possibile effettuare il provisioning della velocità effettiva a livello di contenitore o di database in termini di [unità richiesta (UR/s o UR)](request-units.md). La tabella seguente elenca i limiti per l'archiviazione e la velocità effettiva per contenitore/database.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di ur per contenitore ([modalità di provisioning della velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 1 milione per impostazione predefinita. È possibile aumentarlo inserendo [un ticket di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Numero massimo di ur per database ([modalità con provisioning della velocità effettiva condivisa](databases-containers-items.md#azure-cosmos-containers)) | 1 milione per impostazione predefinita. È possibile aumentarlo inserendo [un ticket di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Numero massimo di ur per chiave di partizione (logica) | 10,000 |
| Archiviazione massima tra tutti gli elementi per ogni chiave di partizione (logica)| 20 GB |
| Numero massimo di chiavi di partizione (logiche) distinte | Nessuna limitazione |
| Spazio di archiviazione massimo per contenitore | Nessuna limitazione |
| Spazio di archiviazione massimo per database | Nessuna limitazione |
| Dimensioni massime degli allegati per ogni account (la funzionalità degli allegati verrà ammortizzata) | 2 GB |
| Numero minimo di ur richieste per 1 GB | 10 UR/sec |

> [!NOTE]
> Per informazioni sulle procedure consigliate per la gestione dei carichi di lavoro con chiavi di partizione che richiedono limiti più elevati per l'archiviazione o la velocità effettiva, vedere [creare una chiave di partizione sintetica](synthetic-partition-keys.md).
>

Un contenitore Cosmos (o un database con velocità effettiva condivisa) deve avere una velocità effettiva minima di 400 ur. Man mano che aumenta il contenitore, la velocità effettiva minima supportata dipende anche dai fattori seguenti:

* La velocità effettiva minima che è possibile impostare in un contenitore dipende dalla velocità effettiva massima di cui è stato effettuato il provisioning nel contenitore. Se ad esempio la velocità effettiva è aumentata a 10000 ur, la velocità effettiva con provisioning più bassa possibile sarà 1000 ur
* La velocità effettiva minima in un database di velocità effettiva condivisa dipende anche dal numero totale di contenitori creati in un database di velocità effettiva condivisa, misurato a 100 ur per ogni contenitore. Se, ad esempio, sono stati creati cinque contenitori all'interno di un database di velocità effettiva condivisa, la velocità effettiva deve essere almeno di 500 UR

La velocità effettiva corrente e minima di un contenitore o di un database può essere recuperata dal portale di Azure o dagli SDK. Per altre informazioni, vedere [provisioning della velocità effettiva nei contenitori e nei database](set-throughput.md). 

> [!NOTE]
> In alcuni casi, potrebbe essere possibile ridurre la velocità effettiva a un valore inferiore al 10%. Usare l'API per ottenere le UR minime esatte per ogni contenitore.
>

In breve, di seguito sono riportati i limiti minimi di provisioning delle UR. 

| Risorsa | Limite predefinito |
| --- | --- |
| Numero minimo di ur per contenitore ([modalità di provisioning della velocità effettiva dedicata](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Numero minimo di ur per database ([modalità con provisioning della velocità effettiva condivisa](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Unità richiesta minime per contenitore all'interno di un database di velocità effettiva condivisa | 100 |

Cosmos DB supporta il ridimensionamento elastico della velocità effettiva (UR) per ogni contenitore o database tramite gli SDK o il portale. Ogni contenitore può essere ridimensionato in modo sincrono e immediatamente entro un intervallo di scala compreso tra 10 e 100 volte, tra i valori minimo e massimo. Se il valore della velocità effettiva richiesta non rientra nell'intervallo, il ridimensionamento viene eseguito in modo asincrono. Il completamento del ridimensionamento asincrono può richiedere minuti o ore, a seconda della velocità effettiva richiesta e delle dimensioni di archiviazione dei dati nel contenitore.  

## <a name="control-plane-operations"></a>Operazioni del piano di controllo

È possibile eseguire il [provisioning e gestire l'account Azure Cosmos](how-to-manage-database-account.md) usando i modelli di portale di Azure, Azure PowerShell, interfaccia della riga di comando di azure e Azure Resource Manager. La tabella seguente elenca i limiti per sottoscrizione, account e numero di operazioni.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di account di database per sottoscrizione | 50 per impostazione predefinita. È possibile aumentarlo inserendo [un ticket di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Numero massimo di failover a livello di area | 1/ora per impostazione predefinita. È possibile aumentarlo inserendo [un ticket di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> I failover a livello di area sono validi solo per le Scritture a area singola. Gli account di scrittura in più aree non richiedono o non hanno limiti per la modifica dell'area di scrittura.

Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. Per informazioni dettagliate sugli intervalli di conservazione del backup e su Windows, vedere [backup online e ripristino dei dati su richiesta in Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limiti per account

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di database | Nessuna limitazione |
| Numero massimo di contenitori per database con velocità effettiva condivisa |25 |
| Numero massimo di contenitori per database o account con velocità effettiva dedicata  |Nessun limite |
| Numero massimo di aree | Nessun limite (tutte le aree di Azure) |

## <a name="per-container-limits"></a>Limiti per contenitore

A seconda dell'API usata, un contenitore di Azure Cosmos può rappresentare una raccolta, una tabella o un grafo. I contenitori supportano le configurazioni per [vincoli di chiave univoca](unique-keys.md), [stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md)e criteri di [indicizzazione](how-to-manage-indexing-policy.md). La tabella seguente elenca i limiti specifici delle configurazioni all'interno di un contenitore. 

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima del nome del database o del contenitore | 255 |
| Numero massimo di stored procedure per contenitore | 100<sup>*</sup>|
| Numero massimo di UDF per contenitore | 25<sup>*</sup>|
| Numero massimo di percorsi nei criteri di indicizzazione| 100<sup>*</sup>|
| Numero massimo di chiavi univoche per ogni contenitore|10<sup>*</sup>|
| Numero massimo di percorsi per vincolo di chiave univoca|16<sup>*</sup>|

<sup>*</sup>È possibile aumentare i limiti per ogni contenitore contattando il supporto tecnico di Azure.

## <a name="per-item-limits"></a>Limiti per singolo elemento

A seconda dell'API usata, un elemento di Azure Cosmos può rappresentare un documento in una raccolta, una riga in una tabella o un nodo o un bordo in un grafico. La tabella seguente mostra i limiti per ogni elemento in Cosmos DB. 

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensioni massime di un elemento | 2 MB (lunghezza UTF-8 della rappresentazione JSON) |
| Lunghezza massima del valore della chiave di partizione | 2048 byte |
| Lunghezza massima del valore ID | 1023 byte |
| Numero massimo di proprietà per elemento | Nessun limite pratico |
| Profondità massima di annidamento | Nessun limite pratico |
| Lunghezza massima del nome della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà | Nessun limite pratico |
| Lunghezza massima del valore della proprietà stringa | Nessun limite pratico |
| Lunghezza massima del valore della proprietà numerica | IEEE754 a precisione doppia 64 bit |

Non sono previste restrizioni per i payload dell'elemento come il numero di proprietà e la profondità di nidificazione, ad eccezione delle limitazioni di lunghezza per la chiave di partizione e i valori ID e la restrizione di dimensione complessiva di 2 MB. Potrebbe essere necessario configurare i criteri di indicizzazione per i contenitori con strutture di elementi grandi o complesse per ridurre il consumo di ur. Vedere [modellazione di elementi in Cosmos DB](how-to-model-partition-example.md) per un esempio reale e modelli per gestire elementi di grandi dimensioni.

## <a name="per-request-limits"></a>Limiti per richiesta

Azure Cosmos DB supporta [operazioni CRUD e di query](https://docs.microsoft.com/rest/api/cosmos-db/) su risorse quali contenitori, elementi e database. Supporta inoltre [richieste batch transazionali](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) su più elementi con la stessa chiave di partizione in un contenitore.

| Risorsa | Limite predefinito |
| --- | --- |
| Tempo massimo di esecuzione per una singola operazione (ad esempio, un'esecuzione stored procedure o un singolo recupero della pagina di query)| 5 secondi |
| Dimensioni massime della richiesta (ad esempio, stored procedure, CRUD)| 2 MB |
| Dimensione massima della risposta (ad esempio, query impaginata) | 4 MB |
| Numero massimo di operazioni in un batch transazionale | 100 |

Una volta che un'operazione come query raggiunge il limite di timeout di esecuzione o la dimensione della risposta, restituisce una pagina di risultati e un token di continuazione al client per riprendere l'esecuzione. Non esiste un limite pratico per la durata di esecuzione di una singola query tra pagine/continuazioni.

Cosmos DB USA HMAC per l'autorizzazione. È possibile usare una chiave master o un token di [risorsa](secure-access-to-data.md) per il controllo di accesso con granularità fine a risorse quali contenitori, chiavi di partizione o elementi. Nella tabella seguente sono elencati i limiti per i token di autorizzazione in Cosmos DB.

| Risorsa | Limite predefinito |
| --- | --- |
| Ora di scadenza massima del token master | 15 min  |
| Tempo di scadenza del token di risorsa minimo | 10 min  |
| Tempo di scadenza massimo del token di risorsa | 24 ore per impostazione predefinita. È possibile aumentarlo inserendo [un ticket di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Sfasamento di clock massimo per l'autorizzazione del token| 15 min |

Cosmos DB supporta l'esecuzione di trigger durante le Scritture. Il servizio supporta un massimo di un pre-trigger e un post-trigger per operazione di scrittura. 

## <a name="autoscale-mode-limits"></a>Limiti della modalità di scalabilità automatica

Vedere l'articolo relativo alla [scalabilità](provision-throughput-autoscale.md#autoscale-limits) automatica per i limiti di velocità effettiva e archiviazione in modalità di scalabilità automatica.

## <a name="sql-query-limits"></a>Limiti per le query SQL

Cosmos DB supporta l'esecuzione di query su elementi con [SQL](how-to-sql-query.md). Nella tabella seguente vengono descritte le restrizioni nelle istruzioni di query, ad esempio in termini di numero di clausole o di lunghezza della query.

| Risorsa | Limite predefinito |
| --- | --- |
| Lunghezza massima della query SQL| 256 kB |
| Numero massimo di JOIN per query| 5<sup>*</sup>|
| Numero massimo di UDF per query| 10<sup>*</sup>|
| Punti massimi per poligono| 4096 |
| Numero massimo di percorsi inclusi per ogni contenitore| 500 |
| Numero massimo di percorsi esclusi per contenitore| 500 |
| Numero massimo di proprietà in un indice composto| 8 |

<sup>*</sup>È possibile aumentare questi limiti per le query SQL contattando il supporto tecnico di Azure.

## <a name="mongodb-api-specific-limits"></a>Limiti specifici dell'API MongoDB

Cosmos DB supporta il protocollo wire di MongoDB per le applicazioni scritte in MongoDB. È possibile trovare i comandi e le versioni del protocollo supportati in [funzionalità e sintassi di MongoDB supportate](mongodb-feature-support.md).

La tabella seguente elenca i limiti specifici del supporto della funzionalità MongoDB. Altri limiti di servizio indicati per l'API SQL (Core) si applicano anche all'API MongoDB.

| Risorsa | Limite predefinito |
| --- | --- |
| Dimensioni massime della memoria della query MongoDB (questa limitazione è solo per la versione del server 3,2) | 40 MB |
| Tempo massimo di esecuzione per le operazioni MongoDB| 30 secondi |
| Timeout connessione inattiva per chiusura connessione lato server * | 30 minuti |

\*È consigliabile che le applicazioni client impostino il timeout della connessione inattiva nelle impostazioni del driver su 2-3 minuti perché il [timeout predefinito per Azure LoadBalancer è 4 minuti](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout).  Questo timeout garantisce che le connessioni inattive non siano chiuse da un servizio di bilanciamento del carico intermedio tra il computer client e Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Prova Cosmos DB limiti gratuiti

La tabella seguente elenca i limiti per la versione di valutazione [gratuita di Try Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) .

| Risorsa | Limite predefinito |
| --- | --- |
| Durata della versione di valutazione | 30 giorni (è possibile rinnovare un numero qualsiasi di volte) |
| Numero massimo di contenitori per sottoscrizione (SQL, Gremlin, API Tabella) | 1 |
| Numero massimo di contenitori per sottoscrizione (API MongoDB) | 3 |
| Velocità effettiva massima per contenitore | 5000 |
| Velocità effettiva massima per database con velocità effettiva condivisa | 20000 |
| Spazio di archiviazione totale massimo per account | 10 GB |

Prova Cosmos DB supporta la distribuzione globale solo nelle aree Stati Uniti centrali, Europa settentrionale e Asia sudorientale. Non è possibile creare ticket di supporto di Azure per gli account di prova Azure Cosmos DB. Tuttavia, viene fornito il supporto per i sottoscrittori con piani di supporto esistenti.

## <a name="free-tier-account-limits"></a>Limiti dell'account del livello gratuito
Nella tabella seguente sono elencati i limiti per [Azure Cosmos DB account del livello gratuito.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di livello gratuito per ogni sottoscrizione di Azure | 1 |
| Durata dello sconto del livello gratuito | Durata dell'account. Deve acconsentire esplicitamente durante la creazione dell'account. |
| Numero massimo di Ur/s gratuito | 400 UR/sec |
| Spazio di archiviazione massimo gratuito | 5 GB |
| Numero massimo di database di velocità effettiva condivisa | 5 |
| Numero massimo di contenitori in un database di velocità effettiva condivisa | 25 <br>Negli account di livello gratuito, le UR/sec minime per un database con velocità effettiva condivisa con un massimo di 25 contenitori sono 400 ur/s. |

  Oltre a quanto indicato sopra, i [limiti per account](#per-account-limits) si applicano anche agli account del livello gratuito.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui concetti di base di Cosmos DB la [distribuzione globale](distribute-data-globally.md) e il [partizionamento](partitioning-overview.md) e la [velocità effettiva con provisioning](request-units.md).

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
* [Come iniziare a usare l'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
* [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
