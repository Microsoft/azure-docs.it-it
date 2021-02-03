---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni su come configurare e modificare i criteri di indicizzazione predefiniti per l'indicizzazione automatica e prestazioni maggiori in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 58ee3bcd0ba14359ea9adaa131b8280b81008b57
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526756"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexing policies in Azure Cosmos DB (Criteri di indicizzazione in Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB, ogni contenitore dispone di un criterio di indicizzazione che determina come devono essere indicizzati gli elementi del contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati indicizzano ogni proprietà di ogni elemento e applicano indici di intervallo per qualsiasi stringa o numero. Ciò consente di ottenere prestazioni ottimali delle query senza dover pensare all'indicizzazione e alla gestione degli indici in anticipo.

In alcune situazioni potrebbe essere necessario eseguire l'override di questo comportamento automatico per soddisfare al meglio le proprie esigenze. È possibile personalizzare i criteri di indicizzazione di un contenitore impostando la *modalità di indicizzazione* e includendo o escludendo i *percorsi delle proprietà*.

> [!NOTE]
> Il metodo di aggiornamento dei criteri di indicizzazione descritti in questo articolo si applica solo all'API SQL (Core) di Azure Cosmos DB. Informazioni sull'indicizzazione nell' [API Azure Cosmos DB per MongoDB](mongodb-indexing.md)

## <a name="indexing-mode"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione:

- **Coerente**: l'indice viene aggiornato in modo sincrono durante la creazione, l'aggiornamento o l'eliminazione di elementi. Ciò significa che la coerenza delle query di lettura sarà la [coerenza configurata per l'account](consistency-levels.md).
- **None**: l'indicizzazione è disabilitata nel contenitore. Questa operazione viene in genere usata quando un contenitore viene usato come archivio chiave-valore puro senza la necessità di indici secondari. Può anche essere usato per migliorare le prestazioni delle operazioni bulk. Una volta completate le operazioni bulk, è possibile impostare la modalità di indicizzazione su coerente e quindi monitorarla utilizzando [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) fino al completamento.

> [!NOTE]
> Azure Cosmos DB supporta anche una modalità di indicizzazione differita. L'indicizzazione differita esegue gli aggiornamenti dell'indice con un livello di priorità molto più basso quando il motore non esegue altre operazioni. Ciò può comportare risultati delle query **incoerenti o incompleti**. Se si prevede di eseguire una query su un contenitore Cosmos, non è consigliabile selezionare l'indicizzazione differita. I nuovi contenitori non possono selezionare l'indicizzazione differita. È possibile richiedere un'esenzione contattando il [supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (eccetto se si usa un account Azure Cosmos in modalità senza [Server](serverless.md) che non supporta l'indicizzazione differita).

Per impostazione predefinita, i criteri di indicizzazione vengono impostati su `automatic` . Per ottenere questo risultato, impostare la `automatic` proprietà nei criteri di indicizzazione su `true` . L'impostazione di questa proprietà su `true` consente ad Azure CosmosDB di indicizzare automaticamente i documenti man mano che vengono scritti.

## <a name="index-size"></a><a id="index-size"></a>Dimensioni indice

In Azure Cosmos DB lo spazio di archiviazione totale usato risulta dalla combinazione delle dimensioni dei dati e delle dimensioni dell'indice. Di seguito sono riportate alcune funzionalità delle dimensioni degli indici:

* La dimensione dell'indice dipende dai criteri di indicizzazione. Se tutte le proprietà sono indicizzate, le dimensioni dell'indice possono essere maggiori delle dimensioni dei dati.
* Quando si eliminano i dati, gli indici vengono compattati in modo quasi continuo. Tuttavia, per le eliminazioni di dati di piccole dimensioni, è possibile che non si osservi immediatamente una riduzione delle dimensioni degli indici.
* È possibile che le dimensioni degli indici crescano temporaneamente quando si suddividono partizioni fisiche. Lo spazio di indice viene rilasciato dopo il completamento della divisione della partizione.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Inclusione ed esclusione dei percorsi delle proprietà

