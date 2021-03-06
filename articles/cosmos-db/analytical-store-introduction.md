---
title: Che cos'è l'archivio analitico di Azure Cosmos DB?
description: Informazioni sull'archivio transazionale (basato su righe) e sull'archivio analitico (basato su colonne) di Azure Cosmos DB. Vantaggi dell'archivio analitico, impatto sulle prestazioni per carichi di lavoro su larga scala e sincronizzazione automatica dei dati dall'archivio transazionale all'archivio analitico
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364608"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Che cos'Azure Cosmos DB archivio analitico?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB archivio analitico è un archivio colonne completamente isolato per consentire l'analisi su larga scala dei dati operativi nel Azure Cosmos DB, senza alcun impatto sui carichi di lavoro transazionali. 

L'archivio transazionale di Azure Cosmos DB è senza schema e consente di eseguire l'iterazione sulle applicazioni transazionali senza dover gestire schemi o indici. Al contrario, l'archivio analitico di Azure Cosmos DB è schematizzato per ottimizzare le prestazioni delle query analitiche. Questo articolo descrive in dettaglio l'archiviazione analitica.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Problemi con l'analisi su larga scala dei dati operativi

I dati operativi multimodello in un contenitore Azure Cosmos DB vengono archiviati internamente in un "archivio transazionale" indicizzato basato su righe. Il formato dell'archivio righe è progettato per consentire letture e scritture transazionali rapide nei tempi di risposta dell'ordine dei millisecondi e query operative. Se le dimensioni del set di dati aumentano, le query analitiche complesse possono essere costose in termini di velocità effettiva con provisioning sui dati archiviati in questo formato. L'utilizzo elevato della velocità effettiva con provisioning, a sua volta, influisce sulle prestazioni dei carichi di lavoro transazionali usati dalle applicazioni e dai servizi in tempo reale.

Tradizionalmente, per analizzare grandi quantità di dati, i dati operativi vengono estratti dall'archivio transazionale di Azure Cosmos DB e archiviati in un livello dati separato. Ad esempio, i dati vengono archiviati in un data warehouse o data lake in un formato appropriato. Questi dati vengono successivamente usati per analisi su larga scala e analizzati con un motore di calcolo come i cluster Apache Spark. Questa separazione dei livelli di archiviazione analitica e di calcolo dai dati operativi comporta una latenza aggiuntiva, perché le pipeline ETL (Extract, Transform, Load) vengono eseguite con minore frequenza per ridurre al minimo l'impatto potenziale sui carichi di lavoro transazionali.

Anche le pipeline ETL diventano complesse quando si gestiscono gli aggiornamenti ai dati operativi rispetto alla gestione dei soli dati operativi appena inseriti. 

## <a name="column-oriented-analytical-store"></a>Archivio analitico orientato alle colonne

L'archivio analitico di Azure Cosmos DB risolve i problemi di complessità e latenza che si verificano con le pipeline ETL tradizionali. L'archivio analitico di Azure Cosmos DB può sincronizzare automaticamente i dati operativi in un archivio colonne separato. Il formato dell'archivio colonne è adatto per l'esecuzione ottimizzata di query analitiche su larga scala, con conseguente miglioramento della latenza di tali query.

Usando Azure Synapse, è ora possibile creare soluzioni HTAP senza ETL collegandosi direttamente Azure Cosmos DB archivio analitico da Azure Synapse Analytics. Consente di eseguire analisi su larga scala in near real-time sui dati operativi.

## <a name="features-of-analytical-store"></a>Funzionalità dell'archivio analitico 

Quando si abilita l'archivio analitico in un contenitore Azure Cosmos DB, viene creato internamente un nuovo archivio colonne in base ai dati operativi presenti nel contenitore. Questo archivio colonne viene mantenuto separato dall'archivio transazionale orientato alle righe di tale contenitore. Gli inserimenti, gli aggiornamenti e le eliminazioni dei dati operativi vengono sincronizzati automaticamente con l'archivio analitico. Non è necessario il feed di modifiche o ETL per sincronizzare i dati.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Archivio colonne per carichi di lavoro analitici su dati operativi

