---
title: Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos
description: Informazioni su come impostare la velocità effettiva con provisioning per contenitori e database di Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251973"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Effettuare il provisioning della velocità effettiva per contenitori e database

Un database di Azure Cosmos è un'unità di gestione per un set di contenitori. Un database è costituito da un set di contenitori indipendenti dallo schema. Un contenitore di Azure Cosmos è l'unità di scalabilità per velocità effettiva e archiviazione. Un contenitore è partizionato orizzontalmente tra un set di computer all'interno di un'area di Azure e viene distribuito in tutte le aree di Azure associate all'account Azure Cosmos.

Con Azure Cosmos DB è possibile effettuare il provisioning della velocità effettiva a due granularità:
 
- Contenitori Azure Cosmos DB
- Database Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Configurare la velocità effettiva in un contenitore  

La velocità effettiva di cui è stato effettuato il provisioning in un contenitore di Azure Cosmos è riservata esclusivamente a tale contenitore. Il contenitore riceve sempre la velocità effettiva con provisioning. La velocità effettiva con provisioning in un contenitore è supportata finanziariamente da contratti di servizio. Per informazioni su come configurare la velocità effettiva in un contenitore, vedere [provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).

L'impostazione della velocità effettiva con provisioning in un contenitore è l'opzione utilizzata più di frequente. È possibile ridimensionare in modo elastico la velocità effettiva per un contenitore effettuando il provisioning di qualsiasi quantità di velocità effettiva usando [unità richiesta (UR)](request-units.md). 

La velocità effettiva di cui viene effettuato il provisioning per un contenitore viene distribuita uniformemente tra le partizioni fisiche e supponendo una chiave di partizione efficace che distribuisca equamente le partizioni logiche tra le partizioni fisiche, la velocità effettiva viene distribuita anche in modo uniforme tra tutte le partizioni logiche del contenitore. Non è possibile specificare selettivamente la velocità effettiva per le partizioni logiche. Poiché una o più partizioni logiche di un contenitore sono ospitate da una partizione fisica, le partizioni fisiche appartengono esclusivamente al contenitore e supportano la velocità effettiva di cui è stato effettuato il provisioning nel contenitore. 

Se il carico di lavoro in esecuzione in una partizione logica consuma più della velocità effettiva allocata a tale partizione logica, le operazioni vengono eseguite con limitazione della frequenza. Quando si verifica una limitazione della frequenza, è possibile aumentare la velocità effettiva con provisioning per l'intero contenitore oppure ripetere le operazioni. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

È consigliabile configurare la velocità effettiva al livello di granularità del contenitore per ottenere prestazioni garantite per il contenitore.

L'immagine seguente mostra in che modo una partizione fisica ospita una o più partizioni logiche di un contenitore:

![Partizione fisica](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Configurare la velocità effettiva in un database

Quando si esegue il provisioning della velocità effettiva in un database di Azure Cosmos, la velocità effettiva viene condivisa tra tutti i contenitori, detti contenitori di database condivisi, nel database. Un'eccezione è rappresentata dal caso in cui sia stata specificata una velocità effettiva con provisioning in contenitori specifici del database. La condivisione della velocità effettiva con provisioning a livello di database tra i contenitori è analoga all'hosting di un database in un cluster di computer. Poiché tutti i contenitori all'interno di un database condividono le risorse disponibili in un computer, naturalmente non si ottengono prestazioni prevedibili in un contenitore specifico. Per informazioni su come configurare la velocità effettiva con provisioning in un database, vedere [configurare la velocità effettiva con provisioning in un database di Azure Cosmos](how-to-provision-database-throughput.md).

Impostando la velocità effettiva in un database di Azure Cosmos si garantisce sempre la velocità effettiva con provisioning per il database. Dato che tutti i contenitori all'interno del database condividono la velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB non garantisce alcuna velocità effettiva prevedibile per un determinato contenitore nel database. La porzione della velocità effettiva che può essere ricevuta da uno specifico contenitore dipende dai fattori seguenti:

* Numero di contenitori.
* Scelta delle chiavi di partizione per vari contenitori.
* Distribuzione del carico di lavoro tra varie partizioni logiche dei contenitori. 

È consigliabile configurare la velocità effettiva in un database se si intende condividere la velocità effettiva tra più contenitori, ma non si vuole riservarla a uno specifico contenitore. 

Di seguito sono riportati alcuni esempi in cui è preferibile effettuare il provisioning della velocità effettiva a livello di database:

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile per un'applicazione multi-tenant. Ogni utente può essere rappresentato da un contenitore di Azure Cosmos distinto.

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile quando si esegue la migrazione in Azure Cosmos DB di un database NoSQL, come MongoDB o Cassandra, ospitato in un cluster di macchine virtuali o in server fisici locali. Si può paragonare la velocità effettiva con provisioning configurata nel database di Azure Cosmos a un equivalente logico (ma più conveniente e flessibile) della capacità di calcolo del cluster MongoDB o Cassandra.  

Tutti i contenitori creati all'interno di un database con la velocità effettiva con provisioning devono essere creati con una [chiave di partizione](partition-data.md). In un determinato momento, la velocità effettiva allocata a un contenitore all'interno di un database viene distribuita in tutte le partizioni logiche del contenitore. Quando si dispone di contenitori che condividono la velocità effettiva con provisioning configurata in un database, non è possibile applicare selettivamente la velocità effettiva a un contenitore o a una partizione logica specifica. 

Se il carico di lavoro in una partizione logica utilizza un livello di velocità effettiva superiore rispetto a quello allocato a una specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione della frequenza, è possibile aumentare la velocità effettiva per l'intero database o ripetere le operazioni. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

I contenitori in un database con velocità effettiva condivisa condividono la velocità effettiva (UR/sec) allocata al database. È possibile avere fino a quattro contenitori con almeno 400 ur/sec nel database. Ogni nuovo contenitore dopo i primi quattro richiederà almeno 100 ur/sec. Se, ad esempio, si dispone di un database di velocità effettiva condivisa con otto contenitori, le UR/sec minime del database saranno 800 ur/sec.

> [!NOTE]
> Nel febbraio 2020 è stata introdotta una modifica che consente di avere un massimo di 25 contenitori in un database di velocità effettiva condivisa, che consente una migliore condivisione della velocità effettiva tra i contenitori. Dopo i primi 25 contenitori, è possibile aggiungere altri contenitori al database solo se ne viene effettuato il [provisioning con una velocità effettiva dedicata](#set-throughput-on-a-database-and-a-container), che è separata dalla velocità effettiva condivisa del database.<br>
Se l'account Azure Cosmos DB contiene già un database di velocità effettiva condivisa con >= 25 contenitori, l'account e tutti gli altri account nella stessa sottoscrizione di Azure sono esenti da questa modifica. Per commenti o domande, [contattare il supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) tecnico. 

Se i carichi di lavoro comportano l'eliminazione e la ricreazione di tutte le raccolte in un database, è consigliabile eliminare il database vuoto e ricreare un nuovo database prima della creazione della raccolta. L'immagine seguente mostra in che modo una partizione fisica può ospitare una o più partizioni logiche che appartengono a contenitori diversi all'interno di un database:

![Partizione fisica](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Configurare la velocità effettiva in un database e in un contenitore

È possibile combinare i due modelli, effettuando il provisioning della velocità effettiva sia nel database che nel contenitore. L'esempio seguente illustra come effettuare il provisioning della velocità effettiva in un database di Azure Cosmos e in un contenitore:

* È possibile creare un database di Azure Cosmos denominato *Z* con la velocità effettiva con provisioning delle UR *"K"* . 
* Successivamente, creare cinque contenitori denominati *A*, *B*, *C*, *D*ed *e all'interno* del database. Quando si crea il contenitore B, assicurarsi di abilitare il provisioning della **velocità effettiva dedicata per questa** opzione del contenitore e configurare in modo esplicito *"P"* ur della velocità effettiva con provisioning in questo contenitore. Si noti che è possibile configurare la velocità effettiva condivisa e dedicata solo quando si creano il database e il contenitore. 

   ![Impostazione della velocità effettiva a livello di contenitore](./media/set-throughput/coll-level-throughput.png)

* La velocità effettiva delle UR *"K"* è condivisa tra i quattro contenitori *A*, *C*, *D*ed *e.* La quantità *esatta di velocità effettiva disponibile per a*, *C*, *D*o *e* varia. Non sono previsti contratti di servizio per la velocità effettiva di ogni singolo contenitore.
* Al contenitore denominato *B* è garantita la velocità effettiva delle UR *"P"* per sempre. ed è supportato da contratti di servizio.

> [!NOTE]
> Un contenitore con velocità effettiva con provisioning non può essere convertito nel contenitore di database condiviso. Viceversa, un contenitore di database condiviso non può essere convertito per avere una velocità effettiva dedicata.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aggiornamento della velocità effettiva in un database o in un contenitore

Dopo aver creato un contenitore di Azure Cosmos o un database, è possibile aggiornare la velocità effettiva con provisioning. Non esiste alcun limite alla velocità effettiva massima con provisioning che è possibile configurare nel database o nel contenitore. La [velocità effettiva minima con provisioning](concepts-limits.md#storage-and-throughput) dipende dai fattori seguenti: 

* Dimensioni massime dei dati che è possibile archiviare nel contenitore
* Velocità effettiva massima di cui si esegue il provisioning nel contenitore
* Il numero corrente di contenitori di Azure Cosmos presenti in un database con velocità effettiva condivisa. 

È possibile recuperare la velocità effettiva minima di un contenitore o di un database a livello di programmazione usando gli SDK o visualizzare il valore nel portale di Azure. Quando si usa .NET SDK, il metodo [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) consente di ridimensionare il valore della velocità effettiva con provisioning. Quando si usa Java SDK, il metodo [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) consente di ridimensionare il valore della velocità effettiva con provisioning. 

Quando si usa .NET SDK, il metodo [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) consente di recuperare la velocità effettiva minima di un contenitore o di un database. 

È possibile ridimensionare la velocità effettiva con provisioning di un contenitore o di un database in qualsiasi momento. Quando viene eseguita un'operazione di ridimensionamento per aumentare la velocità effettiva, può essere necessario più tempo a causa delle attività di sistema per il provisioning delle risorse necessarie. È possibile controllare lo stato dell'operazione di ridimensionamento in portale di Azure o a livello di codice usando gli SDK. Quando si usa .NET SDK, è possibile ottenere lo stato dell'operazione di ridimensionamento usando il `DocumentClient.ReadOfferAsync` metodo.

## <a name="comparison-of-models"></a>Confronto tra modelli

|**Parametro**  |**Velocità effettiva con provisioning in un database**  |**Velocità effettiva con provisioning in un contenitore**|
|---------|---------|---------|
|UR minime |400. Dopo i primi quattro contenitori, ogni contenitore aggiuntivo richiede almeno 100 UR al secondo. |400|
|UR minime per contenitore|100|400|
|UR massime|Illimitate, nel database.|Illimitate, nel contenitore.|
|UR assegnate o disponibili per un contenitore specifico|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà. Le proprietà possono essere, a scelta, le chiavi di partizione dei contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro e il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|
|Archiviazione massima per un contenitore|Senza limiti.|Senza limiti.|
|Velocità effettiva massima per partizione logica di un contenitore|10.000 UR|10.000 UR|
|Spazio di archiviazione massimo (data + indice) per partizione logica di un contenitore|20 GB|20 GB|

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [partizioni logiche](partition-data.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)

