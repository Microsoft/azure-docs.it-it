---
title: Prestazioni di Phoenix in Azure HDInsight
description: Procedure consigliate per ottimizzare le prestazioni Apache Phoenix per i cluster HDInsight di Azure
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8d1dff01c9e7b5232cfac0cf5581c077e67f6937
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079497"
---
# <a name="apache-phoenix-performance-best-practices"></a>Procedure consigliate per le prestazioni di Apache Phoenix

L'aspetto più importante delle prestazioni di [Apache Phoenix](https://phoenix.apache.org/) consiste nell'ottimizzazione dell'istanza di [Apache HBase](https://hbase.apache.org/) sottostante. Phoenix crea un modello di dati relazionale basato su HBase che converte le query SQL in operazioni HBase, ad esempio analisi. La struttura dello schema di tabella, la selezione e l'ordine dei campi della chiave primaria e l'uso degli indici sono tutti aspetti che influenzano le prestazioni di Phoenix.

## <a name="table-schema-design"></a>Struttura dello schema di tabella

Quando si crea una tabella in Phoenix, tale tabella viene archiviata in una tabella HBase. La tabella HBase contiene gruppi di colonne (famiglie di colonne) a cui viene eseguito l'accesso contemporaneamente. Una riga nella tabella Phoenix è una riga nella tabella HBase, dove ogni riga è costituita da celle con controllo delle versioni associate a una o più colonne. Dal punto di vista logico, una singola riga di HBase è una raccolta di coppie chiave-valore, ognuna con lo stesso valore rowkey. Ciò significa che ogni coppia chiave-valore ha un attributo rowkey e il valore di tale attributo rowkey è lo stesso per una riga specifica.

La struttura dello schema di una tabella Phoenix include la struttura della chiave primaria, la struttura della famiglia di colonne, la struttura delle singole colonne e la modalità di partizionamento dei dati.

### <a name="primary-key-design"></a>Struttura della chiave primaria

La chiave primaria definita in una tabella Phoenix determina come sono archiviati i dati nell'oggetto rowkey della tabella HBase sottostante. In HBase l'unico modo per accedere a una particolare riga è tramite rowkey. I dati archiviati in una tabella HBase sono inoltre ordinati in base al valore rowkey. Phoenix compila il valore RowKey concatenando i valori di ogni colonna nella riga, nell'ordine in cui sono definiti nella chiave primaria.

Una tabella di contatti, ad esempio, ha nome, cognome, numero di telefono e indirizzo nella stessa famiglia di colonne. È possibile definire una chiave primaria in base a un numero di sequenza crescente:

|rowkey|       address|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Luca|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Tuttavia, se si eseguono spesso query in base al valore di lastName, questa chiave primaria potrebbe non essere ottimale, perché ogni query richiede un'analisi completa della tabella per leggere il valore di ogni oggetto lastName. In alternativa, è possibile definire una chiave primaria in base alle colonne lastName, firstName e socialSecurityNum. L'ultima colonna permette di evitare ambiguità tra due persone che risiedono allo stesso indirizzo e hanno lo stesso nome, ad esempio padre e figlio.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Luca|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Con questa nuova chiave primaria, i valori rowkey generati da Phoenix sono:

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    Luca|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Nella prima riga sopra, i dati per rowkey sono rappresentati come illustrato:

|rowkey|       Key|   Valore|
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |Luca|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

L'oggetto rowkey archivia ora una copia duplicata dei dati. Prendere in considerazione le dimensioni e il numero di colonne incluse nella chiave primaria, perché questo valore è incluso in ogni cella nella tabella HBase sottostante.