I carichi di lavoro analitici in genere comportano aggregazioni e analisi sequenziali dei campi selezionati. Archiviando i dati in ordine column-major, l'archivio analitico consente di serializzare insieme un gruppo di valori per ogni campo. Questo formato riduce le operazioni di I/O al secondo necessarie per l'analisi o il calcolo delle statistiche su campi specifici. Migliora notevolmente i tempi di risposta delle query per le analisi su set di dati di grandi dimensioni. 

Ad esempio, se le tabelle operative hanno il formato seguente:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Tabella operativa di esempio" border="false":::

L'archivio righe salva in modo permanente i dati sopra riportati in un formato serializzato, per ogni riga, sul disco. Questo formato consente letture e scritture transazionali più veloci e query operative, ad esempio "restituire informazioni su Product1". Tuttavia, man mano che le dimensioni del set di dati aumentano e se si vogliono eseguire query analitiche complesse sui dati, può essere costoso. Se, ad esempio, si vogliono ottenere "le tendenze di vendita per un prodotto nella categoria denominata 'Equipment' in diverse business unit e mesi", è necessario eseguire una query complessa. Le analisi di grandi dimensioni in questo set di dati possono risultare costose in termini di velocità effettiva con provisioning e possono anche influire sulle prestazioni dei carichi di lavoro transazionali che alimentano le applicazioni e i servizi in tempo reale.

L'archivio analitico, che è un archivio colonne, è più adatto per tali query perché serializza insieme i campi di dati simili e riduce le operazioni di I/O al secondo del disco.

Nell'immagine seguente viene illustrato l'archivio righe transazionale rispetto all'archivio colonne analitico in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Archivio righe transazionale rispetto all'archivio colonne analitico in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Prestazioni separate per carichi di lavoro analitici

Non vi è alcun impatto sulle prestazioni dei carichi di lavoro transazionali a causa di query analitiche, in quanto l'archivio analitico è separato dall'archivio transazionale.  Per l'archivio analitico non è necessario allocare unità richiesta separate (UR).

### <a name="auto-sync"></a>Sincronizzazione automatica

La sincronizzazione automatica si riferisce alla funzionalità completamente gestita di Azure Cosmos DB in cui inserimenti, aggiornamenti ed eliminazioni nei dati operativi vengono sincronizzati automaticamente dall'archivio transazionale all'archivio analitico quasi in tempo reale. La latenza di sincronizzazione automatica è in genere entro 2 minuti. Nei casi di database con velocità effettiva condivisa con un numero elevato di contenitori, la latenza di sincronizzazione automatica dei singoli contenitori potrebbe essere superiore e richiedere fino a 5 minuti. Per altre informazioni, vedere come questa latenza si adatta agli scenari. Per questo, contattare il [team Azure Cosmos DB .](mailto:cosmosdbsynapselink@microsoft.com)

La funzionalità di sincronizzazione automatica insieme all'archivio analitico offre i vantaggi principali seguenti:

### <a name="scalability--elasticity"></a>Scalabilità ed elasticità

Grazie al partizionamento orizzontale, l'archivio transazionale di Azure Cosmos DB può dimensionare in modo elastico l'archiviazione e la velocità effettiva senza tempi di inattività. Il partizionamento orizzontale nell'archivio transazionale fornisce scalabilità ed elasticità nella sincronizzazione automatica per garantire la sincronizzazione dei dati nell'archivio analitico in near real-time. La sincronizzazione dei dati viene eseguita indipendentemente dalla velocità effettiva del traffico transazionale, sia che si tratti di 1.000 operazioni al secondo che di un milione di operazioni al secondo, e non influisce sulla velocità effettiva con provisioning nell'archivio transazionale. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Gestione automatica degli aggiornamenti dello schema

L'archivio transazionale di Azure Cosmos DB è senza schema e consente di eseguire l'iterazione sulle applicazioni transazionali senza dover gestire schemi o indici. Al contrario, l'archivio analitico di Azure Cosmos DB è schematizzato per ottimizzare le prestazioni delle query analitiche. Con la funzionalità di sincronizzazione automatica, Azure Cosmos DB gestisce l'inferenza dello schema rispetto agli aggiornamenti più recenti dall'archivio transazionale.  Gestisce inoltre per impostazione predefinita la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati.