Un criterio di indicizzazione personalizzato può specificare percorsi di proprietà inclusi o esclusi in modo esplicito dall'indicizzazione. Ottimizzando il numero di percorsi indicizzati, è possibile ridurre sostanzialmente la latenza e l'addebito delle unità richiesta per le operazioni di scrittura. Questi percorsi vengono definiti seguendo [il metodo descritto nella sezione Panoramica dell'indicizzazione](index-overview.md#from-trees-to-property-paths) con le aggiunte seguenti:

- un percorso che conduce a un valore scalare (stringa o numero) termina con `/?`
- gli elementi di una matrice vengono risolti insieme tramite la `/[]` notazione `/0` , anziché e `/1` così via.
- il `/*` carattere jolly può essere usato per trovare la corrispondenza con qualsiasi elemento al di sotto del nodo

Riprendendo lo stesso esempio:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- il `headquarters` `employees` percorso di è `/headquarters/employees/?`

- il `locations` `country` percorso è `/locations/[]/country/?`

- il percorso di qualsiasi elemento in `headquarters` è `/headquarters/*`

Ad esempio, è possibile includere il `/headquarters/employees/?` percorso. Questo percorso garantisce che la proprietà Employees venga indicizzata, ma non verrà indicizzato un altro JSON annidato all'interno di questa proprietà.

## <a name="includeexclude-strategy"></a>Includi/Escludi strategia

Tutti i criteri di indicizzazione devono includere il percorso radice `/*` come un percorso incluso o escluso.

- Includere il percorso radice per escludere in modo selettivo i percorsi che non devono essere indicizzati. Si tratta dell'approccio consigliato, in quanto consente Azure Cosmos DB indicizzare in modo proattivo tutte le nuove proprietà che possono essere aggiunte al modello.
- Escludere il percorso radice per includere in modo selettivo i percorsi che devono essere indicizzati.

- Per i percorsi con caratteri regolari che includono caratteri alfanumerici e _ (carattere di sottolineatura), non è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/Path/?"). Per i percorsi con altri caratteri speciali, è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/ \" path-ABC \" /?"). Se si prevede che nel percorso siano presenti caratteri speciali, è possibile evitare ogni percorso per la sicurezza. Dal punto di vista funzionale, non fa alcuna differenza se si Escape ogni percorso rispetto a quelli che contengono caratteri speciali.

- Per `_etag` impostazione predefinita, la proprietà di sistema viene esclusa dall'indicizzazione, a meno che l'eTag non venga aggiunto al percorso incluso per l'indicizzazione.

- Se la modalità di indicizzazione è impostata su **coerente**, le proprietà di sistema `id` e `_ts` vengono indicizzate automaticamente.

Quando si includono ed escludono i percorsi, è possibile che si verifichino gli attributi seguenti:

- `kind` può essere `range` o `hash` . Il supporto per gli indici hash è limitato ai filtri di uguaglianza. La funzionalità degli indici di intervallo fornisce tutte le funzionalità degli indici hash, nonché l'ordinamento efficiente, i filtri di intervallo, le funzioni di sistema. È sempre consigliabile usare un indice di intervallo.

- `precision` numero definito a livello di indice per i percorsi inclusi. Il valore `-1` indica la precisione massima. È consigliabile impostare sempre questo valore su `-1` .

- `dataType` può essere `String` o `Number` . Indica i tipi di proprietà JSON che saranno indicizzate.

Quando non è specificato, queste proprietà avranno i valori predefiniti seguenti:

| **Nome della proprietà**     | **Valore predefinito** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Per esempi di criteri di indicizzazione per l'inclusione e l'esclusione di percorsi, vedere [questa sezione](how-to-manage-indexing-policy.md#indexing-policy-examples) .

## <a name="includeexclude-precedence"></a>Includi/Escludi precedenza

Se i percorsi inclusi e i percorsi esclusi presentano un conflitto, il percorso più preciso avrà la precedenza.

Ecco un esempio:

**Percorso incluso**: `/food/ingredients/nutrition/*`

**Percorso escluso**: `/food/ingredients/*`

In questo caso, il percorso incluso avrà la precedenza sul percorso escluso perché è più preciso. In base a questi percorsi, tutti i dati nel `food/ingredients` percorso o annidati all'interno di verrebbero esclusi dall'indice. L'eccezione è costituita dai dati all'interno del percorso incluso: `/food/ingredients/nutrition/*` , che verrebbe indicizzato.

Di seguito sono riportate alcune regole per la precedenza dei percorsi inclusi ed esclusi nei Azure Cosmos DB:

- I percorsi più profondi sono più precisi dei percorsi più ristretti. ad esempio: `/a/b/?` è più preciso di `/a/?` .

- `/?`È più preciso di `/*` . Ad esempio `/a/?` , è più preciso di quanto abbia la `/a/*` `/a/?` precedenza.

- Il percorso `/*` deve essere un percorso incluso o un percorso escluso.

## <a name="spatial-indexes"></a>Indici spaziali

Quando si definisce un percorso spaziale nei criteri di indicizzazione, è necessario definire quale indice ```type``` applicare a tale percorso. I tipi possibili per gli indici spaziali includono:

* Point

* Polygon

* MultiPolygon

* LineString

Per impostazione predefinita, Azure Cosmos DB non creerà indici spaziali. Se si desidera utilizzare le funzioni predefinite di SQL spaziali, è necessario creare un indice spaziale sulle proprietà obbligatorie. Vedere [questa sezione](sql-query-geospatial-index.md) per esempi di criteri di indicizzazione per l'aggiunta di indici spaziali.

## <a name="composite-indexes"></a>Indici composti

`ORDER BY`Per le query con una clausola con due o più proprietà è necessario un indice composto. È anche possibile definire un indice composito per migliorare le prestazioni di molte query di uguaglianza e di intervallo. Per impostazione predefinita, non sono definiti indici compositi, pertanto è necessario [aggiungere indici compositi](how-to-manage-indexing-policy.md#composite-index) in base alle esigenze.

A differenza dei percorsi inclusi o esclusi, non è possibile creare un percorso con il `/*` carattere jolly. Ogni percorso composito ha un implicito `/?` alla fine del percorso che non è necessario specificare. I percorsi compositi portano a un valore scalare ed è l'unico valore incluso nell'indice composto.

Quando si definisce un indice composito, è necessario specificare:

- Due o più percorsi di proprietà. Sequenza in cui vengono definiti i percorsi delle proprietà.

- Ordine (crescente o decrescente).

> [!NOTE]
> Quando si aggiunge un indice composito, la query utilizzerà gli indici di intervallo esistenti fino al completamento della nuova aggiunta dell'indice composito. Pertanto, quando si aggiunge un indice composito, è possibile che non si osservi immediatamente il miglioramento delle prestazioni. È possibile tenere traccia dello stato di avanzamento della trasformazione dell'indice [usando uno degli SDK](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Ordina per query su più proprietà:

Quando si usano indici compositi per le query con una `ORDER BY` clausola con due o più proprietà, vengono usate le considerazioni seguenti:

- Se i percorsi di indice composito non corrispondono alla sequenza delle proprietà nella `ORDER BY` clausola, l'indice composito non può supportare la query.

- L'ordine dei percorsi di indice composito (crescente o decrescente) deve corrispondere anche `order` a nella `ORDER BY` clausola.

- L'indice composito supporta anche una `ORDER BY` clausola con l'ordine opposto su tutti i percorsi.

Si consideri l'esempio seguente in cui viene definito un indice composito per le proprietà Name, Age e _ts:

| **Indice composto**     | **Query di esempio `ORDER BY`**      | **È supportata da un indice composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

È necessario personalizzare i criteri di indicizzazione per poter soddisfare tutte le `ORDER BY` query necessarie.

### <a name="queries-with-filters-on-multiple-properties"></a>Query con filtri su più proprietà

Se una query dispone di filtri su due o più proprietà, potrebbe essere utile creare un indice composto per queste proprietà.

Si consideri, ad esempio, la query seguente con un filtro di uguaglianza e di intervallo:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

Questa query sarà più efficiente, richiedendo meno tempo e consumando meno ur, se è in grado di utilizzare un indice composito in `(name ASC, age ASC)` .

È possibile ottimizzare le query con più filtri di intervallo anche con un indice composto. Ogni singolo indice composito, tuttavia, può ottimizzare solo un filtro di intervallo singolo. I filtri di intervallo includono `>` ,, `<` `<=` , `>=` e `!=` . Il filtro di intervallo deve essere definito per ultimo nell'indice composto.

Si consideri la query seguente con un filtro di uguaglianza e due filtri di intervallo:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

Questa query sarà più efficiente con un indice composito in `(name ASC, age ASC)` e `(name ASC, _ts ASC)` . Tuttavia, la query non utilizzerebbe un indice composto in `(age ASC, name ASC)` perché le proprietà con i filtri di uguaglianza devono essere definite per prime nell'indice composto. Sono necessari due indici compositi separati anziché un singolo indice composto in `(name ASC, age ASC, _ts ASC)` , in quanto ogni indice composito può ottimizzare solo un filtro di intervallo singolo.

Quando si creano indici compositi per le query con filtri su più proprietà, vengono usate le considerazioni seguenti.

- Le espressioni di filtro possono utilizzare più indici compositi.
- Le proprietà nel filtro della query devono corrispondere a quelle nell'indice composto. Se una proprietà è nell'indice composto ma non è inclusa nella query come filtro, la query non utilizzerà l'indice composto.
- Se una query include proprietà aggiuntive nel filtro che non sono state definite in un indice composito, per valutare la query verrà utilizzata una combinazione di indici composti e di intervallo. Questa operazione richiederà meno ur rispetto all'uso esclusivo degli indici di intervallo.
- Se una proprietà ha un filtro di intervallo ( `>` , `<` , `<=` , `>=` o `!=` ), questa proprietà deve essere definita per ultima nell'indice composto. Se una query contiene più di un filtro di intervallo, può trarre vantaggio da più indici compositi.
- Quando si crea un indice composto per ottimizzare le query con più filtri, l'oggetto `ORDER` dell'indice composto non avrà alcun effetto sui risultati. Questa proprietà è facoltativa.

Si considerino gli esempi seguenti in cui viene definito un indice composito per le proprietà Name, Age e timestamp:

| **Indice composto**     | **Query di esempio**      | **È supportata da un indice composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>Query con filtro e ORDER BY

Se una query Filtra in base a una o più proprietà e dispone di proprietà diverse nella clausola ORDER BY, può essere utile aggiungere le proprietà del filtro alla `ORDER BY` clausola.

Se ad esempio si aggiungono le proprietà nel filtro alla `ORDER BY` clausola, è possibile riscrivere la query seguente per sfruttare un indice composto:

Query con l'indice di intervallo:

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

Query con indice composito:

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

Le stesse ottimizzazioni di query possono essere generalizzate per qualsiasi `ORDER BY` query con filtri, tenendo presente che i singoli indici compositi possono supportare al massimo un filtro di intervallo.

Query con l'indice di intervallo:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

Query con indice composito:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

Inoltre, è possibile utilizzare gli indici compositi per ottimizzare le query con le funzioni di sistema e ORDER BY:

Query con l'indice di intervallo:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

Query con indice composito:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

Quando si creano indici compositi per ottimizzare una query con un filtro e una clausola, vengono utilizzate le considerazioni seguenti `ORDER BY` :

* Se non si definisce un indice composto in una query con un filtro su una proprietà e una clausola separata con `ORDER BY` una proprietà diversa, la query avrà comunque esito positivo. Tuttavia, il costo ur della query può essere ridotto con un indice composito, in particolare se la proprietà nella `ORDER BY` clausola ha una cardinalità elevata.
* Se la query Filtra le proprietà, è necessario includerle prima nella `ORDER BY` clausola.
* Se la query Filtra su più proprietà, i filtri di uguaglianza devono essere le prime proprietà nella `ORDER BY` clausola.
* Se la query Filtra in base a più proprietà, è possibile disporre di un massimo di un filtro di intervallo o di una funzione di sistema utilizzata per ogni indice composto. La proprietà utilizzata nel filtro di intervallo o nella funzione di sistema deve essere definita per ultima nell'indice composto.
* Sono comunque valide tutte le considerazioni per la creazione di indici compositi per le `ORDER BY` query con più proprietà e per le query con filtri su più proprietà.


| **Indice composto**                      | **Query di esempio `ORDER BY`**                                  | **È supportata da un indice composto?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="index-transformationmodifying-the-indexing-policy"></a><index-Transformation>la modifica dei criteri di indicizzazione

I criteri di indicizzazione di un contenitore possono essere aggiornati in qualsiasi momento [usando il portale di Azure o uno degli SDK supportati](how-to-manage-indexing-policy.md). Un aggiornamento ai criteri di indicizzazione attiva una trasformazione dall'indice precedente a quello nuovo, che viene eseguito online e sul posto (pertanto non viene utilizzato alcuno spazio di archiviazione aggiuntivo durante l'operazione). Il criterio di indicizzazione precedente viene trasformato in modo efficiente nei nuovi criteri senza influire sulla disponibilità di scrittura, sulla disponibilità in lettura o sulla velocità effettiva di cui è stato effettuato il provisioning nel contenitore. La trasformazione dell'indice è un'operazione asincrona e il tempo necessario per il completamento dipende dalla velocità effettiva con provisioning, dal numero di elementi e dalle relative dimensioni.

> [!IMPORTANT]
> La trasformazione dell'indice è un'operazione che utilizza le [unità richiesta](request-units.md). Le unità di richiesta utilizzate da una trasformazione dell'indice non sono attualmente fatturate se si utilizzano contenitori senza [Server](serverless.md) . Queste unità di richiesta verranno fatturate una volta che il server diventa disponibile a livello generale.

> [!NOTE]
> È possibile tenere traccia dello stato di avanzamento della trasformazione dell'indice [usando uno degli SDK](how-to-manage-indexing-policy.md).

Non vi è alcun effetto sulla disponibilità di scrittura durante le trasformazioni degli indici. La trasformazione dell'indice usa le UR di cui è stato effettuato il provisioning ma con una priorità inferiore rispetto alle operazioni CRUD o alle query.

Non vi è alcun effetto sulla disponibilità di lettura quando si aggiunge un nuovo indice. Quando la trasformazione dell'indice viene completata, le query utilizzeranno solo nuovi indici. Durante la trasformazione dell'indice, il motore di query continuerà a utilizzare gli indici esistenti, pertanto si osserveranno le prestazioni di lettura analoghe durante la trasformazione di indicizzazione rispetto a quanto osservato prima di avviare la modifica dell'indicizzazione. Quando si aggiungono nuovi indici, non esiste alcun rischio di risultati di query incompleti o incoerenti.

Quando si rimuovono gli indici e si eseguono immediatamente query che filtrano gli indici eliminati, non esiste una garanzia di risultati della query coerenti o completi. Se si rimuovono più indici e si esegue questa operazione in un'unica modifica dei criteri di indicizzazione, il motore di query fornisce risultati coerenti e completi durante la trasformazione dell'indice. Tuttavia, se si rimuovono gli indici tramite più modifiche ai criteri di indicizzazione, il motore di query non fornirà risultati coerenti o completi fino al completamento di tutte le trasformazioni degli indici. La maggior parte degli sviluppatori non elimina gli indici e quindi tenta immediatamente di eseguire query che utilizzano questi indici, pertanto, in pratica, questa situazione è improbabile.

> [!NOTE]
> Laddove possibile, provare sempre a raggruppare più modifiche di indicizzazione in un'unica modifica dei criteri di indicizzazione

## <a name="indexing-policies-and-ttl"></a>Criteri di indicizzazione e TTL

L'uso della [funzionalità di durata (TTL)](time-to-live.md) richiede l'indicizzazione. Ciò significa che:

- non è possibile attivare la durata (TTL) in un contenitore in cui la modalità di indicizzazione è impostata su `none` ,
- non è possibile impostare la modalità di indicizzazione su None in un contenitore in cui è attivato TTL.

Per gli scenari in cui non è necessario indicizzare alcun percorso di proprietà, ma è necessario un valore TTL, è possibile usare un criterio di indicizzazione con una modalità di indicizzazione impostata su `consistent` , nessun percorso incluso e `/*` come unico percorso escluso.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica sull'indicizzazione](index-overview.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