Se, inoltre, la chiave primaria contiene valori a incremento progressivo costante, creare la tabella con *bucket di tipo salt* per evitare la creazione di aree sensibili di scrittura. Vedere [Partizionare i dati](#partition-data).

### <a name="column-family-design"></a>Struttura della famiglia di colonne

Se ad alcune colonne si accede più frequentemente rispetto ad altre, è consigliabile creare più famiglie di colonne per separare le colonne a cui si accede di frequente da quelle a cui si accede raramente.

Se inoltre ad alcune colonne si accede in genere contemporaneamente, inserire tali colonne nella stessa famiglia di colonne.

### <a name="column-design"></a>Struttura delle colonne

* Mantieni le colonne VARCHAR in circa 1 MB a causa dei costi di I/O delle colonne di grandi dimensioni. Quando elabora le query, HBase materializza completamente le celle prima di inviarle al client e il client le riceve completamente prima di passarle al codice dell'applicazione.
* Archiviare i valori di colonna usando un formato compatto, ad esempio protobuf, Avro, msgpack o BSON. JSON non è consigliato, perché è più grande.
* Prendere in considerazione la compressione dei dati prima dell'archiviazione, per ridurre la latenza e i costi di I/O.

### <a name="partition-data"></a>Dati di partizione

Phoenix consente di controllare il numero di aree in cui i dati sono distribuiti, per poter migliorare notevolmente le prestazioni di lettura/scrittura. Quando si crea una tabella Phoenix, è possibile usare il salting o dividere preventivamente i dati.

Per usare il salting per una tabella durante la creazione, specificare il numero di bucket di tipo salt:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Il salting divide la tabella lungo i valori delle chiavi primarie, scegliendo automaticamente i valori. 

Per controllare la posizione in cui la tabella viene divisa, è possibile dividere preventivamente la tabella fornendo i valori di intervallo lungo cui si verifica la divisione. Per creare, ad esempio, una divisione della tabella lungo tre aree:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Struttura degli indici

Un indice Phoenix è una tabella HBase che archivia una copia di alcuni o di tutti i dati della tabella indicizzata. Un indice migliora le prestazioni per tipi di query specifici.

Quando sono definiti più indici e si esegue una query su una tabella, Phoenix seleziona automaticamente l'indice migliore per la query. L'indice primario viene creato automaticamente in base alle chiavi primarie selezionate.

Per le query previste, è anche possibile creare indici secondari specificando le relative colonne.

Quando si progettano gli indici:

* Creare solo gli indici necessari.
* Limitare il numero di indici nelle tabelle aggiornate di frequente. Gli aggiornamenti a una tabella comportano operazioni di scrittura sia nella tabella principale che nelle tabelle di indice.

## <a name="create-secondary-indexes"></a>Creare indici secondari

Gli indici secondari possono migliorare le prestazioni di lettura trasformando quella che sarebbe un'analisi completa della tabella in una ricerca di punti, a discapito dello spazio di archiviazione e della velocità di scrittura. Gli indici secondari possono essere aggiunti o rimossi dopo la creazione della tabella e non richiedono modifiche alle query esistenti. Le query vengono semplicemente eseguite più velocemente. A seconda delle esigenze, è consigliabile creare indici di copertura, indici funzionali o entrambi.

### <a name="use-covered-indexes"></a>Usare gli indici di copertura

Gli indici di copertura sono indici che includono i dati della riga oltre ai valori indicizzati. Dopo aver individuato la voce di indice desiderata, non è necessario accedere alla tabella primaria.

Nella tabella di contatti di esempio, è possibile creare ad esempio un indice secondario solo per la colonna socialSecurityNum. Questo indice secondario consente di eseguire più velocemente le query che richiedono un filtro in base ai valori di socialSecurityNum, ma per recuperare altri valori dei campi sarà necessaria un'altra operazione di lettura nella tabella principale.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    Luca|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Se, tuttavia, in genere si vuole cercare firstName e lastName in base a socialSecurityNum, è possibile creare un indice di copertura che include firstName e lastName come dati effettivi nella tabella dell'indice:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Questo indice di copertura consente alla query seguente di acquisire tutti i dati semplicemente leggendo la tabella contenente l'indice secondario:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Usare gli indici funzionali

Gli indici funzionali consentono di creare un indice in base a un'espressione arbitraria che si prevede verrà usata nelle query. Una volta creato un indice funzionale, se una query usa tale espressione, è possibile usare l'indice per recuperare i risultati al posto della tabella dati.

È ad esempio possibile creare un indice per consentire di eseguire ricerche senza distinzione tra maiuscole e minuscole sul valore combinato di nome e cognome di una persona:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Struttura delle query

Le principali considerazioni relative alla struttura delle query sono le seguenti:

* Comprendere il piano di query e verificarne il comportamento previsto.
* Creare un join in modo efficiente.

### <a name="understand-the-query-plan"></a>Comprendere il piano di query

In [SQLLine](http://sqlline.sourceforge.net/) usare il comando EXPLAIN seguito da una query SQL per visualizzare il piano delle operazioni che verranno eseguite da Phoenix. Verificare che il piano:

* Usi la chiave primaria quando appropriato.
* Usi gli indici secondari appropriati al posto della tabella dati.
* Usi RANGE SCAN o SKIP SCAN quando possibile al posto di TABLE SCAN.

#### <a name="plan-examples"></a>Esempi di piano

Si supponga, ad esempio, di avere una tabella denominata FLIGHTS che archivia le informazioni sui ritardi dei voli.

Per selezionare tutti i voli con un airlineid di `19805` , dove airlineid è un campo che non si trova nella chiave primaria o in un indice:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Eseguire il comando explain come illustrato di seguito:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

Il piano di query è simile al seguente:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

In questo piano si noti l'espressione FULL SCAN OVER FLIGHTS. Questa espressione indica l'esecuzione di una scansione di tabella su tutte le righe nella tabella, invece di usare la più efficiente opzione RANGE SCAN o SKIP SCAN.

Si supponga ora di voler eseguire una query per i voli del 2 gennaio 2014 per il vettore (carrier) `AA` con flightnum maggiore di 1. Si supponga che nella tabella di esempio siano presenti le colonne year, month, dayofmonth, carrier e flightnum, che fanno tutte parte della chiave primaria composta. La query sarà simile alla seguente:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Si esamini il piano per la query:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Il piano risultante è:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

I valori tra parentesi quadre rappresentano l'intervallo di valori per le chiavi primarie. In questo caso, i valori dell'intervallo sono fissi con year 2014, month 1 e dayofmonth 2, mentre i valori per flightnum vanno da 2 in su (`*`). Questo piano di query conferma che viene usata la chiave primaria, come previsto.

Creare quindi un indice nella tabella FLIGHTS denominato `carrier2_idx` solo sul campo carrier. Questo indice include anche flightdate, tailnum, origin e flightnum come colonne di copertura i cui dati vengono archiviati anch'essi nell'indice.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Si supponga di voler ottenere i valori relativi a carrier, flightdate e tailnum, come nella query seguente:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

L'indice in uso dovrebbe essere il seguente:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Per un elenco completo degli elementi che possono essere presenti nei risultati di un piano Explain, vedere la sezione relativa ai piani Explain nella [guida all'ottimizzazione di Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Creare un join in modo efficiente

In genere è preferibile evitare i join, a meno che un lato non sia piccolo, in particolare per le query frequenti.

Se necessario, è possibile creare join di grandi dimensioni con l'hint `/*+ USE_SORT_MERGE_JOIN */`, ma un join di grandi dimensioni è un'operazione dispendiosa su grandi quantità di righe. Se le dimensioni complessive di tutte le tabelle sul lato destro superano la memoria disponibile, usare l'hint `/*+ NO_STAR_JOIN */`.

## <a name="scenarios"></a>Scenari

Le linee guida seguenti descrivono alcuni modelli comuni.

### <a name="read-heavy-workloads"></a>Carichi di lavoro con intensa attività di lettura

Per i casi d'uso intensivo di lettura, assicurarsi di usare gli indici. Per risparmiare l'overhead del tempo di lettura, è inoltre consigliabile creare indici di copertura.

### <a name="write-heavy-workloads"></a>Carichi di lavoro con intensa attività di scrittura

Per carichi di lavoro a elevato utilizzo di scrittura in cui la chiave primaria è a incremento progressivo costante, creare bucket di Salt per evitare la scrittura di hotspot, a scapito della velocità effettiva di lettura complessiva, a causa delle analisi aggiuntive necessarie. Quando si usa UPSERT per scrivere un numero elevato di record, disattivare inoltre il commit automatico e creare batch dei record.

### <a name="bulk-deletes"></a>Eliminazione in blocco

Quando si elimina un set di dati di grandi dimensioni, attivare autocommit prima di emettere la query di eliminazione, in modo che il client non debba ricordare le chiavi di riga per tutte le righe eliminate. Il commit automatico impedisce al client di eseguire il buffering delle righe interessate dall'eliminazione, in modo che Phoenix possa eliminarle direttamente nei server di area senza doverle restituire al client.

### <a name="immutable-and-append-only"></a>Caratteristiche non modificabili e di solo accodamento

Se lo scenario predilige la velocità di scrittura rispetto all'integrità dei dati, prendere in considerazione la disabilitazione del log write-ahead durante la creazione delle tabelle:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Per informazioni dettagliate su questa e altre opzioni, vedere la [grammatica di Apache Phoenix](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Passaggi successivi

* [Guida all'ottimizzazione di Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Indici secondari](https://phoenix.apache.org/secondary_indexing.html)