Con l'evolversi dello schema e l'aggiunta di nuove proprietà nel tempo, l'archivio analitico presenta automaticamente uno schema unionizzato in tutti gli schemi cronologici nell'archivio transazionale.

#### <a name="schema-constraints"></a>Vincoli dello schema

I vincoli seguenti sono applicabili ai dati operativi in Azure Cosmos DB quando si abilita l'archivio analitico per dedurre automaticamente e rappresentare correttamente lo schema:

* È possibile avere un massimo di 1000 proprietà a qualsiasi livello di annidamento nello schema e una profondità di annidamento massima di 127.
  * Nell'archivio analitico vengono rappresentate solo le prime 1000 proprietà.
  * Nell'archivio analitico vengono rappresentati solo i primi 127 livelli annidati.

* Anche se i documenti JSON (e Cosmos DB raccolte/contenitori) fa distinzione tra maiuscole e minuscole dal punto di vista dell'univocità, l'archivio analitico non lo è.

  * **Nello stesso documento:** I nomi delle proprietà nello stesso livello devono essere univoci se confrontati senza distinzione tra maiuscole e minuscole. Ad esempio, il documento JSON seguente ha "Name" e "name" nello stesso livello. Sebbene sia un documento JSON valido, non soddisfa il vincolo di univocità e pertanto non verrà rappresentato completamente nell'archivio analitico. In questo esempio, "Name" e "name" sono gli stessi se confrontati in modo senza distinzione tra maiuscole e minuscole. Verrà `"Name": "fred"` rappresentato solo nell'archivio analitico, perché è la prima occorrenza. E `"name": "john"` non verranno rappresentati affatto.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **In documenti diversi:** Le proprietà nello stesso livello e con lo stesso nome, ma in casi diversi, verranno rappresentate all'interno della stessa colonna, usando il formato del nome della prima occorrenza. Ad esempio, i documenti JSON seguenti hanno `"Name"` e `"name"` nello stesso livello. Poiché il primo formato del documento è , verrà usato per rappresentare il `"Name"` nome della proprietà nell'archivio analitico. In altre parole, il nome della colonna nell'archivio analitico sarà `"Name"` . Nella colonna verranno rappresentati sia che `"fred"` `"john"` `"Name"` .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Il primo documento della raccolta definisce lo schema dell'archivio analitico iniziale.
  * Le proprietà nel primo livello del documento verranno rappresentate come colonne.
  * I documenti con più proprietà rispetto allo schema iniziale genereranno nuove colonne nell'archivio analitico.
  * Le colonne non possono essere rimosse.
  * L'eliminazione di tutti i documenti in una raccolta non reimposta lo schema dell'archivio analitico.
  * Non è disponibile il controllo delle versioni dello schema. L'ultima versione dedotta dall'archivio transazionale è quella che verrà visualizzata nell'archivio analitico.

* Attualmente non è possibile leggere Azure Synapse spark che legge i nomi di colonna che contengono spazi vuoti.

* Prevedere un comportamento diverso per quanto riguarda i valori `null` espliciti:
  * I pool di Spark Azure Synapse leggeranno questi valori come `0` (zero).
  * I pool sql serverless Azure Synapse leggeranno questi valori come se il primo documento della raccolta abbia, per la stessa proprietà, un valore `NULL` con un tipo di dati `non-numeric` .
  * I pool sql serverless in Azure Synapse leggeranno questi valori come (zero) se il primo documento della raccolta ha, per la stessa proprietà, un valore con `0` un tipo di dati `numeric` .

* Prevedere un comportamento diverso per quanto riguarda le colonne mancanti:
  * I pool di Spark in Azure Synapse rappresenteranno queste colonne come `undefined` .
  * I pool sql serverless in Azure Synapse rappresenteranno queste colonne come `NULL` .

#### <a name="schema-representation"></a>Rappresentazione dello schema

Nell'archivio analitico esistono due modalità di rappresentazione dello schema. Queste modalità presentano compromessi tra la semplicità di una rappresentazione a colonne, la gestione degli schemi polimorfici e la semplicità d'uso delle query:

* Rappresentazione dello schema ben definita
* Rappresentazione completa dello schema di fedeltà

> [!NOTE]
> Per gli account API SQL (Core), quando l'archivio analitico è abilitato, la rappresentazione dello schema predefinita nell'archivio analitico è ben definita. Mentre per Azure Cosmos DB'API per gli account MongoDB, la rappresentazione dello schema predefinita nell'archivio analitico è una rappresentazione dello schema fedeltà completa. In caso di scenari che richiedono una rappresentazione dello schema diversa da quella predefinita per ognuna di queste API, contattare il team di [Azure Cosmos DB per](mailto:cosmosdbsynapselink@microsoft.com) abilitarla.

**Rappresentazione dello schema ben definita**

La rappresentazione dello schema ben definita crea una semplice rappresentazione tabulare dei dati indipendenti da schema nell'archivio transazionale. La rappresentazione dello schema ben definita presenta le considerazioni seguenti:

* Una proprietà ha sempre lo stesso tipo tra più elementi.
* È consentita solo la modifica di un tipo, da Null a qualsiasi altro tipo di dati. La prima occorrenza non Null definisce il tipo di dati della colonna.

  * Ad esempio, `{"a":123} {"a": "str"}` non ha uno schema ben definito perché `"a"` a volte è una stringa e a volte un numero. In questo caso, l'archivio analitico registra il tipo di dati di come tipo di dati di nel primo elemento che si verifica `"a"` `“a”` nella durata del contenitore. Il documento verrà comunque incluso nell'archivio analitico, ma gli elementi in cui il tipo di dati di `"a"` è diverso non lo saranno.
  
    Questa condizione non è valida per le proprietà Null. Ad esempio, `{"a":123} {"a":null}` è ancora ben definito.

* I tipi matrice devono contenere un singolo tipo ripetuto.

  * Ad esempio, `{"a": ["str",12]}` non è uno schema ben definito perché la matrice contiene una combinazione di tipi integer e stringa.

> [!NOTE]
> Se l'Azure Cosmos DB analitico segue la rappresentazione dello schema ben definita e la specifica precedente viene violata da determinati elementi, tali elementi non verranno inclusi nell'archivio analitico.

* Si prevede un comportamento diverso per quanto riguarda tipi diversi nello schema ben definito:
  * I pool spark in Azure Synapse rappresenteranno questi valori come `undefined` .
  * I pool sql serverless in Azure Synapse rappresenteranno questi valori come `NULL` .


**Rappresentazione completa dello schema di fedeltà**

La rappresentazione completa dello schema di fedeltà è progettata per gestire l'intera gamma di schemi polimorfici nei dati operativi indipendenti da schema. In questa rappresentazione dello schema non viene eliminato alcun elemento dall'archivio analitico anche se vengono violati i vincoli dello schema ben definiti ,ovvero non sono presenti campi di tipo misto né matrici di tipi di dati misti.

A tale scopo, tradurre le proprietà foglia dei dati operativi nell'archivio analitico con colonne distinte in base al tipo di dati dei valori nella proprietà . I nomi delle proprietà foglia vengono estesi con tipi di dati come suffisso nello schema dell'archivio analitico in modo che possano essere query senza ambiguità.

Si consideri ad esempio il documento di esempio seguente nell'archivio transazionale:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

La proprietà foglia `streetNo` all'interno dell'oggetto `address` annidato verrà rappresentata nello schema dell'archivio analitico come colonna `address.object.streetNo.int32` . Il tipo di dati viene aggiunto come suffisso alla colonna. In questo modo, se viene aggiunto un altro documento all'archivio transazionale in cui il valore della proprietà foglia è "123" (si noti che è una stringa), lo schema dell'archivio analitico si evolve automaticamente senza modificare il tipo di una colonna scritta in `streetNo` precedenza. Nuova colonna aggiunta all'archivio analitico come posizione in cui è archiviato il valore `address.object.streetNo.string` "123".

**Mapping tra il tipo di dati e il suffisso**

Ecco una mappa di tutti i tipi di dati delle proprietà e delle relative rappresentazioni di suffisso nell'archivio analitico:

|Tipo di dati originale  |Suffisso  |Esempio  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Array | ".array" |    ["a", "b"]|
|Binary | ".binary" |0|
|Boolean    | ".bool"   |Vero|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | Null|
|string|    ".string" | "ABC"|
|Timestamp |    ".timestamp" |  Timestamp(0, 0)|
|Datetime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Documento   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Archiviazione conveniente di dati cronologici

La suddivisione in livelli dei dati si riferisce alla separazione dei dati tra le infrastrutture di archiviazione ottimizzate per diversi scenari. Migliorando in tal modo le prestazioni complessive e la convenienza dello stack di dati end-to-end. Con l'archivio analitico, Azure Cosmos DB supporta ora la suddivisione in livelli automatica dei dati dall'archivio transazionale all'archivio analitico con layout di dati diversi. Con l'archivio analitico ottimizzato in termini di costi di archiviazione rispetto all'archivio transazionale, consente di mantenere orizzonti molto più lunghi dei dati operativi per l'analisi cronologica.

Dopo aver abilitato l'archivio analitico, in base alle esigenze di conservazione dei dati dei carichi di lavoro transazionali, è possibile configurare la proprietà 'Transactional Store Time to Live (Transactional TTL)' in modo che i record vengano eliminati automaticamente dall'archivio transazionale dopo un determinato periodo di tempo. Analogamente, la proprietà 'Analytical Store Time To Live (Analytical TTL)' consente di gestire il ciclo di vita dei dati conservati nell'archivio analitico indipendentemente dall'archivio transazionale. Abilitando l'archivio analitico e configurando le proprietà TTL, è possibile suddividere in livelli i dati e definire il periodo di conservazione dei dati per i due archivi con facilità.

### <a name="global-distribution"></a>Distribuzione globale

Se si ha un account Azure Cosmos DB distribuito a livello globale, un archivio analitico abilitato per un contenitore sarà disponibile in tutte le aree di tale account.  Tutte le modifiche apportate ai dati operativi vengono replicate a livello globale in tutte le aree. È possibile eseguire efficacemente query analitiche sulla copia locale più vicina dei dati in Azure Cosmos DB.

### <a name="security"></a>Sicurezza

L'autenticazione con l'archivio analitico è identica a quella dell'archivio transazionale per un determinato database. È possibile usare chiavi primarie o di sola lettura per l'autenticazione. È possibile sfruttare il servizio collegato in Synapse Studio per evitare di incollare le chiavi di Azure Cosmos DB nei notebook di Spark. L'accesso a questo servizio collegato è disponibili per chiunque abbia accesso all'area di lavoro.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Supporto di più runtime di Azure Synapse Analytics

L'archivio analitico è ottimizzato per offrire scalabilità, elasticità e prestazioni per carichi di lavoro analitici senza alcuna dipendenza dai runtime di calcolo. La tecnologia di archiviazione è gestita automaticamente per ottimizzare i carichi di lavoro analitici senza interventi manuali.

Separando il sistema di archiviazione analitico dal sistema di calcolo analitico, è possibile eseguire simultaneamente query sui dati nell'archivio analitico di Azure Cosmos DB da diversi runtime di analisi supportati da Azure Synapse Analytics. A partire da oggi, Azure Synapse Analytics il pool SQL Apache Spark serverless con un Azure Cosmos DB analitico.

> [!NOTE]
> È possibile leggere solo dall'archivio analitico usando Azure Synapse Analytics di esecuzione. È possibile scrivere di nuovo i dati nell'archivio transazionale come livello di gestione.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Prezzi

L'archivio analitico segue un modello di prezzi a consumo in base al quale viene addebitato il costo:

* Archiviazione: il volume dei dati conservati nell'archivio analitico ogni mese, inclusi i dati cronologici definiti dalla proprietà Analytical TTL.

* Operazioni di scrittura analitica: sincronizzazione completamente gestita degli aggiornamenti dei dati operativi nell'archivio analitico dall'archivio transazionale (sincronizzazione automatica)

* Operazioni di lettura analitica: operazioni di lettura eseguite sull'archivio analitico Azure Synapse Analytics pool Spark e tempi di esecuzione del pool SQL serverless.

Il prezzo dell'archivio analitico è separato dal modello di prezzi dell'archivio transazioni. Non esiste alcun concetto di UR con provisioning nell'archivio analitico. Per informazioni dettagliate sul modello di prezzi per l'archivio analitico, vedere la [pagina dei prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Per ottenere una stima dei costi di alto livello per abilitare l'archivio analitico in un contenitore di Azure Cosmos DB, è possibile usare lo [strumento di pianificazione della capacità di Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) e ottenere una stima dei costi di archiviazione analitica e delle operazioni di scrittura. I costi per le operazioni di lettura analitica dipendono dalle caratteristiche del carico di lavoro di analisi, ma come stima di alto livello, l'analisi di 1 TB di dati nell'archivio analitico comporta in genere 130.000 operazioni di lettura analitica con un costo pari a $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Durata (TTL) dei dati analitici

La durata (TTL) dei dati analitici indica per quanto tempo i dati devono essere conservati nell'archivio analitico, per un contenitore. 

Se l'archivio analitico è abilitato, gli inserimenti, gli aggiornamenti e le eliminazioni ai dati operativi vengono sincronizzati automaticamente dall'archivio transazionale all'archivio analitico, indipendentemente dalla configurazione TTL transazionale. La conservazione di questi dati operativi nell'archivio analitico può essere controllata dal valore della durata (TTL) dei dati analitici a livello di contenitore, come specificato di seguito:

Il valore della durata (TTL) dei dati analitici in un contenitore viene impostato usando la proprietà `AnalyticalStoreTimeToLiveInSeconds`:

* Se il valore è impostato su "0", manca (o è impostato su Null): l'archivio analitico è disabilitato e i dati non vengono replicati dall'archivio transazionale all'archivio analitico

* Se presente e il valore è impostato su "-1": l'archivio analitico conserva tutti i dati cronologici, indipendentemente dalla conservazione dei dati nell'archivio transazionale. Questa impostazione indica che l'archivio analitico ha una conservazione infinita dei dati operativi

* Se presente e il valore è impostato su un numero positivo "n": gli elementi scadranno dall'archivio analitico "n" secondi dopo l'ora dell'ultima modifica nell'archivio transazionale. Questa impostazione può essere sfruttata se si vogliono conservare i dati operativi per un periodo di tempo limitato nell'archivio analitico, indipendentemente dalla conservazione dei dati nell'archivio transazionale

Alcune informazioni da considerare:

*   Dopo aver abilitato l'archivio analitico con un valore della durata (TTL) dei dati analitici, è possibile aggiornarlo con un altro valore valido in un secondo momento. 
*   Mentre la durata (TTL) dei dati transazionali può essere impostata a livello di contenitore o di elemento, la durata (TTL) dei dati analitici può essere impostata solo a livello di contenitore.
*   È possibile ottenere un periodo di conservazione più lungo dei dati operativi nell'archivio analitico impostando la proprietà TTL dei dati analitici su un valore maggiore o uguale alla proprietà TTL dei dati transazionali a livello di contenitore.
*   L'archivio analitico può essere creato per eseguire il mirroring dell'archivio transazionale impostando TTL analitico = TTL transazionale.

Quando si abilita l'archivio analitico in un contenitore:

* Dal portale di Azure, l'opzione TTL analitica è impostata sul valore predefinito -1. È possibile modificare questo valore in "n" secondi passando alle impostazioni del contenitore in Esplora dati. 
 
* Da Azure SDK o PowerShell o dall'interfaccia della riga di comando, l'opzione TTL analitica può essere abilitata impostandola su -1 o 'n'. 

Per altre informazioni, vedere [come configurare la durata (TTL) dei dati analitici in un contenitore](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link.md)

* [Introduzione a Collegamento ad Azure Synapse per Azure Cosmos DB](configure-synapse-link.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casi d'uso di Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md)
